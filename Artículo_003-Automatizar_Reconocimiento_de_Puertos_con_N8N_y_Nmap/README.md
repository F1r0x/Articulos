
# Automatizando escaneos de puertos con Nmap usando n8n (100% gratuito)

<img width="972" height="366" alt="Captura de pantalla 2025-12-23 095945" src="https://github.com/user-attachments/assets/4a00896c-f002-4f98-a414-24e3c618888a" />

## Introducción

En este artículo voy a mostrar cómo he construido un **flujo de automatización de pentesting** utilizando **n8n** y **Nmap**, ejecutándose en un entorno completamente **gratuito**, controlado y reproducible.

El objetivo no es solo realizar un escaneo de puertos, sino demostrar cómo **n8n puede convertirse en un orquestador de tareas de reconocimiento**, permitiendo:

* Ejecutar herramientas de pentesting de forma automática
* Filtrar y procesar resultados
* Enriquecer la información
* Generar informes listos para usar
* Escalar el flujo con nuevos escaneos y lógica personalizada

Este flujo es una **prueba sencilla**, pero sienta las bases para automatizaciones mucho más avanzadas.

---

## Arquitectura del entorno

Antes de entrar en el flujo, es importante entender **dónde y por qué** está montado todo.

### Infraestructura utilizada

La arquitectura es la siguiente:

1. **VirtualBox**

   * Utilizado para crear una máquina virtual aislada.
2. **Ubuntu Server**

   * Sistema operativo de la máquina virtual.
3. **Docker**

   * Instalado sobre Ubuntu Server.
4. **n8n en Docker**

   * n8n se ejecuta dentro de un contenedor Docker.

### ¿Por qué esta arquitectura?

Esta combinación nos permite:

* Usar **n8n de forma completamente gratuita**
* Tener **control total del sistema**
* Instalar herramientas de pentesting sin limitaciones
* Evitar costes de cloud
* Trabajar en un entorno **seguro y aislado**

Además, el **Ubuntu Server actúa como nuestro “pentesting box”**, donde instalamos herramientas como:

* Nmap
* (Futuro) Nikto, Gobuster, Amass, WhatWeb, etc.

---

## Conexión por SSH y ejecución remota

n8n **no ejecuta Nmap directamente**, sino que:

* Se conecta por **SSH** al Ubuntu Server
* Ejecuta los comandos de pentesting allí
* Recibe la salida (`stdout`)
* La procesa dentro del flujo

Esto es clave porque convierte a n8n en un **cerebro de automatización**, mientras que el servidor es el **ejecutor de herramientas**.

---

## Descripción general del flujo

El flujo completo realiza lo siguiente:

1. Lanza un escaneo inicial con Nmap
2. Extrae IP y puertos abiertos
3. Realiza un segundo escaneo más profundo solo sobre esos puertos
4. Analiza servicios, versiones y detecciones específicas
5. Genera un informe HTML automático

Visualmente, el flujo sigue esta lógica:

```
Iniciar
  ↓
Nmap (escaneo inicial)
  ↓
Filtrar Puertos
  ↓
Nmap (escaneo detallado)
  ↓
Filtrar Escaneo
  ↓
Crear Informe HTML
```

---

## Paso 1 – Disparador manual

### Nodo: **Iniciar**

El flujo comienza con un **Manual Trigger**, ideal para pruebas y desarrollo.

Este nodo permite lanzar el flujo bajo demanda, aunque en el futuro podría sustituirse por:

* Webhooks
* Cron jobs
* Eventos externos
* Integración con otros sistemas

---

## Paso 2 – Escaneo inicial de puertos

### Nodo: **Nmap**

Aquí se ejecuta el primer comando:

```bash
nmap -sV -Pn 192.168.68.53
```

Este escaneo tiene como objetivo:

* Detectar puertos abiertos
* Identificar servicios básicos
* Obtener una primera visión del objetivo

<img width="751" height="330" alt="Captura de pantalla 2025-12-23 101254" src="https://github.com/user-attachments/assets/44086ccb-49b7-4664-8414-0bc99a76d658" />


Es un escaneo **rápido y general**, pensado para alimentar los siguientes pasos.

---

## Paso 3 – Filtrado de puertos abiertos

### Nodo: **Filtrar Puertos**

Este nodo usa JavaScript para procesar la salida de Nmap (`stdout`).

``` js
const output = $json.stdout;

// Extraer IP
const ipMatch = output.match(/\d{1,3}(\.\d{1,3}){3}/);
const ip_address = ipMatch ? ipMatch[0] : "Unknown";

// Extraer puertos abiertos
const ports = [...output.matchAll(/(\d{1,5})\/tcp\s+open/g)]
  .map(match => match[1]);

return [{
  ip_address,
  open_ports: ports,
  ports_string: ports.join(','),
  port_count: ports.length
}];
```

