---
title: "Monitoria Virtual #06: Escalonamento, Deslocamento e reflexão dos sinais (exemplo computacional)"
date: "2020-05-29"
categories: 
  - "monitoria-virtual-ss2020"
  - "sinais"
---

#### Resolução do Exercício 1.2-2 do livro:  
Lathi, B. P.. Sinais e Sistemas Lineares. 2 ed. Editora Bookman, 2007

https://www.youtube.com/watch?v=FhG2mL9DO3o

Veja resolução teórica [aqui](https://ej-ensino.com.br/2020/05/26/monitoria-virtual-05-escalonamento-deslocamento-e-reflexao-dos-sinais/).

## Código utilizado no vídeo

```python
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Reflexão e escalonamento                                            
%                                                                           
% autor: Leandro Vidigal
% data: 07/05/2020
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

clear all
clc


%========================CONSTRUÇÃO DO SINAL===================================%


ti = -8
tf = -4

tz1=linspace(ti,tf,1000); % Cria Periodo T
for i = 1:length(tz1)  
z1(i)=0; 
end

ti = -4
tf = 0

ta=linspace(ti,tf,1000); % Cria Periodo T
a=-1; 
for i = 1:length(ta)  
x(i)=a*(ta(i));
end

ti = 0
tf = 2

tb=linspace(ti,tf,1000); % Cria Periodo T
a=1;  
for i = 1:length(tb)  
y(i)=a*(tb(i));
end

ti = 2
tf = 6

tz2=linspace(ti,tf,1000); % Cria Periodo T
for i = 1:length(tz2) 
z2(i)=0; 
end


X = [ z1 x y z2 ];
t = [ tz1 ta tb tz2];

%=================== PLOTA A FIGURA DO PERIODO DO SINAL =======================%

figure
plot(t,X);
legend("SINAL EM UM PERIODO")
hold on



%=====================DETERMINA PARAMETROS DO SINAL============================%
deslocamento=0
escala=1
reflexao=1
%========================CONSTRUÇÃO DO SINAL===================================%

if reflexao == 1
  
ti = (((-8)/escala)*reflexao)-deslocamento
tf = (((-4)/escala)*reflexao)-deslocamento

else

tf = (((-8)/escala)-deslocamento)*reflexao
ti = (((-4)/escala)-deslocamento)*reflexao
end


tz1=linspace(ti,tf,1000); % Cria Periodo T
for i = 1:length(tz1)  
z1(i)=0; 
end

if reflexao == 1
  
ti = (((-4 )/escala)*reflexao)-deslocamento
tf = (((0)/escala)*reflexao)-deslocamento

else

tf = (((-4 )/escala)-deslocamento)*reflexao
ti = (((0)/escala)-deslocamento)*reflexao

end

ta=linspace(ti,tf,1000); % Cria Periodo T
a=-1; 
for i = 1:length(ta)  
x(i)=a*(reflexao*escala*(ta(i)+deslocamento*reflexao)); 
end

if reflexao == 1
  
ti = (((0)/escala)*reflexao)-deslocamento
tf = (((2)/escala)*reflexao)-deslocamento

else

tf = (((0)/escala)-deslocamento)*reflexao
ti = (((2)/escala)-deslocamento)*reflexao

end

tb=linspace(ti,tf,1000); % Cria Periodo T
a=1;  
for i = 1:length(tb)  
y(i)=a*(reflexao*escala*(tb(i)+deslocamento*reflexao)); 
end


if reflexao == 1
  
ti = (((2)/escala)*reflexao)- deslocamento
tf = (((6)/escala)*reflexao)- deslocamento

else

tf = (((2)/escala)-deslocamento)*reflexao
ti = (((6)/escala)-deslocamento)*reflexao

end

tz2=linspace(ti,tf,1000); % Cria Periodo T
for i = 1:length(tz2) 
z2(i)=0; 
end

if reflexao ==1
X = [ z1 x y z2 ];
t = [ tz1 ta tb tz2];
else
X = [ z2 y x z1 ];
t = [ tz2 tb ta tz1];
end
 
%=================== PLOTA A FIGURA DO PERIODO DO SINAL =======================%

plot(t,X);
legend(["SINAL EM UM PERIODO"; "SINAL MODIFICADO"])
hold 

```
