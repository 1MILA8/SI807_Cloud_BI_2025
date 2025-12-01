# 📊 Conexión de Power BI con Google BigQuery

Este módulo documenta el proceso de conexión entre Power BI Desktop y Google BigQuery, utilizando una Cuenta de Servicio como mecanismo seguro de autenticación. A partir de esta conexión se consumen los datos refinados del proyecto grupo6-scotiabank, permitiendo la construcción del modelo semántico y dashboards analíticos.

## 🛠️ 1. Inicio del proceso de conexión

Abrimos Power BI Desktop y seleccionamos:
![alt text](Evidencias/1-Coneccion_PowerBI.png)

Esto permite reconocer la infraestructura de Google Cloud como origen de datos.

![alt text](Evidencias/2-Credenciales.png)

## 🔐 2. Cuenta de Servicio configurada en GCP

La Cuenta de Servicio previamente creada en Google Cloud IAM contiene los permisos necesarios para leer datasets y ejecutar consultas en BigQuery.

- No requiere credenciales personales

- Permite control granular desde IAM

- Facilita el acceso automatizado sin renovación constante de tokens

![alt text](Evidencias/3-Correo-Cuenta_de_Servicio.png)

Power BI solicita el archivo JSON correspondiente a la cuenta de servicio.
Este archivo contiene todos los campos necesarios para validar la identidad del cliente ante GCP.
Es necesario juntar todo el json en una sola linea al ingresar las credenciales:

![Clave json](Evidencias/4-Archivo_Json.png)

## 🔑 3. Ingreso de credenciales en Power BI

Luego de seleccionar el archivo .json, Power BI valida las credenciales e inicia la conexión con los servicios de BigQuery.

![alt text](Evidencias/5-Ingresar_Credenciales.png)

## 📁 4. Elección del dataset y tablas

Una vez establecida la conexión, Power BI muestra los:

- Proyectos disponibles

- Datasets cargados en BigQuery

- Tablas listas para modelado

![alt text](Evidencias/6-Seleccion_Tablas.png)

## 📅 5. Relación del modelo de tablas en Power BI

![alt text](Evidencias/7-Esquema-Relacional.png)

## 📈 6. Dashboard final

(gráficos, KPIs, segmentaciones y hallazgos)

