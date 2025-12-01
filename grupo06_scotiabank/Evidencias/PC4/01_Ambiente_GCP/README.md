# 1. Creación del Proyecto

Se creó un proyecto independiente en Google Cloud Platform para aislar todos los recursos de la práctica, evitando contaminaciones con otros proyectos personales o institucionales.

## 🛂 2. Gestión de Identidades y Accesos (IAM)

Se aplicó el principio de mínimo privilegio, asignando permisos únicamente según las funciones técnicas del flujo de datos. Cada rol fue otorgado vía CLI usando gcloud, garantizando trazabilidad.

![Ingresar_IAM](Evidencias/2_Input_IAM.png)

### ✔️ Roles técnicos generales del proyecto

```json
gcloud projects add-iam-policy-binding grupo6-scotiabank \
  --member="serviceAccount:sbs-scraper-sa@grupo6-scotiabank.iam.gserviceaccount.com" \
  --role="roles/storage.objectCreator"

gcloud projects add-iam-policy-binding grupo6-scotiabank \
  --member="serviceAccount:75587073872-compute@developer.gserviceaccount.com" \
  --role="roles/artifactregistry.reader"

gcloud projects add-iam-policy-binding grupo6-scotiabank \
  --member="serviceAccount:service-75587073872@gcp-sa-eventarc.iam.gserviceaccount.com" \
  --role="roles/storage.objectViewer"

``` 
Servicio / Cuenta	Función

- storage.objectCreator:Permite carga de archivos desde scraping hacia el Data Lake

- artifactregistry.reader: Acceso a imágenes necesarias para servicios compute
storage.objectViewer	Permite lectura de objetos para flujos event-driven
## 👥 3. Roles asignados según función en el pipeline

Asignar roles a los usuarios a travez de la linea de comandos CLI de Google Cloud Plataform

- **Cambiar en Usuario1** : PONER@USUARIO1 -> por el usuario 1 admitido
- **Cambiar en Usuario2** : PONER@USUARIO2 -> por el usuario 2 admitido

### 🔸 Rol 1 – Scraping y carga de datos al Data Lake

Responsabilidades:

- Obtención de archivos Excel desde la web del SBS

- Ejecución periódica del scraping

- Carga automatizada de los archivos raw a Cloud Storage

**Permisos otorgados para su actividad:**


```bash
gcloud projects add-iam-policy-binding grupo6-scotiabank --member="user:PONER@USUARIO1" --role="roles/cloudfunctions.developer" && \
gcloud projects add-iam-policy-binding grupo6-scotiabank --member="user:PONER@USUARIO1" --role="roles/storage.admin" && \
gcloud projects add-iam-policy-binding grupo6-scotiabank --member="user:PONER@USUARIO1" --role="roles/cloudscheduler.admin" && \
gcloud projects add-iam-policy-binding grupo6-scotiabank --member="user:PONER@USUARIO1" --role="roles/iam.serviceAccountUser" && \
gcloud projects add-iam-policy-binding grupo6-scotiabank --member="user:PONER@USUARIO1" --role="roles/run.admin" && \
gcloud projects add-iam-policy-binding grupo6-scotiabank --member="user:PONER@USUARIO1" --role="roles/resourcemanager.projectIamAdmin"
```

➡️ Con este set, el rol puede programar, ejecutar y operar funciones serverless encargadas de capturar los datos fuente y almacenarlos en la capa bronze del Data Lake.


### 🔸 Rol 2 – Procesamiento, ETL y modelado analítico

Responsabilidades:

- Transformación de datos con ETL

- Uso de BigQuery como repositorio analítico

- Creación y administración de datasets

- Diseño de capas Bronze, Silver (Plata) y Gold (Oro)


**Permisos otorgados:**
```bash
gcloud projects add-iam-policy-binding grupo6-scotiabank --member=user:PONER@USUARIO2 --role="roles/bigquery.dataOwner" && \
gcloud projects add-iam-policy-binding grupo6-scotiabank --member=user:PONER@USUARIO2 --role="roles/cloudfunctions.developer" && \
gcloud projects add-iam-policy-binding grupo6-scotiabank --member=user:PONER@USUARIO2 --role="roles/storage.admin" && \
gcloud projects add-iam-policy-binding grupo6-scotiabank --member=user:PONER@USUARIO2 --role="roles/cloudscheduler.admin" && \
gcloud projects add-iam-policy-binding grupo6-scotiabank --member=user:PONER@USUARIO2 --role="roles/run.admin"
```

➡️ Este rol gobierna la evolución de los datos, pasando de sin procesar → curados → listos para explotación analítica.

![Respuesta_Output](Evidencias/3-Out_Put_IAM.png)


### 🔸 Cuenta de Servicio - Clave

Se creo una clave para la cuenta de servicio con el cual permitira la explotación analítica.

![Creacion de clave cuenta de servicio 1](Evidencias/4_Agregar_Clave.png)
![Creacion de clave cuenta de servicio 2](Evidencias/5_Escoger_formato.png)
![Creacion de clave cuenta de servicio 3](Evidencias/6_Clave_Creada.png)

Esto descargará y generará un archivo JSON con credenciales y claves que permitiran la coneccion con fuentes externar analíticas como el PowerBI

![Creacion de clave cuenta de servicio 4](Evidencias/7_Json_Vista.png)

📘 Puede comprobar la conexión en [08_PowerBI](../08_PowerBI/README.md)


## 🔐 4. Principios de Seguridad aplicados

Se implementaron prácticas recomendadas:

- ✔ IAM granular por función técnica
- ✔ Ningún usuario con rol Owner
- ✔ Acceso a Storage y BigQuery controlado por capas
- ✔ Service Accounts independientes para automatizaciones
- ✔ Uso de CLI → mayor auditabilidad del despliegue

![Configuración_IAM](Evidencias\1-IAM_Roles.png)

## 🌐 5. Componentes de Red

Esta sección se completará tras definir la configuración final de la VPC, firewalls y segmentación interna del proyecto.

## 📊 6. Diagrama del Ambiente

El diagrama arquitectónico será agregado como:

01_Ambiente_GCP/arquitectura_gcp.mmd
01_Ambiente_GCP/arquitectura_gcp.png


Incluyendo los flujos:
```json
SBS → Web Scraping → Cloud Storage (Bronce)
       ↓
Dataproc / PySpark → BigQuery (Plata / Oro)
       ↓
Power BI
```

El entorno de GCP se encuentra adecuadamente preparado para soportar:

- Ingesta

- Procesamiento distribuido

- Explotación analítica

- Visualización en Power BI

