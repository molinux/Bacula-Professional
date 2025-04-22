# Para verificar qual distribuição e versão do Linux
cat /etc/os-release

# Verificar quantidade memoria
free -m

# Verificar CPU
cat /proc/cpuinfo

# Verificar discos
lsblk

# Pre-Install
## Atualizar o servidor

### RPM Based
dnf update

#### Desabilitar o SELinux

```bash
vim /etc/selinux/config
SELINUX=disabled
```

Desabilitar o SELinux para a sessão atual

```bash
setenforce 0
```

### DEB Based
apt update
apt upgrade

!!! note 
    Se tiver atualização do kernel é uma boa prática fazer o reboot para subir com o kernel novo

## Definir hostname

```bash
hostnamectl set-hostname bacula

```

# Instalação do Bacula

BCMI - Bacula Community Molinux Install

github.com/molinux/bacula_molinux_install/

Baixe o arquivo de configuração:
wget https://abre.ai/bacula-molinux-install-conf

Baixe o arquivo de instalação
wget https://abre.ai/bacula-molinux-install-app

Insira suas credenciais no arquivo bacula_molinux_install.conf

Torne o arquivo bacula_molinux_install-app executável:
chmod +x bacula_molinux_install-app

Execute o instalador:
./bacula_molinux_install-app


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

# Exercicio 1: Diferencial x Incremental

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