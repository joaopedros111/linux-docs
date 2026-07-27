---
layout: default
title: Nutanix Volumes
permalink: /nutanix/volumes/
---

# 🥜 Resumo para estudo — Nutanix Volumes

## 1. O que é o Nutanix Volumes
O Nutanix Volumes é uma solução de armazenamento em blocos definida por software.

Ele permite fornecer discos para:

- Máquinas virtuais.
- Servidores físicos.
- Aplicações externas ao cluster Nutanix.

A conexão normalmente é realizada pelo protocolo iSCSI. Em máquinas virtuais executadas no AHV, também é possível conectar grupos de volumes diretamente à VM.

---

## 2. Tipos de armazenamento

### Armazenamento em blocos

- Fornecido pelo Nutanix Volumes.
- Apresentado como LUN.
- Permite acesso granular no nível de bloco.
- Indicado para bancos de dados e aplicações que precisam de discos dedicados.

### Armazenamento de arquivos

- Fornecido pelo Nutanix Files.
- Utiliza protocolos como NFS e SMB.
- Os dados são organizados em arquivos e diretórios.

### Armazenamento de objetos

- Fornecido pelo Nutanix Objects.
- Indicado para dados não estruturados.
- Acessado por APIs REST compatíveis com S3.
- Possui estrutura plana, sem diretórios tradicionais.

---

## 3. Nutanix Unified Storage
O Nutanix Unified Storage consolida armazenamento de blocos, arquivos e objetos em uma única plataforma.

Principais benefícios:

- Redução de silos de armazenamento.
- Gerenciamento centralizado.
- Simplificação da infraestrutura.
- Maior facilidade para proteger e administrar dados.
- Possibilidade de uso em ambiente local, nuvem pública ou borda.

O licenciamento do Nutanix Unified Storage e do Data Lens é baseado na capacidade utilizável em TiB.

---

## 4. Por que usar o Nutanix Volumes
O Nutanix Volumes é útil quando uma aplicação precisa de armazenamento em blocos por iSCSI.

Principais vantagens:

- Gerenciamento centralizado pelo Prism.
- Escalabilidade horizontal, ou scale-out.
- Crescimento linear de capacidade e desempenho.
- Alta disponibilidade automática.
- Uso da infraestrutura de rede existente.
- Suporte a servidores físicos e virtuais.
- Provisionamento thin.
- Suporte a compactação, desduplicação e erasure coding.

Ele também ajuda a integrar cargas de trabalho bare metal e virtualizadas na mesma infraestrutura Nutanix.

---

## 5. Arquitetura
Cada nó do cluster Nutanix possui uma Controller VM, chamada CVM.

As CVMs fornecem os serviços de armazenamento e podem apresentar volumes por iSCSI.

Com isso:

- A aplicação pode utilizar recursos de todo o cluster.
- O desempenho pode crescer junto com a quantidade de nós.
- Falhas ou atualizações podem ser tratadas sem interrupção da aplicação.

A descoberta dos volumes é feita utilizando o endereço virtual chamado:

`iSCSI Data Services IP`

Esse endereço deve ser configurado no Prism Element.

---

## 6. Volume Groups
O armazenamento é organizado por meio de Volume Groups, ou VGs.

Um VG é uma coleção de um ou mais discos virtuais, chamados vDisks.

Os VGs podem ser apresentados para:

- Máquinas virtuais.
- Servidores físicos.
- Hosts externos ao cluster.

Quando um host se conecta a um VG por iSCSI, os vDisks são reconhecidos como dispositivos SCSI.

Os discos herdam as propriedades do Storage Container onde foram criados, incluindo:

- Fator de replicação.
- Compactação.
- Erasure coding.
- Outras políticas de armazenamento.

Por padrão, os discos são provisionados de forma thin.

---

## 7. Expansão de volumes
Um Volume Group pode ser expandido sem desligar o ambiente.

É possível:

- Adicionar novos discos ao VG.
- Aumentar o tamanho dos discos existentes.

Os VGs funcionam em clusters Nutanix com:

- AHV.
- VMware ESXi.
- Microsoft Hyper-V.

