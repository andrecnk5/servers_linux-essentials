# 6. Segurança Básica do NGINX

## 6.1 Introdução

A segurança é um aspecto crucial na administração de servidores web. Neste módulo, abordaremos várias medidas de segurança básicas para proteger seu servidor NGINX contra ameaças comuns.

## 6.2 Atualizações de Segurança

### 6.2.1 Manter o Sistema Atualizado

Regularmente atualize seu sistema operacional e o NGINX:

```bash
sudo apt update
sudo apt upgrade
```

### 6.2.2 Habilitar Atualizações Automáticas de Segurança

Configure atualizações automáticas para patches de segurança:

```bash
sudo apt install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

## 6.3 Configurações de Segurança do NGINX

### 6.3.1 Ocultar Informações de Versão

Edite o arquivo de configuração principal do NGINX:

```bash
sudo nano /etc/nginx/nginx.conf
```

Adicione ou modifique a seguinte linha:

```nginx
server_tokens off;
```

Isso impede que o NGINX revele sua versão em cabeçalhos de resposta e páginas de erro.

### 6.3.2 Desativar Métodos HTTP Não Utilizados

No bloco `server` da sua configuração de site:

```nginx
if ($request_method !~ ^(GET|HEAD|POST)$) {
    return 444;
}
```

Isso restringe os métodos HTTP permitidos a GET, HEAD e POST.

### 6.3.3 Configurar Cabeçalhos de Segurança

Adicione os seguintes cabeçalhos no bloco `server`:

```nginx
add_header X-Frame-Options "SAMEORIGIN";
add_header X-XSS-Protection "1; mode=block";
add_header X-Content-Type-Options "nosniff";
add_header Referrer-Policy "strict-origin-when-cross-origin";
add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline';";
```

Explicação:
- `X-Frame-Options`: Previne ataques de clickjacking.
- `X-XSS-Protection`: Ativa a proteção contra XSS em navegadores antigos.
- `X-Content-Type-Options`: Previne ataques de MIME type sniffing.
- `Referrer-Policy`: Controla as informações de referência enviadas.
- `Content-Security-Policy`: Define políticas de segurança de conteúdo.

### 6.3.4 Limitar o Tamanho do Buffer

No bloco `http` do nginx.conf:

```nginx
client_body_buffer_size 1k;
client_header_buffer_size 1k;
client_max_body_size 1k;
large_client_header_buffers 2 1k;
```

Isso ajuda a prevenir certos tipos de ataques de buffer overflow.

### 6.3.5 Configurar Timeouts

Adicione no bloco `http`:

```nginx
client_body_timeout 10;
client_header_timeout 10;
keepalive_timeout 5 5;
send_timeout 10;
```

Isso ajuda a mitigar ataques de negação de serviço lentos.

## 6.4 Controle de Acesso

### 6.4.1 Restringir Acesso por IP

Para restringir o acesso a certas páginas ou diretórios:

```nginx
location /admin {
    allow 192.168.1.0/24;
    deny all;
}
```

### 6.4.2 Implementar Autenticação Básica

Primeiro, crie um arquivo de senha:

```bash
sudo apt install apache2-utils
sudo htpasswd -c /etc/nginx/.htpasswd usuario
```

Então, configure o NGINX para usar esta autenticação:

```nginx
location /admin {
    auth_basic "Área Restrita";
    auth_basic_user_file /etc/nginx/.htpasswd;
}
```

## 6.5 SSL/TLS

### 6.5.1 Instalar Certbot para Let's Encrypt

```bash
sudo apt install certbot python3-certbot-nginx
```

### 6.5.2 Obter e Configurar Certificado SSL

```bash
sudo certbot --nginx -d seudominio.com -d www.seudominio.com
```

### 6.5.3 Configurações SSL Recomendadas

No bloco `server` para HTTPS:

```nginx
ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers on;
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
ssl_stapling on;
ssl_stapling_verify on;
```

## 6.6 Firewall

### 6.6.1 Configurar UFW (Uncomplicated Firewall)

Instale e configure o UFW:

```bash
sudo apt install ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

### 6.6.2 Verificar Status do Firewall

```bash
sudo ufw status verbose
```

## 6.7 Fail2Ban

### 6.7.1 Instalar e Configurar Fail2Ban

```bash
sudo apt install fail2ban
```

Crie um arquivo de configuração local:

```bash
sudo nano /etc/fail2ban/jail.local
```

Adicione:

```ini
[nginx-http-auth]
enabled = true
filter = nginx-http-auth
port = http,https
logpath = /var/log/nginx/error.log
maxretry = 3
bantime = 600

[nginx-noscript]
enabled = true
port = http,https
filter = nginx-noscript
logpath = /var/log/nginx/access.log
maxretry = 6
bantime = 3600
findtime = 60
```

Reinicie o Fail2Ban:

```bash
sudo systemctl restart fail2ban
```

## 6.8 Monitoramento e Logs

### 6.8.1 Configurar Logs do NGINX

No bloco `http` do nginx.conf:

```nginx
log_format detailed '$remote_addr - $remote_user [$time_local] '
                    '"$request" $status $body_bytes_sent '
                    '"$http_referer" "$http_user_agent" '
                    '$request_time $upstream_response_time';

access_log /var/log/nginx/access.log detailed;
error_log /var/log/nginx/error.log;
```

### 6.8.2 Monitorar Logs

Use ferramentas como `tail` ou `grep` para monitorar logs em tempo real:

```bash
sudo tail -f /var/log/nginx/access.log
```

## 6.9 Exercícios Práticos

1. Configure o NGINX para usar HTTPS e redirecionar todo o tráfego HTTP para HTTPS.
2. Implemente autenticação básica para um diretório específico do seu site.
3. Configure o Fail2Ban para proteger contra tentativas de força bruta no login SSH.

## 6.10 Conclusão

A segurança é um processo contínuo. Estas medidas fornecem uma base sólida, mas é importante manter-se atualizado sobre novas ameaças e melhores práticas. Regularmente revise e atualize suas configurações de segurança para manter seu servidor NGINX protegido.

