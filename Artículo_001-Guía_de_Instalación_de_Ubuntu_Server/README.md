# Está es una guía de instalación de Ubuntu Server 

👉 [Descargar Ubuntu Server 24.04.5 LTS](https://ubuntu.com/download/alternative-downloads)

## 🧩 Cómo configurar la máquina virtual (VirtualBox)
Parámetros recomendados:

* Opción	Valor
* Nombre	n8n-server
* Tipo	Linux
* Versión	Ubuntu (64-bit)
* RAM	2 GB (4 GB ideal)
* CPU	2
* Disco	30 GB (VDI dinámico)
* Red	NAT (por ahora) / una vez iniciada la instalación pasar a "Adaptador Puente"

<img width="609" height="346" alt="Captura de pantalla 2025-12-22 232011" src="https://github.com/user-attachments/assets/1c5c6093-72b6-47a9-8742-878cedca425f" />
<img width="605" height="230" alt="Captura de pantalla 2025-12-22 232032" src="https://github.com/user-attachments/assets/c5766de9-ae71-499a-8529-31160c40958f" />
<img width="605" height="265" alt="Captura de pantalla 2025-12-22 232044" src="https://github.com/user-attachments/assets/c3163e74-2b4c-4d46-93ba-3e81ca1ecd63" />



## 🚀 Primer Arranque

``` bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl ca-certificates gnupg lsb-release
```


## ⌨️ Configurar Teclado en Español

1️⃣ Comprobar la distribución actual

En la terminal, escribe:
``` bash
localectl status
```

Esto te mostrará algo como:
``` bash
System Locale: LANG=en_US.UTF-8
VC Keymap: us
X11 Layout: us
```

Si ves us en VC Keymap o X11 Layout, significa que tu teclado está en inglés.

2️⃣ Cambiar la distribución del teclado

Ejecuta:
``` bash
sudo dpkg-reconfigure keyboard-configuration
```

Se abrirá un asistente de configuración en modo texto.

Pasos típicos dentro del asistente:

* Selecciona el tipo de teclado → normalmente: Generic 105-key (Intl) PC
* Distribución del teclado → Spanish
* AltGr key → Por defecto (Right Alt)
* Compose key → Ninguna (o la que prefieras)
* Ctrl+Alt+Backspace → No (a menos que quieras reiniciar el servidor gráfico con esto)

3️⃣ Aplicar cambios

Después de terminar el asistente, ejecuta:
``` bash
sudo setupcon
```

Y luego, reinicia la sesión:
``` bash
exit
```

y vuelve a entrar, o reinicia la máquina virtual:
``` bash
sudo reboot
```

4️⃣ Verificar que el teclado está en español

En la terminal, prueba caracteres especiales de tu teclado español:
``` bash
!
@
#
€
ñ
```

## 🤖 Crear Usuario

1️⃣ Crear usuario (si no lo creaste en la instalación)

``` bash
adduser firox
```

Sigue el asistente (contraseña, nombre, etc.).

2️⃣ Dar permisos de sudo al usuario

``` bash
usermod -aG sudo firox
```

3️⃣ Salir de root e entrar con el usuario

``` bash
exit
```

Luego entra con:
``` bash
login: firox
password: ********
```

<img width="798" height="192" alt="Captura de pantalla 2025-12-22 231947" src="https://github.com/user-attachments/assets/b278931c-db55-49c3-9cb9-31177e03a4c4" />
