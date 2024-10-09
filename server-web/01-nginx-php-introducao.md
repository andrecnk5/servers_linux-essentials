# Instalação e Configuração Detalhada do Servidor Web NGINX com PHP no Debian

## 1. Introdução

Neste tutorial abrangente, exploraremos a instalação, configuração e otimização do servidor web NGINX com suporte a PHP, domínios e subdomínios no sistema operacional Debian. Este guia é especialmente elaborado para estudantes do Ensino Técnico Médio em Informática, oferecendo uma compreensão profunda dos conceitos e práticas envolvidos na administração de servidores web.

### 1.1 O que é NGINX?

NGINX (pronuncia-se "engine-x") é um servidor web de código aberto, reconhecido por sua alta performance, estabilidade, rica funcionalidade, configuração simples e baixo consumo de recursos. Além de atuar como servidor web, o NGINX pode ser utilizado como proxy reverso, balanceador de carga e cache HTTP.

### 1.2 História e Desenvolvimento

- Criado por Igor Sysoev em 2002, inicialmente para resolver o problema C10K (como lidar com 10.000 conexões simultâneas).
- Lançado publicamente em 2004 sob a licença BSD de 2 cláusulas.
- Ganhou popularidade rapidamente devido à sua eficiência e capacidade de lidar com alto tráfego.

### 1.3 Por que usar NGINX?

1. **Alto desempenho**: Capaz de lidar com um grande número de conexões simultâneas com baixo uso de memória.
2. **Eficiência de recursos**: Consome menos CPU e memória em comparação com servidores web tradicionais.
3. **Escalabilidade**: Ideal para sites de alto tráfego e aplicações web complexas.
4. **Arquitetura de evento assíncrono**: Permite processar múltiplas solicitações sem bloqueio.
5. **Flexibilidade**: Pode ser usado como servidor web, proxy reverso, balanceador de carga e mais.
6. **Configuração simples**: Sintaxe de configuração intuitiva e fácil de entender.
7. **Suporte a múltiplos domínios e subdomínios**: Facilita a hospedagem de vários sites em um único servidor.

### 1.4 Portas e Protocolos

O NGINX opera principalmente nas seguintes portas e protocolos:

- **Porta 80**: HTTP (Hypertext Transfer Protocol)
- **Porta 443**: HTTPS (HTTP Secure, usando SSL/TLS)
- **Protocolo HTTP/1.0, HTTP/1.1**: Suporte completo
- **Protocolo HTTP/2**: Suportado a partir da versão 1.9.5
- **Protocolo HTTP/3**: Suporte experimental a partir da versão 1.25.0

### 1.5 Importância na Infraestrutura Web Moderna

1. **Desempenho em Escala**: Crucial para sites de alto tráfego e aplicações web complexas.
2. **Segurança**: Oferece recursos para mitigar ataques DDoS e outras vulnerabilidades web.
3. **Eficiência Energética**: Menor consumo de recursos leva a uma redução no consumo de energia dos data centers.
4. **Microserviços e Containers**: Ideal para arquiteturas modernas baseadas em microserviços e containers.
5. **CDN e Edge Computing**: Frequentemente usado em redes de distribuição de conteúdo e edge computing.

### 1.6 NGINX vs. Apache

Embora ambos sejam servidores web populares, NGINX e Apache têm diferenças significativas:

| Característica | NGINX | Apache |
|----------------|-------|--------|
| Arquitetura    | Assíncrona, orientada a eventos | Baseada em processos/threads |
| Desempenho sob alta carga | Excelente | Bom, mas pode degradar com muitas conexões |
| Consumo de recursos | Baixo | Moderado a Alto |
| Configuração   | Geralmente mais simples | Pode ser mais complexa |
| Módulos        | Carregados estaticamente | Podem ser carregados dinamicamente |
| Suporte a .htaccess | Não suporta diretamente | Suporte nativo |

3. 

