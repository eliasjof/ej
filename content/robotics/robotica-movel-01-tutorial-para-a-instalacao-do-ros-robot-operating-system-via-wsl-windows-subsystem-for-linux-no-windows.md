---
title: "Tutorial para a Instalação do ROS (Robot Operating System) via WSL (Windows Subsystem for Linux) no Windows"
date: "2021-12-28"
categories: 
  - "robotica"
---

\[embed\]https://youtu.be/R1Zbo1aFk7M\[/embed\]

## 1 - Instalação o WSL 2

### 1.1 - Verificação da versão do Windows

```
options(Encoding="UTF-8")
```

Você deve estar executando o Windows 10 versão 2004 e superior (Build 19041 e superior) ou o Windows 11. Verifique sua versão:

```

Windows + R, digite winver e selecione OK

```

### 1.2 - Instalando o WSL

No PowerShell **administrador** :

```

wsl --install

```

Após a instalação reincie o computador.

#### 1.2.1 - Instalando o Ubuntu

A instalação do Ubuntu irá iniciar após o computador ser reiniciado (em torno de 5 minutos).

Insira um novo usuário e senha para o Ubuntu.

Após a instalação, verifique a versão instalada no terminal do linux:

```

lsb_release -a

```

Obs.: Versão desejada 20.04.

#### 1.2.2 - Verificando a Versão do WSL

Verifique a versão do WSL no PowerShell **administrador** :

```

wsl -l -v

```

Obs.: Versão desejada WSL 2.

### 1.3 - Configurações Extras

### 1.3.1 - X-Server

- Instale no Ubuntu (via terminal) alguns programas que permitirão a conexão remota:

```
sudo apt install build-essential

sudo apt install net-tools

sudo apt install xrdp -y && sudo systemctl enable xrdp
```

- Instale uma interface para o Ubuntu. Foi escolhida a xfce4, que é leve:

```

sudo apt install -y tasksel

sudo tasksel install xubuntu-desktop

sudo apt install gtk2-engines
```

_Obs.: Irá demorar uns 10 minutos para finalizar essa instalação._ _Caso deseje instalar o GNOME utilize o comando (Não recomendado):_

`sudo tasksel`

Selecione Ubuntu minimal desktop

- Configure as variáveis do sistema, adicionando as seguintes linhas no arquivo ~/.bashrc

`vi ~/.bashrc`

`export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2; exit;}'):0.0`

`export LIBGL_ALWAYS_INDIRECT=0   `

`sudo /etc/init.d/dbus start &> /dev/null`

_Obs.: Para adicionar uma linha utilizando o programa VI: aperte i para inserir e Esc+wq+Enter para salvar e fechar o arquivo. Modificado para funcionar com o ros-RVIZ._

Após salvar o arquivo, digite no terminal:

`source ~/.bashrc`

- Instale o programa **VcXsrv** para acrescentar um GUI de visualização para o Linux no Windows:

\[[https://sourceforge.net/projects/vcxsrv/](https://sourceforge.net/projects/vcxsrv/)\]

- No terminal rode o seguinte comando para iniciar a interface gráfica:

`xfce4-session`

Obs.: Se for o GNOME, utilize

`gnome-session`

### 1.3.2 - Acessando as pastas do Windows no Linux WSL

Todos os arquivos do computador estarão mapeados na pasta /mnt. Caso deseje acessar via terminal poderá ser feito por:

```
 cd /mnt/
```

### 1.3.3 - Acessando as pastas do Linux WSL no Windows

Caso deseje acessar os arquivos do Linux, utilize o Navegador de arquivos do Windows e digite na barra do explorer: s

```
 \\wsl$\Ubuntu\
```

## 2 - Instalação do ROS

A instalação do ROS Noetic pode ser feita seguindo os passos diretamente do site \[[http://wiki.ros.org/noetic/Installation/Ubuntu](http://wiki.ros.org/noetic/Installation/Ubuntu)\]. Em síntese, seguem os comandos necessários a serem digitados no terminal do Ubuntu 20.04:

```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
 
sudo apt install curl # if you haven't already installed curl

curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -

sudo apt update

sudo apt install ros-noetic-desktop-full

source /opt/ros/noetic/setup.bash

echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc

source ~/.bashrc

sudo apt install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential

sudo apt install python3-rosdep

sudo rosdep init

rosdep update
```

_Obs.: Irá demorar uns 10 a 15 minutos para finalizar essa instalação._

### 2.1 - Verificando o funcionamento

Rode no terminal o seguinte comando, caso não apareça nenhum erro o ROS foi instalado com sucesso.

```
roscore
```

### 3 - Possíveis erros e soluções

### 3.1 - Reconhecimento do python de uma determinada versão

**Opção 1**

- If Python 3 has been installed, run these commands: `whereis python3`
    
- Then we create a symlink to it: `sudo ln -s /usr/bin/python3 /usr/bin/python`
    

**Opção 2:**

`sudo apt-get install python3.9`

Define o comando python como sendo o python3.9

sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.9 1

### 3.2 Instalação via pip

Em casos de não conseguir instalar aplicações com o pip, tente essa solução:

[https://askubuntu.com/questions/1254309/not-installing-pip-on-ubuntu-20-04](https://askubuntu.com/questions/1254309/not-installing-pip-on-ubuntu-20-04)

### 3.3 Erro no ubuntu

Se precisar reinstalar ou reparar o Ubuntu, pode-se realizar esses procedimentos:

![](Captura-de-tela-2022-01-21-194827-300x164.png)

### 3.4 Erro no GNOME

Tente essa solução:

[https://gist.github.com/Ta180m/e1471413f62e3ed94e72001d42e77e22](https://gist.github.com/Ta180m/e1471413f62e3ed94e72001d42e77e22)

3.5 Erro
