# 🥷 Ninja Linux

Portal de documentação e estudos sobre Linux, DevOps, Kubernetes, redes, segurança e infraestrutura.

O Ninja Linux nasceu para centralizar conhecimentos adquiridos na rotina de infraestrutura, servindo como base de consulta rápida para administração de servidores, containers, Kubernetes, redes e ferramentas corporativas.

---

## 📚 Conteúdo

| Área | Conteúdo principal |
| --- | --- |
| [Linux](linux/) | Administração, LVM e certificados SSL |
| [Kubernetes](kubernetes/) | Instalação, Kubeconfig, kubectl, workloads, rede e troubleshooting |
| [Docker](docker/) | Containers, imagens, volumes e Docker Compose |
| [DevOps](devops/) | CI/CD, Azure DevOps, Jenkins, Harbor e Git |
| [Git](git/) | Fundamentos e fluxo de trabalho |
| [Redes](redes/) | DNS, conectividade e diagnóstico |
| [Squid](squid/) | Proxy e administração |
| [Monitoramento](monitoramento/) | Zabbix e Grafana |
| [Nutanix](nutanix/) | Fundamentos, volumes, Files e Data Lens |
| [VMware](vmware/) | ESXi, iDRAC e infraestrutura física |
| [Windows](windows/) | Windows Server, aplicações e Active Directory |
| [WatchGuard](watchguard/) | Firewall e segurança de rede |
| [Troubleshooting](troubleshooting/) | Incidentes, evidências e procedimentos de recuperação |

---

## ✨ Destaques

- Navegação organizada por área técnica.
- Exemplos práticos e comandos prontos para consulta.
- Layout responsivo para desktop e dispositivos móveis.
- Publicação automatizada com GitHub Actions e GitHub Pages.

---

## 🎯 Objetivo

- Consolidar conhecimento técnico.
- Compartilhar documentação.
- Registrar procedimentos e soluções.
- Criar uma base de consulta rápida para o dia a dia.
- Padronizar anotações de infraestrutura, suporte e DevOps.

---

## 🚀 Tecnologias

- Markdown
- Jekyll
- Ruby e Bundler
- GitHub Pages
- GitHub Actions
- HTML
- CSS

---

## 💻 Executar localmente

Pré-requisitos:

- Ruby 3.2 ou compatível
- Bundler

Instale as dependências:

```bash
bundle install
```

Inicie o servidor local:

```bash
bundle exec jekyll serve --livereload
```

Acesse:

```text
http://127.0.0.1:4000/ninja-linux/
```

Para gerar somente os arquivos estáticos:

```bash
bundle exec jekyll build
```

O resultado será criado no diretório `_site/`.

---

## 📂 Estrutura do Projeto

```text
ninja-linux/
├── .github/workflows/
├── linux/
├── kubernetes/
├── docker/
├── devops/
├── git/
├── redes/
├── squid/
├── monitoramento/
├── nutanix/
├── vmware/
├── watchguard/
├── windows/
├── troubleshooting/
├── assets/
├── docs/
├── images/
├── _layouts/
├── _config.yml
├── Gemfile
└── index.md
```

---

## 🧭 Padrão de documentação

Cada página deve seguir, sempre que fizer sentido, esta estrutura:

- Introdução
- Quando usar
- Exemplos práticos
- Comandos úteis
- Troubleshooting
- Boas práticas
- Resumo

---

## 📌 Observação

Este repositório é uma base viva de conhecimento. Os conteúdos podem evoluir conforme novos procedimentos, incidentes e aprendizados forem documentados.

---

## 🚧 Como contribuir

1. Crie uma branch com o nome do tema ou da correção.
2. Adicione o conteúdo seguindo o padrão do repositório.
3. Valide o site localmente.
4. Confirme que exemplos não contêm credenciais ou dados sensíveis.
5. Envie um Pull Request explicando claramente a alteração.

---

## 📦 Deploy

O deploy é executado automaticamente pelo workflow [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml).

Ao receber um push na branch `main`, o GitHub Actions:

1. Prepara o Ruby 3.2.
2. Instala as dependências com Bundler.
3. Executa `bundle exec jekyll build`.
4. Publica o diretório `_site/` na branch `gh-pages`.

Antes do primeiro deploy, ajuste em [`_config.yml`](_config.yml) os campos `url`, `baseurl`, `github_username` e `repository`.

Após a publicação, o portal ficará disponível no endereço configurado para o GitHub Pages.
