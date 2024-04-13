---
title: "Monitoria Virtual #13 – Transformada Discreta de Fourier (utilizando a fft)"
date: "2020-09-30"
categories: 
  - "monitoria-virtual-ss2020"
  - "sinais"
---

https://youtu.be/cv-sOmAFP7w

## Código utilizado

```python
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% TDF                                            
% Utilize a Transformada Discreta de Fourier (TDF) para calcular a transformada
% de (e^–2t)u(t) e trace o espectro de Fourier resultante.                                                                          
% autor: Lorena
% data: 30/09/2020
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

clc;
clear all;
close all;

% Passo 1 - Determinar T e T0
T_0 = 4; % Período de repetição do sinal amostrado
N_0 = 256; % Numero de amostras
T = T_0/N_0 % Período de amostragem = 0.0156
t = (0:T:T*(N_0-1)); % 0 até 3.978, com passo de 0.0156
x = T*exp(-2*t); % xn = Tx(nT)
x(1) = T*(exp(-2*T_0)+1)/2; % Primeira amostra (em t= 0) é 0,5 

figure
stem(t,x)

% Passo 2 - Cálculo da TDF
X_r = fft(x)
r = [-N_0/2:N_0/2-1]; -128:127
omega_r = r*2*pi/T_0;

% Calculo da Transformada de Fourier verdadeira para comparação
%e^-at(u(t)) - X(w) = 1/ a + jw
omega =  linspace(-pi/T,pi/T,4097);
X = 1./(j*omega+2)

% Visualizando o espectro de Fourier resultante 
figure
subplot(211);
plot(omega,abs(X),'k',omega_r,fftshift(abs(X_r)),'ko');
% fftshift -> Muda a freqüência zero para centro do espectro.
xlabel('\omega');
ylabel('|X(\omega)|');
axis([-0.01 40 -0.01 0.51]);
legend('TF Verdadeira',['TDF com T_0 = ', num2str(T_0),...
',N_0 = ',num2str(N_0)],0);

subplot(212);
plot(omega,angle(X),'k',omega_r,fftshift(angle(X_r)),'ko');
xlabel('\omega');
ylabel('\angle X(\omega)');
axis([-0.01 40 -pi/2-0.01 0.01]);
legend('TF Verdadeira',['TDF com T_0 = ', num2str(T_0),...
',N_0 = ',num2str(N_0)],0);


```