---

## 8. Grupos de volumes compartilhados
Vários hosts externos podem acessar discos do mesmo VG.

Essa configuração é utilizada principalmente em ambientes de cluster, como:

- Windows Server Failover Cluster.
- Sistemas de arquivos clusterizados.

Antes de compartilhar um volume entre vários servidores, é necessário garantir que a aplicação ou o sistema de arquivos ofereça controle adequado de acesso simultâneo.

Compartilhar um disco comum sem tecnologia de cluster pode provocar corrupção de dados.

---

## 9. iSCSI Qualified Name
O IQN identifica iniciadores e destinos iSCSI.

Formato:

`iqn.aaaa-mm.autoridade-de-nome:nome-unico`

Exemplo:

`iqn.2024-06.com.nutanix.iscsi:servidor01`

Partes do IQN:

- `iqn`: identifica o padrão.
- `aaaa-mm`: data relacionada à propriedade do domínio.
- `autoridade-de-nome`: normalmente baseada em um domínio registrado.
- `nome-unico`: identificação específica do iniciador ou destino.

A autoridade responsável deve garantir que o IQN seja único.

---

## 10. Conectividade iSCSI
Os clientes iSCSI são chamados de iniciadores.

O cluster Nutanix atua como destino.

Portas que devem estar liberadas dos iniciadores para o cluster:

- TCP `3260`.
- TCP `3205`.

Essas portas devem estar acessíveis para:

- iSCSI Data Services IP.
- Endereço IP de cada CVM.

Os servidores também devem ser incluídos na lista de permissões no Prism.

---

## 11. Alta disponibilidade
O Nutanix Volumes gerencia automaticamente a alta disponibilidade.

O redirecionamento iSCSI ajuda a:

- Distribuir as conexões entre as CVMs.
- Manter o acesso durante falhas.
- Evitar interrupções durante atualizações.
- Simplificar o balanceamento de carga.

Segundo o material, isso reduz a necessidade de ferramentas no cliente, como MPIO, em determinados cenários.

---

## 12. Casos de uso
O Nutanix Volumes pode ser usado em:

- Bancos de dados Oracle.
- Oracle RAC.
- Microsoft SQL Server.
- IBM DB2.
- Servidores bare metal.
- Máquinas virtuais fora do cluster.
- Clusters de servidores.
- Aplicações que precisam de baixa latência.
- Sistemas que exigem discos dedicados.
- Inicialização de sistemas operacionais por iSCSI.

---

## 13. Inicialização por iSCSI
O Nutanix Volumes permite que servidores físicos iniciem um sistema operacional diretamente de uma LUN iSCSI.

Nesse cenário:

- O sistema operacional não fica em um disco local.
- O servidor utiliza uma LUN fornecida pelo cluster Nutanix.
- A placa de rede, o HBA e o sistema operacional precisam ser homologados.

---

## 14. Gerenciamento
Os Volume Groups podem ser gerenciados por:

- Prism Central.
- Prism Element.
- aCLI.
- nCLI.
- PowerShell.

No Prism Central, o painel de Volume Groups permite acompanhar:

- Uso.
- Discos.
- Conexões.
- IOPS.
- Largura de banda.
- Latência.
- Cluster associado.
- Alertas e métricas.

---

## 15. Sistemas operacionais compatíveis
O material informa suporte para:

- Microsoft Windows Server.
- Red Hat.
- Oracle Linux.
- CentOS.
- Oracle Solaris.
- SUSE Linux.
- IBM AIX.

---

## 16. Placas de rede citadas
O material cita suporte para:

- Intel 10 GbE X520 / I350.
- QLogic QLE 8442.
- PCIe2 4-Port 10 Gb + 1 GbE SR+RJ45.

A lista completa e atualizada deve ser consultada no portal Support & Insights da Nutanix.

---

## 17. Limitações e requisitos importantes
Não utilizar Nutanix Volumes para criar datastore iSCSI para hosts:

- VMware ESXi.
- Microsoft Hyper-V.

Essa configuração não é suportada.

Outras limitações apresentadas:

