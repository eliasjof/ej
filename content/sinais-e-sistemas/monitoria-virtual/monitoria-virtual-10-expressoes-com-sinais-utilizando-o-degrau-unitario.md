---
title: "Monitoria Virtual #10: Expressões com sinais (utilizando o degrau unitário)"
date: "2020-06-29"
categories: 
  - "monitoria-virtual-ss2020"
  - "sinais"
---

#### Resolução do Exercício 1.4-1 e 1.4-2 do livro:  
Lathi, B. P.. Sinais e Sistemas Lineares. 2 ed. Editora Bookman, 2007

A resolução desses exercícios foi dividia em 3 partes.

Parte 1:

https://www.youtube.com/watch?v=AmZuVtkfNj4

## Resolução computacional

Parte 2:

https://youtu.be/1garja5RypI

Parte 3:

https://youtu.be/uSiTTSv\_qX0

## Códigos utilizados nos vídeos

Função STEP (salvar arquivo STEP.m na pasta de trabalho):

```python
function [u,t] = STEP(ti,tf,deslocamento,pontos)
t=linspace(ti,tf,pontos); 
for i = 1:length(t)
  if t(i)< -deslocamento
    u(i)=0;
  else
    u(i)=1;
  end
end

endfunction
```

Resolução dos exercícios:

```python
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Reflexão e escalonamento                                            
%                                                                          
% autor: Leandro Vidigal
% data: 07/05/2020
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
clear all
clc
%=====================DETERMINA PARAMETROS GERAIS==============================%
ti=-10;
tf=20;
pontos=20000;
t=linspace(ti,tf,pontos);
%========================CONSTRUÇÃO DOS DEGRAUS================================%
u1=STEP(ti,tf,-2,pontos);
u2=STEP(ti,tf,-4,pontos);
u3=STEP(ti,tf,-2,pontos);
u4=STEP(ti,tf,-4,pontos);
%=================== PLOTA A FIGURA DO PERIODO DO SINAL =======================%
figure 1
subplot(2,2,1)
plot(t,u1);
axis([-5 10 -2 2])
legend("DEGRAU 1");
subplot(2,2,2)
plot(t,-u2);
axis([-5 10 -2 2])
legend("DEGRAU 2");
u= u1 + (-u2);
subplot(2,2,3)
plot(t,u);
hold on
axis([-5 10 -2 2])
plot(t,u1);
plot(t,-u2);
legend("SOMA DEGRAU","DEGRAU 1","DEGRAU 2");
hold off;
%==================  APLICANDO DEGRAU =========================================%
a=1; 
for i = 1:length(t)    
y1(i)=a*(t(i))^2;
end
a=1;
b=-4; 
for i = 1:length(t)    
y2(i)=a*(t(i))+b;
end
nova=y2.*[u1-u2];
figure 2
plot(t,nova);
hold on
legend("FUNÇÃO")
```
