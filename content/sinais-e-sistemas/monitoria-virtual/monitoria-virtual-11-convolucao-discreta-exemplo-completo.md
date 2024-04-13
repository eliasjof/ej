---
title: "Monitoria Virtual #11 - Convolução discreta (exemplo completo)"
date: "2020-09-08"
categories: 
  - "monitoria-virtual-ss2020"
  - "sinais"
---

Assista ao vídeo e descubra mais sobre convolução discreta;

https://www.youtube.com/watch?v=Td58okYbhDk

## Curiosidade:

https://www.youtube.com/watch?v=O1MkuE4nFro

## Código utilizado:

Soma de Convolução (gráfica)

```python
################################################################################
# Soma de convolução de dois sinais causais através do
# procedimento gráfico
# autor: Elias J.
# data: 01/04/2019
################################################################################
clc;
clear all;
close all;
fontsize = 20;
fontsize2 = 18;

# Definindo o numero de amostras e os sinais 
# x[n] e g[n]

n = -10:1:10;

% Sinal x[n]
x = zeros(1,length(n));
x(12) = 1; % n = 1 -> x[n] = 1
x(13) = 2; % n = 2 -> x[n] = 2
x(14) = 3; % n = 3 -> x[n] = 3
x(15) = 2; % n = 4 -> x[n] = 2
x(16) = 1; % n = 5 -> x[n] = 1

%Sinal g[n]
g = zeros(1,length(n));
g(11) = 1;
g(12) = 1;
g(13) = 1;
g(14) = 1;
g(15) = 1;
g(16) = 1;
##
% Verificando os sinais 
figure
subplot(3,1,1)
stem(n,x,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
xlabel('n',"fontsize", fontsize)
ylabel('x[n]',"fontsize", fontsize)

subplot(3,1,2)
stem(n,g,'r')
set(gca, "linewidth", 1, "fontsize", fontsize2)
grid on
grid minor on
xlabel('n',"fontsize", fontsize)
ylabel('g[n]',"fontsize", fontsize)

####################################################################################
% Convolução
% 1º Passo -> Inverter g[m] para obter g[-m]

gmenos = zeros(1,length(g));
for i = 1:length(g)-1
  gmenos(i) = g(length(g)-i+1) 
endfor

subplot(3,1,3)
stem(n,gmenos,'g')
set(gca, "linewidth", 1, "fontsize", fontsize2)
grid on
grid minor on
xlabel('n',"fontsize", fontsize)
ylabel('g[-n]',"fontsize", fontsize)

% 2º Passo -> Deslocar h[-m] por n unidades
% 3º Passo -> Multiplicar x[m] por h[-m] e somar todos
% os produtos enquanto para cada descolamento de n

figure
y = [];
for n_desloc = -10:10
  gdesloc = desloc(gmenos,-n_desloc); #invertido pois h foi invertido
  yn = sum(x.*gdesloc)
  y = [y, yn]
  
  subplot(2,1,1)
  stem(n,x,'b')  
  hold on
  stem(n,gdesloc,'xr') 
  set(gca, "linewidth", 1, "fontsize", fontsize2)
  title('Soma de convolução')
  grid on
  grid minor on
  axis([-10 10 0 5])
  xlabel('n',"fontsize", fontsize)
  legend(['x[m]'; strcat('g[', num2str(n_desloc),' - m]')])
  hold off
  
  subplot(2,1,2)
  stem(n(1:length(y)),y,'r')   
  set(gca, "linewidth", 1, "fontsize", fontsize2)
  grid on
  grid minor on
  axis([-10 10 0 10])
  xlabel('n',"fontsize", fontsize)
  ylabel(strcat('y[', num2str(n_desloc), '] = \Sigma_m x[m]\cdot g[', num2str(n_desloc),' - m]'),"fontsize", 16)  
  
  pause(3)
  n_desloc
  
endfor

% Outra maneira é utilizando a função do Octave.
% y = conv(x,h,"same");
```

Soma de Convolução (direta)

```python
% Soma de Convolução
% Autor: Elias J
% Data: 03/09/2020

clear all;
close all;

% Definindo o número de amostras
N = 10;
n_time = -N:N;

% Convolucão = x[n]*h[n] onde h[n] é a resposta do 
% sistema ao impulso
% Convolução = somatorio de m = -infinito a +infinito de 
% x[m].h[n-m]

% Entrada x[n]:
% Exemplo: Degrau unitário x[n] = u[n]
x = [zeros(1,N), ones(1,N+1)];

% Resposta ao impulso h[n]:
% Iremos precisar para a convolução conhecer os termos dessa resposta
% Exemplo: h[m] = (gamma)^n * u[n]
gamma = 0.8;

% Desde -2*N até 2*N quando estamos desejando encontrar a resposta ao sistema
% de -N a N

% Porque -2N até 2N?
% n -> -N até N
% m -> -N até N
% NMin = -N + (-N) = -2N
% NMax = N + N = 2N

h = zeros(1,2*N);
for ni=0:2*N
  hi = gamma^(ni);
  h = [h, hi];
end

% Convolução
% Convolução = somatorio de m = -infinito a +infinito de 
% x[m].h[n-m]

% m = -N ate N
% n = -N ate N 

y =[]; % armazenará a resposta do sistema y[n] (condições iniciais nulas)
for n = -N:N % tempo em que se deseja avaliar o sistema    
  yn = 0; % valor de y[n] para um dado n
  for m = -N:N % somatório
    % Conversao do tempo discreto da convolução n-m para índice
    idH = (n - m) + 2*N+1 % indice p/ acessar o termo h[n-m]
    idX = m + N + 1;     % indice para acessar o termo x[m]
    yn += x(idX)*h(idH);
  end
  y = [y, yn]; % insere yn no vetor y
end

% Outra maneira é utilizando a função do Octave.
% y = conv(x,h,"same");

% Visualizando os resultados
figure
subplot(3,1,1)
stem(n_time,x)
title('Entrada x[n]')

subplot(3,1,2)
stem(n_time,h(-N+2*N+1:N+2*N+1),'r')
title('Resposta ao impulso h[n]')

subplot(3,1,3)
stem(n_time,y,'g')
title('Convolução')
```

desloc.m

```python
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Função para deslocar no tempo discreto de ndesloc unidades um sinal qualquer
% obs.: preenche de zeros quando não tiver informação do sinal
% autor: Elias J.
% data: 25/03/2019
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
function signal_desloc = desloc(signal, ndesloc)
  
  if ndesloc > 0
    signal_desloc = [signal(ndesloc+1:length(signal)), zeros(1, ndesloc)];
    
  else
    signal_desloc = [zeros(1, -ndesloc) , signal(1:length(signal)+ndesloc)];
    
    
  endif 

endfunction
```

No Octave/Matlab para realizar a convolução discreta, também é possível por meio do comando conv(x,h), vejam mais detalhes [aqui](https://www.mathworks.com/help/matlab/ref/conv.html).
