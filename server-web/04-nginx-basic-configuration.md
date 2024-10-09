# 4. Configuração Básica do NGINX

## 4.1 Visão Geral da Estrutura de Configuração do NGINX

O NGINX utiliza um sistema de configuração baseado em arquivos de texto. Entender esta estrutura é fundamental para configurar e gerenciar eficientemente seu servidor web.

### 4.1.1 Localização dos Arquivos de Configuração

- Arquivo principal: `/etc/nginx/nginx.conf`
- Configurações de sites: `/etc/nginx/sites-available/` e `/etc/nginx/sites-enabled/`
- Módulos: `/etc/nginx/modules-enabled/`

### 4.1.2 Hierarquia de Configuração

O NGINX segue uma estrutura hierárquica de configuração:

1. Contexto principal (main)
2. Contexto http
3. Contexto server
4. Contexto location

Cada contexto herda e pode sobrescrever configurações dos contextos superiores.

## 4.2 O Arquivo nginx.conf

Este é o ponto de entrada para a configuração do NGINX. Vamos examinar suas partes principais:

```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 768;
    # multi_accept on;
}

http {
    ##
    # Configurações Básicas
    ##

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    # server_tokens off;

    # server_names_hash_bucket_size 64;
    # server_name_in_redirect off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ##
    # Configurações de SSL
    ##

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
    ssl_prefer_server_ciphers on;

    ##
    # Configurações de Logging
    ##

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    ##
    # Configurações de Gzip
    ##

    gzip on;

    # gzip_vary on;
    # gzip_proxied any;
    # gzip_comp_level 6;
    # gzip_buffers 16 8k;
    # gzip_http_version 1.1;
    # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    ##
    # Configurações de Virtual Host
    ##

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

### 4.2.1 Explicação das Diretivas Principais

- `user`: Define o usuário do sistema sob o qual o NGINX irá executar.
- `worker_processes`: Define o número de processos de trabalho. "auto" ajusta automaticamente com base no número de núcleos de CPU.
- `pid`: Especifica o arquivo onde o ID do processo principal será armazenado.
- `events`: Configura o processamento de conexões.
  - `worker_connections`: Número máximo de conexões simultâneas que cada worker process pode manipular.

- No contexto `http`:
  - `sendfile`: Otimiza a transferência de arquivos.
  - `tcp_nopush` e `tcp_nodelay`: Otimizam o envio de pacotes TCP.
  - `keepalive_timeout`: Tempo que uma conexão persistente permanecerá aberta.
  - `types_hash_max_size`: Tamanho máximo da tabela hash para tipos MIME.
  - `include`: Inclui outros arquivos de configuração.

## 4.3 Configurando um Site Básico

Vamos criar uma configuração básica para um site:

1. Crie um novo arquivo de configuração:

```bash
sudo nano /etc/nginx/sites-available/meusite.conf
```

2. Adicione o seguinte conteúdo:

```nginx
server {
    listen 80;
    server_name meusite.com www.meusite.com;

    root /var/www/meusite;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }
}
```

3. Crie um link simbólico para habilitar o site:

```bash
sudo ln -s /etc/nginx/sites-available/meusite.conf /etc/nginx/sites-enabled/
```

4. Teste a configuração e reinicie o NGINX:

```bash
sudo nginx -t
sudo systemctl restart nginx
```

### 4.3.1 Explicação das Diretivas do Server Block

- `listen`: Especifica a porta em que o NGINX irá escutar para este servidor.
- `server_name`: Define os nomes de domínio que este bloco de servidor irá gerenciar.
- `root`: Diretório raiz para os arquivos do site.
- `index`: Arquivos que serão considerados como índice do diretório.
- `location`: Define como o NGINX deve lidar com solicitações para diferentes URIs.
- `try_files`: Especifica a ordem de tentativa de arquivos.
- `error_page`: Define páginas personalizadas para códigos de erro HTTP.

## 4.4 Configurando Logs

Os logs são cruciais para monitoramento e solução de problemas. O NGINX usa dois tipos principais de logs:

### 4.4.1 Access Log

Registra todas as solicitações feitas ao servidor.

```nginx
access_log /var/log/nginx/meusite_access.log;
```

### 4.4.2 Error Log

Registra erros e eventos importantes.

```nginx
error_log /var/log/nginx/meusite_error.log;
```

Você pode adicionar estas linhas ao bloco `server` da sua configuração de site.

## 4.5 Configurações de Segurança Básica

Algumas configurações simples podem melhorar significativamente a segurança:

```nginx
server_tokens off;
add_header X-Frame-Options "SAMEORIGIN";
add_header X-XSS-Protection "1; mode=block";
```

- `server_tokens off`: Oculta a versão do NGINX nas respostas HTTP.
- `X-Frame-Options`: Previne ataques de clickjacking.
- `X-XSS-Protection`: Ativa a proteção contra XSS em navegadores modernos.

## 4.6 Testando e Recarregando Configurações

Sempre teste suas configurações antes de aplicá-las:

```bash
sudo nginx -t
```

Se não houver erros, recarregue o NGINX:

```bash
sudo systemctl reload nginx
```

## 4.7 Exercícios Práticos

1. Crie uma configuração para um novo site chamado "meuportfolio.com".
2. Configure logs personalizados para este site.
3. Adicione uma diretiva `location` para servir arquivos estáticos de um diretório `/var/www/meuportfolio/static/`.
4. Implemente as configurações de segurança básica mencionadas.

## 4.8 Conclusão

Esta configuração básica fornece uma base sólida para hospedar sites com o NGINX. À medida que você se familiariza com estas configurações, poderá explorar recursos mais avançados como proxy reverso, balanceamento de carga e configurações SSL/TLS.

