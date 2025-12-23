Vamos a instalar **Docker** en un Ubuntu Server y después configuraremos **n8n**. 

---

## **1️⃣ Actualizar Ubuntu**

Antes de nada, asegurémonos de que tu sistema esté actualizado:

```bash
sudo apt update
sudo apt upgrade -y
```

---

## **2️⃣ Instalar dependencias necesarias para Docker**

```bash
sudo apt install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release -y
```

---

## **3️⃣ Añadir el repositorio oficial de Docker**

1. Añadimos la clave GPG:

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

2. Añadimos el repositorio:

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

---

## **4️⃣ Instalar Docker Engine**

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

---

## **5️⃣ Verificar la instalación**

```bash
docker --version
docker compose version
```

* Deberías ver algo como `Docker version 24.x.x` y `Docker Compose version 2.x.x`.


### Añadir tu usuario al grupo docker

Ejecuta:
```
sudo usermod -aG docker $USER
```

Esto añade tu usuario actual al grupo docker, que tiene permisos sobre /var/run/docker.sock.

### Cerrar sesión y volver a entrar

Esto es obligatorio para que los cambios de grupo tengan efecto.

Cierra la sesión de la VM y vuelve a entrar, o ejecuta:
```
newgrp docker
```

Esto aplica los cambios de grupo sin necesidad de reiniciar sesión.

---

## **6️⃣ Ejecutar Docker sin sudo (opcional, recomendado)**

```bash
sudo usermod -aG docker $USER
```

* Cierra sesión y vuelve a entrar en la VM para que los cambios de grupo tengan efecto.
* Ahora puedes ejecutar `docker` y `docker compose` sin `sudo`.

---

## **7️⃣ Instalar n8n con Docker Compose**

1. Crear un directorio para n8n:

```bash
mkdir ~/n8n
cd ~/n8n
```

2. Crear un archivo `docker-compose.yml`:

```yaml
version: '3.1'

services:
  n8n:
    image: n8nio/n8n
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=tu_contraseña_segura
      - N8N_SECURE_COOKIE=false  
```

> Cambia `tu_contraseña_segura` por la contraseña que quieras.

3. Iniciar n8n:

```bash
docker compose up -d
```

4. Verificar que esté corriendo:

```bash
docker ps
```

* Deberías ver el contenedor `n8n` activo.


<img width="947" height="165" alt="Captura de pantalla 2025-12-23 011036" src="https://github.com/user-attachments/assets/a004538c-d54e-445a-be90-f79a1c2af8ee" />


* Ahora puedes abrir en tu navegador de Windows:

```
http://IP_DE_UBUNTU:5678
```

* Loguearte con usuario `admin` y la contraseña que pusiste.

---


<img width="564" height="531" alt="Captura de pantalla 2025-12-23 012028" src="https://github.com/user-attachments/assets/23d91863-82cd-47aa-8ac7-674b3b381849" />

