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
* Ahora puedes abrir en tu navegador de Windows:

```
http://IP_DE_UBUNTU:5678
```

* Loguearte con usuario `admin` y la contraseña que pusiste.

---

Si quieres, puedo hacerte una **versión mejorada del `docker-compose.yml` para producción**, con **persistencia de datos**, que asegura que tus flujos no se pierdan al reiniciar el contenedor.

¿Quieres que haga eso?
