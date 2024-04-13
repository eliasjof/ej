---
title: "Sinais e Sistemas #08: Como obter a FFT (Fast Fourier Transform) no Octave"
date: "2020-10-02"
categories: 
  - "sinais"
---

## 1 - Introdução

Para um sinal \[latex\]x\[n\]\[/latex\] discreto e aperiódico no tempo a Transformada Discreta de Fourier (TDF) será um sinal contínuo e periódico na frequência. Caso \[latex\]x\[n\]\[/latex\] seja periódico, a TDF será um sinal discreto e periódico na frequência.

A TDF é dada por:

\[latex\] X(\\omega )=\\sum \_{n=-\\infty }^{\\infty }x\[n\]\\,e^{-i\\omega n} \[/latex\]

Uma maneira eficiente de obter computacionalmente amostras da função \[latex\] X(\\omega )\[/latex\] é utilizar o algoritmo chamado de _Fast Fourier Transform_ (FFT).

## 2 - Octave

No Octave, para visualizar corretamente os dados retornados pela função _fft()_ deve ser levado em consideração:

- o número de pontos utilizados (quanto maior, melhor será a fidelidade das amostras da fft com a TDF);
- a frequência na qual os pontos foram amostrados (para corresponder a frequência em Hz é necessário conhecer a frequência na qual os dados do sinal discreto foram obtidos);

Para facilitar, pode-se utilizar a seguinte função:

```python
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Analisador de frequências (utilizando FFT)
%
% autor: Elias J.
% data: 29/01/2020
% fs = frquencia de amostragem
% x = sinal de entrada
% X = vetor da resposta em frequência de x
% freq = vetor de frequências
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Analisador de frequências (utilizando FFT)
%
% autor: Elias J.
% data: 23/07/2021
% fs = frquencia de amostragem
% x = sinal de entrada
% X = vetor da resposta em frequência de x
% freq = vetor de frequências
% use scale = true -> divide o vetor por 1/N (relação direta com a série de Fourier)
% use scale = false -> TDF
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


function [X, freq] = plot_fft (x, fs, scale=true, logscale=false, viewfase=false)
  
  N = length(x);  
  T = N/fs; % período
  freq = linspace(0,N,N)/T;
  fc = ceil(N/2); % para ajustar os dados do vetor
  if scale
    X = fftn(x)/N;  
    X = 2*X(1:fc);
  else
    X = fftn(x);  
    X = X(1:fc);
  end
  
  if viewfase % visualização da fase
    subplot(2,1,1)
  end 
  if logscale
    plot(freq(1:fc), 20*log10(abs(X*N)));
    ylabel('20log|X|')
  else
    plot(freq(1:fc), abs(X));
    ylabel('|X|')
  end
  title('Análise de Fourier')
  xlabel('freq (Hz)')
  grid on
  X = round(X*1000)/1000.0;
  if viewfase
    subplot(2,1,2)
    plot(freq(1:fc), atan2d(imag(X), real(X)),'.');
    ylabel('fase')
    xlabel('freq (Hz)')
    grid on 
  end 
  
end

```

## 3 - Resultados

### 3.1 - Sinal periódico

Considere o seguinte sinal

\[latex\] x\[n\] = 0.8cos(2\\pi \\cdot 100n) + 2sin(2\\pi \\cdot 100n) \[/latex\]

```python
fs = 5000;
n = -100:1/fs:100;

x = 0.8*cos(2*pi*100*n) + 2*sin(2*pi*200*n);

figure
[X,f] = plot_fft(x,fs);
axis([0 500 0 max(abs(X))])
```

![](images/image.png)

### 3.2 - Sinal aperiódico

Considere o seguinte sinal

\[latex\] x\[n\] = 10 \\cdot sinc(2 \\cdot 10n) \[/latex\]

```
fs = 500;
n = -100:1/fs:100;

x = 10*sinc(2*10*n);

figure
[X,f] = plot_fft(x,fs);
axis([0 15 0 max(abs(X))])
```

### 3.3 - Tempo de execução

Para medir o tempo de execução de uma determinada função basta usar os comandos tic e toc

```
tic
X = fftn(x)/N;
tempo_decorrido = toc
```

## Dicas

Assista aos vídeos:

https://www.youtube.com/watch?v=uRnT6AGfgfU&list=PLYnNpzT-Y7kW8YCCOrsAl6qVfAKoL0YtC&index=22
