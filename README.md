# Atividade Prática Linux - #PB - NOV 2024 | DevSecOps

## Documentação do Projeto: Validação e Monitoramento do Servidor Nginx

Este projeto tem como objetivo configurar um servidor Nginx, criar um script de validação do serviço, gerar logs com o status do servidor e automatizar essa verificação usando o cron. Todas as etapas foram realizadas e versionadas em um repositório Git. Aqui está a documentação completa do que foi feito.

---

## Requisitos

- Windows 10 ou Windows 11
- WSL (Windows Subsystem for Linux)
- Distribuição Linux (Ubuntu 20.04 ou superior)

---

## Requisitos da atividade
- Utilizar o WSL do Windows e criar um subsistema do Ubuntu 20.04 ou superior. 
- Subir um servidor Nginx que esteja online e rodando.
- Criar um script que valide se o serviço está online e enviar o resultado da validação para  um diretório definido pelo usuário.
- O script deve conter: data e hora, nome do serviço, status e mensagem personalizada de online ou offline.
- O script deve gerar dois arquivos de saída: um para o serviço online e um para o serviço offline.
- Preparar a execução automatizada do script a cada 5 minutos.

---

## Índice


<ol>
    <li><a href="#criando-ambiente-linux-no-windows">Criando um ambiente Linux no Windows</a></li>
    <li><a href="#etapas-projeto">Etapas do Projeto</a></li>
    <li><a href="#verificar-saida-script">Verificar Saída do Script</a></li>
    <li><a href="#estrutura-final">Estrutura Final do Projeto</a></li>
    <li><a href="#consideracoes-finais"> Considerações Finais</a></li>
</ol>


---



<h2 id="criando-ambiente-linux-no-windows">1. Criando um ambiente Linux no Windows</h2>

O Windows Subsystem for Linux (WSL) permite executar um ambiente Linux diretamente no Windows. Siga os passos abaixo para configurá-lo:

### 1.1 Habilitar o WSL
Abra o PowerShell como administrador e execute o comando:
```bash
wsl --install
```
Isso instala automaticamente o WSL 2 e a distribuição padrão do Ubuntu.

### 1.2 Escolher uma Distribuição Linux
Instale a distribuição com:
```bash
wsl --install -d Ubuntu-20.04
```
Se quiser outra distribuição, visualize as opções disponíveis:
```bash
wsl --list --online
```

### 1.3 Configurar o Ambiente Linux
Após a instalação, abra a distribuição Linux pelo menu Iniciar e configure seu usuário e senha.

### 1.4 Instalar Dependências
Dentro do ambiente Linux, atualize os pacotes e instale o que for necessário para o projeto:
```bash
sudo apt update
sudo apt install nginx git -y
```

<h2 id="etapas-projeto">2. Etapas do Projeto</h2>

### 2.1 Configuração do Servidor Nginx

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

### 2.2 Criação do Script de Validação

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

### 2.3 Configuração do Cron

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

### 2.4 Versionamento no Git

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

<h2 id="verificar-saida-script">3. Verificar Saída do Script</h2>

#### **Mude para o diretório em que os logs estão localizados**
```bash
cd /var/log/nginx_status/online
```

#### **Liste todos os logs**
```bash
ls -l
```

#### **Visualize a saída do script**
```bash
cat <nome_do_arquivo.log>
```

---

<h2 id="estrutura-final">4. Estrutura Final do Projeto</h2>

- **Script:** `nginx_validacao.sh`
- **Diretórios de logs:**
  - `/var/log/nginx_status/online`
  - `/var/log/nginx_status/offline`
- **Automatização:** Configurada no cron para rodar a cada 5 minutos.
- **Versionamento:** Realizado com Git.

---


<h2 id="consideracoes-finais">5. Considerações Finais</h2>
Este projeto implementa a validação e monitoramento do servidor Nginx, criando logs organizados e automatizando a tarefa com cron. Todas as etapas foram versionadas no Git para controle e rastreabilidade.
