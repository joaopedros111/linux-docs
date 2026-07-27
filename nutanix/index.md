---
layout: default
title: Nutanix
---

# 🥜 Nutanix

## Introdução

Nutanix é uma plataforma de infraestrutura hiperconvergente usada para virtualização, armazenamento, rede e gerenciamento de ambientes corporativos.

Esta seção reúne anotações, procedimentos e troubleshooting relacionados ao uso do Prism, máquinas virtuais, clusters e operações de infraestrutura.

---

## Temas da seção

* Prism Element
* Prism Central
* máquinas virtuais
* snapshots
* storage
* redes
* clusters
* troubleshooting de infraestrutura

---

## Checklist de operação

* Verificar saúde do cluster.
* Validar alertas ativos.
* Conferir uso de CPU, memória e storage.
* Monitorar snapshots e políticas de retenção.
* Documentar alterações em VMs críticas.
* Validar conectividade antes de intervenções.

---

## Boas práticas

* Evite alterações sem janela ou registro.
* Documente VMs críticas e dependências.
* Acompanhe alertas do cluster antes e depois de mudanças.
* Monitore capacidade de storage.
* Valide backups antes de operações sensíveis.

---

## Resumo para estudo — Nutanix Volumes

### 1. O que é o Nutanix Volumes

O Nutanix Volumes é uma solução de armazenamento em blocos definida por software.

Ele permite fornecer discos para:

- Máquinas virtuais.
- Servidores físicos.
- Aplicações externas ao cluster Nutanix.

A conexão normalmente é realizada pelo protocolo iSCSI. Em máquinas virtuais executadas no AHV, também é possível conectar grupos de volumes diretamente à VM.

### 2. Tipos de armazenamento

#### Armazenamento em blocos

- Fornecido pelo Nutanix Volumes.
- Apresentado como LUN.
- Permite acesso granular no nível de bloco.
- Indicado para bancos de dados e aplicações que precisam de discos dedicados.

#### Armazenamento de arquivos

- Fornecido pelo Nutanix Files.
- Utiliza protocolos como NFS e SMB.
- Os dados são organizados em arquivos e diretórios.

#### Armazenamento de objetos

- Fornecido pelo Nutanix Objects.
- Indicado para dados não estruturados.
- Acessado por APIs REST compatíveis com S3.
- Possui estrutura plana, sem diretórios tradicionais.

### 3. Nutanix Unified Storage

O Nutanix Unified Storage consolida armazenamento de blocos, arquivos e objetos em uma única plataforma.

Principais benefícios:

- Redução de silos de armazenamento.
- Gerenciamento centralizado.
- Simplificação da infraestrutura.
- Maior facilidade para proteger e administrar dados.
- Possibilidade de uso em ambiente local, nuvem pública ou borda.

O licenciamento do Nutanix Unified Storage e do Data Lens é baseado na capacidade utilizável em TiB.

### 4. Por que usar o Nutanix Volumes

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

### 5. Arquitetura

Cada nó do cluster Nutanix possui uma Controller VM, chamada CVM.

As CVMs fornecem os serviços de armazenamento e podem apresentar volumes por iSCSI.

Com isso:

- A aplicação pode utilizar recursos de todo o cluster.
- O desempenho pode crescer junto com a quantidade de nós.
- Falhas ou atualizações podem ser tratadas sem interrupção da aplicação.

A descoberta dos volumes é feita utilizando o endereço virtual chamado:

`iSCSI Data Services IP`

Esse endereço deve ser configurado no Prism Element.

### 6. Volume Groups

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

### 7. Expansão de volumes

Um Volume Group pode ser expandido sem desligar o ambiente.

É possível:

- Adicionar novos discos ao VG.
- Aumentar o tamanho dos discos existentes.

Os VGs funcionam em clusters Nutanix com:

- AHV.
- VMware ESXi.
- Microsoft Hyper-V.

### 8. Grupos de volumes compartilhados

Vários hosts externos podem acessar discos do mesmo VG.

Essa configuração é utilizada principalmente em ambientes de cluster, como:

- Windows Server Failover Cluster.
- Sistemas de arquivos clusterizados.

Antes de compartilhar um volume entre vários servidores, é necessário garantir que a aplicação ou o sistema de arquivos ofereça controle adequado de acesso simultâneo.

Compartilhar um disco comum sem tecnologia de cluster pode provocar corrupção de dados.

### 9. iSCSI Qualified Name

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

### 10. Conectividade iSCSI

