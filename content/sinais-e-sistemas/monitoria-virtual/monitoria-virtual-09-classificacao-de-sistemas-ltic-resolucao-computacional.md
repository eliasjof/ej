---
title: "Monitoria Virtual #09: Classificação de sistemas LTIC (resolução computacional)"
date: "2020-06-23"
categories: 
  - "info"
  - "monitoria-virtual-ss2020"
  - "sinais"
---

#### Resolução do Exercício 1.7-7 e 1.7-12 do livro:  
Lathi, B. P.. Sinais e Sistemas Lineares. 2 ed. Editora Bookman, 2007

Resolução computacional:

https://youtu.be/WkJ\_bmoAQoI

## Código utilizado no vídeo:

Exercício 1.7-7

```python
%%% ######################################################################## %%% 
% Classificação de Sistemas

% Exercício 1.7-7 - Classificar os sistemas em causais e não causais
# Letra a -> y(t) = x(t-2)
# Letra b -> y(t) = x(-t)
# Letra c -> y(t) = x(at), com a>1
# Letra d -> y(t) = x(at), com a<1

%%% ######################################################################## %%%

% Incializando o Octave
clc;
clear all;
close all;

% Para os gráficos
fontsize = 20;
fontsize2 = 18;

% Paramentros inicias
t1 = -10
t2 = 10
t = linspace(t1,t2,1000);

% Definindo o sinal de entrada (considerando a entrada uma função exponencial)
sinal_entrada_x = e.^(-0.5*t);

% Definindo o sinal de saída

# Letra a -> y(t) = x(t-2)
# Letra b -> y(t) = x(-t)
# Letra c -> y(t) = x(at), com a>1
# Letra d -> y(t) = x(at), com a<1

% Operações
deslocamento = 0
reflexao = 1
escala = 0.5

if reflexao == 1
ti = (((t1)/escala)*reflexao)-deslocamento
tf = (((t2)/escala)*reflexao)-deslocamento
tn1 = linspace(ti,tf,1000)

else
tf = (((t1)/escala)-deslocamento)*reflexao
ti = (((t2)/escala)-deslocamento)*reflexao
tn2= linspace(ti,tf,1000)
end


ta = linspace(ti,tf,1000);
for i = 1:length(ta)
  sinal_saida_y(i) = e.^(-0.5*(reflexao*escala*ta(i)+deslocamento));
end

# Plotando os resultados
figure
plot(t,sinal_entrada_x,'r') 
hold on
if reflexao ==1
  plot(tn1,sinal_saida_y)
else
  plot(tn2,sinal_saida_y)
end
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
xlabel('t',"fontsize", fontsize)
legend(['x(t)'; 'y(t)'])
```

Exercício 1.7-12

