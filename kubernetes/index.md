---
layout: default
title: Kubernetes
---

# ☸️ Kubernetes

## Introdução

O Kubernetes é uma plataforma de orquestração de containers utilizada para automatizar implantação, escalabilidade e gerenciamento de aplicações.

Atualmente é uma das principais tecnologias utilizadas em ambientes corporativos, nuvem e arquiteturas de microsserviços.

Permite executar aplicações de forma distribuída, resiliente e altamente disponível.

---

## Conteúdo

* [KUBECONFIG](kubeconfig.md)
* [Kubectl](kubectl.md)
* [Instalação de Cluster Kubernetes com Rocky Linux, containerd e Calico](instalacao-cluster-rocky-containerd-calico.md)
* [Instalação do Kubernetes (kubeadm) no Rocky Linux](tutorial-kubernetes-rocky-linux.md)
* [Pods](pods.md)
* [Deployments](deployments.md)
* [Services](services.md)
* [Ingress](ingress.md)
* [Troubleshooting Kubernetes](troubleshooting.md)

---

## Fluxo recomendado de estudo

```text
KUBECONFIG --> Kubectl --> Instalação do Cluster --> Pods --> Deployments --> Services --> Ingress --> Troubleshooting
```

---

> 💡 Recomenda-se iniciar pelos tópicos KUBECONFIG, Kubectl e Pods antes de avançar para Deployments, Services, Ingress e Troubleshooting.
