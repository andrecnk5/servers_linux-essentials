# 4. Testando o Servidor DNS

Após a instalação, configuração e verificação inicial do seu servidor DNS Bind9, é crucial realizar testes abrangentes para garantir que ele esteja funcionando corretamente e atendendo às necessidades da sua rede. Nesta seção, exploraremos em detalhes as ferramentas e técnicas para testar minuciosamente seu servidor DNS.

### 4.1 Ferramentas de Teste DNS

Existem várias ferramentas poderosas disponíveis para testar servidores DNS. Vamos explorar cada uma delas em detalhes:

#### 4.1.1 dig (Domain Information Groper)

O `dig` é uma das ferramentas mais versáteis e poderosas para testar servidores DNS.

Sintaxe básica:
```bash
dig [@servidor] nome_do_domínio [tipo]
```

Exemplos de uso:

1. Consulta básica:
   ```bash
   dig @localhost exemplo.com
   ```

2. Consulta de um tipo específico de registro:
   ```bash
   dig @localhost exemplo.com MX
   ```

3. Consulta reversa:
   ```bash
   dig @localhost -x 192.168.1.10
   ```

4. Consulta com rastreamento completo:
   ```bash
   dig @localhost exemplo.com +trace
   ```

5. Consulta com informações DNSSEC:
   ```bash
   dig @localhost exemplo.com +dnssec
   ```

6. Consulta de transferência de zona (AXFR):
   ```bash
   dig @localhost exemplo.com AXFR
   ```

Opções úteis do dig:
- `+short`: Exibe apenas a resposta resumida.
- `+noall +answer`: Mostra apenas a seção de resposta.
- `+noquestion +noauthority +noadditional +nostats`: Remove seções extras da saída.
- `+tcp`: Força o uso de TCP em vez de UDP.

#### 4.1.2 nslookup

O `nslookup` é uma ferramenta interativa útil para consultas DNS rápidas.

Uso básico:
```
nslookup
> server localhost
> www.exemplo.com
```

Exemplo de consulta não interativa:
```bash
nslookup www.exemplo.com localhost
```

#### 4.1.3 host

O `host` é uma ferramenta simples para consultas DNS.

Sintaxe básica:
```bash
host [-t tipo] nome servidor
```

Exemplos:
1. Consulta básica:
   ```bash
   host www.exemplo.com localhost
   ```

2. Consulta de tipo específico:
   ```bash
   host -t MX exemplo.com localhost
   ```

#### 4.1.4 delv (Domain Entity Lookup & Validation)

O `delv` é uma ferramenta mais recente, especialmente útil para diagnósticos relacionados ao DNSSEC.

Exemplo de uso:
```bash
delv @localhost exemplo.com
```

### 4.2 Testes Abrangentes

Agora que conhecemos as ferramentas, vamos realizar uma série de testes abrangentes:

#### 4.2.1 Teste de Resolução de Nomes

1. Teste a resolução de um nome de host:
   ```bash
   dig @localhost www.exemplo.com
   ```

2. Verifique se o resultado retorna o endereço IP correto conforme configurado em sua zona.

#### 4.2.2 Teste de Registros MX

1. Consulte os registros MX:
   ```bash
   dig @localhost exemplo.com MX
   ```

2. Confirme se os servidores de e-mail estão listados corretamente.

#### 4.2.3 Teste de Resolução Reversa

1. Faça uma consulta reversa:
   ```bash
   dig @localhost -x 192.168.1.10
   ```

2. Verifique se o nome de host retornado corresponde à configuração da sua zona reversa.

#### 4.2.4 Teste de Autoridade

1. Verifique se seu servidor é autoritativo para a zona:
   ```bash
   dig @localhost exemplo.com SOA
   ```

2. Confirme se a seção "AUTHORITY SECTION" está presente e correta.

#### 4.2.5 Teste de Recursão

1. Teste uma consulta recursiva para um domínio externo:
   ```bash
   dig @localhost www.example.com
   ```

2. Verifique se a resposta é recebida corretamente.

#### 4.2.6 Teste de DNSSEC (se configurado)

1. Verifique a presença de registros DNSKEY:
   ```bash
   dig @localhost exemplo.com DNSKEY
   ```

2. Teste a validação DNSSEC:
   ```bash
   delv @localhost +rtrace exemplo.com
   ```

