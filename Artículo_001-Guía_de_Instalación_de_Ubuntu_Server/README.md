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
* Red	NAT (por ahora)

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
