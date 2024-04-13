---
title: "Monitoria Virtual #02 – Exemplos no Octave/Matlab de invariância no tempo"
date: "2020-05-21"
categories: 
  - "monitoria-virtual-ss2020"
  - "sinais"
---

https://www.youtube.com/watch?v=8VIXh3r71bY

Veja a resolução teórica dos exercícios [aqui](https://ej-ensino.com.br/2020/05/08/monitoria-virtual-01-classificacao-dos-sistemas-linearidade-e-invariancia-no-tempo/).

## Códigos do Octave utilizados no vídeo

### Função deslocamento:

```python
################################################################################
# Função para deslocar no tempo discreto de ndesloc unidades um sinal qualquer
# obs.: preenche de zeros quando não tiver informação do sinal
# autor: Elias J.
# data: 25/03/2019
################################################################################

function signal_desloc = desloc(signal, ndesloc)
  
  if ndesloc > 0
    signal_desloc = [signal(ndesloc+1:length(signal)), zeros(1, ndesloc)]; 
    
# A função retorna um sinal deslocado para a esquerda
    
  else
    signal_desloc = [zeros(1, -ndesloc) , signal(1:length(signal)+ndesloc)];

# A função retorna um sinal deslocado para a direita
    
  endif 

endfunction
```

### Exemplos resolvidos

#### 1.7-2(a)

```python
################################################################################
# Variância e Invariância no Tempo                                             
#                                                                           
# autor: Elias J.
# data: 07/05/2020
################################################################################

%%% ======================= Inicializando o Octave ========================= %%%
clc;
clear all;
close all;

# Para os gráficos 
fontsize = 20;
fontsize2 = 18;

%%% ======================= Parâmetros iniciais =========================== %%%
step = 0.01 % passo
t = -10:step:10; % vetor tempo
T = 2 % atraso de teste
n_desloc = int32(T/step) % amostras que o atraso representa -> 200 amostras

##%%% ======================= Definindo a entrada x ======================== %%%

% Sinal de entrada exponencial: e.^(-0,5*t) , com t >= 0

sinal_entrada_x = e.^(-0.5*t(t>=0));

x = [ zeros(1,length(t(t<0))) , sinal_entrada_x ]; 

tamanho = length(t(t<0))

##% concatenar vetor cujo valores sao resultados da função exponencial com vetor 
##% de zeros, que comeca em 1 e vai até o comprimento do vetor t, considerando 
##% somente os valores de t menores que 0
##
##%%% ======================= Definindo a saída y ============================ %%%

% Letra a -> y(t) = x(t-2)

y_ndesloc_negativo = desloc(x,-int32(1/step));
y_ndesloc_positivo = desloc(x,int32(1/step));

%%% ==================== Plotando a entrada x, a saida y =================== %%%
figure
subplot(1,3,1)
plot(t,x,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Entrada x')
xlabel('t',"fontsize", fontsize)
ylabel('x(t)',"fontsize", fontsize)

subplot(1,3,2)
plot(t,y_ndesloc_negativo,'g')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('y com ndesloc negativo')
xlabel('t',"fontsize", fontsize)
ylabel('y(t)',"fontsize", fontsize)

subplot(1,3,3)
plot(t,y_ndesloc_positivo,'r')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('y com ndesloc positivo')
xlabel('t',"fontsize", fontsize)
ylabel('y(t)',"fontsize", fontsize)

##%%% ============= Procedimentos para classificação do sistema ============== %%%

### 1º: Aplicando atraso na saída -> Saida atrasada y(t-T)

y = desloc(x,-int32(1/step));  
yatrasado = desloc(y,-n_desloc);%Saida atrasada y(t-T)

# Plotando a entrada x, a saida y e a saida atrasada y(t-T) 
figure
subplot(1,3,1)
plot(t,x,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Entrada x(t)')
xlabel('t',"fontsize", fontsize)
ylabel('x(t)',"fontsize", fontsize)

subplot(1,3,2)
plot(t,y,'g')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Saída y(t)')
xlabel('t',"fontsize", fontsize)
ylabel('y(t)',"fontsize", fontsize)

subplot(1,3,3)
plot(t,yatrasado,'r')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Saída atrasada y(t-T)')
xlabel('t',"fontsize", fontsize)
ylabel('y(t-T)',"fontsize", fontsize)

# 2º: Acrescentando atraso na entrada -> Entrada atrasada x(t-T)
xatrasado = desloc(x,-n_desloc); % n_desloc = 200

### Saida y letra a 
y2 = desloc(xatrasado,-int32(1/step)); % ndesloc = 100

# Plotando a entrada x, a entrada atrasada x(t-T) e a saida y2 
figure
subplot(1,3,1)
plot(t,x,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Entrada x(t)')
xlabel('t',"fontsize", fontsize)
ylabel('x(t)',"fontsize", fontsize)

subplot(1,3,2)
plot(t,xatrasado,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Entrada atrasada x(t-T)')
xlabel('t',"fontsize", fontsize)
ylabel('x(t-T)',"fontsize", fontsize)

subplot(1,3,3)
plot(t,y2,'m')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Saída obtida após aplicar x(t-T) ao sistema')
xlabel('t',"fontsize", fontsize)
ylabel('y_a(t)',"fontsize", fontsize)

% ====== Comparação dos dois resultados e classificação do sistema ======= %%%
figure
plot(t,yatrasado,'r') %obtido aplicando atraso na saida
hold on
plot(t,y2,'--m') %obtido aplicando uma entrada atrasada no sistema
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Comparação dos resultados')
xlabel('t',"fontsize", fontsize)
legend(['y(t-T)'; 'y_a(t)'])

printf('\nClassificação do sistema:')
if (yatrasado==y2)
  printf('\nO sistema é invariante no tempo')
else
  printf('\nO sistema é variante no tempo')
end
```

#### 1.7-2(c)

```python
################################################################################
# Variância e Invariância no Tempo                                             
#                                                                           
# autor: Elias J.
# data: 07/05/2020
################################################################################

%%% ======================= Inicializando o Octave ========================= %%%
clc;
clear all;
close all;

# Para os gráficos 
fontsize = 20;
fontsize2 = 18;

%%% ======================= Parâmetros iniciais ============================ %%%
step = 0.01 
t = -10:step:10; 
T = 2 
n_desloc = int32(T/step) #amostras que o atraso representa

%%% ======================= Definindo a entrada x ========================== %%%

x = [ zeros(1,length(t(t<0))) , e.^(-0.5*t(t>=0)) ]; 

%%% ======================= Definindo a saída y ============================ %%%

% Letra c -> y(t) = x(at) % comprimido por um fator a

a = 3 
texpandido = -10*a:step:10*a;
xexpandido = [ zeros(1,length(texpandido(texpandido<0))),e.^(-0.5*texpandido(texpandido>=0)) ]; 

y = []
for ti=t
  id = int32(a*(ti+10)/step);
  yi = xexpandido(id+1);
  y = [y, yi];
endfor

%%% =================== Plotando a entrada x e a saida y =================== %%%
figure
subplot(1,2,1)
plot(t,x,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Entrada x(t)')
xlabel('t',"fontsize", fontsize)
ylabel('x(t)',"fontsize", fontsize)

subplot(1,2,2)
plot(t,y,'g')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Saída y(t)')
xlabel('t',"fontsize", fontsize)
ylabel('y(t)',"fontsize", fontsize)

%%% ============= Procedimentos para classificação do sistema ============ %%%

%1º: Aplicando atraso na saída -> Saida atrasada y(t-T) 
yatrasado = desloc(y,-n_desloc);

%Plotando a entrada x, a saida y e a saida atrasada y(t-T) 
figure
subplot(1,3,1)
plot(t,x,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Entrada x(t)')
xlabel('t',"fontsize", fontsize)
ylabel('x(t)',"fontsize", fontsize)

subplot(1,3,2)
plot(t,y,'g')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Saída y(t)')
xlabel('t',"fontsize", fontsize)
ylabel('y(t)',"fontsize", fontsize)

subplot(1,3,3)
plot(t,yatrasado,'r')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Saída atrasada y(t-T)')
xlabel('t',"fontsize", fontsize)
ylabel('y(t-T)',"fontsize", fontsize)

%2º: Acrescentando atraso na entrada -> Entrada atrasada x(t-T)
xatrasado = desloc(x,-n_desloc);
x_atrasado_expandido = desloc(xexpandido,-n_desloc);

%Saida y letra a 
y2 = []
for ti=t
  id = int32(a*(ti+10)/step);
  yi = x_atrasado_expandido(id+1) ;
  y2 = [y2, yi];
endfor;

%Plotando a entrada x, a entrada atrasada x(t-T) e a saida y 
figure
subplot(1,3,1)
plot(t,x,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Entrada x(t)')
xlabel('t',"fontsize", fontsize)
ylabel('x(t)',"fontsize", fontsize)

subplot(1,3,2)
plot(t,xatrasado,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Entrada atrasada x(t-T)')
xlabel('t',"fontsize", fontsize)
ylabel('x(t-T)',"fontsize", fontsize)

subplot(1,3,3)
plot(t,y2,'m')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Saída obtida ao aplicar x(t-T) ao sistema')
xlabel('t',"fontsize", fontsize)
ylabel('y_a(t)',"fontsize", fontsize)

%%% ====== Comparação dos dois resultados e classificação do sistema ======= %%%
figure
plot(t,yatrasado,'r') %obtido aplicando atraso na saida
hold on
plot(t,y2,'--m') % obtido aplicando entrada atrasada no sistema
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Comparação dos resultados')
xlabel('t',"fontsize", fontsize)
legend(['y(t-T)'; 'y_a(t)'])

printf('\nClassificação do sistema:')
if (yatrasado==y2)
  printf('\nO sistema é invariante no tempo')
else
  printf('\nO sistema é variante no tempo')
end

```

#### 1.7-2(d)

```python
################################################################################
# Variância e Invariância no Tempo                                             
#                                                                           
# autor: Elias J.
# data: 07/05/2020
################################################################################

%%% ======================= Inicializando o Octave ========================= %%%
clc;
clear all;
close all;

# Para os gráficos 
fontsize = 20;
fontsize2 = 18;

%%% ======================= Parâmetros iniciais ============================ %%%
step = 0.01; 
t = -10:step:10; 
T = 2;
n_desloc = int32(T/step) #amostras que o atraso representa

%%% ======================= Definindo a entrada x ========================== %%%

x = [ zeros(1,length(t(t<0))) , e.^(-0.5*t(t>=0)) ]; 

%%% ======================= Definindo a saída y ============================ %%%

# Letra d -> y(t) = tx(t-2) % deslocamento no tempo
y = desloc(x,-int32(1/step)).*t;

%%% =================== Plotando a entrada x e a saida y =================== %%%
figure
subplot(1,2,1)
plot(t,x,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Entrada x(t)')
xlabel('t',"fontsize", fontsize)
ylabel('x(t)',"fontsize", fontsize)

subplot(1,2,2)
plot(t,y,'g')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Saída y(t)')
xlabel('t',"fontsize", fontsize)
ylabel('y(t)',"fontsize", fontsize)

%%% ============= Procedimentos para classificação do sistema ============== %%%

# 1º: Aplicando atraso na saída -> Saida atrasada y(t-T) 
yatrasado = desloc(y,-n_desloc);

# Plotando a entrada x, a saida y e a saida atrasada y(t-T) 
figure
subplot(1,3,1)
plot(t,x,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Entrada x(t)')
xlabel('t',"fontsize", fontsize)
ylabel('x(t)',"fontsize", fontsize)

subplot(1,3,2)
plot(t,y,'g')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Saída y(t)')
xlabel('t',"fontsize", fontsize)
ylabel('y(t)',"fontsize", fontsize)

subplot(1,3,3)
plot(t,yatrasado,'r')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Saída atrasada y(t-T)')
xlabel('t',"fontsize", fontsize)
ylabel('y(t-T)',"fontsize", fontsize)

# 2º: Acrescentando atraso na entrada -> Entrada atrasada x(t-T)
xatrasado = desloc(x,-n_desloc);

# Saida y letra d -> y(t) = tx(t-2)
y2 = desloc(xatrasado,-int32(1/step)).*t;

# Plotando a entrada x, a entrada atrasada x(t-T) e a saida y2 
figure
subplot(1,3,1)
plot(t,x,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Entrada x(t)')
xlabel('t',"fontsize", fontsize)
ylabel('x(t)',"fontsize", fontsize)

subplot(1,3,2)
plot(t,xatrasado,'b')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 2])
title('Entrada atrasada x(t-T)')
xlabel('t',"fontsize", fontsize)
ylabel('x(t-T)',"fontsize", fontsize)

subplot(1,3,3)
plot(t,y2,'m')
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 3])
title('Saída obtida ao aplicar x(t-T) ao sistema')
xlabel('t',"fontsize", fontsize)
ylabel('y_a(t)',"fontsize", fontsize)

%%% ====== Comparação dos dois resultados e classificação do sistema ======= %%%
figure
plot(t,yatrasado,'r') %obtido aplicando atraso na saida 
hold on
plot(t,y2,'--m') %obtido aplicando a entrada atrasada no sistema
set(gca, "linewidth", 1, "fontsize", fontsize2)
hold on
grid on
grid minor on
axis([-10 10 -2 3])
title('Comparação dos resultados')
xlabel('t',"fontsize", fontsize)
legend(['y(t-T)'; 'y_a(t)'])

printf('\nClassificação do sistema:')
if (yatrasado==y2)
  printf('\nO sistema é invariante no tempo')
else
  printf('\nO sistema é variante no tempo')
end

```

Obs.: Para utilizar os códigos no MATLAB é necessário compatibilizar alguns comandos. Por exemplo, em MATLAB o comentário é %.
