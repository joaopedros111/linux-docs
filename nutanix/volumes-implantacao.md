---
layout: default
title: Nutanix-volumes
---

# Implantação e configuração do Nutanix Volumes

## Visão geral

O **Nutanix Volumes** fornece armazenamento em bloco por meio do protocolo **iSCSI**. Ele pode ser utilizado por máquinas virtuais e servidores físicos, incluindo cenários de inicialização do sistema operacional por iSCSI.

---

## Implantação do Nutanix Volumes

O fluxo inicial para habilitar e implementar o Nutanix Volumes envolve as seguintes etapas:

1. Obter o **iSCSI Qualified Name (IQN)** exclusivo do iniciador do cliente.
2. Adicionar esse IQN à lista de permissões do grupo de volumes.
3. Obter o nome do iniciador iSCSI do cliente Windows.
4. Obter o nome do iniciador iSCSI do cliente Linux.
5. Alterar o nome do iniciador no AIX, quando necessário.
6. Criar um endereço **IP de Serviços de Dados iSCSI** para o cluster Nutanix.
7. Provisionar o armazenamento criando um **Volume Group (VG)** composto por um ou mais **vDisks**.
8. Criar uma lista de permissões para autorizar o acesso dos clientes ao Volume Group.
9. Informar os IQNs dos iniciadores dos clientes na configuração do Volume Group.
10. Criar um segredo para o Volume Group, caso a autenticação CHAP seja utilizada.
11. Executar a descoberta do destino iSCSI do cluster Nutanix a partir dos clientes.
12. Opcionalmente, configurar autenticação **CHAP** ou **CHAP mútuo** nos iniciadores e no cluster Nutanix.

> O IP de Serviços de Dados iSCSI não pode ser igual ao IP virtual do cluster.

---

## Configuração do Nutanix Volumes

Um cluster Nutanix pode utilizar todas as **Controller VMs (CVMs)** ao apresentar armazenamento baseado em Volume Groups aos hosts.

Cada vDisk é hospedado por apenas uma CVM por vez, e todo o acesso primário a esse disco ocorre por meio da CVM responsável. Por isso, a distribuição dos discos entre as CVMs pode afetar:

- o consumo de rede;
- o balanceamento de utilização das CVMs;
- o desempenho geral do armazenamento.

O Nutanix Volumes utiliza **redirecionamento iSCSI** para controlar o gerenciamento dos caminhos, o balanceamento de carga dos discos e a resiliência das conexões.

---

## Redirecionamento de destino iSCSI

O método de redirecionamento de destino iSCSI usado na conectividade externa com Volume Groups não depende do **MPIO** para balanceamento de armazenamento ou resiliência de caminhos.

Em vez de configurar sessões iSCSI diretamente para os endereços IP das CVMs, utiliza-se um **IP de Serviços de Dados iSCSI**.

Esse endereço funciona como:

- portal de descoberta;
- ponto inicial de conexão;
- endereço de acesso ao cluster para os clientes iSCSI.

Apenas uma CVM possui o IP de Serviços de Dados por vez. Caso essa CVM fique indisponível, o endereço é transferido para outra CVM, mantendo a disponibilidade do serviço.

Os principais mecanismos envolvidos são:

- redirecionamento iSCSI na conexão inicial;
- destinos virtuais do Volume Group;
- redirecionamento iSCSI em caso de falha;
- redirecionamento do cluster para uma CVM apropriada.

---

## Apresentação a servidor físico

O Nutanix Volumes permite inicializar um sistema operacional por iSCSI em servidores físicos.

Nesse cenário, o host pode iniciar um sistema operacional compatível a partir de uma **LUN**, em vez de utilizar um disco local.

A configuração pode envolver diferentes interfaces e adaptadores, como:

- placa de rede Intel;
- HBA QLogic;
- adaptador IBM PCIe.

---

## Apresentação a máquina virtual

Para fornecer acesso ao armazenamento do cluster, o Nutanix Volumes utiliza o IP de Serviços de Dados iSCSI para a descoberta dos destinos.

A Nutanix não recomenda configurar sessões de clientes iSCSI diretamente nos endereços IP das CVMs.

O IP de Serviços de Dados:

