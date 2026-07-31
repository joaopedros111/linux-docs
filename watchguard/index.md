---
layout: default
title: WatchGuard
---

# 🔥 WatchGuard

## Introdução

WatchGuard é uma solução de firewall e segurança de rede usada para controlar tráfego, VPNs, políticas de acesso e proteção de perímetro.

Esta seção reúne procedimentos, boas práticas e troubleshooting relacionados à administração de firewalls WatchGuard.

---

## Temas da seção

* Instalação do WatchGuard NDR Collection Agent.
* [Coleta de Logs do Collection Agent](watchguardndrcollectionagent)
* Troubleshooting de Heartbeat Offline.
* Troubleshooting de NetFlow e sFlow.
* Integração com Firebox.
* Entendendo os logs ndr_nf_aggregator.log.
* Como interpretar heartbeat upload status.
---

## Checklist de análise

* Identificar origem, destino, porta e protocolo.
* Verificar se existe política permitindo o tráfego.
* Conferir ordem e escopo das regras.
* Validar NAT quando houver publicação externa.
* Consultar logs no momento do teste.
* Confirmar se há proxy, rota ou DNS envolvido.

---

## Boas práticas

* Documente regras criadas ou alteradas.
* Evite regras amplas sem necessidade.
* Use nomes claros para políticas.
* Remova regras antigas quando não forem mais usadas.
* Valide logs após cada mudança.

---

> 💡 Esta seção serve como ponto de entrada para documentação de firewall, VPN, NAT e políticas WatchGuard.
