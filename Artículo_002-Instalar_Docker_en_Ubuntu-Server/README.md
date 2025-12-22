# Copiar y pegar desde Ubuntu Server

## 1️⃣ Instalar el servidor SSH en Ubuntu

Abre la terminal en tu Ubuntu Server y ejecuta:
```
sudo apt update
sudo apt install openssh-server -y
```

Verifica que esté funcionando:
```
sudo systemctl enable ssh      # Para que arranque al iniciar
sudo systemctl start ssh       # Para iniciar el servicio ahora
sudo systemctl status ssh      # Para comprobar que está activo
```

Deberías ver algo como:

* Active: active (running)

## 2️⃣ Comprobar la IP de tu Ubuntu

Necesitamos la IP para conectarnos desde Windows:
```
ip a
```

Busca la interfaz que tenga inet.


* Algo como: 192.168.0.101.

Esa será la IP que usarás para conectarte desde Windows.

## 3️⃣ Conectarse desde Windows con SSH (para copiar/pegar)
Opción A: Usar PowerShell (nativo en Windows 11)

Abre PowerShell.

Ejecuta:
```
ssh tu_usuario@IP_DE_UBUNTU
```

Sustituye tu_usuario por tu usuario de Ubuntu.

Sustituye IP_DE_UBUNTU por la IP que viste antes (192.168.x.x).

La primera vez te pedirá confirmar la clave, escribe yes.

Luego te pedirá la contraseña del usuario.

💡 Una vez dentro, ya puedes copiar y pegar texto desde PowerShell hacia Ubuntu usando Ctrl+C/Ctrl+V (el comportamiento de la terminal de Windows es el estándar de SSH).