- deve estar na mesma sub-rede das interfaces `eth0` das CVMs;
- ajuda a balancear as solicitações de armazenamento;
- permite a otimização de caminhos no cluster;
- evita gargalos;
- elimina a necessidade de configurar serviços de multipath, como o Microsoft MPIO.

Esse endereço também pode ser utilizado por outros produtos Nutanix, incluindo o Nutanix Files.

### Alteração do IP de Serviços de Dados

A Nutanix recomenda configurar esse endereço apenas uma vez por cluster. Entretanto, ele pode ser alterado no Prism Element.

Ao alterar o endereço, os clientes precisam ser reconfigurados para utilizar o novo IP.

As ações necessárias incluem:

1. Encerrar ou desconectar as sessões iSCSI ou Nutanix Files existentes.
2. Remover o endereço IP antigo, se necessário.
3. Executar novamente a descoberta do destino.
4. Restabelecer as sessões do Nutanix Files, quando aplicável.

> A alteração do IP de Serviços de Dados pode causar indisponibilidade ou outros problemas em recursos como Volumes, NCM Self-Service, Nutanix Disaster Recovery, Nutanix Kubernetes Engine, Objects e Files.

---

## Adição do IP de Serviços de Dados iSCSI

No **Prism Element**, acesse:

```text
Settings > Cluster Details
```

Na página de detalhes do cluster, informe ou atualize:

- nome do cluster;
- FQDN;
- IP virtual;
- IP de Serviços de Dados iSCSI.

Depois, clique em **Save**.

---

## Balanceamento de vDisks em um Volume Group

O balanceamento de vDisks permite que máquinas virtuais com uso intenso de entrada e saída aproveitem a capacidade de armazenamento de várias CVMs.

Em hosts AHV, o balanceamento de vDisks pode ser utilizado para máquinas virtuais convidadas.

Quando o balanceamento está habilitado:

- a VM convidada se comunica diretamente com cada CVM que hospeda um vDisk;
- cada vDisk continua sendo atendido por uma única CVM;
- vários vDisks podem distribuir a carga entre diferentes CVMs.

Para aproveitar várias CVMs, recomenda-se:

1. Criar mais de um vDisk para o sistema de arquivos.
2. Utilizar volumes distribuídos ou com striping no sistema operacional.
3. Distribuir a carga de entrada e saída entre os discos.

Essa configuração melhora o desempenho e reduz a possibilidade de gargalos.

### Comportamento padrão

- O balanceamento de vDisks é desabilitado por padrão em Volume Groups conectados diretamente às VMs.
- O balanceamento é habilitado por padrão em Volume Groups conectados às VMs por meio do IP de Serviços de Dados.
- Um clone de Volume Group criado pela interface web não mantém o balanceamento habilitado por padrão.
- É possível conectar no máximo **10 Volume Groups balanceados** por VM convidada.

Para máquinas virtuais Linux, o tempo limite do dispositivo SCSI deve ser configurado para **60 segundos**.

### Criar um Volume Group com balanceamento

Conecte-se a uma CVM por SSH e execute:

```bash
nutanix@cvm$ acli vg.create vg_name load_balance_vm_attachments=true
```

### Habilitar em um Volume Group existente

```bash
nutanix@cvm$ acli vg.update vg_name load_balance_vm_attachments=true
```

### Verificar a configuração

```bash
nutanix@cvm$ acli vg.get vg_name
```

> Antes de habilitar o balanceamento em um Volume Group existente, desconecte todas as VMs associadas a ele.

---

## CHAP

O **Challenge-Handshake Authentication Protocol (CHAP)** adiciona autenticação às conexões iSCSI usadas pelo Nutanix Volumes.

O CHAP permite que o cliente iSCSI e o destino se autentiquem utilizando uma senha, também chamada de **segredo**.

O Nutanix Volumes oferece suporte a:

- CHAP unidirecional;
- CHAP mútuo.

### CHAP unidirecional

No CHAP unidirecional, o destino autentica o iniciador do cliente durante a conexão.

A Nutanix recomenda o uso de CHAP unidirecional como proteção básica entre o iniciador e o destino do cluster.

Para clientes Linux com CentOS 8 ou superior, são suportados os algoritmos:

- MD5;
- SHA-1;
- SHA-256.

