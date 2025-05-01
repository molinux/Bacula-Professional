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

# Exercício 1: Diferencial x Incremental

1. Criar o diretório de backups

```bash
mkdir /home/repositorio
cd /home/repositorio
```

## Dia 1: Backup Full

Criar o arq-1, arq-2, arq-3

Acessar o site https://loremipsum.io/generator/?n=50&t=p copiar o conteúdo e colar no **arq-1**

Depois criar o **arq-2** e **arq-3**

```bash
cat arq-1 > arq-2
cat arq-1 > arq-3
```

Executar o backup FULL

```bash
run job=Backup-REPOSITORIO level=Full yes
```

## Dia 2: Backup Diferencial e Backup Incremental

Adicionar **arq-4** e **arq-5**

```bash
dd if=arq-1 of=arq-4 conv=ucase
dd if=arq-1 of=arq-5 conv=ucase
echo "Adicionando mais uma linha" >> arq-1
```

Executar o backup no level **DIFERENCIAL**
Executar o backup no level **INCREMENTAL**

```bash
run job=Backup-REPOSITORIO level=Differential yes
run job=Backup-REPOSITORIO level=Incremental yes
```

## Dia 3: Backup Incremental e Backup Diferencial

Copiar o conteúdo do chat e colar no **arq-6**

Remover o **arq-1**

```bash
rm arq-1
```

Criar o **arq-7** e **arq-8**

```bash
cat arq-6 > arq-7
dd if=arq-2 of=arq-8 conv=swab
```

Modificar o **arq-2**

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

