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

O Nutanix Volumes é a camada de armazenamento em blocos da plataforma Nutanix. Ele serve para fornecer discos para VMs, servidores físicos e sistemas externos ao cluster, normalmente via iSCSI.

### O que é importante entender

- O objetivo principal é entregar armazenamento em bloco, não arquivos.
- O acesso é feito por Volume Groups, que agrupam um ou mais vDisks.
- O endereço iSCSI usado para descoberta é o iSCSI Data Services IP.
- As portas TCP 3260 e 3205 precisam estar abertas.
- Os hosts precisam estar autorizados no Prism.
- Os discos são provisionados thin por padrão.
- O serviço é resiliente e pode expandir volumes sem interrupção.

### Conceitos-chave

- Volume Group: conjunto de discos virtuais apresentados a hosts.
- vDisk: disco virtual dentro do Volume Group.
- CVM: Controller VM responsável por oferecer os serviços de armazenamento.
- IQN: identificador do iniciador ou destino iSCSI.

### Pontos práticos para o ambiente da Infraero

- O Nutanix Volumes é relevante para aplicações que precisam de discos dedicados e baixa latência.
- É comum em bancos de dados, clusters e servidores bare metal.
- Pode ser usado para boot de servidores físicos por iSCSI, desde que hardware e SO sejam compatíveis.
- Não é o cenário adequado para criar datastore iSCSI para ESXi ou Hyper-V.

### Resumo final

Em resumo, o Nutanix Volumes é um serviço de armazenamento em blocos baseado em iSCSI, organizado em Volume Groups, com alta disponibilidade, expansão online e integração com ambientes físicos e virtuais.

> 💡 Este material foi organizado para estudo prático do funcionamento do ambiente Nutanix na Infraero.
