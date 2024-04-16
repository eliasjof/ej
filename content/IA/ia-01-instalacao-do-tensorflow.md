---
title: "#IA01 - Instalação do TensorFlow 2.0"
date: "2021-12-29"
categories: 
  - "inteligencia-artificial"
---

<figure>

![](image-3%201.png)

<figcaption>

Wikipedia

</figcaption>

</figure>

## 1 - Instale o Anaconda (Windows)

- Versão mais nova + python 3.9:

[https://www.anaconda.com/products/individual#windows](https://www.anaconda.com/products/individual#windows)

Obs.:

- Durante a instalação marque essas opções:

![](image-4%201.png)

![](image-6%201.png)

- Irá ocupar entre 8 a 10GB do disco. Por garantia, reinicie o computador após a instalação.

## 2.1 - Pelo terminal (Windows ou Linux)

**Opção mais simples:**

No terminal digite (se for no Windows utilize o prompt do Anaconda ou do Visual Code):

```
pip install tensorflow
```

## 2.2 - Opção utilizando o Anaconda Navigator (Windows)

- Abrir o Anaconda Navigator

![](image%201.png)

- Ir na guia Enviroments -> Not Installed->buscar por tensor
    - Marcar as opções tensorboard, tensorflow e tensorflow-datasets (opcional Keras e pyTorch)

![](image-1-1024x546.png)

- Clique em **Apply**.

Obs.: Pode ser que o Navegador não encontre as bibliotecas.

![](image-2%201.png)

Se isso acontecer, atualize o Anaconda pelo prompt:

```
conda update conda
```