- Replicação síncrona não é suportada para VGs.
- Metro Availability não é suportado para VGs.
- O iSCSI Data Services IP deve sempre ser configurado.
- As portas `3260` e `3205` devem estar liberadas.

---

# O que memorizar

1. O Nutanix Volumes fornece armazenamento em bloco.
2. A conexão principal é feita por iSCSI.
3. O armazenamento é organizado em Volume Groups.
4. Cada Volume Group possui um ou mais vDisks.
5. O iSCSI Data Services IP precisa estar configurado.
6. As portas TCP `3260` e `3205` precisam estar liberadas.
7. Os hosts precisam ser permitidos no Prism.
8. Os volumes são thin provisioned por padrão.
9. Compartilhar discos entre vários hosts só faz sentido com tecnologia de cluster.
10. Não é suportado usar Nutanix Volumes como datastore iSCSI para ESXi ou Hyper-V.

---

# Revisão rápida

## Perguntas

1. Qual tipo de armazenamento é fornecido pelo Nutanix Volumes?
2. Qual protocolo é utilizado para conectar hosts externos?
3. O que é um Volume Group?
4. O que é um vDisk?
5. Qual endereço deve ser configurado para a descoberta iSCSI?
6. Quais portas precisam estar liberadas?
7. É possível expandir um volume sem desligar o ambiente?
8. Vários hosts podem acessar o mesmo Volume Group?
9. Nutanix Volumes pode ser usado como datastore iSCSI para ESXi?
10. Como os discos são provisionados por padrão?

## Respostas rápidas

1. Armazenamento em blocos.
2. iSCSI.
3. Uma coleção de discos virtuais apresentados a hosts.
4. Um disco virtual pertencente a um Volume Group.
5. O iSCSI Data Services IP.
6. TCP `3260` e TCP `3205`.
7. Sim, é possível expandir online.
8. Sim, desde que exista tecnologia de cluster apropriada.
9. Não, essa configuração não é suportada.
10. De forma thin.

---

# Implantação e configuração do Nutanix Volumes

## Visão geral

O Nutanix Volumes fornece armazenamento em bloco por meio do protocolo iSCSI. Ele pode ser utilizado por máquinas virtuais e servidores físicos, incluindo cenários de inicialização do sistema operacional por iSCSI.

---

## Implantação do Nutanix Volumes

O fluxo inicial para habilitar e implementar o Nutanix Volumes envolve as seguintes etapas:

1. Obter o iSCSI Qualified Name (IQN) exclusivo do iniciador do cliente.
2. Adicionar esse IQN à lista de permissões do grupo de volumes.
3. Obter o nome do iniciador iSCSI do cliente Windows.
4. Obter o nome do iniciador iSCSI do cliente Linux.
5. Alterar o nome do iniciador no AIX, quando necessário.
6. Criar um endereço IP de Serviços de Dados iSCSI para o cluster Nutanix.
7. Provisionar o armazenamento criando um Volume Group (VG) composto por um ou mais vDisks.
8. Criar uma lista de permissões para autorizar o acesso dos clientes ao Volume Group.
9. Informar os IQNs dos iniciadores dos clientes na configuração do Volume Group.
10. Criar um segredo para o Volume Group, caso a autenticação CHAP seja utilizada.
11. Executar a descoberta do destino iSCSI do cluster Nutanix a partir dos clientes.
12. Opcionalmente, configurar autenticação CHAP ou CHAP mútuo nos iniciadores e no cluster Nutanix.

> O IP de Serviços de Dados iSCSI não pode ser igual ao IP virtual do cluster.

---

## Configuração do Nutanix Volumes

Um cluster Nutanix pode utilizar todas as Controller VMs (CVMs) ao apresentar armazenamento baseado em Volume Groups aos hosts.

Cada vDisk é hospedado por apenas uma CVM por vez, e todo o acesso primário a esse disco ocorre por meio da CVM responsável. Por isso, a distribuição dos discos entre as CVMs pode afetar:

- o consumo de rede;
- o balanceamento de utilização das CVMs;
- o desempenho geral do armazenamento.

