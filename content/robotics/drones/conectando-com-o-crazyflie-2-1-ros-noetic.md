---
title: Conectando com o CrazyFlie 2.1 - ROS Noetic
date: 2023-07-03
categories: []
---

# Montagem

# Configuração do cliente

```bash
export CSW_PYTHON=python3
sudo apt install -y ros-noetic-tf ros-noetic-tf-conversions ros-noetic-joy
sudo apt install -y libpcl-dev libusb-1.0-0-dev
sudo apt install -y swig lib${CSW_PYTHON}-dev ${CSW_PYTHON}-pip
${CSW_PYTHON} -m pip install pytest numpy PyYAML scipy
sudo apt install -y ffmpeg
${CSW_PYTHON} -m pip install ffmpeg-python
```
