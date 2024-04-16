---
title: "#IA03 - OpenAI Gym (parte 1)"
date: "2022-03-01"
categories: 
  - "inteligencia-artificial"
---

Vamos utilizar a plataforma OpenAI Gym para treinar e testar um sistema de controle baseado em Aprendizado por Reforço.

## Instalação

Se estiver utilizando no Windows, abra o prompt do Anaconda (administrador) e digite:

```python
pip install --upgrade gym

# No windows
pip install pygame --upgrade
# No linux
sudo apt-get install python3-pygame

# para salvar os vídeos das simulações:
#.mp4
conda install -c conda-forge ffmpeg 

```

![](gym-install-1024x452.png)

## Utilização básica

Algumas possibilidades de simulações disponibilizadas pela plataforma Gym, mais detalhes em: [https://gym.openai.com/](https://gym.openai.com/)

```
import gym
gym.envs.registry.all()
```

![](gym-simulations.png)

## Importações

```python
# Python ≥3.5 is required
import sys
assert sys.version_info >= (3, 5)
# Scikit-Learn ≥0.20 is required
import sklearn
assert sklearn.__version__ >= "0.20"

# TensorFlow ≥2.0 is required
import tensorflow as tf
from tensorflow import keras
assert tf.__version__ >= "2.0"

if not tf.config.list_physical_devices('GPU'):
    print("No GPU was detected. CNNs can be very slow without a GPU.")
    if IS_COLAB:
        print("Go to Runtime > Change runtime and select a GPU hardware accelerator.")
    if IS_KAGGLE:
        print("Go to Settings > Accelerator and select GPU.")

# Common imports
import numpy as np
import os

# to make this notebook's output stable across runs
np.random.seed(42)
tf.random.set_seed(42)

# To plot pretty figures
%matplotlib inline
import matplotlib as mpl
import matplotlib.pyplot as plt
mpl.rc('axes', labelsize=14)
mpl.rc('xtick', labelsize=12)
mpl.rc('ytick', labelsize=12)

# To get smooth animations
import matplotlib.animation as animation
mpl.rc('animation', html='jshtml')
from matplotlib.animation import PillowWriter

```

## Funções para visualizar e salvar (animações e figuras)

```python
def update_scene(num, frames, patch):
    patch.set_data(frames[num])
    return patch,

def plot_animation(frames, repeat=False, interval=40):
    fig = plt.figure()
    patch = plt.imshow(frames[0])
    plt.axis('off')
    anim = animation.FuncAnimation(
        fig, update_scene, fargs=(frames, patch),
        frames=len(frames), repeat=repeat, interval=interval)
    plt.close()
    return anim

def save_animation(anime, type='mp4', fps=1,path='videos/', name=None):
    if type in 'mp4':
        writer = animation.writers['ffmpeg']
        writer = writer(fps=fps, metadata=dict(artist='Me'), bitrate=1800)   
        anime.save(path + name +'.mp4', writer=writer)    
    elif type in 'gif':
        writer = PillowWriter(fps=fps)  
        anime.save(path + name +'.gif', writer=writer)  

TEST_ID = "rl"
IMAGES_PATH = os.path.join(PROJECT_ROOT_DIR, "images", TEST_ID )
os.makedirs(IMAGES_PATH, exist_ok=True)

def save_fig(fig_id, tight_layout=True, fig_extension="png", resolution=300):
    path = os.path.join(IMAGES_PATH, fig_id + "." + fig_extension)
    print("Saving figure", fig_id)
    if tight_layout:
        plt.tight_layout()
    plt.savefig(path, format=fig_extension, dpi=resolution)

def plot_environment(env, figsize=(5,4)):
    plt.figure(figsize=figsize)
    img = env.render(mode="rgb_array")
    plt.imshow(img)
    plt.axis("off")
    return img
```

Obs.: No windows instale a seguinte biblioteca:

```
conda install -c conda-forge ffmpeg
```

## Rodando um exemplo simples

Utilizamos nesse exemplo o problema Cart-pole (um carrinho com uma haste que deve se movimentar para manter a haste na vertical, a cada intervalo que a haste não caiu no chão, é fornecido pelo jogo uma recompensa de 1 ponto). Para garantir o sucesso ao longo de 200 intervalos, definimos uma política (algoritmo que o agente utiliza para maximizar as recompensas) para o agente: se o ângulo da haste é positiva, o carrinho deve deslocar para a direita (1), caso contrário, desloque para esquerda (0).

```python
env = gym.make('CartPole-v1')
env.seed(42)

def basic_policy(obs):
    angle = obs[2]
    return 0 if angle < 0 else 1

totals = []
for episode in range(500):
    episode_rewards = 0
    obs = env.reset()
    for step in range(200):
        action = basic_policy(obs)
        obs, reward, done, info = env.step(action)
        episode_rewards += reward
        if done:
            break
    totals.append(episode_rewards)

anime = plot_animation(frames)
save_animation(anime, type='gif', fps = 30, name="Cartpole-v1-p1")
anime
```

<figure>

![](Cartpole-v1-p1.gif)

<figcaption>

Resultado da política simples: 78/200 intervalos sem deixar a haste cair

</figcaption>

</figure>