```python
%%% ####################################################################### %%%
% Classificação de Sistemas

% Exercício 1.7-12 - Classificar o sistema abaixo quanto a linearidade, 
% invariancia no tempo, causalidade e memória

% y(t) = x(t)  se x(t) > 0
% y(t) = 0     se x(t) <= 0

%%% ####################################################################### %%%


# not a function file:
1;
% Inicializando o Octave
clc;
clear all;
close all;
fontsize = 20;
fontsize2 = 18;

% Definindo o sinal de entrada 
ti = -5
tf = 10
t1 = linspace(ti,0,1000);
t2 = linspace(0,tf,1000);
t = [t1(1:end-1), t2]; #exclui o t=0 de t1
sinal_entrada_x = [ zeros(1, length(t1(1:end-1))), cos(2*pi*1/2.5*t2)]; # sinal causal

##figure
##plot(t, sinal_entrada_x)
##grid on
##xlabel("t")
##ylabel("x")
##axis([ti tf -1 1])

% Definindo o sinal de saída
function y = sistema(x)
  y = [];
  for i = 1:length(x)    
    if x(i) > 0
      y = [y, x(i)];
    else
      y = [y, 0];
    end
    
  end  
endfunction

Y = sistema(sinal_entrada_x);

%% 
% Plotando os resultados do sistema
figure
subplot(2,1,1)
plot(t, sinal_entrada_x)
title("Entrada -> Saída do sistema")
grid on
xlabel("t")
ylabel("x")
axis([ti tf -1 1])
 
subplot(2,1,2)
plot(t,Y,'r')
grid on
xlabel("t")
ylabel("y")
axis([ti tf -1 1])
 
%% 
% Linearidade
% Teste linearidade (basta uma inverdade para ser não linear)
 
x1 = -0.5*ones(1, length(sinal_entrada_x));
y1 = sistema(x1,t);
 
x2 = sinal_entrada_x;
y2 = sistema(x2,t);
 
% Propriedade superposicao: aditividade + homogeinidade
k1 = 2;
k2 = 3;
x3 = k1*x1 + k2*x2;
y3 = sistema(x3,t);
 
y3_teste = k1*y1 + k2*y2;

%%
% Plot resultado da linearidade
figure
subplot(3,2,1)
plot(t,x1)
title("Teste linearidade")
axis([ti tf -4 4])
grid on
xlabel("t")
ylabel("x1")
 
subplot(3,2,2)
plot(t,y1,'r')
grid on
xlabel("t")
ylabel("y1")
axis([ti tf -4 4])
 
subplot(3,2,3)
plot(t,x2)
grid on
xlabel("t")
ylabel("x2")
axis([ti tf -4 4])
 
subplot(3,2,4)
plot(t,y2,'r')
grid on
xlabel("t")
ylabel("y2")
axis([ti tf -4 4])
 
subplot(3,2,5)
plot(t,x3)
grid on
xlabel("t")
ylabel("x3")
axis([ti tf -4 4])
 
subplot(3,2,6)
plot(t,y3,'r')
grid on
xlabel("t")
ylabel("y3")
hold on
plot(t,y3_teste,'k')
legend(['y3 sist'],['y3 teste'],"location","southwest")
axis([ti tf -4 4])


%%
% Plot resultado da
% Causalidade
figure
plot(t,sinal_entrada_x,'r') 
title("Teste da causalidade")
hold on
plot(t,Y,'--m') 
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
xlabel('t',"fontsize", fontsize)
legend(['Sinal Entrada'; 'Saída do Sistema'])

%% 
% Invariancia no tempo
step = 0.01
T = 2
n_desloc = int32(T/step)
 
% Aplicando atraso na saida do sistema
yatrasado = desloc(Y,-n_desloc)

# Aplicando entrada atrasada ao sistema
xatrasado = desloc(sinal_entrada_x,-n_desloc);
ya = sistema(xatrasado,t);
 
% Plot resultado da Invariancia no tempo 
figure
subplot(2,3,1)
plot(t,sinal_entrada_x,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
xlabel('t',"fontsize", fontsize)
ylabel('x(t)',"fontsize", fontsize)
 
subplot(2,3,2)
plot(t,Y,'g')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
xlabel('t',"fontsize", fontsize)
ylabel('y(t)',"fontsize", fontsize)
 
subplot(2,3,3)
plot(t,yatrasado,'r')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
xlabel('t',"fontsize", fontsize)
ylabel('y(t-T)',"fontsize", fontsize)
 

subplot(2,3,4)
plot(t,sinal_entrada_x,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
xlabel('t',"fontsize", fontsize)
ylabel('x(t)',"fontsize", fontsize)
 
subplot(2,3,5)
plot(t,xatrasado,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
xlabel('t',"fontsize", fontsize)
ylabel('x(t-T)',"fontsize", fontsize)
 
subplot(2,3,6)
plot(t,ya,'m')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
xlabel('t',"fontsize", fontsize)
ylabel('y_a(t)',"fontsize", fontsize)
 
figure
plot(t,yatrasado,'r') %obtido aplicando atraso na saida
hold on
plot(t,ya,'--m') %obtido aplicando uma entrada atrasada no sistema
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Comparação dos resultados - Invariância no tempo')
xlabel('t',"fontsize", fontsize)
legend(['y(t-T)'; 'y_a(t)'])

```

salvar a seguinte função na mesma pasta, com o nome desloc.m

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

Veja a resolução teórica [aqui](https://ej-ensino.com.br/2020/06/23/monitoria-virtual-08-classificacao-de-sistemas-ltic-linear-invariante-no-tempo-e-causal/?preview=true).
