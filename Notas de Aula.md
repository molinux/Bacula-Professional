![](assets/Color%20logo%20with%20background.png)

<h1 class='unlisted'><center>Treinamento Bacula Professional </center></h1>

<br>

<h3 class='unlisted'><center>Notas de Aulas - Turma TN-25_04</center></h3>

<br>
<br>
<br>
<br>
<br>
<br>

<center>

> <center> ----------
>
> <br>
>
> _<center> Este material faz parte do conteúdo do curso Bacula Professional da **NOMALAND Tecnologia** </center>_
>
> <br>
>
> <center> NOMALAND Tecnologia ©️ - Todos os direitos reservados
>
> <center> www.nomaland.com.br
>
> <br>
>
> <center> ----------

</center>

<div style="page-break-after: always;"></div>

<h2 class='unlisted'>Sumário</h2>

- [Pre-Install](#pre-install)
  - [Para verificar qual distribuição e versão do Linux](#para-verificar-qual-distribuição-e-versão-do-linux)
  - [Verificar quantidade memoria](#verificar-quantidade-memoria)
  - [Verificar CPU](#verificar-cpu)
  - [Verificar discos](#verificar-discos)
  - [Atualizar o servidor](#atualizar-o-servidor)
    - [RPM Based (RHEL | Alma Linux | Rocky Linux | Oracle Linux)](#rpm-based-rhel--alma-linux--rocky-linux--oracle-linux)
      - [Desabilitar o SELinux](#desabilitar-o-selinux)
    - [DEB Based (Debian | Ubuntu)](#deb-based-debian--ubuntu)
  - [Definir hostname](#definir-hostname)
- [Instalação do Bacula](#instalação-do-bacula)
  - [BCMI - Bacula Community Molinux Install](#bcmi---bacula-community-molinux-install)
- [Post Install](#post-install)
  - [Acertar data e hora](#acertar-data-e-hora)
- [Estrutura do Bacula](#estrutura-do-bacula)
  - [Estrutura de arquivos](#estrutura-de-arquivos)
- [BConsole](#bconsole)
  - [Comando do bconsole](#comando-do-bconsole)
- [Exercício 1: Diferencial x Incremental](#exercício-1-diferencial-x-incremental)
  - [Dia 1: Backup Full](#dia-1-backup-full)
  - [Dia 2: Backup Diferencial e Backup Incremental](#dia-2-backup-diferencial-e-backup-incremental)
  - [Dia 3: Backup Incremental e Backup Diferencial](#dia-3-backup-incremental-e-backup-diferencial)
- [Exercício 2: VirtualFull](#exercício-2-virtualfull)
- [Exercício 3: Storage](#exercício-3-storage)
  - [Método 1: Disco inteiro](#método-1-disco-inteiro)
    - [Montagem no fstab via UUID](#montagem-no-fstab-via-uuid)
  - [Metodo 2: LVM](#metodo-2-lvm)
    - [Criar o LVM](#criar-o-lvm)
    - [LVM: Montagem no fstab via UUID](#lvm-montagem-no-fstab-via-uuid)
    - [Configurando o Storage Daemon](#configurando-o-storage-daemon)
    - [Configurando o storage no Director](#configurando-o-storage-no-director)
- [Volume retention](#volume-retention)

# Pre-Install

## Para verificar qual distribuição e versão do Linux

```bash
cat /etc/os-release
```

## Verificar quantidade memoria

```bash
free -m
```

## Verificar CPU

```bash
cat /proc/cpuinfo
```

## Verificar discos

```bash
lsblk
```

## Atualizar o servidor

### RPM Based (RHEL | Alma Linux | Rocky Linux | Oracle Linux)

```bash
dnf update
```

#### Desabilitar o SELinux

```bash
vim /etc/selinux/config
SELINUX=disabled
```

Desabilitar o SELinux para a sessão atual

```bash
setenforce 0
```

### DEB Based (Debian | Ubuntu)

```bash
apt update
apt upgrade
```

!!! note
    Se tiver atualização do kernel é uma boa prática fazer o reboot para subir com o kernel novo

## Definir hostname

```bash
hostnamectl set-hostname bacula
```

# Instalação do Bacula

## BCMI - Bacula Community Molinux Install

Você pode encontrar as instruções em:

github.com/molinux/bacula_molinux_install/

Baixe o arquivo de configuração:

```bash
wget https://abre.ai/bacula-molinux-install-conf
```

Baixe o arquivo de instalação

```bash
wget https://abre.ai/bacula-molinux-install-app
```

Insira suas credenciais no arquivo bacula_molinux_install.conf

Torne o arquivo bacula_molinux_install-app executável:

```bash
chmod +x bacula_molinux_install-app
```

Execute o instalador:

```bash
./bacula_molinux_install-app
```

!!! attention Bacula DIRECTOR e STORAGE devem estar SEMPRE na mesma versão

# Post Install

Verificar se o Bacula está executando corretamente

```bash
# Verificar os serviços
systemctl status bacula-dir
systemctl status bacula-sd
systemctl status bacula-fd

# Verificar os processos
ps aux | grep bacula-

# Verificar as portas 
ss -nltp | grep bacula
```

## Acertar data e hora

```bash
timedatectl set-timezone 'America/Sao_Paulo'
```

# Estrutura do Bacula

## Estrutura de arquivos

Toda a estrutura do Bacula está em: `/opt/bacula`

# BConsole

## Comando do bconsole

Status dos componentes

```bash
status dir 
status storage 
status client
```

Volumes e Pools

```bash
list media pools
```

Listar o log de um job

```bash
list joblog jobid=<id>
list joblog jobid=15
```

# Exercício 1: Diferencial x Incremental

1. Criar o diretório de backups

```bash
mkdir /home/repositorio
cd /home/repositorio
```

## Dia 1: Backup Full

Criar o arq-1, arq-2, arq-3

Acessar o site https://loremipsum.io/generator/?n=50&t=p copiar o conteúdo e colar no arq-1

Depois criar o arq-2 e arq-3

```bash
cat arq-1 > arq-2
cat arq-1 > arq-3
```

Executar o backup FULL

```bash
run job=Backup-REPOSITORIO level=Full yes
```

## Dia 2: Backup Diferencial e Backup Incremental

Adicionar arq-4 e arq-5

```bash
dd if=arq-1 of=arq-4 conv=ucase
dd if=arq-1 of=arq-5 conv=ucase
echo "Adicionando mais uma linha" >> arq-1
```

Executar o backup no level DIFERENCIAL
Executar o backup no level INCREMENTAL

```bash
run job=Backup-REPOSITORIO level=Differential yes
run job=Backup-REPOSITORIO level=Incremental yes
```

## Dia 3: Backup Incremental e Backup Diferencial

Copiar o conteúdo do chat e colar no arq-6

Remover o arq-1

```bash
rm arq-1
```

Criar o arq-7 e arq-8

```bash
cat arq-6 > arq-7
dd if=arq-2 of=arq-8 conv=swab
```

Modificar o arq-2

```bash
echo "Arquivo 2 modificado no 3o dia" > arq-2
```

Executar o backup no level INCREMENTAL
Executar o backup no level DIFERENCIAL

```bash
run job=Backup-REPOSITORIO level=Incremental yes
run job=Backup-REPOSITORIO level=Differential yes
```

# Exercício 2: VirtualFull

Para o job de backup do tipo **VirtualFull** é necessário ter uma pool de destino e defini-la na execução do job e/ou na Pool de Origem (NextPool)

```bash
run job=Backup-Repositorio level=VirtualFull nextpool=VirtualFull
```

# Exercício 3: Storage

## Método 1: Disco inteiro

1- Adicionar um disco
2- Particionar o disco
3- Formatar
4- Montar
5- Testar

```bash
# Para identificar o disco
lsblk
fdisk /dev/sdc
```

```bash
# Para particionar o disco
fdisk /dev/sdc

Welcome to fdisk (util-linux 2.37.4).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x822ead90.

Command (m for help): p
Disk /dev/sdc: 50 GiB, 53687091200 bytes, 104857600 sectors
Disk model: QEMU HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x822ead90

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-104857599, default 2048):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-104857599, default 104857599):

Created a new partition 1 of type 'Linux' and of size 50 GiB.

Command (m for help): p
Disk /dev/sdc: 50 GiB, 53687091200 bytes, 104857600 sectors
Disk model: QEMU HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x822ead90

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 104857599 104855552  50G 83 Linux

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

[root@bacula etc]# lsblk
NAME               MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda                  8:0    0   80G  0 disk
├─sda1               8:1    0    1G  0 part /boot
└─sda2               8:2    0   79G  0 part
  ├─almalinux-root 253:0    0 47.8G  0 lvm  /
  ├─almalinux-swap 253:1    0  7.9G  0 lvm  [SWAP]
  └─almalinux-home 253:2    0 23.3G  0 lvm  /home
sdb                  8:16   0   32G  0 disk
└─sdb1               8:17   0   32G  0 part
sdc                  8:32   0   50G  0 disk
└─sdc1               8:33   0   50G  0 part
sr0                 11:0    1    4M  0 rom
sr1                 11:1    1    2G  0 rom
```

Formatar e montar

```bash
mkfs.xfs /dev/sdc1
df -h
mkdir /backup
mount /dev/sdc1 /backup
df -h

# Teste de escrita no disco
touch /backup/teste1.txt
ls /backup

# Para desmontar o disco
umount /backup
```

Configurar o Linux para sempre montar o disco no boot

/etc/fstab

```bash
/dev/sdc1            /backup        xfs      defaults      0 0
```

### Montagem no fstab via UUID

```bash
# Para pegar o UUID
blkid
```

/etc/fstab

```bash
UUID=0bca22a5-0eac-4b54-9fe3-54b357878008            /backup        xfs      defaults      0 0
```

```bash
systemctl daemon-reload
mount /backup
```

## Metodo 2: LVM

```bash
# Excluir a partição criada anteriomente
umount /backup
df -h

fdisk /dev/sdc

Welcome to fdisk (util-linux 2.37.4).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): d
Selected partition 1
Partition 1 has been deleted.

Command (m for help): p
Disk /dev/sdc: 50 GiB, 53687091200 bytes, 104857600 sectors
Disk model: QEMU HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x822ead90

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

[root@bacula ~]# lsblk
NAME               MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda                  8:0    0   80G  0 disk
├─sda1               8:1    0    1G  0 part /boot
└─sda2               8:2    0   79G  0 part
  ├─almalinux-root 253:0    0 47.8G  0 lvm  /
  ├─almalinux-swap 253:1    0  7.9G  0 lvm  [SWAP]
  └─almalinux-home 253:2    0 23.3G  0 lvm  /home
sdb                  8:16   0   32G  0 disk
└─sdb1               8:17   0   32G  0 part
sdc                  8:32   0   50G  0 disk
sr0                 11:0    1    4M  0 rom
sr1                 11:1    1    2G  0 rom
```

### Criar o LVM

```bash
# Instalar o LVM no Debian 12
sudo apt install system-config-lvm

# Verificar se esta com as ferramentas do LVM instalado
lvdisplay
```

```bash
pvcreate /dev/sdc
pvdisplay
vgcreate backup /dev/sdc
vgdisplay
lvcreate -l 100%FREE -n storage1 backup
lvdisplay backup
mkfs.xfs /dev/backup/storage1
mount /dev/backup/storage1 /backup/
touch /backup/teste1.txt
```

### LVM: Montagem no fstab via UUID

```bash
# Para pegar o UUID
blkid
```

/etc/fstab

```bash
UUID=0bca22a5-0eac-4b54-9fe3-54b357878008            /backup        xfs      defaults      0 0
```

### Configurando o Storage Daemon

/opt/bacula/etc/bacula-sd.conf

```bash
# RoboVirtual
Device {
  Name = "Drive-0"
  MediaType = "File3"
  ArchiveDevice = "/backup"
  RemovableMedia = no
  RandomAccess = yes
  AutomaticMount = yes
  LabelMedia = yes
  AlwaysOpen = no
  MaximumConcurrentJobs = 5
}
Device {
  Name = "Drive-1"
  MediaType = "File3"
  ArchiveDevice = "/backup"
  RemovableMedia = no
  RandomAccess = yes
  AutomaticMount = yes
  LabelMedia = yes
  AlwaysOpen = no
  MaximumConcurrentJobs = 5
}
Autochanger {
  Name = "RoboVirtual"
  Device = "Drive-0"
  Device = "Drive-1"
  ChangerDevice = "/dev/null"
  ChangerCommand = ""
}
```

```bash
systemctl restart bacula-sd
```

### Configurando o storage no Director

/opt/bacula/etc/bacula-dir.conf

```bash
Storage {
  Name = "RoboVirtual"
  SdPort = 9103
  Address = "bacula"
  Password = "Usn294KQ/9GbjjsWobk/ty/k/WOM9JTlTnRGY8n7LMlt"
  Device = "RoboVirtual"
  MediaType = "File3"
  Autochanger = "RoboVirtual"
  MaximumConcurrentJobs = 10
}
```

```bash
systemctl restart bacula-dir
```

# Volume retention

Definido na pool

Para alterar o tempo de retenção de um volume específico, utilize o update volume

```bash
# Retenção em segundos (15 dias)
update volume=Vol-0003 volrention=1296000 # Definição em segundos
update volume=Vol-0003 volrention=15d     # Definição em dias

# Retenção em Semanas
update volume=Vol-0003 volrention=2w

# Retenção em Anos
update volume=Vol-0003 volrention=1y
```