---
layout: default
title: Nutanix Volumes
permalink: /nutanix/volumes/
---

# 🥜 Nutanix Volumes

## Introdução

O Nutanix Volumes é uma solução de armazenamento em blocos definida por software, usada para fornecer discos para máquinas virtuais, servidores físicos e aplicações externas ao cluster Nutanix.

A conexão é normalmente feita por iSCSI e pode ser usada em cenários de alta disponibilidade, bancos de dados e inicialização de sistemas operacionais.

---

## Conceitos principais

* Armazenamento em bloco
* Volume Groups
* vDisks
* iSCSI Data Services IP
* IQN
* LUNs
* thin provisioning

---

## Pontos de estudo

* O Nutanix Volumes oferece armazenamento em bloco para ambientes físicos e virtuais.
* O acesso é feito por iSCSI e o armazenamento é organizado em Volume Groups.
* Os vDisks herdam características do Storage Container onde foram criados.
* O iSCSI Data Services IP deve ser configurado no Prism Element.
* As portas TCP 3260 e 3205 devem estar abertas.
* Os hosts precisam estar autorizados no Prism.
* O provisionamento é feito por padrão em modo thin.

---

> 💡 Este material reúne os conceitos fundamentais do Nutanix Volumes para estudo e referência rápida.