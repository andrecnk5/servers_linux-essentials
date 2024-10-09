# 5. Instalação e Configuração do PHP com NGINX

## 5.1 Introdução

A integração do PHP com o NGINX é essencial para executar aplicações web dinâmicas. Neste módulo, aprenderemos como instalar o PHP, configurar o PHP-FPM (FastCGI Process Manager) e integrá-lo ao NGINX.

## 5.2 Instalação do PHP e PHP-FPM

### 5.2.1 Atualização do Sistema

Antes de iniciar, atualize o sistema:

```bash
sudo apt update
sudo apt upgrade -y
```

### 5.2.2 Instalação do PHP e Módulos Comuns

Vamos instalar o PHP e alguns módulos frequentemente utilizados:

```bash
sudo apt install php-fpm php-mysql php-curl php-gd php-intl php-mbstring php-soap php-xml php-xmlrpc php-zip
```

Explicação dos módulos:
- `php-fpm`: FastCGI Process Manager
- `php-mysql`: Suporte a MySQL/MariaDB
- `php-curl`: Suporte a requisições HTTP
- `php-gd`: Processamento de imagens
- `php-intl`: Internacionalização
- `php-mbstring`: Manipulação de strings multibyte
- `php-soap`: Suporte a SOAP para web services
- `php-xml` e `php-xmlrpc`: Processamento XML
- `php-zip`: Manipulação de arquivos ZIP

### 5.2.3 Verificação da Instalação

Verifique a versão do PHP instalada:

```bash
php -v
```

## 5.3 Configuração do PHP-FPM

O PHP-FPM gerencia pools de processos PHP. Vamos configurá-lo para trabalhar eficientemente com o NGINX.

### 5.3.1 Localização dos Arquivos de Configuração

- Configuração principal: `/etc/php/7.4/fpm/php.ini` (substitua 7.4 pela sua versão)
- Configuração do pool: `/etc/php/7.4/fpm/pool.d/www.conf`

### 5.3.2 Ajustes no php.ini

Edite o arquivo php.ini:

```bash
sudo nano /etc/php/7.4/fpm/php.ini
```

Ajuste as seguintes configurações:

```ini
memory_limit = 256M
upload_max_filesize = 64M
post_max_size = 64M
max_execution_time = 300
date.timezone = America/Sao_Paulo
```

Explicação:
- `memory_limit`: Limite de memória por processo PHP
- `upload_max_filesize` e `post_max_size`: Tamanho máximo de upload de arquivos
- `max_execution_time`: Tempo máximo de execução de scripts
- `date.timezone`: Fuso horário (ajuste conforme sua localização)

### 5.3.3 Configuração do Pool FPM

Edite o arquivo de configuração do pool:

```bash
sudo nano /etc/php/7.4/fpm/pool.d/www.conf
```

Ajuste as seguintes configurações:

```ini
user = www-data
group = www-data
listen = /run/php/php7.4-fpm.sock
listen.owner = www-data
listen.group = www-data
pm = dynamic
pm.max_children = 5
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3
```

Explicação:
- `user` e `group`: Define o usuário e grupo sob os quais o PHP-FPM irá executar
- `listen`: Socket Unix para comunicação com o NGINX
- `pm`: Gerenciador de processos (dynamic permite ajuste automático)
- `pm.max_children`: Número máximo de processos filhos
- Outras configurações `pm.*`: Controle fino do número de processos

## 5.4 Integração do PHP-FPM com o NGINX

### 5.4.1 Configuração do NGINX para PHP

Edite o arquivo de configuração do seu site no NGINX:

```bash
sudo nano /etc/nginx/sites-available/meusite.conf
```

Adicione ou modifique o bloco de localização para processar arquivos PHP:

```nginx
server {
    # ... outras configurações ...

    location ~ \.php$ {
        fastcgi_pass unix:/run/php/php7.4-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    # ... outras configurações ...
}
```

Explicação:
- `location ~ \.php$`: Captura todas as requisições para arquivos .php
- `fastcgi_pass`: Especifica o socket do PHP-FPM
- `include fastcgi_params`: Inclui parâmetros FastCGI padrão
- `fastcgi_param SCRIPT_FILENAME`: Define o caminho do script PHP

### 5.4.2 Habilitando o Índice PHP

Adicione `index.php` à diretiva `index`:

```nginx
index index.php index.html index.htm;
```

## 5.5 Testando a Configuração

### 5.5.1 Criando um Arquivo PHP de Teste

Crie um arquivo PHP no diretório raiz do seu site:

```bash
sudo nano /var/www/meusite/info.php
```

Adicione o seguinte conteúdo:

```php
<?php
phpinfo();
?>
```

### 5.5.2 Reiniciando os Serviços

Reinicie o PHP-FPM e o NGINX:

```bash
sudo systemctl restart php7.4-fpm
sudo systemctl restart nginx
```

### 5.5.3 Testando

Acesse `http://seudominio.com/info.php` no navegador. Você deve ver a página de informações do PHP.

## 5.6 Otimizações

### 5.6.1 Ajuste de Desempenho do PHP-FPM

Para sites com tráfego moderado a alto, considere ajustar:

```ini
pm = dynamic
pm.max_children = 50
pm.start_servers = 5
pm.min_spare_servers = 5
pm.max_spare_servers = 35
```

### 5.6.2 Caching de OpCode

Instale e configure o OPcache para melhorar o desempenho:

```bash
sudo apt install php-opcache
```

Edite `/etc/php/7.4/fpm/php.ini`:

```ini
opcache.enable=1
opcache.memory_consumption=128
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=4000
opcache.revalidate_freq=60
opcache.fast_shutdown=1
```

## 5.7 Segurança

### 5.7.1 Ocultando Informações do PHP

No `php.ini`, ajuste:

```ini
expose_php = Off
```

### 5.7.2 Limitando Acesso a Arquivos

No bloco `location` do NGINX para PHP, adicione:

```nginx
location ~ /\.ht {
    deny all;
}
```

## 5.8 Exercícios Práticos

1. Instale um CMS PHP popular (como WordPress) usando o NGINX e PHP-FPM.
2. Configure um pool PHP-FPM separado para um site específico.
3. Implemente caching de página inteira usando FastCGI Cache do NGINX.

## 5.9 Conclusão

A integração correta do PHP com o NGINX é crucial para o desempenho e segurança de aplicações web dinâmicas. Este módulo forneceu uma base sólida para configurar e otimizar o ambiente PHP em servidores NGINX. À medida que você ganha experiência, explore configurações mais avançadas e técnicas de otimização para melhorar ainda mais o desempenho de suas aplicações PHP.