O Nutanix Volumes utiliza redirecionamento iSCSI para controlar o gerenciamento dos caminhos, o balanceamento de carga dos discos e a resiliência das conexões.

---

## Redirecionamento de destino iSCSI

O método de redirecionamento de destino iSCSI usado na conectividade externa com Volume Groups não depende do MPIO para balanceamento de armazenamento ou resiliência de caminhos.

Em vez de configurar sessões iSCSI diretamente para os endereços IP das CVMs, utiliza-se um IP de Serviços de Dados iSCSI.

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

Nesse cenário, o host pode iniciar um sistema operacional compatível a partir de uma LUN, em vez de utilizar um disco local.

A configuração pode envolver diferentes interfaces e adaptadores, como:

- placa de rede Intel;
- HBA QLogic;
- adaptador IBM PCIe.

---

## Apresentação a máquina virtual

Para fornecer acesso ao armazenamento do cluster, o Nutanix Volumes utiliza o IP de Serviços de Dados iSCSI para a descoberta dos destinos.

A Nutanix não recomenda configurar sessões de clientes iSCSI diretamente nos endereços IP das CVMs.

O IP de Serviços de Dados:

- deve estar na mesma sub-rede das interfaces eth0 das CVMs;
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

No Prism Element, acesse:

```text
Settings > Cluster Details
```

Na página de detalhes do cluster, informe ou atualize:

- nome do cluster;
- FQDN;
- IP virtual;
- IP de Serviços de Dados iSCSI.

Depois, clique em Save.

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
- É possível conectar no máximo 10 Volume Groups balanceados por VM convidada.

Para máquinas virtuais Linux, o tempo limite do dispositivo SCSI deve ser configurado para 60 segundos.

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

O Challenge-Handshake Authentication Protocol (CHAP) adiciona autenticação às conexões iSCSI usadas pelo Nutanix Volumes.

O CHAP permite que o cliente iSCSI e o destino se autentiquem utilizando uma senha, também chamada de segredo.

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

Os endereços do modo túnel IPsec podem ser configurados na guia Configuration do console de gerenciamento do iniciador iSCSI.

Essa configuração pode incluir:

- endereço de destino;
- endereço externo do modo túnel;
- adaptador local;
- IP do iniciador.

---

## Listas de permissões

Uma lista de permissões, ou allowlist, é um conjunto de endereços ou identificadores autorizados a acessar um cluster, um produto ou um recurso específico.

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

Nesse tipo de lista, utiliza-se o IQN do cliente, e não o endereço IP.

> O AOS não oferece suporte a endereços IP em listas de permissões de Volume Groups.

---

## Lista de permissões do servidor proxy

A comunicação do Prism é encaminhada pelo servidor proxy até que uma entrada permitida determine que o proxy deve ser ignorado.

Para evitar falhas de comunicação entre o Prism Element e o Prism Central registrado, os destinos podem ser adicionados manualmente à lista de permissões pelo Prism Element ou pelo nCLI.

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
- Os comandos não aceitam máscara IPv4 em notação de prefixo ou com *.
- Sub-redes podem ser informadas com máscara completa.

Exemplos:

```text
10.0.0.0/255.0.0.0
192.168.1.0/255.255.255.0
```

- contoso.com e www.contoso.com são tratados como destinos diferentes.
- Recomenda-se utilizar nomes de domínio totalmente qualificados, ou FQDNs.

---

## Quando utilizar a lista de permissões

A porta SSL 9440 deve estar aberta nos dois sentidos entre a VM do Prism Central e os clusters registrados ou que serão registrados.

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

---

# Nutanix Volumes — Perguntas e Respostas

Material de revisão sobre armazenamento, iSCSI e Grupos de Volumes.

> Seção de revisão rápida para estudar e confirmar os conceitos principais do Nutanix Volumes.

## 1. Alta disponibilidade e integração com o WSFC

Pergunta: Um administrador deseja implantar uma aplicação que exige alta disponibilidade e gerenciamento eficiente de dados. A aplicação será executada em um cluster de servidores físicos e precisa ser integrada a uma configuração existente do Windows Server Failover Clustering (WSFC). Qual solução de armazenamento deve ser usada para dar suporte a essa aplicação?

