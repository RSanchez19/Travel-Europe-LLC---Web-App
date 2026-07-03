# Capstone group-3

# Travel Europe LLC - Web App (Capstone Project)

Documentación correspondiente al Capstone Project Group 3. 

El objetivo de este proyecto fue tomar una aplicación web existente, contenerizarla y publicarla en internet a través de la infraestructura de GCP.

Repositorio Original del Proyecto:
[https://github.com/RSanchez19/Travel-Europe-LLC---Web-App](https://github.com/RSanchez19/Travel-Europe-LLC---Web-App)

---

## Sprints

###  Sprint 1: Contenerización y Artifact Registry

Se implementó una web app desde un repositorio de Github. Para poder crear la imagen contenerizada se clonó el repositorio en Cloudshell de Google Cloud y se creó un `dockerfile` utilizando python:3.8-slim, así mismo, se instalan las librerías y dependencias necesarias de python para poder servir la página web, se utilizó Flask por lo que se expuso el puerto 5000 para poder acceder a la página.

La imagen de Docker travel-europe-py se construyó y se probó de manera exitosa accediendo desde localhost:5000

Finalmente, se creó la tag y se hizo push a un repositorio privado de Google dentro de Artifact Registry en la región us-central1.

###  Sprint 2: Despliegue Público en la Web
 
Para exponer la página, usamos dos opciones:

#### 1: Google Compute Engine (GCE) + Load Balancer
Se desplegó el contenedor en una MIG de GCE, que se compone de 3 VM’s siguiendo un template para la creación de cada una de estas.  

Luego configuró un Load Balancer HTTP Global (Capa 7) conectado a un grupo de instancias administrado, se crearon las reglas de firewall para que el acceso a las VM’s sea limitado permitiendo unicamente los chequeos de salud de Google 130.211.0.0/22, 35.191.0.0/16 y que a pesar de tener IP Externa estas no son accesibles.

Endpoint Externo: La página se hizo pública a través de la IP generada por el Load Balancer http://8.232.126.132:80.

#### 2: Google Kubernetes Engine (GKE)
Se creó un clúster de GKE en modo Autopilot travel-europe-cluster, y se realizó un despliegue travel-europe-llc-deploy extrayendo la imagen directamente alojada en Artifact Registry.

El servicio fue expuesto al exterior configurando un servicio de tipo LoadBalancer en el puerto 5000 para poder acceder a la pagina web y recibir el tráfico desde el puerto 80.

Endpoint Externo (GKE): Se asignó una IP pública 34.63.166.5:80 con un target port de 5000 debido a que es el puerto donde el container escucha haciendo posible el acceso a la web de manera pública.

Y se configuró una regla de firewall en la red personalizada para permitir el tráfico de ingreso externo 0.0.0.0/0 hacia el puerto 80 del clúster.

### 3: Cloud Run

Para poder acceder a la página web desde el servicio de Cloud Run, se necesita deployar un contenedor ya existente. En este caso desde el repositorio ya creado de Artifact Registry. Se seleccionó la imagen del container y al igual que en los otros servicios se necesita configurar el puerto 5000 para poder acceder de manera externa. Para poder acceder ocupamos la siguiente URL: https://travel-europe-py-730535479509.us-central1.run.app


---

## Utilidades
* **Contenedores:** Docker
* **Servidor Web:** Python-flask
* **Almacenamiento de Imágenes:** Google Artifact Registry
* **Cómputo:** Google Compute Engine (GCE), Google Kubernetes Engine (GKE Autopilot)
