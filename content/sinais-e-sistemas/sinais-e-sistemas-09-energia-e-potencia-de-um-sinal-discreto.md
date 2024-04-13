---
title: "Sinais e Sistemas #09: Energia e Potência de um sinal discreto"
date: "2020-08-22"
categories: 
  - "sinais"
---

Assista ao vídeo:

https://www.youtube.com/watch?v=pqUW8eizhlk

```python
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Calcula a energia e a potência de um sinal discreto
% autor: Elias J.
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

1; %o arquivo contém funções

%%
% Função que calcula a energia do sinal
function e = energia(sinal)
  e = 0;
  for i=1:length(sinal)
    e += abs(sinal(i))^2;
  end    
end

%%
N = 10000; %número de amostras do sinal
vetor_n = -N:N; % vetor de tempo discreto

%%
% criação do sinal x[n] desejado utilizando for
x = [];
for n=-N:N
  xn = e^(j*(pi/2*n + pi/8)); % valor de x para um dado n
  x = [x, xn]; % acrescenta xn no sinal
end

%%
energia_do_sinal = energia(x) %obtém a energia do sinal

potencia_do_sinal = energia_do_sinal/(2*N + 1) %obtém a potência do sinal
```
