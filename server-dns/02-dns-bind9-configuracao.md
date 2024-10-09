# 2. Configuração Básica

Após a instalação do Bind9, é necessário configurá-lo corretamente para atender às necessidades específicas do seu ambiente. A configuração do Bind9 é feita principalmente através de arquivos de texto localizados no diretório `/etc/bind/`. Vamos examinar cada arquivo importante e suas configurações em detalhes.

### 2.1 Visão Geral dos Arquivos de Configuração

Antes de mergulharmos nos detalhes, é importante entender a estrutura dos arquivos de configuração do Bind9:

- `/etc/bind/named.conf`: O arquivo de configuração principal que inclui outros arquivos.
- `/etc/bind/named.conf.options`: Contém opções globais para o servidor DNS.
- `/etc/bind/named.conf.local`: Usado para definir zonas DNS locais.
- `/etc/bind/named.conf.default-zones`: Contém configurações para zonas padrão como localhost e reverso.
- Arquivos de zona: Contêm os registros DNS reais para cada domínio.

### 2.2 Configurando o arquivo named.conf.options

Este arquivo é crucial para definir o comportamento global do seu servidor DNS. Vamos editá-lo e explicar cada opção em detalhes:

```bash
sudo nano /etc/bind/named.conf.options
```

Adicione ou modifique o arquivo para que ele se pareça com isto:

```
options {
    directory "/var/cache/bind";

    // Habilita consultas recursivas
    recursion yes;
    
    // Define quem pode fazer consultas recursivas
    allow-recursion { 
        localhost;
        192.168.1.0/24;
    };
    
    // Define em quais interfaces o servidor irá escutar
    listen-on { 
        127.0.0.1; 
        192.168.1.10;  // Substitua pelo IP do seu servidor
    };
    
    // Restringe transferências de zona
    allow-transfer { none; };

    // Configura servidores DNS para encaminhamento
    forwarders {
        8.8.8.8;
        8.8.4.4;
    };

    // Configurações adicionais de segurança
    version none;
    dnssec-validation auto;
    auth-nxdomain no;
};
```

Explicação detalhada das opções:

- `directory "/var/cache/bind";`: Define o diretório de trabalho para o Bind9.
- `recursion yes;`: Permite que o servidor faça consultas recursivas em nome dos clientes.
- `allow-recursion { localhost; 192.168.1.0/24; };`: Especifica quais clientes podem fazer consultas recursivas. Neste caso, permite localhost e a rede 192.168.1.0/24.
- `listen-on { 127.0.0.1; 192.168.1.10; };`: Define em quais interfaces de rede o servidor DNS irá escutar. Ajuste 192.168.1.10 para o IP do seu servidor.
- `allow-transfer { none; };`: Impede transferências de zona não autorizadas.
- `forwarders { 8.8.8.8; 8.8.4.4; };`: Configura servidores DNS do Google como encaminhadores para consultas que o servidor não pode resolver diretamente.
- `version none;`: Oculta a versão do Bind para melhorar a segurança.
- `dnssec-validation auto;`: Habilita a validação DNSSEC automática.
- `auth-nxdomain no;`: Desabilita a geração de respostas autoritativas para consultas de domínios inexistentes.

### 2.3 Configurando o arquivo named.conf.local

Este arquivo é usado para definir as zonas DNS que seu servidor irá gerenciar. Vamos criar uma configuração para o domínio "exemplo.com":

```bash
sudo nano /etc/bind/named.conf.local
```

Adicione o seguinte conteúdo:

```
zone "exemplo.com" {
    type master;
    file "/etc/bind/db.exemplo.com";
    allow-transfer { 192.168.1.11; };  // IP do servidor DNS secundário, se houver
};

zone "1.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192";
    allow-transfer { 192.168.1.11; };  // IP do servidor DNS secundário, se houver
};
```

Explicação:

- A primeira `zone` define a zona direta para "exemplo.com".
- A segunda `zone` define a zona reversa para a rede 192.168.1.0/24.
- `type master;` indica que este é o servidor DNS primário para estas zonas.
- `file` especifica o caminho para o arquivo de zona correspondente.
- `allow-transfer` especifica quais servidores podem receber transferências de zona (útil para servidores DNS secundários).

### 2.4 Criando os Arquivos de Zona

Agora, vamos criar os arquivos de zona para o nosso domínio e para a zona reversa:

#### 2.4.1 Arquivo de Zona Direta

```bash
sudo nano /etc/bind/db.exemplo.com
```

Adicione o seguinte conteúdo:

```
$TTL    3600
@       IN      SOA     ns1.exemplo.com. admin.exemplo.com. (
                  2023061001     ; Serial
                  3600           ; Refresh
                  1800           ; Retry
                  604800         ; Expire
                  86400 )        ; Negative Cache TTL
;
@       IN      NS      ns1.exemplo.com.
@       IN      A       192.168.1.10
ns1     IN      A       192.168.1.10
www     IN      A       192.168.1.20
mail    IN      A       192.168.1.30
@       IN      MX  10  mail.exemplo.com.
```

