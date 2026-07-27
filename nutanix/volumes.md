---
layout: default
title: Nutanix-volumes
---

# Nutanix Volumes

## Índice

1. [Visão geral](#visão-geral)
2. [Tipos de armazenamento](#tipos-de-armazenamento)
3. [Nutanix Unified Storage](#nutanix-unified-storage)
4. [O que é o Nutanix Volumes](#o-que-é-o-nutanix-volumes)
5. [Principais benefícios](#principais-benefícios)
6. [Arquitetura](#arquitetura)
7. [Volume Groups](#volume-groups)
8. [Volume Groups compartilhados](#volume-groups-compartilhados)
9. [Formato IQN](#formato-iqn)
10. [Conectividade iSCSI](#conectividade-iscsi)
11. [Alta disponibilidade](#alta-disponibilidade)
12. [Casos de uso](#casos-de-uso)
13. [Inicialização por iSCSI](#inicialização-por-iscsi)
14. [Gerenciamento](#gerenciamento)
15. [Sistemas operacionais compatíveis](#sistemas-operacionais-compatíveis)
16. [Placas de rede citadas](#placas-de-rede-citadas)
17. [Requisitos e limitações](#requisitos-e-limitações)
18. [Pontos para memorizar](#pontos-para-memorizar)
19. [Perguntas de revisão](#perguntas-de-revisão)

---

## Visão geral

O **Nutanix Volumes** é uma solução de armazenamento em blocos definida por software.

Ele permite fornecer discos para:

- Máquinas virtuais;
- Servidores físicos;
- Aplicações externas ao cluster Nutanix;
- Bancos de dados e aplicações que necessitam de LUNs dedicadas.

A conexão normalmente é realizada pelo protocolo **iSCSI**. Em máquinas virtuais executadas no hipervisor AHV, também é possível conectar grupos de volumes diretamente à VM.

---

## Tipos de armazenamento

### Armazenamento em blocos

- Fornecido pelo **Nutanix Volumes**;
- Apresentado como LUN;
- Permite acesso granular no nível de bloco;
- Indicado para bancos de dados e aplicações que precisam de discos dedicados.

### Armazenamento de arquivos

- Fornecido pelo **Nutanix Files**;
- Utiliza protocolos como NFS e SMB;
- Os dados são organizados em arquivos e diretórios.

### Armazenamento de objetos

- Fornecido pelo **Nutanix Objects**;
- Indicado para dados não estruturados;
- Acessado por APIs REST compatíveis com S3;
- Possui estrutura plana, sem diretórios tradicionais.

---

## Nutanix Unified Storage

O **Nutanix Unified Storage** consolida armazenamento de blocos, arquivos e objetos em uma única plataforma.

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

## O que é o Nutanix Volumes

O Nutanix Volumes é uma solução corporativa de armazenamento em blocos.

Ele permite conectar recursos de armazenamento diretamente a:

- Máquinas virtuais;
- Servidores físicos;
- Hosts externos ao cluster;
- Aplicações que precisam de acesso direto a discos.

Cada Controller VM, ou **CVM**, pode apresentar volumes por iSCSI. Isso permite que as aplicações utilizem os recursos de todo o cluster para ampliar desempenho e disponibilidade.

---

## Principais benefícios

- Gerenciamento centralizado pelo Prism;
- Escalabilidade horizontal, ou scale-out;
- Crescimento linear de capacidade e desempenho;
- Alta disponibilidade automática;
- Uso da infraestrutura de rede existente;
- Suporte a servidores físicos e virtuais;
- Provisionamento thin;
- Suporte a compactação;
- Suporte a desduplicação;
- Suporte a erasure coding;
- Integração entre cargas bare metal e virtualizadas.

---

## Arquitetura

Cada nó do cluster Nutanix possui uma **Controller VM**, chamada CVM.

As CVMs fornecem os serviços de armazenamento e podem apresentar volumes por iSCSI.

Com isso:

- A aplicação pode utilizar recursos de todo o cluster;
- O desempenho pode crescer junto com a quantidade de nós;
- Falhas podem ser tratadas sem interrupção;
- Atualizações podem ser realizadas de forma não disruptiva.

A descoberta dos volumes utiliza um endereço virtual chamado:

```text
iSCSI Data Services IP
```

Esse endereço deve ser configurado no Prism Element.

---

## Volume Groups

O armazenamento é organizado por meio de **Volume Groups**, ou VGs.

Um VG é uma coleção de um ou mais discos virtuais chamados **vDisks**.

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

Por padrão, os discos são provisionados de forma thin.

### Expansão

Um Volume Group pode ser expandido sem desligar o ambiente.

É possível:

- Adicionar novos discos ao VG;
- Aumentar o tamanho dos discos existentes.

Os VGs oferecem conectividade iSCSI em clusters Nutanix com:

- AHV;
- VMware ESXi;
- Microsoft Hyper-V.

---

## Volume Groups compartilhados

Vários hosts externos podem acessar discos do mesmo VG.

Esse cenário é comum em:

- Windows Server Failover Cluster;
- Ambientes com sistemas de arquivos clusterizados;
- Aplicações que utilizam armazenamento compartilhado.

Antes de compartilhar discos entre vários hosts, é necessário garantir que exista uma tecnologia de cluster apropriada.

> Compartilhar um disco comum entre vários hosts sem tecnologia de cluster pode causar corrupção de dados.

---

## Formato IQN

O **iSCSI Qualified Name**, ou IQN, identifica iniciadores e destinos iSCSI.

Formato:

```text
iqn.aaaa-mm.autoridade-de-nome:nome-unico
```

Exemplo:

```text
iqn.2024-06.com.nutanix.iscsi:servidor01
```

Partes do IQN:

- `iqn`: identifica o padrão;
- `aaaa-mm`: data relacionada à propriedade do domínio;
- `autoridade-de-nome`: normalmente baseada em um domínio registrado;
- `nome-unico`: identificação específica do iniciador ou destino.

A autoridade responsável deve garantir que cada IQN seja único.

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

- iSCSI Data Services IP;
- Endereço IP de cada CVM.

Os servidores também devem ser incluídos na lista de permissões do Prism.

---

## Alta disponibilidade

O Nutanix Volumes gerencia automaticamente a alta disponibilidade.

O redirecionamento iSCSI ajuda a:

- Distribuir conexões entre as CVMs;
- Manter o acesso durante falhas;
- Evitar interrupções durante atualizações;
- Simplificar o balanceamento de carga.

Segundo o material, isso reduz a necessidade de ferramentas no cliente, como MPIO, em determinados cenários.

---

## Casos de uso

O Nutanix Volumes pode ser utilizado em:

- Bancos de dados Oracle;
- Oracle RAC;
- Microsoft SQL Server;
- IBM DB2;
- Servidores bare metal;
- Máquinas virtuais fora do cluster;
- Clusters de servidores;
- Aplicações que precisam de baixa latência;
- Sistemas que exigem discos dedicados;
- Inicialização de sistemas operacionais por iSCSI.

---

## Inicialização por iSCSI

O Nutanix Volumes permite que servidores físicos inicializem um sistema operacional diretamente a partir de uma LUN iSCSI.

Nesse cenário:

- O sistema operacional não precisa estar em um disco local;
- O servidor utiliza uma LUN fornecida pelo cluster Nutanix;
- A placa de rede, o HBA e o sistema operacional precisam ser homologados.

---

## Gerenciamento

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

## Sistemas operacionais compatíveis

O material cita suporte para:

- Microsoft Windows Server;
- Red Hat;
- Oracle Linux;
- CentOS;
- Oracle Solaris;
- SUSE Linux;
- IBM AIX.

---

## Placas de rede citadas

O material cita as seguintes placas:

- Intel 10 GbE Network Adapter X520 / I350;
- QLogic QLE 8442 Converged Network Adapter;
- PCIe2 4-Port 10 Gb + 1 GbE SR+RJ45 Adapter.

A lista completa e atualizada deve ser consultada no portal Support & Insights da Nutanix.

---

## Requisitos e limitações

### Requisitos

- Configurar o iSCSI Data Services IP no Prism Element;
- Liberar as portas TCP 3260 e 3205;
- Permitir comunicação com os endereços IP das CVMs;
- Adicionar os iniciadores à lista de permissões do Prism.

### Limitações

Não utilize o Nutanix Volumes para criar datastore iSCSI para:

- VMware ESXi;
- Microsoft Hyper-V.

Essa configuração não é suportada.

Também não são suportados para Volume Groups:

- Replicação síncrona;
- Metro Availability.

---

## Pontos para memorizar

1. Nutanix Volumes fornece armazenamento em blocos.
2. A conexão principal é feita por iSCSI.
3. O armazenamento é organizado em Volume Groups.
4. Cada Volume Group possui um ou mais vDisks.
5. O iSCSI Data Services IP deve ser configurado.
6. As portas necessárias são TCP 3260 e 3205.
7. Os hosts precisam ser permitidos no Prism.
8. Os volumes são thin provisioned por padrão.
9. Vários hosts só devem compartilhar discos quando houver tecnologia de cluster.
10. Não é suportado criar datastore iSCSI para ESXi ou Hyper-V usando Nutanix Volumes.

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

O iSCSI Data Services IP.

### 6. Quais portas precisam ser liberadas?

TCP 3260 e TCP 3205.

### 7. É possível aumentar um volume sem desligar o ambiente?

Sim. É possível adicionar discos e expandir discos existentes online.

### 8. Vários hosts podem acessar o mesmo Volume Group?

Sim, desde que exista uma tecnologia de cluster ou sistema de arquivos apropriado.

### 9. Nutanix Volumes pode ser usado como datastore iSCSI para ESXi?

Não. Essa configuração não é suportada.

### 10. Como os discos são provisionados por padrão?

De forma thin.

### 11. Onde os Volume Groups podem ser gerenciados?

No Prism Central, Prism Element, aCLI, nCLI ou PowerShell.

### 12. É possível inicializar um servidor físico por iSCSI?

Sim, desde que o hardware e o sistema operacional sejam compatíveis.
