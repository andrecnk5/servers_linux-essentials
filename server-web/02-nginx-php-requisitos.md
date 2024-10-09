# 2. Pré-requisitos

Antes de começarmos a instalação e configuração do NGINX, certifique-se de que você possui:

- Um servidor Debian atualizado (recomenda-se Debian 11 Bullseye ou mais recente)
- Acesso root ou privilégios sudo
- Conhecimento básico de linha de comando Linux
- Familiaridade com conceitos básicos de redes (IP, DNS, HTTP)
- Editor de texto de sua preferência (nano, vim, etc.)

### 2.1 Preparação do Ambiente

1. **Atualização do Sistema**:

   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Instalação de Ferramentas Básicas**:

   ```bash
   sudo apt install curl wget gnupg2 ca-certificates lsb-release -y
   ```

3. **Configuração do Fuso Horário**:

   ```bash
   sudo dpkg-reconfigure tzdata
   ```

4. **Configuração do Hostname** (opcional):

   ```bash
   sudo hostnamectl set-hostname seu_servidor_nginx
   ```

