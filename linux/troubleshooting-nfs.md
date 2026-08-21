---
layout: default
title: Troubleshooting NFS (Rocky Linux e Windows)
---

# Troubleshooting NFS (Rocky Linux e Windows)

> Guia de diagnóstico para resolver os problemas mais comuns encontrados em ambientes NFS.

---

# Objetivo

Este guia apresenta os principais comandos para diagnosticar problemas entre:

- Servidor NFS (Rocky Linux)
- Cliente Rocky Linux
- Cliente Windows

A ideia é seguir as verificações em ordem, eliminando uma possível causa por vez.

---

# Cenário

| Equipamento | IP |
|------------|------------|
| Servidor NFS | 10.0.97.16 |
| Cliente Linux | 10.0.97.24 |
| Cliente Windows | 10.0.97.57 |

Compartilhamento:

```text
/dados
```

---

# 1. Verificar conectividade

## Linux

```bash
ping -c 4 10.0.97.16
```

## Windows

```powershell
ping 10.0.97.16
```

---

# 2. Verificar porta 2049

## Linux

```bash
nc -zv 10.0.97.16 2049
```

Caso não exista o comando:

```bash
dnf install -y nmap-ncat
```

## Windows

```powershell
Test-NetConnection 10.0.97.16 -Port 2049
```

Resultado esperado:

```text
TcpTestSucceeded : True
```

---

# 3. Verificar serviços no servidor

```bash
systemctl status rpcbind
```

```bash
systemctl status nfs-server
```

Ambos devem retornar:

```text
active (running)
```

---

# 4. Reiniciar serviços

```bash
systemctl restart rpcbind
systemctl restart nfs-server
```

---

# 5. Conferir compartilhamentos exportados

```bash
exportfs -v
```

Exemplo:

```text
/dados 10.0.97.0/24(rw,sync,no_subtree_check)
```

---

# 6. Recarregar exportações

Após alterar `/etc/exports`:

```bash
exportfs -rav
```

Resultado esperado:

```text
exporting 10.0.97.0/24:/dados
```

---

# 7. Consultar compartilhamentos disponíveis

## Linux

```bash
showmount -e 10.0.97.16
```

Resultado esperado:

```text
Export list for 10.0.97.16

/dados 10.0.97.0/24
```

---

# Erro

```text
clnt_create: RPC: Unable to receive
```

Possíveis causas:

- Firewall
- Serviço parado
- Rede
- IP incorreto

---

# 8. Verificar firewall

```bash
firewall-cmd --list-services
```

Deve conter:

```text
nfs
mountd
rpc-bind
```

Adicionar serviços:

```bash
firewall-cmd --permanent --add-service=nfs
firewall-cmd --permanent --add-service=mountd
firewall-cmd --permanent --add-service=rpc-bind

firewall-cmd --reload
```

---

# 9. Verificar SELinux

```bash
getenforce
```

Caso esteja em:

```text
Enforcing
```

Habilite exportação:

```bash
setsebool -P nfs_export_all_rw on
```

---

# 10. Verificar portas RPC

```bash
rpcinfo -p
```

Exemplo:

```text
100003    4   tcp   2049
100005    3   tcp  20048
```

---

# 11. Confirmar montagem no cliente Linux

```bash
mount | grep nfs
```

ou

```bash
df -h
```

---

# 12. Confirmar montagem no Windows

```powershell
Get-PSDrive Z
```

ou

```powershell
Get-ChildItem Z:\
```

---

# 13. Erro: Permission denied

Exemplo:

```text
Permission denied
```

Verifique:

```bash
ls -ld /dados
```

Exemplo:

```text
drwxr-xr-x
```

Verifique também:

```bash
ls -ln /dados
```

---

# 14. Verificar ACL

```bash
getfacl /dados
```

Caso não exista:

```bash
dnf install -y acl
```

---

# 15. Verificar proprietário

```bash
ls -ldn /dados
```

Exemplo:

```text
drwxr-xr-x 1001 1001
```

---

# 16. Verificar usuários

```bash
id
```

ou

```bash
id nfsuser
```

---

# 17. Testar escrita

Cliente Linux:

```bash
echo "Teste NFS" > /mnt/dados/teste.txt
```

Cliente Windows:

```powershell
"Teste" | Out-File Z:\teste.txt
```

---

# 18. Verificar montagem

Linux:

```bash
nfsstat -m
```

---

# 19. Confirmar arquivo criado

Servidor:

```bash
ls -l /dados
```

ou

```bash
ls -ln /dados
```

---

# 20. Verificar `/etc/exports`

```bash
cat /etc/exports
```

Exemplo:

```text
/dados 10.0.97.0/24(rw,sync,no_subtree_check)
```

---

# 21. Conferir configuração aplicada

```bash
exportfs -v
```

---

# 22. Desmontar compartilhamento

Linux:

```bash
umount /mnt/dados
```

Windows:

```powershell
umount.exe Z:
```

---

# 23. Montar novamente

Linux:

```bash
mount -t nfs 10.0.97.16:/dados /mnt/dados
```

Windows:

```powershell
mount.exe -o anon \\\10.0.97.16\dados Z:
```

---

# 24. Verificar serviço NFS no Windows

```powershell
Get-Service NfsClnt
```

Resultado esperado:

```text
Running
```

Iniciar:

```powershell
Start-Service NfsClnt
```

---

# 25. Confirmar instalação do cliente NFS

```powershell
Get-WindowsOptionalFeature -Online -FeatureName ServicesForNFS-ClientOnly
```

Resultado esperado:

```text
State : Enabled
```

---

# 26. Consultar eventos do Windows

```powershell
Get-WinEvent `
-LogName "Microsoft-Windows-ServicesForNFS-Client/Operational" `
-MaxEvents 20
```

---

# 27. Verificar Identity Mapping

```powershell
nfsadmin mapping
```

---

# 28. Verificar configuração do cliente

```powershell
nfsadmin client
```

---

# 29. Verificar o registro (UID/GID anônimo)

```powershell
Get-ItemProperty `
-Path "HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default"
```

---

# 30. Fluxo recomendado de diagnóstico

```text
Conectividade
        │
        ▼
Porta 2049
        │
        ▼
Serviços
        │
        ▼
Firewall
        │
        ▼
SELinux
        │
        ▼
/etc/exports
        │
        ▼
exportfs
        │
        ▼
showmount
        │
        ▼
Montagem
        │
        ▼
Permissões
        │
        ▼
UID/GID
        │
        ▼
Leitura
        │
        ▼
Escrita
```

---

# Comandos mais utilizados

## Servidor

```bash
systemctl status nfs-server
systemctl restart nfs-server
exportfs -v
exportfs -rav
showmount -e localhost
rpcinfo -p
```

## Cliente Linux

```bash
showmount -e 10.0.97.16
mount
df -h
nfsstat -m
```

## Cliente Windows

```powershell
Get-Service NfsClnt
mount.exe
umount.exe
Get-PSDrive
nfsadmin client
nfsadmin mapping
```

---

# Boas práticas

- Utilize IPs fixos para o servidor NFS.
- Restrinja os clientes autorizados no `/etc/exports`.
- Evite permissões `777` em ambientes de produção.
- Utilize `exportfs -rav` após alterar o arquivo `/etc/exports`.
- Teste primeiro a leitura e, em seguida, a escrita.
- Verifique UID/GID e permissões antes de alterar a configuração do servidor.
- Consulte os logs do Windows e do Linux antes de modificar parâmetros do NFS.