Explicação dos registros:

- `$TTL 3600`: Define o Time To Live padrão para 1 hora.
- `SOA` (Start of Authority): Define informações autoritativas sobre a zona.
- `NS`: Define o servidor de nomes para o domínio.
- `A`: Mapeia nomes de host para endereços IPv4.
- `MX`: Define o servidor de e-mail para o domínio.

#### 2.4.2 Arquivo de Zona Reversa

```bash
sudo nano /etc/bind/db.192
```

Adicione o seguinte conteúdo:

```
$TTL    3600
@       IN      SOA     ns1.exemplo.com. admin.exemplo.com. (
                  2023061001     ; Serial
                  3600           ; Refresh
                  1800           ; Retry
                  604800         ; Expire
                  86400 )        ; Negative Cache TTL
;
@       IN      NS      ns1.exemplo.com.
10      IN      PTR     ns1.exemplo.com.
20      IN      PTR     www.exemplo.com.
30      IN      PTR     mail.exemplo.com.
```

Explicação:

- Esta zona reversa mapeia endereços IP para nomes de domínio.
- `PTR`: Registros de ponteiro para resolução reversa de IP para nome.

### 2.5 Verificando a Configuração

Antes de reiniciar o serviço, é crucial verificar se não há erros na configuração:

```bash
sudo named-checkconf
sudo named-checkzone exemplo.com /etc/bind/db.exemplo.com
sudo named-checkzone 1.168.192.in-addr.arpa /etc/bind/db.192
```

Estes comandos verificarão a sintaxe dos arquivos de configuração e das zonas. Se não houver saída, significa que não foram encontrados erros.

### 2.6 Reiniciando o Serviço Bind9

Após confirmar que não há erros, reinicie o serviço Bind9:

```bash
sudo systemctl restart bind9
```

Verifique o status do serviço para garantir que ele reiniciou com sucesso:

```bash
sudo systemctl status bind9
```

## 3. Testando o Servidor DNS

Agora que o servidor DNS está configurado e em execução, é importante testá-lo para garantir que está funcionando corretamente.

### 3.1 Testando Consultas DNS

Use o comando `dig` para testar as consultas DNS:

```bash
dig @localhost exemplo.com
dig @localhost www.exemplo.com
dig @localhost mail.exemplo.com
```

Para testar a resolução reversa:

```bash
dig @localhost -x 192.168.1.20
```

### 3.2 Verificando os Logs

Os logs do Bind9 podem fornecer informações valiosas sobre o funcionamento do servidor:

```bash
sudo tail -f /var/log/syslog | grep named
```

Este comando mostrará as entradas de log em tempo real relacionadas ao Bind9.

## 4. Considerações de Segurança Adicionais

### 4.1 Implementando TSIG (Transaction SIGnatures)

TSIG adiciona uma camada de segurança para transferências de zona:

1. Gere uma chave TSIG:
   ```bash
   sudo tsig-keygen -a hmac-sha256 exemplo_key > /etc/bind/exemplo_key.key
   ```

2. Inclua a chave em `named.conf.local`:
   ```
   include "/etc/bind/exemplo_key.key";
   ```

3. Modifique a configuração de zona para usar TSIG:
   ```
   zone "exemplo.com" {
       type master;
       file "/etc/bind/db.exemplo.com";
       allow-transfer { key exemplo_key; };
   };
   ```

### 4.2 Configurando DNSSEC

DNSSEC adiciona autenticação e integridade às respostas DNS:

1. Gere as chaves DNSSEC:
   ```bash
   sudo dnssec-keygen -a RSASHA256 -b 2048 -n ZONE exemplo.com
   sudo dnssec-keygen -a RSASHA256 -b 4096 -n ZONE -f KSK exemplo.com
   ```

2. Assine a zona:
   ```bash
   sudo dnssec-signzone -A -3 $(head -c 1000 /dev/random | sha1sum | cut -b 1-16) -N INCREMENT -o exemplo.com -t /etc/bind/db.exemplo.com
   ```

3. Atualize `named.conf.local` para usar a zona assinada.

## Conclusão

Parabéns! Você agora tem um servidor DNS Bind9 totalmente configurado no Debian, com suporte a cache, domínios e subdomínios, além de configurações básicas de segurança. Este tutorial cobriu os aspectos fundamentais da configuração do Bind9, mas lembre-se de que em um ambiente de produção, você pode precisar de configurações adicionais e medidas de segurança mais robustas.

Algumas práticas recomendadas adicionais incluem:

1. Manter o Bind9 e o sistema operacional atualizados regularmente.
2. Monitorar os logs do servidor DNS para detectar atividades suspeitas.
3. Implementar firewalls e regras de acesso para proteger o servidor DNS.
4. Considerar a implementação de servidores DNS secundários para redundância.
5. Realizar auditorias de segurança periódicas em sua configuração DNS.

Lembre-se de que a configuração de um servidor DNS é uma tarefa crítica e deve ser realizada com cuidado, especialmente em ambientes de produção. Sempre teste suas configurações em um ambiente seguro antes de implementá-las em produção.

