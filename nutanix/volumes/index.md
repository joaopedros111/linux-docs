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

# Nutanix Volumes — Perguntas e Respostas

Material de revisão sobre armazenamento, iSCSI e Grupos de Volumes.

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
