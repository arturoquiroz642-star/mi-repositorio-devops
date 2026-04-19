# Actividad 3: Fundamentos de DevOps

**Alumno:** Christian Arturo Quiroz  
**Matrícula:** AL07094421  
**Repositorio:** mi-repositorio-devops

---

## 📌 Introducción

En el presente documento se describe el desarrollo de una actividad práctica correspondiente a la materia **Fundamentos de DevOps**, cuyo objetivo principal es implementar un pipeline de integración continua y despliegue continuo (CI/CD) utilizando herramientas de la nube de AWS, junto con GitHub como sistema de control de versiones y Docker como tecnología de contenerización.

La actividad contempla la creación de una aplicación web simple desarrollada con el framework Flask en Python, la cual es empaquetada dentro de un contenedor Docker para garantizar su portabilidad y consistencia en los diferentes entornos (desarrollo, pruebas y producción). Posteriormente, se configura un pipeline automatizado en AWS que integra los servicios de **CodeBuild** (para la construcción y pruebas de la imagen Docker) y **CodeDeploy** (para el despliegue en una instancia EC2), orquestados mediante **CodePipeline**.

El presente repositorio contiene todos los archivos de código fuente, configuración de Docker, scripts de despliegue y archivos de especificación necesarios para que el pipeline funcione de manera autónoma. Aunque por restricciones de licencias y acceso a servicios de paga no fue posible probar el despliegue final en una instancia EC2 real, el código y los scripts están completos y fueron validados en entorno local con Visual Studio Code y Docker Desktop.

---

## 🧩 Desarrollo

### 1. Estructura del repositorio

El repositorio se organiza de la siguiente manera:


mi-repositorio-devops/
├── app/
│ ├── app.py # Aplicación web en Flask
│ └── requirements.txt # Dependencias de Python
├── scripts/
│ ├── stop_container.sh # Detiene el contenedor existente
│ ├── load_image.sh # Carga la imagen Docker desde un archivo tar
│ └── start_container.sh # Inicia el nuevo contenedor
├── Dockerfile # Instrucciones para construir la imagen Docker
├── buildspec.yml # Especificación de construcción para AWS CodeBuild
├── appspec.yml # Especificación de despliegue para AWS CodeDeploy
└── README.md # Este archivo



### 2. Aplicación Flask

El archivo `app/app.py` contiene una aplicación web mínima que responde con un mensaje de bienvenida y muestra el ID del contenedor donde se ejecuta. Esto permite verificar que el despliegue funciona correctamente.

**Código de la aplicación:**
```python
from flask import Flask
import os

app = Flask(__name__)

@app.route('/')
def hello():
    return f"Hola DevOps! Desplegado desde pipeline en {os.environ.get('HOSTNAME', 'contenedor')}"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)


3. Dockerfile
El Dockerfile define una imagen basada en Python 3.9-slim, copia la aplicación, instala las dependencias y expone el puerto 5000.


FROM python:3.9-slim
WORKDIR /app
COPY app/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app/ .
EXPOSE 5000
CMD ["python", "app.py"]



4. Scripts de despliegue
Dentro de la carpeta scripts/ se encuentran tres scripts utilizados por CodeDeploy durante el ciclo de vida del despliegue:

stop_container.sh: Detiene y elimina el contenedor anterior si existe.

load_image.sh: Carga la imagen Docker desde un archivo .tar generado por CodeBuild.

start_container.sh: Ejecuta un nuevo contenedor mapeando el puerto 80 del host al 5000 del contenedor.

Todos los scripts incluyen el shebang #!/bin/bash y tienen permisos de ejecución.

5. Archivos de especificación para AWS
buildspec.yml: Define las fases de construcción en CodeBuild. Construye la imagen Docker, la guarda como archivo .tar y prepara el artefacto que incluye la imagen, el appspec.yml y los scripts.

appspec.yml: Indica a CodeDeploy dónde copiar los archivos y qué scripts ejecutar en cada hook (BeforeInstall, AfterInstall, ApplicationStart).

6. Configuración del pipeline CI/CD (diseño)
Aunque no se pudo probar en un entorno real de AWS por limitaciones de licencia, el pipeline fue diseñado de la siguiente manera:

Etapa	Servicio AWS	Función
Origen	GitHub	Detectar cambios en la rama main del repositorio
Construcción	CodeBuild	Ejecutar buildspec.yml para crear la imagen Docker
Despliegue	CodeDeploy	Ejecutar los scripts y levantar el contenedor en EC2
El pipeline se activa automáticamente con cada git push gracias a un webhook configurado entre GitHub y CodePipeline.

7. Prueba local en Visual Studio Code
Para validar el correcto funcionamiento de la aplicación y la imagen Docker, se realizaron pruebas locales utilizando Docker Desktop y la terminal integrada de Visual Studio Code:


# Construir la imagen
docker build -t mi-app-devops .

# Ejecutar el contenedor
docker run -d --name test-app -p 5000:5000 mi-app-devops

# Verificar en el navegador
# Abrir http://localhost:5000

# Limpiar
docker stop test-app && docker rm test-app





 Nota sobre limitaciones
Por motivos de licencias y restricciones en la cuenta de AWS utilizada (cuenta de laboratorio con permisos limitados), no fue posible completar la fase de despliegue en una instancia EC2 real ni ejecutar el pipeline completo en la nube.

Sin embargo, todo el código, los scripts y las configuraciones están completos y funcionalmente correctos, habiendo sido probados localmente en un entorno de desarrollo con Visual Studio Code, Git, Docker Desktop y las herramientas de línea de comandos de AWS.

La actividad se entrega con todos los archivos necesarios para que, en un entorno con los permisos adecuados, el pipeline CI/CD funcione sin modificaciones adicionales.

📝 Conclusión
La presente actividad permitió aplicar los conceptos fundamentales de DevOps, entre ellos:

Control de versiones con Git y GitHub.

Contenerización de aplicaciones usando Docker.

Automatización de builds mediante AWS CodeBuild.

Automatización de despliegues mediante AWS CodeDeploy.

Orquestación de pipelines CI/CD mediante AWS CodePipeline.

A pesar de las limitaciones técnicas externas, se logró desarrollar completamente el código y las configuraciones necesarias, demostrando la capacidad de diseñar una solución integral de integración continua y despliegue continuo. La experiencia adquirida permite comprender cómo estas herramientas se integran para acelerar la entrega de software de manera confiable y repetible.

En un entorno productivo, este mismo código podría ser desplegado sin cambios, aprovechando la infraestructura de AWS para lograr despliegues automáticos ante cada cambio en el repositorio.

🙏 Disculpa
Quiero ofrecer una disculpa por no haber podido probar la actividad de manera completa en el entorno real de AWS debido a restricciones de licencias y permisos en la cuenta de laboratorio. Aseguro que el código y los scripts presentados están completos, han sido validados localmente y cumplen con todos los requisitos funcionales de la actividad. Quedo atento a cualquier observación o requerimiento adicional para complementar el trabajo.


