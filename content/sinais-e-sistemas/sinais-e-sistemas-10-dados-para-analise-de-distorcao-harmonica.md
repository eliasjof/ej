---
title: "Sinais e Sistemas #10: Dados para análise de distorção harmônica"
date: "2022-04-02"
categories: 
  - "sinais"
---

Os dados fornecidos no arquivo .zip contem em cada arquivo .csv: 4 sinais coletados de um circuito elétrico com uma frequência de amostragem de 120kHz durante 40ms. Sendo, respectivamente, a tensão fornecida a uma carga, v(t) , a corrente total consumida por essa carga, i(t). Essa corrente é a soma de duas componentes: a corrente fundamental, i0(t), e a corrente referente à distorção total provocada pela carga no circuito inoise(t).

[dados\_distorção-harmônica](https://ej-ensino.com.br/wp-content/uploads/2022/04/dados_distorcao-harmonica.zip)[Descarregar](https://ej-ensino.com.br/wp-content/uploads/2022/04/dados_distorcao-harmonica.zip)

Exemplo de código para realizar a leitura do arquivo .csv

```python
df = pd.read_csv('dados_g1.csv')
 v = df['v'].to_numpy()
i = df['i'].to_numpy()    
i0 = df['i0'].to_numpy()
inoise = df['inoise'].to_numpy()
```

Exemplo de um dos dados:

![](imagem_2022-04-02_200430.png)
