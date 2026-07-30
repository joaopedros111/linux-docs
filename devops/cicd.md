---
layout: default
title: CI/CD
---

# 🔄 CI/CD

## Introdução

CI/CD é um conjunto de práticas utilizadas para automatizar a integração, teste, entrega e implantação de aplicações.

A sigla significa:

* **CI** – Continuous Integration (Integração Contínua)
* **CD** – Continuous Delivery / Continuous Deployment

Essas práticas reduzem erros manuais e aceleram a entrega de software.

---

## O que é CI?

Continuous Integration (CI) consiste em integrar alterações de código frequentemente em um repositório central.

Sempre que um desenvolvedor envia código para o repositório, um pipeline pode executar automaticamente:

* Compilação
* Testes
* Validações
* Análises de qualidade

### Fluxo de CI

```text
Desenvolvedor
      ↓
Git / Azure DevOps
      ↓
Jenkins
      ↓
Build
      ↓
Testes
```

### Benefícios

* Detectar erros rapidamente
* Evitar problemas de integração
* Garantir qualidade do código
* Automatizar validações

---

## O que é CD?

CD é a automação da entrega e implantação das aplicações.

Após a conclusão da CI, a aplicação pode ser disponibilizada automaticamente para testes, homologação ou produção.

Existem duas abordagens:

### Continuous Delivery

O deploy fica pronto para ser executado, mas requer aprovação humana.

Exemplo:

```text
Build
   ↓
Homologação
   ↓
Aprovação
   ↓
Produção
```

### Continuous Deployment

O deploy é realizado automaticamente após a aprovação dos testes.

```text
Build
   ↓
Testes
   ↓
Produção
```

Sem intervenção humana.

---

## Exemplo Prático

Fluxo semelhante ao utilizado em um ambiente corporativo:

```text
Azure DevOps
      ↓
Jenkins
      ↓
Build da aplicação
      ↓
Criação da imagem Docker
      ↓
Push para Harbor
      ↓
Deploy em Kubernetes (DSV)
      ↓
Aprovação para HML
      ↓
Deploy em HML
      ↓
Aprovação para PRD
      ↓
Deploy em Produção
```

---

## Ferramentas Utilizadas

| Ferramenta   | Função                   |
| ------------ | ------------------------ |
| Git          | Controle de versão       |
| Azure DevOps | Repositório de código    |
| Jenkins      | Pipeline CI/CD           |
| Docker       | Criação de imagens       |
| Harbor       | Armazenamento de imagens |
| Kubernetes   | Deploy das aplicações    |

---

## Pipeline

Um pipeline é uma sequência de etapas automatizadas.

Exemplo:

```text
Checkout
    ↓
Build
    ↓
Testes
    ↓
Push da imagem
    ↓
Deploy
```

No Jenkins essas etapas normalmente aparecem como:

```text
Building the image
Pushing to registry
Deploying to DSV
Aprovação para HML
Deploying to HML
Aprovação para PRD
Deploying to PRD
```

---

## Vantagens do CI/CD

* Automatização de tarefas repetitivas
* Redução de erros manuais
* Entregas mais rápidas
* Padronização dos processos
* Melhor rastreabilidade das alterações
* Facilidade de rollback

---

## Resumo

```text
CI = Construir e validar a aplicação

CD = Entregar e implantar a aplicação
```

Fluxo resumido:

```text
Código
   ↓
CI
(Build + Testes)
   ↓
CD
(Deploy)
   ↓
Produção
```