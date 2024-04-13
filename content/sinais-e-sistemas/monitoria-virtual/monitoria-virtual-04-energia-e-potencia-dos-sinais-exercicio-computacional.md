---
title: "Monitoria Virtual #04: Energia e Potência dos sinais (exercício computacional)"
date: "2020-05-25"
categories: 
  - "monitoria-virtual-ss2020"
  - "sinais"
---

https://www.youtube.com/watch?v=Ll64zoPaCdQ

Veja a resolução teórica dos exercícios [aqui](https://ej-ensino.com.br/2020/05/08/monitoria-virtual-02-energia-e-potencia-dos-sinais/).

## Código do Octave utilizado no vídeo

```python
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Reflexão e escalonamento                                            
%                                                                           
% autor: Leandro Vidigal
% data: 07/05/2020
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

clear all
clc

%======================== REPRODUÇÃO DO SINAL==================================%
ti = 0;
tf = 20;
t=linspace(ti,tf,1000); % Cria Periodo T
A = 10 % Escolhido
T=tf-ti;
a=(2*A)/T; % Y=ax+b  b = 0  y, x , a = 2A/T

for i = 1:length(t)/2  % Constroi função f(x)=ax até medade do ciclo do periodo
x(i)=a*t(i); 
end

for i = (length(t)/2)+1:length(t) % Controi os zeros da segunda metade do ciclo
x(i)=0;
end
 
%=================== PLOTA A FIGURA DO PERIODO DO SINAL =======================%

figure
stem(t,x);
legend("SINAL EM UM PERIODO")
%=================== SIMULA SINAL PERIODICO DO EXERCÍCIO ======================%
X = [x x x x];  % CONCATENA O SINAL
t1 = linspace(-40,40,length(X)); % CRIA UM NOVO PERIODO PARA CONTEMPLAR O SINAL
%figure
%plot(t1,X);  % PLOTA O SINAL
%legend("SIMULACAO DO SINAL DO PROBLEMA")


%=================== PLOTA A FIGURA DO PERIODO DO SINAL =======================%

%figure
%stem(t,x);
%legend("INTEGRAL")

%========================== DETERMINAÇÃO DA POTÊNCIA ==========================%
ENERGIA=0; 
TA=(tf-ti)/length(t); % DEFINE TAXA DE AMOSTRAGEM
for i = 1:length(t) % ENCONTRA A ENERGIA FORNCIDA POR UM PERIODO
  ENERGIA=ENERGIA+TA*(x(i))^2; %DEFINE O VA'LOR DA ALTURA DO RETANGULO RELATIVO A CURVA
end

energiadoperiodo=ENERGIA
potencia=ENERGIA/T %DETERMINA A POTÊNCIA DO SINAL 
```
