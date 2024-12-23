# Atividade Prática Linux - #PB - NOV 2024 | DevSecOps

## Documentação do Projeto: Validação e Monitoramento do Servidor Nginx

Este projeto tem como objetivo configurar um servidor Nginx, criar um script de validação do serviço, gerar logs com o status do servidor e automatizar essa verificação usando o cron. Todas as etapas foram realizadas e versionadas em um repositório Git. Aqui está a documentação completa do que foi feito.

---

## Índice

1. [Requisitos](#requisitos)
2. [Como Criar um Ambiente Linux no Windows Usando WSL](#como-criar-um-ambiente-linux-no-windows-usando-wsl)
   - [2.1. Habilitar o WSL](#1-habilitar-o-wsl)
   - [2.2. Escolher uma Distribuição Linux](#2-escolher-uma-distribuição-linux)
   - [2.3. Configurar o Ambiente Linux](#3-configurar-o-ambiente-linux)
   - [2.4. Instalar Dependências](#4-instalar-dependências)
3. [Etapas do Projeto](#etapas-do-projeto)
   - [3.1. Configuração do Servidor Nginx](#1-configuração-do-servidor-nginx)
   - [3.2. Criação do Script de Validação](#2-criação-do-script-de-validação)
   - [3.3. Configuração do Cron](#3-configuração-do-cron)
   - [3.4. Versionamento no Git](#4-versionamento-no-git)
4. [Estrutura Final do Projeto](#estrutura-final-do-projeto)
5. [Como Executar Manualmente](#como-executar-manualmente)
6. [Considerações Finais](#considerações-finais)

---

## Requisitos

- Windows 10 ou Windows 11
- WSL (Windows Subsystem for Linux)
- Distribuição Linux (Ubuntu 20.04 ou superior)

---

## Como Criar um Ambiente Linux no Windows Usando WSL

O Windows Subsystem for Linux (WSL) permite executar um ambiente Linux diretamente no Windows. Siga os passos abaixo para configurá-lo:

### 1. Habilitar o WSL
Abra o PowerShell como administrador e execute o comando:
```bash
wsl --install
```
Isso instala automaticamente o WSL 2 e a distribuição padrão do Ubuntu.

### 2. Escolher uma Distribuição Linux
Instale a distribuição com:
```bash
wsl --install -d <Ubuntu-20.04>
```
Se quiser outra distribuição, visualize as opções disponíveis:
```bash
wsl --list --online
```

### 3. Configurar o Ambiente Linux
Após a instalação, abra a distribuição Linux pelo menu Iniciar e configure seu usuário e senha.

### 4. Instalar Dependências
Dentro do ambiente Linux, atualize os pacotes e instale o que for necessário para o projeto:
```bash
sudo apt update
sudo apt install nginx git -y
```

## Etapas do Projeto

### 1. Configuração do Servidor Nginx

#### **Instalar o Nginx**
Execute os comandos abaixo para instalar o Nginx:
```bash
sudo apt update
sudo apt install nginx -y
```

#### **Iniciar e habilitar o Nginx**
Certifique-se de que o serviço esteja ativo e configurado para iniciar automaticamente:
```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

#### **Verificar se o servidor está online**
Confirme que o servidor Nginx está rodando:
```bash
systemctl status nginx
```
Testar no navegador: Acesse `http://localhost` ou o IP da máquina.

---

### 2. Criação do Script de Validação

#### **Criar o script `nginx_validacao.sh`**
O script verifica o status do serviço Nginx e gera logs com as informações relevantes.
```bash
vim nginx_validacao.sh
```

#### **Conteúdo do script:**
```bash
#!/bin/bash

# Diretórios de saída
DIR_ONLINE="/var/log/nginx_status/online"
DIR_OFFLINE="/var/log/nginx_status/offline"

# Criar os diretórios, se não existirem
mkdir -p "$DIR_ONLINE" "$DIR_OFFLINE"

# Verificar status do serviço
STATUS=$(systemctl is-active nginx)
DATE_TIME=$(date '+%Y-%m-%d %H:%M:%S')
SERVICE="Nginx"
MESSAGE=""

if [ "$STATUS" == "active" ]; then
    MESSAGE="ONLINE"
    OUTPUT_FILE="$DIR_ONLINE/status_$(date '+%Y%m%d%H%M%S').log"
else
    MESSAGE="OFFLINE"
    OUTPUT_FILE="$DIR_OFFLINE/status_$(date '+%Y%m%d%H%M%S').log"
fi

# Gerar saída
echo "
Data e hora: $DATE_TIME
Serviço: $SERVICE
Status do servidor: $STATUS
Serviço está $MESSAGE" > "$OUTPUT_FILE"
```

#### **Dar permissão de execução ao script**
```bash
chmod +x nginx_validacao.sh
```

#### **Testar o script manualmente**
```bash
./nginx_validacao.sh
```

---

### 3. Configuração do Cron

#### **Editar o crontab**
Configure o cron para executar o script a cada 5 minutos:
```bash
sudo crontab -e
```
Adicione a linha abaixo:
```bash
*/5 * * * * /home/linuxleobzr/nginx_validacao.sh
```


---

### 4. Versionamento no Git

#### **Inicializar o repositório Git**
```bash
git init
```

#### **Adicionar os arquivos ao repositório**
```bash
git add nginx_validacao.sh
```

#### **Criar um commit**
```bash
git commit -m "Adicionar script de validação do Nginx"
```

---

## Estrutura Final do Projeto

- **Script:** `nginx_validacao.sh`
- **Diretórios de logs:**
  - `/var/log/nginx_status/online`
  - `/var/log/nginx_status/offline`
- **Automatização:** Configurada no cron para rodar a cada 5 minutos.
- **Versionamento:** Realizado com Git.

---

## Como Executar Manualmente
Se quiser executar o script sem esperar o cron:
```bash
./nginx_validacao.sh
```

---
## Considerações Finais
Este projeto implementa a validação e monitoramento do servidor Nginx, criando logs organizados e automatizando a tarefa com cron. Todas as etapas foram versionadas no Git para controle e rastreabilidade.
