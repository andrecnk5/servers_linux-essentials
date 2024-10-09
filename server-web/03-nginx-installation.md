# 3. Instalação do NGINX

## 3.1 Visão Geral

Neste módulo, abordaremos o processo de instalação do NGINX no sistema operacional Debian. Existem várias maneiras de instalar o NGINX, mas focaremos nos métodos mais comuns e recomendados.

## 3.2 Métodos de Instalação

### 3.2.1 Repositórios Oficiais do Debian

Este é o método mais simples e recomendado para a maioria dos usuários.

### 3.2.2 Repositórios Oficiais do NGINX

Oferece versões mais recentes do NGINX, mas requer configuração adicional.

### 3.2.3 Compilação a partir do Código Fonte

Oferece maior controle e personalização, mas é mais complexo e geralmente não recomendado para iniciantes.

## 3.3 Instalação a partir dos Repositórios Oficiais do Debian

### 3.3.1 Atualização do Sistema

Antes de instalar qualquer novo software, é crucial atualizar o sistema:

```bash
sudo apt update
sudo apt upgrade -y
```

### 3.3.2 Instalação do NGINX

Para instalar o NGINX, execute o seguinte comando:

```bash
sudo apt install nginx
```

Durante a instalação, o sistema pode solicitar confirmação. Digite 'Y' e pressione Enter para continuar.

### 3.3.3 Verificação da Instalação

Após a instalação, verifique se o NGINX foi instalado corretamente:

```bash
nginx -v
```

Isso deve exibir a versão do NGINX instalada.

### 3.3.4 Iniciar o Serviço NGINX

O NGINX geralmente inicia automaticamente após a instalação. Para verificar o status:

```bash
sudo systemctl status nginx
```

Se não estiver em execução, inicie-o com:

```bash
sudo systemctl start nginx
```

### 3.3.5 Habilitar o NGINX para Iniciar na Inicialização do Sistema

Para garantir que o NGINX inicie automaticamente quando o servidor for reiniciado:

```bash
sudo systemctl enable nginx
```

## 3.4 Instalação a partir dos Repositórios Oficiais do NGINX

### 3.4.1 Adição do Repositório NGINX

1. Instale as dependências necessárias:

```bash
sudo apt install curl gnupg2 ca-certificates lsb-release debian-archive-keyring
```

2. Importe a chave GPG oficial do NGINX:

```bash
curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
    | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null
```

3. Adicione o repositório do NGINX:

```bash
echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
http://nginx.org/packages/debian `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list
```

### 3.4.2 Instalação do NGINX

Atualize o índice de pacotes e instale o NGINX:

```bash
sudo apt update
sudo apt install nginx
```

### 3.4.3 Verificação e Inicialização

Siga os passos 3.3.3 a 3.3.5 da seção anterior para verificar, iniciar e habilitar o NGINX.

## 3.5 Compilação a partir do Código Fonte

Este método é avançado e geralmente não recomendado para iniciantes. No entanto, é útil para personalização avançada.

### 3.5.1 Instalação de Dependências

```bash
sudo apt install build-essential libpcre3 libpcre3-dev zlib1g zlib1g-dev libssl-dev
```

### 3.5.2 Download e Extração do Código Fonte

```bash
wget https://nginx.org/download/nginx-1.20.1.tar.gz
tar zxvf nginx-1.20.1.tar.gz
cd nginx-1.20.1
```

### 3.5.3 Configuração e Compilação

```bash
./configure --sbin-path=/usr/bin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --with-pcre --pid-path=/var/run/nginx.pid --with-http_ssl_module
make
sudo make install
```

### 3.5.4 Criação do Serviço Systemd

Crie um arquivo de unidade systemd para gerenciar o NGINX:

```bash
sudo nano /etc/systemd/system/nginx.service
```

Adicione o seguinte conteúdo:

```
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/bin/nginx -t
ExecStart=/usr/bin/nginx
ExecReload=/usr/bin/nginx -s reload
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

Salve e feche o arquivo. Em seguida, recarregue o systemd:

```bash
sudo systemctl daemon-reload
```

### 3.5.5 Inicialização e Habilitação

Siga os passos 3.3.4 e 3.3.5 para iniciar e habilitar o serviço NGINX.

## 3.6 Verificações Pós-instalação

### 3.6.1 Teste da Configuração

Verifique se não há erros na configuração:

```bash
sudo nginx -t
```

### 3.6.2 Acesso via Navegador

Abra um navegador e acesse `http://localhost` ou `http://[seu_ip_do_servidor]`. Você deve ver a página de boas-vindas padrão do NGINX.

### 3.6.3 Verificação de Portas

Verifique se o NGINX está escutando nas portas corretas:

```bash
sudo ss -tulnp | grep nginx
```

Você deve ver o NGINX escutando na porta 80 (HTTP).

## 3.7 Resolução de Problemas Comuns

### 3.7.1 NGINX não Inicia

Verifique os logs de erro:

```bash
sudo journalctl -u nginx.service
```

### 3.7.2 Conflitos de Porta

Se outro serviço estiver usando a porta 80, você precisará parar esse serviço ou configurar o NGINX para usar uma porta diferente.

### 3.7.3 Problemas de Permissão

Certifique-se de que o usuário do NGINX (geralmente www-data) tenha as permissões corretas nos diretórios relevantes.

## 3.8 Próximos Passos

Com o NGINX instalado e funcionando, o próximo passo é aprender sobre sua configuração básica, que será abordada no próximo módulo.