#### 4.2.7 Teste de Transferência de Zona

1. Tente uma transferência de zona:
   ```bash
   dig @localhost exemplo.com AXFR
   ```

2. Isso deve falhar se as restrições de transferência de zona estiverem configuradas corretamente.

#### 4.2.8 Teste de Tempo de Resposta

1. Use o dig com a opção +stats para medir o tempo de resposta:
   ```bash
   dig @localhost exemplo.com +stats
   ```

2. Observe o valor "Query time" na saída.

### 4.3 Testes de Carga e Desempenho

Para testar o desempenho do seu servidor DNS sob carga, você pode usar ferramentas especializadas:

#### 4.3.1 dnsperf

O `dnsperf` é uma ferramenta para teste de desempenho DNS.

1. Instale o dnsperf:
   ```bash
   sudo apt install dnsperf
   ```

2. Crie um arquivo de consultas (queries.txt):
   ```
   www.exemplo.com A
   mail.exemplo.com A
   exemplo.com MX
   ```

3. Execute o teste:
   ```bash
   dnsperf -s localhost -d queries.txt -c 10 -l 30
   ```

   Isso simula 10 clientes fazendo consultas por 30 segundos.

#### 4.3.2 queryperf

O `queryperf` é outra ferramenta para teste de desempenho incluída no pacote BIND.

1. Crie um arquivo de consultas similar ao usado com dnsperf.

2. Execute o teste:
   ```bash
   queryperf -s localhost -d queries.txt
   ```

### 4.4 Monitoramento Contínuo

Para um monitoramento mais robusto e contínuo, considere as seguintes opções:

1. **Nagios ou Icinga**: Para monitoramento de serviços DNS.
   - Configure checks para verificar a resolução de nomes específicos.
   - Monitore o tempo de resposta das consultas DNS.

2. **Grafana com InfluxDB**: Para visualização de métricas de desempenho.
   - Colete métricas como número de consultas por segundo, tempos de resposta, etc.

3. **ELK Stack (Elasticsearch, Logstash, Kibana)**: Para análise avançada de logs.
   - Colete e analise logs do Bind9 para identificar padrões e anomalias.

4. **DNSViz**: Uma ferramenta web para análise e visualização da saúde e segurança de domínios DNS.
   - Útil especialmente para diagnósticos relacionados ao DNSSEC.

### 4.5 Testes de Segurança

1. Teste de Amplificação DNS:
   ```bash
   dig @localhost +short test.openresolver.com TXT
   ```
   Se configurado corretamente, seu servidor não deve responder a esta consulta.

2. Verifique se a versão do BIND está oculta:
   ```bash
   dig @localhost version.bind CHAOS TXT
   ```

3. Teste de Cache Poisoning:
   - Use ferramentas como `dnschef` para simular ataques de envenenamento de cache.

### 4.6 Documentação e Registro de Testes

1. Mantenha um registro detalhado de todos os testes realizados.
2. Documente os resultados esperados para cada teste.
3. Estabeleça um cronograma regular para repetir estes testes.

## Conclusão

Testar minuciosamente seu servidor DNS Bind9 é crucial para garantir sua confiabilidade, desempenho e segurança. As ferramentas e técnicas descritas neste tutorial fornecem uma base sólida para validar a funcionalidade do seu servidor DNS, identificar problemas potenciais e garantir que ele esteja operando de acordo com as expectativas.

Lembre-se de que o teste de um servidor DNS é um processo contínuo. À medida que sua rede evolui e novos desafios de segurança surgem, é importante revisar e atualizar regularmente seus procedimentos de teste.

Práticas recomendadas finais:

1. Automatize os testes sempre que possível para garantir consistência e regularidade.
2. Mantenha-se atualizado com as últimas técnicas de teste e ferramentas disponíveis.
3. Realize testes abrangentes após qualquer alteração significativa na configuração.
4. Considere a implementação de um ambiente de teste separado para validar alterações antes de aplicá-las em produção.
5. Treine sua equipe nas práticas de teste e diagnóstico do Bind9 para garantir uma resposta rápida a quaisquer problemas.
6. Revise regularmente os resultados dos testes e use-os para orientar melhorias contínuas na configuração e desempenho do seu servidor DNS.

