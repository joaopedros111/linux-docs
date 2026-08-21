---
layout: default
title: Cliente NFS no Rocky Linux 9
---

# Cliente NFS no Rocky Linux 9

> Tutorial para instalação e configuração de um cliente NFS no Rocky Linux 9.

---

# Objetivo

Ao final deste tutorial, o cliente Rocky Linux será capaz de acessar um compartilhamento NFS exportado por um servidor Rocky Linux.

Neste guia será configurado:

- Cliente Rocky Linux
- Montagem manual do compartilhamento
- Montagem automática utilizando `/etc/fstab`
- Testes de leitura e escrita

> Este tutorial pressupõe que o servidor NFS já esteja configurado.

---

# Cenário

| Equipamento | IP |
|------------|------------|
| Servidor NFS | `10.0.97.16` |
| Cliente Rocky Linux | `10.0.97.24` |
| Compartilhamento | `/dados` |
| Ponto de montagem | `/mnt/dados` |

---

# Verificar conectividade

Antes de iniciar, confirme que o cliente consegue acessar o servidor.

```bash
ping -c 4 10.0.97.16
```

Teste também a porta do NFS:

```bash
nc -zv 10.0.97.16 2049
```

Caso o `nc` não esteja instalado:

```bash
dnf install -y nmap-ncat
```

---

# Instalar o cliente NFS

Instale os utilitários do NFS:

```bash
dnf install -y nfs-utils
```

---

# Verificar os compartilhamentos disponíveis

Liste os compartilhamentos exportados pelo servidor:

```bash
showmount -e 10.0.97.16
```

Resultado esperado:

```text
Export list for 10.0.97.16:
/dados 10.0.97.0/24
```

Caso apareça:

```text
clnt_create: RPC: Unable to receive
```

Verifique:

- Firewall
- Serviço `nfs-server`
- Serviço `rpcbind`
- Conectividade de rede

---

# Criar o ponto de montagem

```bash
mkdir -p /mnt/dados
```

---

# Montar o compartilhamento

```bash
mount -t nfs 10.0.97.16:/dados /mnt/dados
```

Também é possível montar explicitando a versão:

```bash
mount -t nfs -o vers=4.2 10.0.97.16:/dados /mnt/dados
```

---

# Confirmar a montagem

```bash
mount | grep dados
```

Exemplo:

```text
10.0.97.16:/dados on /mnt/dados type nfs4 (...)
```

Ou:

```bash
df -h
```

Resultado esperado:

```text
Filesystem             Size  Used Avail Mounted on
10.0.97.16:/dados       XXG   XXG   XXG /mnt/dados
```

---

# Verificar o conteúdo

```bash
ls -la /mnt/dados
```

Exemplo:

```text
arquivo1.txt
arquivo2.txt
```

Visualize um arquivo:

```bash
cat /mnt/dados/arquivo1.txt
```

Resultado esperado:

```text
Arquivo 1
```

---

# Testar leitura

```bash
cat /mnt/dados/arquivo2.txt
```

Resultado esperado:

```text
Arquivo 2
```

---

# Testar escrita

Crie um arquivo:

```bash
echo "Teste NFS" > /mnt/dados/teste-nfs.txt
```

Verifique:

```bash
ls -l /mnt/dados
```

Leia o conteúdo:

```bash
cat /mnt/dados/teste-nfs.txt
```

---

# Verificar informações da montagem

```bash
nfsstat -m
```

Exemplo:

```text
/mnt/dados from 10.0.97.16:/dados
 Flags: rw,vers=4.2,...
```

---

# Desmontar o compartilhamento

```bash
umount /mnt/dados
```

Confirme:

```bash
mount | grep dados
```

Não deverá retornar nenhum resultado.

---

# Configurar montagem automática

Edite:

```bash
vim /etc/fstab
```

Adicione:

```text
10.0.97.16:/dados    /mnt/dados    nfs    defaults,_netdev    0 0
```

Descrição:

| Opção | Descrição |
|--------|-----------|
| defaults | Opções padrão |
| _netdev | Aguarda a rede antes da montagem |

---

# Testar o arquivo fstab

Sem reiniciar:

```bash
mount -a
```

Caso não haja erros:

```bash
mount | grep dados
```

---

# Reiniciar o cliente

Após reiniciar:

```bash
reboot
```

Confirme:

```bash
df -h
```

O compartilhamento deverá estar montado automaticamente.

---

# Comandos úteis

Ver compartilhamentos disponíveis:

```bash
showmount -e 10.0.97.16
```

Montar:

```bash
mount -t nfs 10.0.97.16:/dados /mnt/dados
```

Desmontar:

```bash
umount /mnt/dados
```

Ver montagens:

```bash
mount | grep nfs
```

Ver estatísticas:

```bash
nfsstat -m
```

---

# Solução de problemas

## Compartilhamento não aparece

Verifique:

```bash
showmount -e 10.0.97.16
```

---

## Erro:

```text
RPC: Unable to receive
```

Verifique:

```bash
systemctl status nfs-server
systemctl status rpcbind
```

No servidor.

Também confira o firewall:

```bash
firewall-cmd --list-services
```

---

## Erro:

```text
Permission denied
```

Verifique:

- Permissões do diretório exportado
- Configuração do `/etc/exports`
- UID/GID do usuário
- `root_squash`
- `anonuid`
- `anongid`

---

## Confirmar exportações do servidor

```bash
showmount -e 10.0.97.16
```

---

# Estrutura final

```text
Servidor Rocky
10.0.97.16
        │
        │
        ▼
Compartilhamento NFS
        │
        ▼
Cliente Rocky
10.0.97.24
        │
        ▼
/mnt/dados
```

---

# Próximos passos

Após concluir este tutorial, prossiga para:

1. Cliente NFS no Windows
2. Permissões POSIX
3. UID/GID
4. root_squash
5. anonuid e anongid
6. Identity Mapping
7. NFSv3 x NFSv4
