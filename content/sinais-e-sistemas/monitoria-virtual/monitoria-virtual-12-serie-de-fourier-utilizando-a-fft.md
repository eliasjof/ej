---
title: "Monitoria Virtual #12 - Série de Fourier (utilizando a fft)"
date: "2020-09-25"
categories: 
  - "monitoria-virtual-ss2020"
  - "sinais"
---

## Código utilizado

```python
################################################################################
# Transformada exponencial de Fourier - Exemplo Computacional
#
# autor: Leandro Henrique Vidigal Sousa


clc;
clear all;
%%===================Parametros Inicais========================================%
T_0 = pi;
N_0 = 256;
T = T_0/N_0;
t = (0:T:T*(N_0 - 1))';
M=10;
x= exp(-t/2);
x(1) = (exp(-pi/2)+1)/2;

%===================Utilizando fft matlab======================================%

D_n = fft(x)/N_0; n=[-N_0/2:N_0/2-1]';
clf;
figure
subplot(2,2,1); stem(n,abs(fftshift(D_n)),'k');
axis([-M M -.1 .6]);xlabel('n');ylabel('|D_n|');
title('Espectro Exponencial com a Transformada Rapida de Fourier');
subplot(2,2,2); stem(n,(180/pi)*angle(fftshift(D_n)),'k');
axis([-M M -100 100]);xlabel('n');ylabel('\angle D_n [Graus]'); 

%%==================Encontrando o espectro trignométrico=======================%

n= [0:M];
C_n(1) = abs(D_n(1));
C_n(2:M+1) = 2*abs(D_n(2:M+1));
theta_n(1) = angle(D_n(1));
theta_n(2:M+1) = angle(D_n(2:M+1));
subplot(2,2,3);stem(n,C_n,'k');
xlabel('n');ylabel('C_n');
title('Espectro Trigonométrico com a Transformada Rapida de Fourier');
subplot(2,2,4);stem(n,(180/pi)*theta_n,'k');
xlabel('n');ylabel('\theta_n [Graus]');

%==============================================================================%


%=====================Utilizando Determinação Numerica=========================%
omega=(2*pi)/N_0;
n=[-128:127];
D_n=0;
for i = 1:256
for k = 0:N_0-1
  D_n = D_n +(x(k+1)*exp(-j*(i-129)*omega*k));
endfor
D_n=(D_n)/N_0;
Dnm(i)=abs(D_n);
Dna(i)=(180/pi)*angle(D_n);
D_n=0;
end
figure
subplot(2,1,1); stem(n,Dnm,'k');
axis([-M M -.1 .6]);xlabel('n');ylabel('|D_n|');
title('Espectro exponencial com a Transformada Discreta de Fourier');
subplot(2,1,2); stem(n,Dna,'k');
axis([-M M -100 100]);xlabel('n');ylabel('\angle D_n [Graus]');


%==============================================================================%
```
