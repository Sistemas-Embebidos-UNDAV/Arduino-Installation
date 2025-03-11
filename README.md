# Instalación de Arduino en Ubuntu

## Instalación con `apt`
```bash
sudo apt update
sudo apt install arduino
```

## Instalación con `flatpak`
```bash
sudo apt install flatpak
sudo apt install gnome-software-plugin-flatpak
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
flatpak install flathub cc.arduino.IDE2
```

## Instalación con `snap`
```bash
sudo snap install arduino
```

## Agregar placas ESP32 y ESP8266
Para agregar compatibilidad con placas ESP32 y ESP8266, abre Arduino y en **Preferencias** agrega las siguientes URLs en **Gestor de URLs Adicionales de Tarjetas**:
```
https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json, http://arduino.esp8266.com/stable/package_esp8266com_index.json
```
Luego, instala las placas desde el **Gestor de Tarjetas** en el menú de Arduino.

## Agregar usuario al grupo `dialout`
Para permitir la comunicación con placas Arduino sin necesidad de `sudo`, agrega tu usuario al grupo `dialout`:
```bash
sudo usermod -a -G dialout $USER
newgrp dialout
```

## Agregar permisos para placas Arduino
Crea una regla de `udev` para permitir el acceso a placas Arduino sin permisos de root:
```bash
sudo nano /etc/udev/rules.d/99-arduino.rules
```
Luego agrega la siguiente línea:
```
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2341", GROUP="plugdev", MODE="0666"
```
Guarda el archivo (`Ctrl + X`, luego `Y` y `Enter`) y recarga las reglas:
```bash
sudo udevadm control --reload-rules
```

## Agregar permisos para ESP32
### 1. **Verificar el `idVendor` y `idProduct` del ESP32**
Ejecuta el siguiente comando para identificar el chip que usa tu ESP32:
```bash
lsusb
```
Busca una línea que mencione **Silicon Labs CP210x** o **QinHeng Electronics CH340**, por ejemplo:

- **CP210x (Silicon Labs)** → `ID 10c4:ea60`
- **CH340 (WCH)** → `ID 1a86:7523`

### 2. **Crear una nueva regla de `udev`**
Si tu ESP32 usa **CP210x** (`10c4:ea60`), crea una nueva regla:
```bash
sudo nano /etc/udev/rules.d/99-esp32.rules
```
Luego agrega la línea correspondiente:

- **Para CP210x (Silicon Labs)**
  ```
  SUBSYSTEM=="tty", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", GROUP="dialout", MODE="0666"
  ```
- **Para CH340 (WCH)**
  ```
  SUBSYSTEM=="tty", ATTRS{idVendor}=="1a86", ATTRS{idProduct}=="7523", GROUP="dialout", MODE="0666"
  ```

Guarda el archivo (`Ctrl + X`, luego `Y` y `Enter`) y recarga las reglas:
```bash
sudo udevadm control --reload-rules
