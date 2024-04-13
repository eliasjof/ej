---
title: "Sinais e Sistemas #07: Captura de som com Octave"
date: "2020-06-01"
categories: 
  - "sinais"
---

## 1 - Introdução

O Octave possui um módulo embutido que é dedicado para a aquisição de som. As principais funções de áudio podem ser acessadas [aqui](https://octave.org/doc/v4.4.1/Audio-Recorder.html#Audio-Recorder).

Com essas funções é possível criar aplicações no Octave para processamento de áudio. Veja um dado obtido:

![](images/image.png)

## 2 - Código

### 2.1 - Função de captura

Crie um arquivo com o mesmo nome da função e coloque-o na mesma pasta do projeto que estiver desenvolvendo.

```python
################################################################################
# Captura de som com Octave
# 16 bits
# 1 canal
#
# autor: Elias J.
# data: 29/01/2020
################################################################################

function [audiodata, fs] = capturaSom (time, fs = 44100, nbits = 16, nchannel = 1)
  
# especifica um gravador 
rec = audiorecorder (fs, nbits, nchannel);

# grava durante time secs
recordblocking (rec, time);

# obtem os dados do audio gravado
audiodata = getaudiodata (rec);


endfunction
```

### 2.2 -Usando a função de captura

```python
[audiodata, fs] = capturaSom(intervalo); #intervalo é o tempo de captura desejado do áudio
```

### 2.3 -Escutando o áudio gravado

```python
freq = 44100; # frequência utilizada na gravação do áudio
nbits = 16; #número de bits da placa de áudio
soundsc(audiodata,freq, nbits)
```

### 2.4 -Visualização gráfica

Uma possível visualização dos dados pode ser feita da seguinte maneira

```python
tfinal = length(x)*1/fs;
t = linspace(0,tfinal, length(x));
figure;
subplot(1,1,1)
plot(t,x,'.b')
grid on
xlabel('t(s)')
ylabel('sinal')
```

### 2.5 -Salvando os dados

Para salvar os dados obtidos em um arquivo .mat

```powershell
save -mat nome_do_arquivo.mat "audiodata" "fs"
```

Ou salvar em um formato de som .wav

```
 audiowrite("som2.wav",audiodata,44100,'BitsPerSample',16)
```

### 2.6 -Carregando os dados

Caso um dado de áudio tenha sido salvo na pasta de trabalho utilizando o Octave, utilize os seguintes comandos para carregá-los para o workspace:

```python
arquivo = 'nome_do_arquivo.mat';
load(arquivo);
```

Caso seja um arquivo de áudio em .wav, utilize o seguinte comando:

```python
arquivo = 'nome_do_arquivo.wav';
info = audioinfo(arquivo);
[x, fs] = audioread(arquivo);
x = x(:,1)'; # vetor linha
Ts = 1/fs;
tfinal = Ts*length(x);
```

Exemplos de arquivos salvos para serem testados:

[som.mat](https://ej-ensino.com.br/wp-content/uploads/2020/06/som.mat)[Descarregar](https://ej-ensino.com.br/wp-content/uploads/2020/06/som.mat)

[som2.wav](https://ej-ensino.com.br/wp-content/uploads/2020/06/som2.wav)[Descarregar](https://ej-ensino.com.br/wp-content/uploads/2020/06/som2.wav)

[som3.mat](https://ej-ensino.com.br/wp-content/uploads/2021/06/som3.mat)[Descarregar](https://ej-ensino.com.br/wp-content/uploads/2021/06/som3.mat)

## 3 - Convertendo .mp3 👉 .mat

Para carregar uma música .mp3 no octave é necessário converter para o formato .mat.

Uma maneira de fazer essa conversão é utilizando o programa gratuito [Audacity](https://audacity.br.uptodown.com/windows).

- Abra o arquivo mp3;

![](image-1-1024x238.png)

- Caso tenha dois canais no áudio, configure para mono;

![](image-2-1024x428.png)

- Exclua um dos canais, clicando no "X";
- Exporte o áudio para o formato .mat.

![](images/image-3.png)

![](images/image-4.png)

**Pronto! Agora você já pode utilizar o arquivo de áudio no Octave.**
