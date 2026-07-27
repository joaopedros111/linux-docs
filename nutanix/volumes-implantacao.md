---
layout: default
title: Nutanix Volumes Implantação
---

# 🥜 Nutanix Volumes — Implantação

## Introdução

Esta página reúne os passos principais para implantar e configurar o Nutanix Volumes em um ambiente Nutanix.

---

## Etapas principais

1. Obter o IQN do iniciador do cliente.
2. Adicionar esse IQN à lista de permissões do Volume Group.
3. Criar o IP de Serviços de Dados iSCSI no cluster.
4. Criar um Volume Group com um ou mais vDisks.
5. Configurar a lista de permissões para o acesso dos hosts.
6. Executar a descoberta do destino iSCSI a partir dos clientes.
7. Opcionalmente, configurar CHAP e CHAP mútuo.

---

## Pontos importantes

* O IP de Serviços de Dados iSCSI não pode ser igual ao IP virtual do cluster.
* O Nutanix Volumes usa redirecionamento iSCSI para balanceamento e resiliência.
* O balanceamento de vDisks pode melhorar o desempenho em cenários com alta E/S.
* O CHAP pode ser usado para autenticação adicional.
* O IPsec pode complementar a segurança do canal iSCSI.

---

> 💡 Esta página é um guia prático para a implantação do Nutanix Volumes.