A Nutanix recomenda utilizar SHA-1 ou SHA-256 em vez do MD5 para autenticação e descoberta de destinos mais seguras, especialmente em ambientes sujeitos aos requisitos FIPS.

---

## IPsec

Caso o firewall do Windows Server já utilize IPsec, o canal iSCSI também pode ser protegido por esse protocolo.

Os endereços do modo túnel IPsec podem ser configurados na guia **Configuration** do console de gerenciamento do iniciador iSCSI.

Essa configuração pode incluir:

- endereço de destino;
- endereço externo do modo túnel;
- adaptador local;
- IP do iniciador.

---

## Listas de permissões

Uma lista de permissões, ou **allowlist**, é um conjunto de endereços ou identificadores autorizados a acessar um cluster, um produto ou um recurso específico.

Ela permite o tráfego de origens autorizadas enquanto bloqueia acessos não permitidos.

Existem diferentes tipos de listas de permissões.

### Lista de permissões de sistema de arquivos

Pode ser utilizada na criação de um contêiner para permitir que determinados endereços acessem o armazenamento.

Uma allowlist configurada no nível do contêiner substitui a allowlist global para aquele contêiner.

### Lista de permissões de Volume Group

Essa lista permite o acesso de clientes externos ou que não residem no cluster.

O procedimento envolve:

1. Configurar os iniciadores iSCSI.
2. Obter o IQN do iniciador do cliente.
3. Informar o IQN no campo de cliente da configuração do Volume Group.

Exemplo de IQN:

```text
iqn.1991-05.com.microsoft:winiqntest01
```

Nesse tipo de lista, utiliza-se o **IQN do cliente**, e não o endereço IP.

> O AOS não oferece suporte a endereços IP em listas de permissões de Volume Groups.

---

## Lista de permissões do servidor proxy

A comunicação do Prism é encaminhada pelo servidor proxy até que uma entrada permitida determine que o proxy deve ser ignorado.

Para evitar falhas de comunicação entre o Prism Element e o Prism Central registrado, os destinos podem ser adicionados manualmente à lista de permissões pelo Prism Element ou pelo `nCLI`.

Essa configuração permite:

- ignorar o proxy HTTP entre Prism Element e Prism Central;
- permitir o tráfego direto entre os componentes;
- registrar novos clusters no Prism Central mesmo quando os clusters usam proxy HTTP.

### Comandos nCLI

```bash
http-proxy add-to-whitelist
```

```bash
http-proxy delete-from-whitelist
```

### Regras importantes

- Apenas uma entrada pode ser adicionada ou removida por vez.
- Cada entrada pode ter no máximo 253 caracteres.
- São suportadas até 1.000 entradas.
- Ao remover uma entrada, deve-se remover o destino, e não o tipo de destino.
- Os comandos não aceitam máscara IPv4 em notação de prefixo ou com `*`.
- Sub-redes podem ser informadas com máscara completa.

Exemplos:

```text
10.0.0.0/255.0.0.0
192.168.1.0/255.255.255.0
```

- `contoso.com` e `www.contoso.com` são tratados como destinos diferentes.
- Recomenda-se utilizar nomes de domínio totalmente qualificados, ou FQDNs.

---

## Quando utilizar a lista de permissões

A porta SSL **9440** deve estar aberta nos dois sentidos entre a VM do Prism Central e os clusters registrados ou que serão registrados.

### Porta 9440 aberta

Quando a porta 9440 está aberta no ambiente com proxy, não é necessário adicionar o Prism Central e os clusters à lista de permissões.

### Porta 9440 fechada

Quando a porta 9440 está fechada, é necessário adicionar o Prism Central e os clusters gerenciados ou registrados à lista de permissões.

---

## Resumo

O Nutanix Volumes utiliza iSCSI para apresentar armazenamento em bloco a máquinas virtuais e servidores físicos. Sua arquitetura utiliza um IP de Serviços de Dados para descoberta e redirecionamento das conexões, garantindo disponibilidade e distribuição de carga entre as CVMs.

Os principais componentes são:

- Volume Groups;
- vDisks;
- IQNs dos iniciadores;
- IP de Serviços de Dados iSCSI;
- listas de permissões;
- CHAP;
- IPsec;
- balanceamento de vDisks;
- redirecionamento iSCSI.
