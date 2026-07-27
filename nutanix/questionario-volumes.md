---
layout: default
title: Nutanix-Volumes
---

# Nutanix Volumes — Perguntas e Respostas

> Material de revisão sobre armazenamento, iSCSI e Grupos de Volumes.

---

## 1. Alta disponibilidade e integração com o WSFC

### Pergunta

Um administrador deseja implantar uma aplicação que exige alta disponibilidade e gerenciamento eficiente de dados. A aplicação será executada em um cluster de servidores físicos e precisa ser integrada a uma configuração existente do **Windows Server Failover Clustering (WSFC)**.

Qual solução de armazenamento deve ser usada para dar suporte a essa aplicação?

### Alternativas

- Usar armazenamento de arquivos e acessá-lo via **NFS**, pois ele oferece simplicidade para o compartilhamento de arquivos.
- Usar o **Nutanix Volumes** e acessá-lo via **Fibre Channel (FC)**, pois ele é mais confiável que o iSCSI.
- Usar armazenamento de objetos e acessá-lo via **HTTP**, pois ele é ideal para dados não estruturados.
- Usar o **Nutanix Volumes** e acessá-lo via **iSCSI** para garantir desempenho adequado e integração com o WSFC.

### Resposta correta

✅ **Usar o Nutanix Volumes e acessá-lo via iSCSI para garantir desempenho adequado e integração com o WSFC.**

### Explicação

O **Nutanix Volumes** fornece armazenamento em bloco via **iSCSI**, adequado para servidores físicos e para integração com o **Windows Server Failover Clustering**.

---

## 2. Formato correto de IQN

### Pergunta

Identifique o formato correto do **iSCSI Qualified Name (IQN)**.

### Alternativas

- `iqn.yyyy-mm:uniquename`
- `iqn.yyyy.naming-authority:uniquename`
- `iqn.yyyy-mm.naming-authority:uniquename`
- `iqn.mm-yyyy:uniquename.naming-authority`

### Resposta correta

✅ `iqn.yyyy-mm.naming-authority:uniquename`

### Exemplo

```text
iqn.2026-07.com.nutanix:storage01
```

### Explicação

O formato do IQN contém:

- o prefixo `iqn`;
- o ano e o mês de registro;
- a autoridade de nomenclatura;
- um identificador exclusivo.

---

## 3. Tipos de armazenamento e tipos de dados

### Pergunta

Relacione os tipos de armazenamento com os tipos de dados que normalmente armazenam.

### Resposta correta

| Tipo de armazenamento | Tipo de dado |
|---|---|
| Armazenamento de arquivos | Dados estruturados e semiestruturados |
| Armazenamento em blocos | Dados estruturados |
| Armazenamento de objetos | Dados não estruturados e semiestruturados |

### Explicação

A associação considera o uso típico de cada modelo:

- **Blocos:** cargas estruturadas, como bancos de dados e discos de máquinas virtuais.
- **Objetos:** arquivos e conteúdos não estruturados ou semiestruturados.
- **Arquivos:** dados organizados em diretórios e compartilhamentos.

---

## 4. Grupos de Volumes e hipervisores

### Pergunta

**Verdadeiro ou falso:** os Grupos de Volumes (**VGs**) podem ser utilizados para conectividade iSCSI somente em hipervisores baseados em AHV.

### Alternativas

- Verdadeiro. Os VGs podem ser usados para conectividade iSCSI em clusters AHV, mas somente se a versão do AOS for 6.8 ou superior.
- Falso. Os VGs podem ser usados para conectividade iSCSI somente em hipervisores baseados em AHV ou ESXi.
- Verdadeiro. Os VGs podem ser usados para conectividade iSCSI somente em hipervisores baseados em AHV.
- Falso. Os VGs oferecem conectividade iSCSI em qualquer cluster Nutanix, independentemente de o hipervisor ser ESXi, Hyper-V ou AHV.

### Resposta correta

✅ **Falso. Os VGs oferecem conectividade iSCSI em qualquer cluster Nutanix, independentemente de o hipervisor ser ESXi, Hyper-V ou AHV.**

### Explicação

Os **Volume Groups** podem ser apresentados por **iSCSI** independentemente de o cluster usar:

- AHV;
- VMware ESXi;
- Microsoft Hyper-V.

---

## 5. Casos de uso do Nutanix Volumes

### Pergunta

Escolha dois casos de uso do **Nutanix Volumes**.

### Alternativas

- Sincronização de IAM
- Ambientes que não são bare-metal
- Inicialização via iSCSI
- Replicação de CVM
- iSCSI para Microsoft Exchange Server

### Respostas corretas

✅ **Inicialização via iSCSI**

✅ **iSCSI para Microsoft Exchange Server**

### Explicação

Esses são casos de uso de armazenamento em bloco disponibilizado por meio de **iSCSI**.

O Nutanix Volumes pode fornecer LUNs para servidores físicos ou virtuais, incluindo cenários de inicialização do sistema operacional e aplicações que exigem armazenamento em bloco compartilhado.

---

## Resumo rápido

| Tema | Resposta |
|---|---|
| WSFC | Nutanix Volumes via iSCSI |
| Formato IQN | `iqn.yyyy-mm.naming-authority:uniquename` |
| Armazenamento em blocos | Dados estruturados |
| VGs e hipervisores | Compatível com AHV, ESXi e Hyper-V |
| Casos de uso | Boot via iSCSI e Microsoft Exchange Server |

---

## Referência

**Material de estudo — Nutanix Unified Storage**
