---
layout: default
title: Nutanix-volumes
---

# Nutanix Volumes — Visão Geral, Implantação e Configuração

Material de estudo sobre armazenamento em blocos, iSCSI, Volume Groups, alta disponibilidade, segurança e administração do Nutanix Volumes.

## Índice

1. [Visão geral](#visão-geral)
2. [Tipos de armazenamento](#tipos-de-armazenamento)
3. [Nutanix Unified Storage](#nutanix-unified-storage)
4. [Principais benefícios](#principais-benefícios)
5. [Arquitetura](#arquitetura)
6. [Volume Groups e vDisks](#volume-groups-e-vdisks)
7. [Volume Groups compartilhados](#volume-groups-compartilhados)
8. [Formato IQN](#formato-iqn)
9. [Implantação do Nutanix Volumes](#implantação-do-nutanix-volumes)
10. [IP de Serviços de Dados iSCSI](#ip-de-serviços-de-dados-iscsi)
11. [Conectividade iSCSI](#conectividade-iscsi)
12. [Redirecionamento e alta disponibilidade](#redirecionamento-e-alta-disponibilidade)
13. [Balanceamento de vDisks](#balanceamento-de-vdisks)
14. [Listas de permissões](#listas-de-permissões)
15. [Segurança com CHAP e IPsec](#segurança-com-chap-e-ipsec)
16. [Apresentação a máquinas virtuais](#apresentação-a-máquinas-virtuais)
17. [Apresentação a servidores físicos](#apresentação-a-servidores-físicos)
18. [Inicialização por iSCSI](#inicialização-por-iscsi)
19. [Alteração do IP de Serviços de Dados](#alteração-do-ip-de-serviços-de-dados)
20. [Gerenciamento e monitoramento](#gerenciamento-e-monitoramento)
21. [Casos de uso](#casos-de-uso)
22. [Sistemas operacionais e adaptadores citados](#sistemas-operacionais-e-adaptadores-citados)
23. [Requisitos e limitações](#requisitos-e-limitações)
24. [Prism Central, proxy e allowlist](#prism-central-proxy-e-allowlist)
25. [Pontos para memorizar](#pontos-para-memorizar)
26. [Perguntas de revisão](#perguntas-de-revisão)

---

## Visão geral

O **Nutanix Volumes** é uma solução corporativa de armazenamento em blocos definida por software.

Ela fornece discos para:

- Máquinas virtuais;
- Servidores físicos;
- Hosts externos ao cluster Nutanix;
- Bancos de dados;
- Aplicações que necessitam de LUNs ou discos dedicados.

A conexão com clientes externos normalmente utiliza o protocolo **iSCSI**. Em máquinas virtuais executadas no AHV, também é possível conectar Volume Groups diretamente à VM.

---

## Tipos de armazenamento

### Armazenamento em blocos

- Fornecido pelo **Nutanix Volumes**;
- Apresentado ao cliente como LUN ou dispositivo SCSI;
- Permite acesso granular no nível de bloco;
- Indicado para bancos de dados e aplicações que precisam de discos dedicados.

### Armazenamento de arquivos

- Fornecido pelo **Nutanix Files**;
- Utiliza protocolos como NFS e SMB;
- Organiza os dados em arquivos e diretórios.

### Armazenamento de objetos

- Fornecido pelo **Nutanix Objects**;
- Indicado para dados não estruturados;
- Acessado por APIs REST compatíveis com S3;
- Utiliza uma estrutura plana, sem diretórios tradicionais.

---

## Nutanix Unified Storage

O **Nutanix Unified Storage**, ou NUS, consolida armazenamento de blocos, arquivos e objetos em uma única plataforma.

Principais benefícios:

- Redução de silos de armazenamento;
- Gerenciamento centralizado;
- Simplificação da infraestrutura;
- Maior facilidade para proteger e administrar dados;
- Suporte a ambientes locais, nuvem pública e borda.

O Nutanix Unified Storage e o Data Lens são licenciados com base na capacidade utilizável em TiB.

A solução pode ser implantada:

- Como cluster dedicado de armazenamento definido por software;
- Junto ao Nutanix Cloud Infrastructure, NCI.

No modo dedicado, é permitida no máximo uma máquina virtual por nó. Caso sejam necessárias mais máquinas virtuais, o NUS deve operar no modo NCI.

---

## Principais benefícios

- Gerenciamento centralizado pelo Prism;
- Escalabilidade horizontal, ou scale-out;
- Crescimento de capacidade e desempenho com a adição de nós;
- Alta disponibilidade automática;
- Uso da infraestrutura de rede existente;
- Suporte a servidores físicos e virtuais;
- Provisionamento thin por padrão;
- Suporte a compactação;
- Suporte a desduplicação;
- Suporte a erasure coding;
- Integração entre cargas bare metal e virtualizadas;
- Expansão de discos sem necessidade de desligamento.

---

## Arquitetura

Cada nó de um cluster Nutanix possui uma **Controller VM**, chamada **CVM**.

As CVMs fornecem os serviços de armazenamento e podem apresentar volumes por iSCSI. Dessa forma:

- A aplicação pode utilizar recursos de todo o cluster;
- O desempenho pode crescer com a quantidade de nós;
- Falhas podem ser tratadas automaticamente;
- Atualizações podem ocorrer de forma não disruptiva.

Cada vDisk é hospedado por apenas uma CVM por vez. O acesso primário ao disco ocorre pela CVM responsável por ele.

Por esse motivo, a distribuição dos vDisks entre as CVMs pode afetar:

- Consumo de rede;
- Utilização das CVMs;
- Balanceamento de carga;
- Desempenho geral do armazenamento.

---

## Volume Groups e vDisks

O armazenamento é organizado por meio de **Volume Groups**, ou **VGs**.

Um Volume Group é uma coleção de um ou mais discos virtuais chamados **vDisks**.

Os VGs podem ser apresentados para:

- Máquinas virtuais;
- Servidores físicos;
- Hosts externos ao cluster.

Quando um host se conecta a um VG por iSCSI, os vDisks são reconhecidos como dispositivos SCSI.

Os discos herdam propriedades do Storage Container onde foram criados, incluindo:

- Fator de replicação;
- Compactação;
- Erasure coding;
- Outras políticas de armazenamento.

Por padrão, os discos são provisionados de forma **thin**.

### Expansão

Um Volume Group pode ser expandido sem desligar o ambiente.

É possível:

- Adicionar novos vDisks ao VG;
- Aumentar o tamanho dos discos existentes.

Os Volume Groups oferecem conectividade iSCSI em clusters Nutanix com:

- AHV;
- VMware ESXi;
- Microsoft Hyper-V.

---

## Volume Groups compartilhados

Vários hosts externos podem acessar discos do mesmo Volume Group.

Esse cenário é comum em:

- Windows Server Failover Cluster, WSFC;
- Ambientes com sistemas de arquivos clusterizados;
- Aplicações que utilizam armazenamento compartilhado.

> Compartilhar um disco comum entre vários hosts sem uma tecnologia de cluster apropriada pode causar corrupção de dados.

---

## Formato IQN

O **iSCSI Qualified Name**, ou **IQN**, identifica iniciadores e destinos iSCSI.

Formato:

```text
iqn.aaaa-mm.autoridade-de-nome:nome-unico
```

Exemplo:

```text
iqn.2024-06.com.nutanix.iscsi:servidor01
```

Outro exemplo de iniciador Windows:

```text
iqn.1991-05.com.microsoft:winiqntest01
```

Partes do IQN:

- `iqn`: identifica o padrão;
- `aaaa-mm`: data relacionada à propriedade do domínio;
- `autoridade-de-nome`: normalmente baseada em um domínio registrado;
- `nome-unico`: identificação específica do iniciador ou destino.

A autoridade responsável deve garantir que cada IQN seja único.

---

## Implantação do Nutanix Volumes

Fluxo geral de implantação:

1. Obter o IQN exclusivo do iniciador iSCSI do cliente.
2. Obter o nome do iniciador em clientes Windows, Linux ou AIX.
3. Alterar o nome do iniciador no AIX, quando necessário.
4. Criar o **IP de Serviços de Dados iSCSI** para o cluster.
5. Criar um **Volume Group**.
6. Adicionar um ou mais **vDisks** ao Volume Group.
7. Criar uma lista de permissões para o VG.
8. Informar os IQNs autorizados na configuração do Volume Group.
9. Criar um segredo, caso a autenticação CHAP seja utilizada.
10. Executar a descoberta do destino iSCSI no cliente.
11. Estabelecer a sessão iSCSI.
12. Opcionalmente, configurar CHAP ou CHAP mútuo.

> O IP de Serviços de Dados iSCSI não pode ser igual ao IP virtual do cluster.

---

## IP de Serviços de Dados iSCSI

A descoberta dos volumes utiliza um endereço virtual chamado:

```text
iSCSI Data Services IP
```

Esse endereço deve ser configurado no **Prism Element**.

Ele funciona como:

- Portal de descoberta;
- Ponto inicial de conexão;
- Endereço de acesso ao cluster para clientes iSCSI;
- Ponto de entrada para o redirecionamento às CVMs apropriadas.

Apenas uma CVM possui o IP de Serviços de Dados por vez. Caso essa CVM fique indisponível, o endereço é transferido para outra CVM.

### Regras importantes

- O endereço deve estar na mesma sub-rede das interfaces `eth0` das CVMs;
- Não deve ser igual ao IP virtual do cluster;
- A Nutanix não recomenda criar sessões diretamente para os IPs das CVMs;
- O endereço pode ser compartilhado com outros serviços Nutanix, incluindo Nutanix Files.

### Configuração no Prism Element

Acesse:

```text
Settings > Cluster Details
```

Na página de detalhes do cluster, informe ou atualize:

- Nome do cluster;
- FQDN;
- IP virtual;
- IP de Serviços de Dados iSCSI.

Depois, clique em **Save**.

---

## Conectividade iSCSI

Os clientes iSCSI são chamados de **iniciadores**.

O cluster Nutanix atua como **destino**.

Portas que devem estar liberadas dos iniciadores para o cluster:

```text
TCP 3260
TCP 3205
```

Essas portas devem estar acessíveis para:

- IP de Serviços de Dados iSCSI;
- Endereço IP de cada CVM.

Os IQNs dos servidores também devem ser adicionados à lista de permissões do Volume Group.

---

## Redirecionamento e alta disponibilidade

O Nutanix Volumes utiliza **redirecionamento de destino iSCSI** para controlar:

- Gerenciamento dos caminhos;
- Distribuição das conexões;
- Balanceamento dos vDisks;
- Resiliência das sessões;
- Continuidade durante falhas e atualizações.

O método de redirecionamento usado na conectividade externa não depende do **MPIO** para balanceamento ou resiliência dos caminhos.

Fluxo simplificado:

1. O cliente se conecta ao IP de Serviços de Dados.
2. O cluster identifica a CVM responsável pelo vDisk.
3. A sessão é redirecionada para o destino virtual apropriado.
4. Em caso de falha, o cluster redireciona a conexão para outra CVM.

Se uma CVM ativa ficar offline, as conexões podem ser redirecionadas e restabelecidas por outra CVM, preservando o acesso ao armazenamento.

---

## Balanceamento de vDisks

O balanceamento de vDisks permite que máquinas virtuais com uso intenso de entrada e saída aproveitem a capacidade de várias CVMs.

Quando o balanceamento está habilitado:

- A VM convidada se comunica diretamente com cada CVM que hospeda um vDisk;
- Cada vDisk continua sendo atendido por uma única CVM;
- Vários vDisks podem distribuir a carga entre diferentes CVMs.

Para aproveitar várias CVMs, recomenda-se:

1. Criar mais de um vDisk para o sistema de arquivos.
2. Utilizar volumes distribuídos ou com striping no sistema operacional.
3. Distribuir a carga de entrada e saída entre os discos.

### Comportamento padrão

- Desabilitado por padrão em VGs conectados diretamente às VMs;
- Habilitado por padrão em VGs conectados às VMs pelo IP de Serviços de Dados;
- Um clone criado pela interface web não mantém o balanceamento habilitado por padrão;
- É possível conectar no máximo **10 Volume Groups balanceados** por VM convidada;
- Para VMs Linux, o timeout do dispositivo SCSI deve ser configurado para **60 segundos**.

### Criar um Volume Group com balanceamento

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

## Listas de permissões

Uma lista de permissões, ou **allowlist**, define quais clientes podem acessar determinado recurso.

### Allowlist de Volume Group

A lista de permissões de um Volume Group autoriza clientes externos ou hosts que não residem no cluster.

Procedimento:

1. Configurar o iniciador iSCSI no cliente;
2. Obter o IQN do cliente;
3. Informar o IQN no campo de clientes do Volume Group.

Nesse tipo de lista, utiliza-se o **IQN do cliente**, e não o endereço IP.

> O AOS não oferece suporte a endereços IP em listas de permissões de Volume Groups.

### Allowlist de sistema de arquivos

Pode ser utilizada na criação de um contêiner para permitir acesso de determinados endereços.

Uma allowlist configurada no nível do contêiner substitui a allowlist global para aquele contêiner.

---

## Segurança com CHAP e IPsec

### CHAP

O **Challenge-Handshake Authentication Protocol**, ou **CHAP**, adiciona autenticação às conexões iSCSI.

O cliente e o destino utilizam uma senha compartilhada, chamada de **segredo**.

O Nutanix Volumes oferece suporte a:

- CHAP unidirecional;
- CHAP mútuo.

#### CHAP unidirecional

No CHAP unidirecional, o destino autentica o iniciador durante a conexão.

A Nutanix recomenda o uso de CHAP unidirecional como proteção básica para conexões iSCSI.

Para clientes Linux com CentOS 8 ou superior, são citados os algoritmos:

- MD5;
- SHA-1;
- SHA-256.

Recomenda-se utilizar SHA-1 ou SHA-256 em vez de MD5, especialmente em ambientes sujeitos a requisitos FIPS.

### IPsec

Caso o firewall do Windows Server já utilize IPsec, o canal iSCSI também pode ser protegido por esse protocolo.

A configuração pode incluir:

- Endereço de destino;
- Endereço externo do túnel;
- Adaptador local;
- IP do iniciador.

Os endereços do modo túnel podem ser configurados na guia **Configuration** do console do iniciador iSCSI.

---

## Apresentação a máquinas virtuais

O Nutanix Volumes pode fornecer armazenamento a máquinas virtuais por meio de:

- Conexão direta do Volume Group à VM, em AHV;
- Conexão iSCSI usando o IP de Serviços de Dados.

O IP de Serviços de Dados:

- Ajuda a balancear solicitações;
- Permite otimização de caminhos;
- Evita gargalos;
- Reduz a necessidade de configurar multipath, como Microsoft MPIO, nos cenários descritos.

---

## Apresentação a servidores físicos

O Nutanix Volumes pode apresentar LUNs a servidores físicos por iSCSI.

O host pode utilizar os discos para:

- Dados de aplicações;
- Bancos de dados;
- Sistemas de arquivos clusterizados;
- Inicialização do sistema operacional.

A configuração pode envolver adaptadores como:

- Placa de rede Intel;
- HBA QLogic;
- Adaptador IBM PCIe.

---

## Inicialização por iSCSI

O Nutanix Volumes permite que servidores físicos inicializem o sistema operacional diretamente de uma LUN iSCSI.

Nesse cenário:

- O sistema operacional não precisa estar em disco local;
- O servidor utiliza uma LUN fornecida pelo cluster Nutanix;
- A placa de rede, o HBA e o sistema operacional devem ser compatíveis e homologados.

---

## Alteração do IP de Serviços de Dados

A Nutanix recomenda configurar o IP de Serviços de Dados apenas uma vez por cluster. Entretanto, ele pode ser alterado pelo Prism Element.

Após a alteração, os clientes precisam ser reconfigurados para utilizar o novo IP.

Ações necessárias:

1. Encerrar ou desconectar as sessões iSCSI ou Nutanix Files existentes.
2. Remover o endereço antigo, quando necessário.
3. Configurar o novo endereço nos clientes.
4. Executar novamente a descoberta do destino.
5. Restabelecer as sessões.

> A alteração pode causar indisponibilidade ou problemas em recursos como Volumes, NCM Self-Service, Nutanix Disaster Recovery, Nutanix Kubernetes Engine, Objects e Files.

---

## Gerenciamento e monitoramento

Os Volume Groups podem ser gerenciados por:

- Prism Central;
- Prism Element;
- aCLI;
- nCLI;
- PowerShell.

No Prism Central, o painel de Volume Groups permite acompanhar:

- Uso;
- Quantidade de discos;
- Conexões;
- IOPS;
- Largura de banda de E/S;
- Latência de E/S;
- Cluster associado;
- Alertas;
- Métricas.

---

## Casos de uso

O Nutanix Volumes pode ser utilizado em:

- Oracle Database;
- Oracle RAC;
- Microsoft SQL Server;
- IBM DB2;
- Servidores bare metal;
- Máquinas virtuais externas ao cluster;
- Clusters de servidores;
- Windows Server Failover Cluster;
- Aplicações que precisam de baixa latência;
- Sistemas que exigem discos dedicados;
- Inicialização de sistemas operacionais por iSCSI.

---

## Sistemas operacionais e adaptadores citados

### Sistemas operacionais

O material cita suporte para:

- Microsoft Windows Server;
- Red Hat;
- Oracle Linux;
- CentOS;
- Oracle Solaris;
- SUSE Linux;
- IBM AIX.

### Adaptadores de rede e HBA

São citados:

- Intel 10 GbE Network Adapter X520 / I350;
- QLogic QLE 8442 Converged Network Adapter;
- PCIe2 4-Port 10 Gb + 1 GbE SR+RJ45 Adapter.

A lista completa e atualizada deve ser consultada no portal Support & Insights da Nutanix.

---

## Requisitos e limitações

### Requisitos

- Configurar o IP de Serviços de Dados iSCSI no Prism Element;
- Garantir que ele esteja na mesma sub-rede das interfaces `eth0` das CVMs;
- Liberar as portas TCP 3260 e 3205;
- Permitir comunicação com os IPs das CVMs;
- Obter os IQNs dos iniciadores;
- Adicionar os IQNs à allowlist do Volume Group;
- Configurar CHAP, quando necessário.

### Limitações

Não utilize o Nutanix Volumes para criar datastore iSCSI para:

- VMware ESXi;
- Microsoft Hyper-V.

Essa configuração não é suportada.

Também não são suportados para Volume Groups:

- Replicação síncrona;
- Metro Availability.

---

## Prism Central, proxy e allowlist

A comunicação do Prism pode ser encaminhada por um servidor proxy. Para evitar falhas entre o Prism Element e o Prism Central, determinados destinos podem ser adicionados à lista de permissões do proxy.

Isso permite:

- Ignorar o proxy HTTP entre Prism Element e Prism Central;
- Permitir tráfego direto entre os componentes;
- Registrar clusters no Prism Central mesmo quando utilizam proxy HTTP.

### Comandos nCLI

Adicionar entrada:

```bash
http-proxy add-to-whitelist
```

Remover entrada:

```bash
http-proxy delete-from-whitelist
```

### Regras importantes

- Apenas uma entrada pode ser adicionada ou removida por vez;
- Cada entrada pode ter no máximo 253 caracteres;
- São suportadas até 1.000 entradas;
- Ao remover, deve-se informar o destino, e não o tipo de destino;
- Os comandos não aceitam máscara IPv4 em notação de prefixo ou com `*`;
- Sub-redes podem ser informadas com máscara completa;
- `contoso.com` e `www.contoso.com` são tratados como destinos diferentes;
- Recomenda-se utilizar FQDNs.

Exemplos:

```text
10.0.0.0/255.0.0.0
192.168.1.0/255.255.255.0
```

### Porta 9440

A porta SSL **9440** deve estar aberta nos dois sentidos entre a VM do Prism Central e os clusters registrados.

- Quando a porta 9440 está aberta, normalmente não é necessário adicionar o Prism Central e os clusters à allowlist do proxy;
- Quando a porta 9440 está fechada, é necessário adicionar os destinos à lista de permissões.

---

## Pontos para memorizar

1. Nutanix Volumes fornece armazenamento em blocos.
2. A conexão principal com hosts externos utiliza iSCSI.
3. O armazenamento é organizado em Volume Groups.
4. Cada Volume Group possui um ou mais vDisks.
5. Cada vDisk é hospedado por uma CVM por vez.
6. O IP de Serviços de Dados iSCSI é o portal de descoberta.
7. O IP de Serviços de Dados não pode ser igual ao IP virtual do cluster.
8. As portas necessárias são TCP 3260 e 3205.
9. A allowlist de Volume Group utiliza o IQN, e não o IP do cliente.
10. Os volumes são thin provisioned por padrão.
11. O redirecionamento iSCSI fornece balanceamento e resiliência.
12. Uma CVM indisponível não deve interromper permanentemente o acesso, pois o serviço é redirecionado.
13. Vários hosts só devem compartilhar discos quando houver tecnologia de cluster.
14. CHAP pode ser usado para proteger as conexões iSCSI.
15. Não é suportado criar datastore iSCSI para ESXi ou Hyper-V com Nutanix Volumes.
16. Ao alterar o IP de Serviços de Dados, os clientes devem ser reconfigurados.
17. O balanceamento de vDisks pode distribuir a carga entre várias CVMs.
18. No máximo 10 Volume Groups balanceados podem ser conectados a uma VM convidada.

---

## Perguntas de revisão

### 1. Qual tipo de armazenamento é fornecido pelo Nutanix Volumes?

Armazenamento em blocos.

### 2. Qual protocolo é utilizado para conectar hosts externos?

iSCSI.

### 3. O que é um Volume Group?

Uma coleção de um ou mais discos virtuais apresentados a máquinas virtuais ou servidores físicos.

### 4. O que é um vDisk?

Um disco virtual pertencente a um Volume Group.

### 5. Qual endereço deve ser configurado para descoberta iSCSI?

O IP de Serviços de Dados iSCSI.

### 6. O IP de Serviços de Dados pode ser igual ao IP virtual do cluster?

Não.

### 7. Quais portas precisam ser liberadas?

TCP 3260 e TCP 3205.

### 8. Qual identificação é usada na allowlist de Volume Group?

O IQN do iniciador iSCSI.

### 9. É possível aumentar um volume sem desligar o ambiente?

Sim. É possível adicionar discos e expandir discos existentes online.

### 10. Vários hosts podem acessar o mesmo Volume Group?

Sim, desde que exista uma tecnologia de cluster ou sistema de arquivos apropriado.

### 11. O que ocorre quando a CVM que possui o IP de Serviços de Dados fica indisponível?

O endereço é transferido para outra CVM, mantendo a disponibilidade do serviço.

### 12. O Nutanix Volumes depende de MPIO para o redirecionamento externo?

Não. O redirecionamento iSCSI gerencia os caminhos e a resiliência nos cenários descritos.

### 13. Como proteger uma conexão iSCSI?

Configurando CHAP unidirecional, CHAP mútuo ou IPsec, conforme o ambiente.

### 14. Como habilitar o balanceamento em um Volume Group existente?

```bash
nutanix@cvm$ acli vg.update vg_name load_balance_vm_attachments=true
```

### 15. O que deve ser feito antes de habilitar o balanceamento em um VG existente?

Desconectar todas as VMs associadas ao Volume Group.

### 16. Quantos Volume Groups balanceados podem ser conectados a uma VM?

No máximo 10.

### 17. Qual timeout SCSI deve ser configurado em VMs Linux?

60 segundos.

### 18. O Nutanix Volumes pode ser usado como datastore iSCSI para ESXi?

Não. Essa configuração não é suportada.

### 19. O que deve ser feito após alterar o IP de Serviços de Dados?

Reconfigurar os clientes, executar novamente a descoberta e restabelecer as sessões.

### 20. Onde os Volume Groups podem ser gerenciados?

No Prism Central, Prism Element, aCLI, nCLI ou PowerShell.