Os clientes iSCSI são chamados de iniciadores.

O cluster Nutanix atua como destino.

Portas que devem estar liberadas dos iniciadores para o cluster:

- TCP `3260`.
- TCP `3205`.

Essas portas devem estar acessíveis para:

- iSCSI Data Services IP.
- Endereço IP de cada CVM.

Os servidores também devem ser incluídos na lista de permissões no Prism.

### 11. Alta disponibilidade

O Nutanix Volumes gerencia automaticamente a alta disponibilidade.

O redirecionamento iSCSI ajuda a:

- Distribuir as conexões entre as CVMs.
- Manter o acesso durante falhas.
- Evitar interrupções durante atualizações.
- Simplificar o balanceamento de carga.

Segundo o material, isso reduz a necessidade de ferramentas no cliente, como MPIO, em determinados cenários.

### 12. Casos de uso

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

### 13. Inicialização por iSCSI

O Nutanix Volumes permite que servidores físicos iniciem um sistema operacional diretamente de uma LUN iSCSI.

Nesse cenário:

- O sistema operacional não fica em um disco local.
- O servidor utiliza uma LUN fornecida pelo cluster Nutanix.
- A placa de rede, o HBA e o sistema operacional precisam ser homologados.

### 14. Gerenciamento

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

### 15. Sistemas operacionais compatíveis

O material informa suporte para:

- Microsoft Windows Server.
- Red Hat.
- Oracle Linux.
- CentOS.
- Oracle Solaris.
- SUSE Linux.
- IBM AIX.

### 16. Placas de rede citadas

O material cita suporte para:

- Intel 10 GbE X520 / I350.
- QLogic QLE 8442.
- PCIe2 4-Port 10 Gb + 1 GbE SR+RJ45.

A lista completa e atualizada deve ser consultada no portal Support & Insights da Nutanix.

### 17. Limitações e requisitos importantes

Não utilizar Nutanix Volumes para criar datastore iSCSI para hosts:

- VMware ESXi.
- Microsoft Hyper-V.

Essa configuração não é suportada.

Outras limitações apresentadas:

- Replicação síncrona não é suportada para VGs.
- Metro Availability não é suportado para VGs.
- O iSCSI Data Services IP deve sempre ser configurado.
- As portas `3260` e `3205` devem estar liberadas.

### Pontos principais para memorizar

1. Nutanix Volumes fornece armazenamento em blocos.
2. A conexão principal é feita por iSCSI.
3. O armazenamento é organizado em Volume Groups.
4. Cada Volume Group possui um ou mais vDisks.
5. O iSCSI Data Services IP deve ser configurado.
6. As portas necessárias são TCP `3260` e `3205`.
7. Os hosts precisam ser permitidos no Prism.
8. Os volumes são thin provisioned por padrão.
9. Vários hosts só devem compartilhar discos quando houver tecnologia de cluster.
10. Não é suportado criar datastore iSCSI para ESXi ou Hyper-V usando Nutanix Volumes.

### Perguntas de revisão

1. Qual tipo de armazenamento é fornecido pelo Nutanix Volumes?
   Armazenamento em blocos.

2. Qual protocolo é utilizado para conectar hosts externos?
   iSCSI.

3. O que é um Volume Group?
   Uma coleção de um ou mais discos virtuais apresentados a máquinas virtuais ou servidores físicos.

4. O que é um vDisk?
   Um disco virtual pertencente a um Volume Group.

5. Qual endereço deve ser configurado para a descoberta iSCSI?
   O iSCSI Data Services IP.

6. Quais portas precisam ser liberadas?
   TCP `3260` e TCP `3205`.

7. É possível aumentar um volume sem desligar o ambiente?
   Sim. É possível adicionar discos e expandir discos existentes online.

8. Vários hosts podem acessar o mesmo Volume Group?
   Sim, desde que exista uma tecnologia de cluster ou sistema de arquivos apropriado.

9. Nutanix Volumes pode ser usado como datastore iSCSI para ESXi?
   Não. Essa configuração não é suportada.

10. Como os discos são provisionados por padrão?
    De forma thin.

11. Onde os Volume Groups podem ser gerenciados?
    No Prism Central, Prism Element, aCLI, nCLI ou PowerShell.

12. É possível inicializar um servidor físico por iSCSI?
    Sim, desde que o hardware e o sistema operacional sejam compatíveis.

> 💡 Esta seção será usada como base de procedimentos para administração e suporte de ambientes Nutanix.
