#  🏦 Proyecto BI Scotiabank 
## 🏗️ PC4 – Arquitectura Analítica en la Nube con Google Cloud Platform
El objetivo de este trabajo es implementar un flujo funcional de sistema de inteligencia de Negocio en la nube para la empresa Scotiabank que permitira:
- ✔ Obtener datos desde la web
- ✔ Procesarlos y almacenarlos en un Data Lake multicapa
- ✔ Transformarlos con PySpark
- ✔ Consolidar información en BigQuery
- ✔ Generar un esquema estrella en BigQuery
- ✔ Visualizar dashboards en Power BI

### Estructura de Carpetas

```json
/PC4
 ├── README.md                 # Documentación principal (este archivo)
 ├── 01_Ambiente_GCP/          # Creación del proyecto, IAM, VPC, buckets, claves
 ├── 02_WebScraping/           # Código Python para extracción de datos
 ├── 03_ETL/                   # Limpieza, consolidación, CSV, carga autom.
 ├── 04_DataLake/              # Raw / Trusted / Refined en GCS
 ├── 05_Procesamiento_Spark/   # Dataproc, PySpark, notebooks
 ├── 06_BigQuery/              # Tablas, particiones, consultas SQL
 ├── 07_Modelo_Estrella/                  # Modelo estrella
 ├── 08_PowerBI/               # Conexión con BigQuery + dashboards
 ├── evidencias/               # Capturas, videos, PR, merges
 └── docs/                     
```
## 🧱 1. Arquitectura Avanzada en la Nube

Nuestra arquitectura fue desplegada sobre Google Cloud Platform (GCP) en el proyecto:

```
ID del Proyecto: grupo6-scotiabank
```

La solución integra servicios básicos, avanzados y complementarios para soportar un flujo completo de analítica de datos, desde la ingesta hasta el consumo en Power BI.

### ✔️ Servicios utilizados y evidencias de costos

Durante la implementación se hizo uso real de los siguientes servicios en la nube (monto total invertido demostrado en gastos de GCP):

| Servicio                 | Costo (S/.) | Rol dentro de la Arquitectura                                       |
|--------------------------|-------------|----------------------------------------------------------------------|
| **Networking**           | 1.19        | Comunicación segura entre servicios, API y rutas privadas            |
| **BigQuery**             | 3.37        | Almacenamiento analítico, consultas SQL, datasets trusted/refined    |
| **BigQuery Reservation** | 12.64       | Reserva de slots para consultas de alto rendimiento                  |
| **Dataproc**             | 21.27       | Procesamiento distribuido con PySpark                                |
| **Compute Engine**       | 6.37        | Nodo de soporte/worker para ejecución puntual                        |
| **Cloud Storage**        | 0.30        | Data Lake multicapa: raw → trusted → refined                         |
| **Cloud Run**            | 18.76       | Servicios serverless para tareas auxiliares y componentes            |
| **Cloud Run Functions**  | 0.10        | Funciones event-driven para automatización                           |
| **Cloud Build**          | 0.00        | Construcción automática de artefactos                                |

![1-facturacion_Servicios](Evidencias_generales\1-Facturacion_Actual.png)


### ✔️ Storage estructurado (raw / trusted / refined)

El Data Lake se organizó bajo la estructura recomendada para arquitecturas analíticas:
```
gs://grupo6_scotiabank_bucket/
 ├── raw/        # Datos originales tal como se ingresa
 ├── trusted/    # Datos curados, limpios y tipificados
 └── refined/    # Datos listos para explotación analítica (Power BI / BigQuery)
```

Buckets adicionales generados automáticamente por los servicios utilizados:

Bucket	Servicio	Función
dataproc-temp-southamerica-west1-...	Dataproc	Área temporal donde Dataproc guarda metadatos, logs, intermediarios y staging
gcf-v2-sources-75587073872-southamerica-west1	Cloud Run / Cloud Functions	Almacena el código fuente desplegado por funciones y servicios serverless, permitiendo versionamiento y redeploy

👉 Este reparto permite separar la capa principal de datos del código operacional, garantizando gobernanza y control de versiones.

### ✔️ Visor BI en la nube

El consumo analítico se realiza mediante:

Power BI conectado a BigQuery


Se empleó:

Cuenta de servicio (Service Account)

Conexión segura por credenciales JSON

Modelo importado/DirectQuery según necesidad

Esto garantiza acceso controlado a datasets refinados sin exponer usuarios finales a los servicios de GCP.

### ✔️ Seguridad, IAM, roles y gobierno

La seguridad fue implementada mediante IAM granular por miembro del equipo y por servicio, asignando permisos mínimos necesarios para cada flujo.

📘 Para más detalle, revisa [01_Ambiente_GCP](01_Ambiente_GCP/README.md)


## Identificación de Servicios
## Arquitectura
## Integración de servicios
### 1. 
### 2.
### x. Dashboard - Looker

