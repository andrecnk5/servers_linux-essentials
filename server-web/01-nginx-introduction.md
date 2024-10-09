# 1. Introdução ao NGINX

## 1.1 O que é NGINX?

NGINX (pronuncia-se "engine-x") é um servidor web de código aberto que pode atuar também como proxy reverso, balanceador de carga, proxy de email e cache HTTP. Desenvolvido por Igor Sysoev em 2002 e lançado publicamente em 2004, o NGINX foi criado com o objetivo de superar as limitações de desempenho do Apache HTTP Server, especialmente em cenários de alta concorrência.

## 1.2 História e Evolução

- 2002: Igor Sysoev inicia o desenvolvimento do NGINX.
- 2004: Primeira versão pública lançada.
- 2011: Fundação da NGINX, Inc. para fornecer suporte comercial e desenvolvimento.
- 2019: F5 Networks adquire a NGINX, Inc., mantendo o compromisso com o código aberto.

## 1.3 Por que usar NGINX?

### 1.3.1 Vantagens Principais

1. **Alto Desempenho**: Arquitetura baseada em eventos, não bloqueante, permite lidar com um grande número de conexões simultâneas com baixo uso de recursos.

2. **Eficiência de Recursos**: Consome menos memória e CPU em comparação com servidores web tradicionais.

3. **Escalabilidade**: Pode escalar verticalmente (em um único servidor) e horizontalmente (em múltiplos servidores) com facilidade.

4. **Flexibilidade**: Configuração modular permite adaptar-se a diversos casos de uso.

5. **Proxy Reverso e Balanceamento de Carga**: Funcionalidades integradas para distribuição eficiente de tráfego.

### 1.3.2 Casos de Uso Comuns

- Servir conteúdo estático com alta eficiência
- Proxy reverso para aplicações web dinâmicas (PHP, Python, Node.js, etc.)
- Balanceamento de carga entre múltiplos servidores de back-end
- Terminação SSL/TLS
- Caching de conteúdo para melhorar o desempenho

## 1.4 Arquitetura e Funcionamento

NGINX utiliza uma arquitetura assíncrona e orientada a eventos, diferente do modelo tradicional baseado em processos ou threads.

### 1.4.1 Modelo de Processo

- Um processo mestre (master process)
- Múltiplos processos de trabalho (worker processes)

O processo mestre lê a configuração e mantém os processos de trabalho, que lidam com as conexões reais.

### 1.4.2 Manipulação de Conexões

- Usa I/O não bloqueante e manipuladores de eventos para processar múltiplas conexões em um único thread.
- Implementa um loop de eventos eficiente que permite que um único worker process gerencie milhares de conexões simultaneamente.

## 1.5 Portas e Protocolos

### 1.5.1 Portas Padrão

- **Porta 80**: HTTP (Hypertext Transfer Protocol)
- **Porta 443**: HTTPS (HTTP Secure, usando SSL/TLS)

NGINX pode ser configurado para escutar em qualquer porta disponível.

### 1.5.2 Protocolos Suportados

- HTTP/1.0, HTTP/1.1, HTTP/2
- HTTPS (com suporte a TLS 1.2, 1.3)
- WebSocket
- SMTP, POP3, IMAP (para proxy de email)

## 1.6 Importância no Cenário Atual

### 1.6.1 Estatísticas de Uso

Segundo o levantamento da Netcraft de 2023, NGINX é usado por:
- Aproximadamente 25% dos sites mais movimentados da web
- Mais de 40% dos servidores web em todo o mundo

### 1.6.2 Papel na Infraestrutura Moderna

1. **Microserviços**: Fundamental para arquiteturas baseadas em microserviços, atuando como gateway de API e balanceador de carga.

2. **Containers e Orquestração**: Amplamente utilizado em ambientes containerizados (Docker) e plataformas de orquestração (Kubernetes).

3. **CDNs (Content Delivery Networks)**: Componente chave em muitas CDNs devido à sua eficiência no cache e entrega de conteúdo estático.

4. **IoT (Internet das Coisas)**: Usado como gateway leve e eficiente para dispositivos IoT.

5. **Segurança Web**: Oferece recursos para mitigação de DDoS, limitação de taxa e terminação SSL/TLS.

## 1.7 NGINX vs. Apache

Embora ambos sejam servidores web populares, NGINX e Apache têm diferenças significativas:

| Característica            | NGINX                           | Apache                                     |
| ------------------------- | ------------------------------- | ------------------------------------------ |
| Arquitetura               | Assíncrona, orientada a eventos | Baseada em processos/threads               |
| Desempenho sob alta carga | Excelente                       | Bom, mas pode degradar com muitas conexões |
| Consumo de recursos       | Baixo                           | Moderado a Alto                            |
| Configuração              | Geralmente mais simples         | Pode ser mais complexa                     |
| Módulos                   | Carregados estaticamente        | Podem ser carregados dinamicamente         |
| Suporte a .htaccess       | Não suporta diretamente         | Suporte nativo                             |

### 1.8 Ecossistema PHP

PHP (PHP: Hypertext Preprocessor) é uma linguagem de script amplamente utilizada para desenvolvimento web. A integração do NGINX com PHP-FPM (FastCGI Process Manager) oferece um ambiente de alto desempenho para aplicações PHP.

#### Benefícios da Integração NGINX + PHP-FPM:

1. **Separação de Responsabilidades**: NGINX lida com solicitações estáticas, enquanto PHP-FPM processa scripts PHP.
2. **Melhor Gerenciamento de Recursos**: PHP-FPM permite um controle mais fino sobre os processos PHP.
3. **Escalabilidade**: Facilita a escalabilidade horizontal de aplicações PHP.

## 1.9 Conclusão

NGINX revolucionou o cenário de servidores web com sua arquitetura eficiente e flexível. Sua capacidade de lidar com alto tráfego, baixo consumo de recursos e versatilidade o tornaram uma escolha popular para uma ampla gama de aplicações, desde pequenos sites até grandes plataformas de e-commerce e redes sociais.

Ao longo deste curso, exploraremos como instalar, configurar e otimizar o NGINX para diversos cenários, fornecendo a você as habilidades necessárias para gerenciar eficientemente infraestruturas web modernas.

