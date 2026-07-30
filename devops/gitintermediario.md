---
layout: default
title: Git Intermediário
---

# 🐙 Git Intermediário

## Objetivo

Aprender os principais recursos intermediários do Git utilizados no dia a dia de administradores Linux, DevOps e equipes de desenvolvimento.

---

# O que é uma Branch?

Uma branch é uma ramificação do projeto.

Ela permite desenvolver novas funcionalidades sem alterar diretamente a branch principal.

Visualmente:

```text
main
 │
 ├── feature-login
 │
 └── feature-monitoramento
```

---

# Listar Branches

Mostrar branch atual:

```bash
git branch
```

Exemplo:

```text
* main
```

O asterisco indica a branch em uso.

---

# Criar uma Branch

```bash
git branch nova-feature
```

Exemplo:

```bash
git branch tutorial-zabbix
```

---

# Trocar de Branch

```bash
git checkout tutorial-zabbix
```

ou (mais moderno):

```bash
git switch tutorial-zabbix
```

---

# Criar e Trocar de Branch

```bash
git checkout -b tutorial-zabbix
```

ou

```bash
git switch -c tutorial-zabbix
```

---

# Enviar Branch para o GitHub

```bash
git push -u origin tutorial-zabbix
```

A opção `-u` define a branch remota padrão.

---

# Mesclar Branches (Merge)

Suponha:

```text
main
 └── tutorial-zabbix
```

Após concluir o trabalho:

Troque para a main:

```bash
git checkout main
```

Execute o merge:

```bash
git merge tutorial-zabbix
```

Resultado:

```text
main
 └── alterações incorporadas
```

---

# Excluir Branch

Local:

```bash
git branch -d tutorial-zabbix
```

Forçar remoção:

```bash
git branch -D tutorial-zabbix
```

Remota:

```bash
git push origin --delete tutorial-zabbix
```

---

# Visualizar Histórico Gráfico

```bash
git log --oneline --graph
```

Exemplo:

```text
* a12b34c Atualização Zabbix
* d56e78f Tutorial Git
* f90g12h Primeiro commit
```

---

# Trabalhando com Conflitos

Um conflito ocorre quando duas alterações modificam a mesma linha de um arquivo.

Exemplo:

```text
[versao local]
Servidor Rocky Linux
[versao da branch-teste]
Servidor Debian
[fim do exemplo]
```

O Git não sabe qual alteração manter.

Escolha a versão correta:

```text
Servidor Rocky Linux
```

Salve o arquivo.

Depois:

```bash
git add arquivo.md
git commit -m "Resolvido conflito"
```

---

# Git Fetch

Atualiza informações do repositório remoto sem alterar arquivos locais.

```bash
git fetch
```

Verificar diferenças:

```bash
git status
```

---

# Git Pull

Atualiza o repositório local.

```bash
git pull origin main
```

Equivale a:

```bash
git fetch
git merge
```

---

# Git Rebase

Reorganiza commits para manter o histórico linear.

Antes:

```text
A---B---C main
     \
      D---E feature
```

Após:

```text
A---B---C---D---E
```

Executar:

```bash
git checkout feature
git rebase main
```

---

# Diferença entre Merge e Rebase

## Merge

Preserva o histórico completo.

```bash
git merge main
```

Vantagem:

* Mais seguro
* Mantém rastreabilidade

---

## Rebase

Reescreve o histórico.

```bash
git rebase main
```

Vantagem:

* Histórico limpo
* Menos commits de merge

---

# Desfazer Alterações

## Descartar alterações de um arquivo

```bash
git restore arquivo.md
```

---

## Remover arquivo da área de stage

```bash
git restore --staged arquivo.md
```

---

## Voltar para commit anterior

Ver histórico:

```bash
git log --oneline
```

Retornar:

```bash
git reset --hard ID_COMMIT
```

Exemplo:

```bash
git reset --hard a12b34c
```

⚠️ Remove alterações posteriores.

---

# Ver Quem Alterou uma Linha

```bash
git blame arquivo.md
```

Exemplo:

```text
a12b34c (Usuário Exemplo) Linha 15
```

---

# Stash

Guardar alterações temporariamente.

Salvar:

```bash
git stash
```

Listar:

```bash
git stash list
```

Restaurar:

```bash
git stash pop
```

---

# Tags

Utilizadas para marcar versões.

Criar:

```bash
git tag v1.0
```

Enviar:

```bash
git push origin v1.0
```

Listar:

```bash
git tag
```

---

# Fluxo de Trabalho Recomendado

Atualizar repositório:

```bash
git pull origin main
```

Criar branch:

```bash
git checkout -b tutorial-squid
```

Realizar alterações:

```bash
git add .
git commit -m "Adicionado tutorial Squid"
```

Enviar:

```bash
git push origin tutorial-squid
```

Mesclar:

```bash
git checkout main
git merge tutorial-squid
git push origin main
```

Excluir branch:

```bash
git branch -d tutorial-squid
```

---

# Comandos Mais Importantes

| Comando         | Função                        |
| --------------- | ----------------------------- |
| git branch      | Listar branches               |
| git checkout -b | Criar branch                  |
| git switch -c   | Criar branch (novo método)    |
| git merge       | Mesclar alterações            |
| git rebase      | Reorganizar histórico         |
| git stash       | Salvar alterações temporárias |
| git reset       | Voltar commits                |
| git restore     | Desfazer alterações           |
| git fetch       | Atualizar referências         |
| git blame       | Identificar autor             |
| git tag         | Criar versões                 |

---

## Resumo

Para a maioria dos administradores Linux e DevOps, os comandos mais utilizados são:

```bash
git pull
git checkout -b nova-branch
git add .
git commit -m "Descrição"
git push
git merge
git log --oneline
git restore
git stash
```

Dominar esses comandos já cobre a maior parte das atividades realizadas no dia a dia.
