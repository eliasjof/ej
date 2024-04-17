---
title: "Solutions for Ubuntu problems"
date: "2022-10-17"
---

## Ubuntu 20.04

### \- Ruído/chiado de audio quando conectado com HDMI

Solução: [https://plus.diolinux.com.br/t/ubuntu-20-04-com-chiado-no-som/35371](https://plus.diolinux.com.br/t/ubuntu-20-04-com-chiado-no-som/35371)

Acrescente `tsched=0` no parâmetro `load-module module-udev-detect` em `/etc/pulse/default.pa`

> sudo gedit /etc/pulse/default.pa

`load-module module-udev-detect tsched=0`

Ctrl + O e depois Enter para salvar a alteração / Ctrl+X para sair do editor nano.

Reinicie o PulseAudio com os dois comandos abaixo:

> pulseaudio -k  
> pulseaudio --start

### \- Tela preta após instalar NVDIA + CUDA 11

Obs: Ao ligar o PC com o cabo hdmi conectado no monitor, não mostrava a tela inicial, ficava em tela preta. Ao ligar o PC sem o cabo hdmi, esperar a inicialização e só depois conectar o monitor, funcionava.

É necessário reinstalar os drivers da NVIDIA (não devem ser o último de 2022). Funcionou com essa versão:

```bash
nvidia-smi
nvcc --version
```

NVIDIA-SMI 510.47.03 Driver Version: 510.47.03 CUDA Version: 11.6

nvcc: NVIDIA (R) Cuda compiler driver  
Copyright (c) 2005-2022 NVIDIA Corporation  
Built on Tue\_Mar\_\_8\_18:18:20\_PST\_2022  
Cuda compilation tools, release 11.6, V11.6.124  
Build cuda\_11.6.r11.6/compiler.31057947\_0

### \- Instalando o Onedrive no Ubuntu 20.04

Primeiro instalar a versão mais recente do onedrive (github): [https://github.com/abraunegg/onedrive/blob/master/docs/ubuntu-package-install.md](https://github.com/abraunegg/onedrive/blob/master/docs/ubuntu-package-install.md)

- wget -qO - https://download.opensuse.org/repositories/home:/npreining:/debian-ubuntu-onedrive/xUbuntu\_20.04/Release.key | sudo apt-key add -

- echo 'deb https://download.opensuse.org/repositories/home:/npreining:/debian-ubuntu-onedrive/xUbuntu\_20.04/ ./' | sudo tee /etc/apt/sources.list.d/onedrive.list

- sudo apt-get update

- sudo apt install --no-install-recommends --no-install-suggests onedrive

Após a instalação do onedrive, realizar a instalação da interface gráfica: [https://github.com/bpozdena/OneDriveGUI](https://github.com/bpozdena/OneDriveGUI)

- https://github.com/bpozdena/OneDriveGUI/releases

- Configura a lista de pastas

### \- Error in NVIDIA drivers

$ nvidia-smi

Error: Failed to initialize NVML: Driver/library version mismatch

#### Solution

Reinstall all drivers:

- Remove old drivers
  ```bash
  sudo apt-get remove --purge nvidia-*
   ```
- Follow this steps: https://developer.nvidia.com/cuda-downloads
- Install driver (choose the correct version, e.g. 550 for cuda 12.4):
  ``` bash
  sudo apt-get install nvidia-driver-550
  ```
- Reboot
- Check
```bash
nvidia-smi
```
![[image-nvidia.png]]