Alternativas:

- Usar armazenamento de arquivos e acessá-lo via NFS, pois ele oferece simplicidade para o compartilhamento de arquivos.
- Usar o Nutanix Volumes e acessá-lo via Fibre Channel (FC), pois ele é mais confiável que o iSCSI.
- Usar armazenamento de objetos e acessá-lo via HTTP, pois ele é ideal para dados não estruturados.
- Usar o Nutanix Volumes e acessá-lo via iSCSI para garantir desempenho adequado e integração com o WSFC.

Resposta correta:

- Usar o Nutanix Volumes e acessá-lo via iSCSI para garantir desempenho adequado e integração com o WSFC.

Explicação: O Nutanix Volumes fornece armazenamento em bloco via iSCSI, adequado para servidores físicos e para integração com Windows Server Failover Clustering.

## 2. Formato correto de IQN

Pergunta: Identifique o formato correto do iSCSI Qualified Name (IQN).

Alternativas:

- iqn.yyyy-mm:uniquename
- iqn.yyyy.naming-authority:uniquename
- iqn.yyyy-mm.naming-authority:uniquename
- iqn.mm-yyyy:uniquename.naming-authority

Resposta correta:

- iqn.yyyy-mm.naming-authority:uniquename

Explicação: Exemplo: iqn.2026-07.com.nutanix:storage01

## 3. Tipos de armazenamento e tipos de dados

Pergunta: Relacione os tipos de armazenamento a seguir com os tipos de dados que eles armazenam.

Alternativas:

- Armazenamento de arquivos
- Armazenamento em blocos
- Armazenamento de objetos

Resposta correta:

- Armazenamento de arquivos → Dados estruturados e semiestruturados
- Armazenamento em blocos → Dados estruturados
- Armazenamento de objetos → Dados não estruturados e semiestruturados

Explicação: A associação considera o uso típico de cada modelo: blocos para cargas estruturadas, objetos para dados não estruturados e arquivos para conteúdos estruturados ou semiestruturados.

## 4. Grupos de Volumes e hipervisores

Pergunta: Verdadeiro ou falso: os Grupos de Volumes (VGs) podem ser utilizados para conectividade iSCSI somente em hipervisores baseados em AHV.

Alternativas:

- Verdadeiro. Os VGs podem ser usados para conectividade iSCSI em clusters AHV, mas somente se a versão do AOS for 6.8 ou superior.
- Falso. Os VGs podem ser usados para conectividade iSCSI somente em hipervisores baseados em AHV ou ESXi.
- Verdadeiro. Os VGs podem ser usados para conectividade iSCSI somente em hipervisores baseados em AHV.
- Falso. Os VGs oferecem conectividade iSCSI em qualquer cluster Nutanix, independentemente de o hipervisor ser ESXi, Hyper-V ou AHV.

Resposta correta:

- Falso. Os VGs oferecem conectividade iSCSI em qualquer cluster Nutanix, independentemente de o hipervisor ser ESXi, Hyper-V ou AHV.

Explicação: Os Volume Groups podem ser apresentados por iSCSI independentemente de o cluster usar AHV, ESXi ou Hyper-V.

## 5. Casos de uso do Nutanix Volumes

Pergunta: Escolha dois casos de uso do Nutanix Volumes. (Escolha duas opções.)

Alternativas:

- Sincronização de IAM
- Ambientes que não são bare-metal
- Inicialização via iSCSI
- Replicação de CVM
- iSCSI para Microsoft Exchange Server

Resposta correta:

- Inicialização via iSCSI
- iSCSI para Microsoft Exchange Server

Explicação: Esses são casos de uso de armazenamento em bloco disponibilizado por iSCSI.

---

## Material de estudo — Nutanix Unified Storage

O Nutanix Unified Storage reúne blocos, arquivos e objetos em uma única plataforma, reduzindo silos e simplificando o gerenciamento do ambiente.

> 💡 Esta seção foi adicionada para complementar o estudo com exercícios práticos e revisão objetiva.
