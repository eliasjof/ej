---
title: "Monitoria Virtual #14 – Filtros analógicos (Sallen Key)"
date: "2020-10-07"
categories: 
  - "monitoria-virtual-ss2020"
  - "sinais"
---

## Teoria

https://youtu.be/LtIHgP9unOs

## Simulação

https://youtu.be/mhIGcQtta-8

### Código utilizado

```python
%%============================Exemplo 4.18====================================%%
pkg load control
clc
clear all
%%============================Parametros======================================%%
​
%R1 = 10000;
%R2 = 10000;
%C1 = 0.000000001;
%C2 = 0.000000001;
​
R1 = 20000;
R2 = 5000;
C1 = 0.000000002;
C2 = 0.0000000005;
​
Ra = 100000000;
Rb = 100;
f0 = 1/((2*pi)*(R1*R2*C1*C2)^(1/2))
W0 = 1/((R1*R2*C1*C2)^(1/2))
%=============================Equacionamento==================================%%
​
K = 1 + (Rb/Ra);
Q=((R1*R2*C1*C2)^(1/2))/(R2*C2+R2*C1+(R1*C2*(1-K)))
A=20*log10(1/Q) % Para a Frequencia de Corte para K = 1 e 0.5<Q<1
Wo = 1/(R1*R2*C1*C2);
a = (1/(R1*C1)) + (1/(R2*C1)) + ((1/(R2*C2))*(1-K)) 
num = [K*Wo];
den = [1 a Wo];
H = tf(num,den)
bode(H);

```

### Simulação

[http://tinyurl.com/y3dbwvmo](http://tinyurl.com/y3dbwvmo)

![](image-1.png)
