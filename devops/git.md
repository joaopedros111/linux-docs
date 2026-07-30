---
layout: default
title: Git Básico para Administradores Linux
---

# 🐙 Git Básico para Administradores Linux

## O que é Git?

Git é um sistema de controle de versão que permite registrar alterações em arquivos, manter histórico de mudanças e colaborar em projetos.

---

## Verificar se o Git está instalado

```bash
git --version
```

Exemplo:

```bash
git version 2.43.5
```

---

## Configuração inicial

Defina seu nome e e-mail:

```bash
git config --global user.name "Usuário Exemplo"
git config --global user.email "usuario@example.com"
```

Verificar configuração:

```bash
git config --list
```

---

## Clonar um repositório

Baixar um projeto existente:

```bash
git clone https://github.com/usuario/repositorio.git
```

Exemplo:

```bash
git clone https://github.com/seu-usuario/linux-docs.git
```

Entrar no diretório:

```bash
cd linux-docs
```

---

## Verificar status

Mostrar arquivos modificados:

```bash
git status
```

---

## Adicionar arquivos

Adicionar um arquivo específico:

```bash
git add arquivo.md
```

Adicionar todos os arquivos:

```bash
git add .
```

---

## Criar um commit

Salvar alterações localmente:

```bash
git commit -m "Adicionado tutorial de Git"
```

---

## Enviar alterações para o GitHub

```bash
git push origin main
```

---

## Atualizar repositório local

Baixar alterações do GitHub:

```bash
git pull origin main
```

---

## Ver histórico

Mostrar commits realizados:

```bash
git log
```

Versão resumida:

```bash
git log --oneline
```

---

## Ver diferenças

Mostrar alterações antes do commit:

```bash
git diff
```

---

## Remover arquivo do Git

```bash
git rm arquivo.md
git commit -m "Removido arquivo antigo"
git push origin main
```

---

## Fluxo básico do dia a dia

Verificar alterações:

```bash
git status
```

Adicionar arquivos:

```bash
git add .
```

Criar commit:

```bash
git commit -m "Descrição da alteração"
```

Enviar para o GitHub:

```bash
git push origin main
```

---

## Comandos mais utilizados

| Comando       | Função                |
| ------------- | --------------------- |
| git status    | Verificar alterações  |
| git add .     | Adicionar arquivos    |
| git commit -m | Criar commit          |
| git push      | Enviar para GitHub    |
| git pull      | Atualizar repositório |
| git log       | Ver histórico         |
| git diff      | Ver diferenças        |
| git clone     | Clonar repositório    |

---

## Exemplo prático

Após criar um novo tutorial:

```bash
git status
git add .
git commit -m "Adicionado tutorial LVM"
git push origin main
```

O conteúdo será enviado para o GitHub e atualizado no site após a publicação.

