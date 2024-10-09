# 2. Pré-requisitos para Instalação e Configuração do NGINX

## 2.1 Requisitos de Sistema

### 2.1.1 Hardware

Para um ambiente de desenvolvimento ou teste, o NGINX pode funcionar com recursos mínimos. No entanto, para um desempenho ideal, considere:

- **CPU**: Processador dual-core de 2GHz ou superior
- **RAM**: Mínimo de 2GB, recomendado 4GB ou mais
- **Armazenamento**: Mínimo de 20GB de espaço livre em disco (SSD preferível para melhor desempenho)
- **Rede**: Conexão de rede estável, preferencialmente Ethernet Gigabit

Para ambientes de produção, os requisitos podem ser significativamente maiores, dependendo do tráfego esperado.

### 2.1.2 Sistema Operacional

O NGINX é compatível com vários sistemas operacionais, mas para este curso, focaremos no Debian:

- Debian 11 "Bullseye" ou mais recente
- Arquitetura: x86_64 (64-bit) recomendada

Certifique-se de que o sistema esteja atualizado:

```bash
sudo apt update && sudo apt upgrade -y
```

## 2.2 Conhecimentos Prévios Recomendados

### 2.2.1 Linux Básico

- Navegação no sistema de arquivos (cd, ls, pwd)
- Edição de arquivos de texto (nano, vim)
- Gerenciamento de permissões (chmod, chown)
- Comandos básicos de rede (ifconfig/ip, ping, netstat)

### 2.2.2 Linha de Comando

- Uso do terminal/shell (Bash)
- Execução de comandos com privilégios sudo
- Redirecionamento de saída (>, >>)
- Pipes (|) para combinar comandos

### 2.2.3 Conceitos de Rede

- Compreensão básica de TCP/IP
- Familiaridade com DNS e resolução de nomes
- Entendimento de portas e serviços de rede

### 2.2.4 Segurança Básica

- Conceitos de firewall
- Noções de criptografia e HTTPS
- Boas práticas de segurança em servidores

## 2.3 Ferramentas e Softwares Necessários

### 2.3.1 Acesso Root ou Sudo

Você precisará de acesso root ou privilégios sudo para instalar e configurar o NGINX.

Verifique se você tem acesso sudo:

```bash
sudo -v
```

Se o comando for bem-sucedido, você tem acesso sudo.

### 2.3.2 Editor de Texto

Um editor de texto para modificar arquivos de configuração. Recomendamos:

- nano (mais amigável para iniciantes)
- vim (mais poderoso, mas com curva de aprendizado mais íngreme)

Instale o nano se ainda não estiver disponível:

```bash
sudo apt install nano
```

### 2.3.3 Gerenciador de Pacotes

O APT (Advanced Package Tool) já vem instalado no Debian. Certifique-se de que está atualizado:

```bash
sudo apt update
```

### 2.3.4 Curl ou Wget

Ferramentas para baixar arquivos e testar o servidor web. Instale-as se não estiverem presentes:

```bash
sudo apt install curl wget
```

## 2.4 Preparação do Ambiente

### 2.4.1 Configuração de Rede

1. Verifique a configuração de rede:

```bash
ip addr show
```

2. Certifique-se de ter um endereço IP estático ou uma configuração DHCP estável.

### 2.4.2 Configuração de Hostname

1. Verifique o hostname atual:

```bash
hostname
```

2. Se necessário, altere o hostname:

```bash
sudo hostnamectl set-hostname seu-novo-hostname
```

3. Atualize o arquivo /etc/hosts:

```bash
sudo nano /etc/hosts
```

Adicione ou atualize a linha:

```
127.0.1.1   seu-novo-hostname
```

### 2.4.3 Firewall

1. Instale o UFW (Uncomplicated Firewall) se ainda não estiver instalado:

```bash
sudo apt install ufw
```

2. Habilite o UFW:

```bash
sudo ufw enable
```

3. Configure regras básicas (será ajustado mais tarde para o NGINX):

```bash
sudo ufw allow ssh
```

### 2.4.4 Atualizações do Sistema

Mantenha o sistema atualizado:

```bash
sudo apt update
sudo apt upgrade -y
sudo apt autoremove
```

## 2.5 Considerações de Segurança

### 2.5.1 Atualizações Automáticas

Configure atualizações automáticas de segurança:

```bash
sudo apt install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

### 2.5.2 Fail2Ban

Instale o Fail2Ban para proteção contra tentativas de força bruta:

```bash
sudo apt install fail2ban
```

### 2.5.3 Contas de Usuário

Evite usar a conta root. Use uma conta de usuário com privilégios sudo.

## 2.6 Verificação Final

Antes de prosseguir com a instalação do NGINX, verifique se:

1. O sistema está atualizado
2. Você tem acesso root ou sudo
3. A rede está configurada corretamente
4. O firewall está ativo e configurado
5. As ferramentas necessárias estão instaladas (curl, wget, editor de texto)

## 2.7 Próximos Passos

Com todos os pré-requisitos atendidos, você está pronto para começar a instalação e configuração do NGINX. No próximo módulo, abordaremos o processo de instalação passo a passo.