#### Qué hace exactamente

* Extrae la IP objetivo
* Detecta todos los puertos TCP abiertos
* Genera:

  * Lista de puertos
  * Cadena de puertos separada por comas
  * Número total de puertos abiertos

Ejemplo de salida estructurada:

```json
{
  "ip_address": "192.168.88.33",
  "open_ports": ["22", "80", "443"],
  "ports_string": "22,80,443",
  "port_count": 3
}
```

<img width="557" height="343" alt="Captura de pantalla 2025-12-23 100659" src="https://github.com/user-attachments/assets/9cb74699-d146-486e-85cf-bfc1748d90a2" />

Esta información es **clave**, ya que permite que el siguiente escaneo sea **mucho más preciso**.

---

## Paso 4 – Escaneo detallado de puertos definidos

### Nodo: **Nmap1**

Usando los puertos detectados previamente, se lanza un segundo escaneo más agresivo:

```bash
nmap -sC -sV -n -v -p <puertos> <ip>
```

Este escaneo:

* Ejecuta scripts por defecto (`-sC`)
* Detecta versiones exactas
* Reduce ruido
* Acelera el proceso
* Mejora la calidad del reconocimiento

<img width="749" height="329" alt="Captura de pantalla 2025-12-23 101331" src="https://github.com/user-attachments/assets/eeef724b-49f8-4656-8082-396a9fff4d97" />


Aquí es donde empieza el **pentesting real**.

---

## Paso 5 – Análisis avanzado del escaneo

### Nodo: **Filtrar Escaneo**

Este nodo transforma la salida cruda en **información estructurada**.

``` js
const output = $json.stdout;

// IP
const ip = output.match(/\d{1,3}(\.\d{1,3}){3}/)?.[0] ?? "N/A";

// Puertos
const ports = [...output.matchAll(/(\d{1,5})\/tcp\s+open\s+([\w\-\?]+)\s*(.*)?/g)]
  .map(p => ({
    port: p[1],
    service: p[2],
    version: p[3]?.trim() || "Unknown"
  }));

// n8n detectado
const n8nDetected = output.includes("n8n@");
const n8nVersion = output.match(/n8n@([\d\.]+)/)?.[1] ?? "Unknown";

return [{
  ip,
  ports,
  n8nDetected,
  n8nVersion,
  date: new Date().toLocaleString()
}];

```

<img width="260" height="209" alt="Captura de pantalla 2025-12-23 100743" src="https://github.com/user-attachments/assets/187ada17-4ac6-4eef-b965-fac1a4c65666" />


#### Funcionalidades clave

* Extrae:

  * IP
  * Puertos
  * Servicio
  * Versión detectada
* Detecta si el servicio **n8n** está expuesto
* Extrae la versión de n8n si existe
* Añade fecha y hora del escaneo

Ejemplo de estructura:

```json
{
  "ip": "192.168.88.33",
  "ports": [
    {
      "port": "5678",
      "service": "http",
      "version": "n8n@1.0.0"
    }
  ],
  "n8nDetected": true,
  "n8nVersion": "1.0.0"
}
```

Este enfoque permite **añadir detecciones personalizadas** para cualquier tecnología.

---

## Paso 6 – Generación automática de informe HTML

### Nodo: **Crear Informe HTML**

El último nodo genera un **informe HTML completo**, con:

* Diseño oscuro moderno
* Tabla de puertos abiertos
* Información del objetivo
* Fecha del escaneo
* Detecciones especiales (como n8n)

El resultado es un **reporte listo para entregar**, guardar o enviar automáticamente.

Este HTML puede:

* Guardarse en disco
* Enviarse por email
* Subirse a un servidor
* Integrarse con herramientas externas

---

## ¿Por qué usar n8n para pentesting?

Este ejemplo demuestra que n8n puede usarse como:

* Orquestador de herramientas
* Motor de lógica
* Normalizador de datos
* Generador de informes
* Plataforma de automatización ofensiva

Y todo ello:

✅ Gratis
✅ Autohospedado
✅ Extensible
✅ Controlado

---

## Conclusión

Este flujo es solo una **prueba simple**, pero muestra el enorme potencial de combinar:

* n8n
* Docker
* Ubuntu Server
* Herramientas de pentesting

A partir de aquí se pueden añadir:

* Nuevos escaneos
* Detección de vulnerabilidades
* Enumeración avanzada
* Integración con SIEM
* Dashboards
* Alertas automáticas

En definitiva, **n8n puede convertirse en una plataforma de automatización de pentesting completamente personalizada**.

