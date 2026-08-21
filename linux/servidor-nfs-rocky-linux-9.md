---
layout: default
title: Servidor NFS no Rocky Linux 9
---

# Servidor NFS no Rocky Linux 9

> Tutorial para instalação e configuração de um servidor NFS no Rocky Linux 9.

---

# Objetivo

Ao final deste tutorial o servidor será capaz de compartilhar um diretório via NFS para clientes Linux e Windows.

Neste guia será configurado:

- Servidor Rocky Linux
- Compartilhamento `/dados`
- Rede `10.0.97.0/24`
- Permissão de leitura e escrita para os clientes

> A configuração dos clientes será apresentada em tutoriais separados.

---

# Cenário

| Equipamento | IP |
|------------|------------|
| Servidor NFS | `10.0.97.16` |
| Rede permitida | `10.0.97.0/24` |
| Diretório compartilhado | `/dados` |

---

# Instalar o servidor NFS

Atualize o sistema:

```bash
dnf update -y
```

Instale o servidor NFS:

```bash
dnf install -y nfs-utils
```

---

# Habilitar os serviços

Habilite os serviços para iniciarem junto com o sistema:

```bash
systemctl enable --now rpcbind
systemctl enable --now nfs-server
```

Verifique:

```bash
systemctl status nfs-server
```

Resultado esperado:

```text
Active: active (running)
```

---

# Criar o diretório compartilhado

Crie o diretório que será exportado:

```bash
mkdir -p /dados
```

---

# Criar arquivos de teste

```bash
echo "Arquivo 1" > /dados/arquivo1.txt
echo "Arquivo 2" > /dados/arquivo2.txt
```

Verifique:

```bash
ls -l /dados
```

Resultado esperado:

```text
arquivo1.txt
arquivo2.txt
```

---

# Configurar o compartilhamento

Edite o arquivo:

```bash
vim /etc/exports
```

Adicione:

```text
/dados 10.0.97.0/24(rw,sync,no_subtree_check)
```

Descrição das opções:

| Opção | Descrição |
|--------|-----------|
| rw | Leitura e escrita |
| sync | Escrita síncrona |
| no_subtree_check | Desabilita verificação de subtree |

---

# Exportar o compartilhamento

Execute:

```bash
exportfs -rav
```

Saída esperada:

```text
exporting 10.0.97.0/24:/dados
```

---

# Verificar os compartilhamentos

```bash
exportfs -v
```

Exemplo:

```text
/dados 10.0.97.0/24(sync,wdelay,hide,no_subtree_check,sec=sys,rw,secure,root_squash,no_all_squash)
```

---

# Configurar o Firewall

Permita o serviço NFS:

```bash
firewall-cmd --permanent --add-service=nfs
firewall-cmd --permanent --add-service=mountd
firewall-cmd --permanent --add-service=rpc-bind

firewall-cmd --reload
```

Verifique:

```bash
firewall-cmd --list-services
```

Deve conter:

```text
nfs
mountd
rpc-bind
```

---

# Configurar SELinux

Caso o SELinux esteja habilitado:

Verifique:

```bash
getenforce
```

Caso esteja em **Enforcing**, execute:

```bash
setsebool -P nfs_export_all_rw on
```

---

# Verificar os serviços

```bash
systemctl status rpcbind
systemctl status nfs-server
```

Todos devem estar:

```text
active (running)
```

---

# Verificar portas abertas

```bash
ss -tulpn | grep rpc
```

ou

```bash
rpcinfo -p
```

Exemplo:

```text
100003    4   tcp   2049  nfs
100005    3   tcp  20048  mountd
```

---

# Testar localmente

Liste os compartilhamentos do próprio servidor:

```bash
showmount -e localhost
```

Resultado esperado:

```text
Export list for localhost:
/dados 10.0.97.0/24
```

Também é possível consultar utilizando o IP do servidor:

```bash
showmount -e 10.0.97.16
```

---

# Estrutura criada

```text
Servidor Rocky Linux
        │
        ├── nfs-utils
        ├── rpcbind
        ├── nfs-server
        │
        └── /dados
              ├── arquivo1.txt
              └── arquivo2.txt
```

---

# Comandos úteis

Listar compartilhamentos:

```bash
exportfs -v
```

Reexportar configurações:

```bash
exportfs -rav
```

Parar o serviço:

```bash
systemctl stop nfs-server
```

Iniciar o serviço:

```bash
systemctl start nfs-server
```

Reiniciar:

```bash
systemctl restart nfs-server
```

Verificar compartilhamentos ativos:

```bash
showmount -e localhost
```

Verificar portas RPC:

```bash
rpcinfo -p
```

---

# Arquivos importantes

| Arquivo | Finalidade |
|----------|------------|
| `/etc/exports` | Compartilhamentos NFS |
| `/etc/nfs.conf` | Configuração do servidor NFS |

---

# Próximos passos

Após concluir este tutorial, prossiga para:

1. Configuração do cliente NFS no Rocky Linux
2. Configuração do cliente NFS no Windows
3. Testes de leitura, escrita e permissões
4. Estudo de UID/GID, `root_squash`, `anonuid`, `anongid` e Identity Mapping
