# Prompts utilizados e inconvenientes del proyecto

## Prompts utilizados

1. **Resumen del proyecto**
   - Solicitud de resumen funcional y técnico del sistema.
2. **Puesta en marcha del entorno**
   - Instrucciones para instalar dependencias, levantar Docker, aplicar migraciones Prisma y arrancar frontend/backend.
3. **Solución de errores de permisos y puertos**
   - Manejo de errores EACCES en npm, conflictos de puertos en Docker, y permisos de usuario para Docker.
4. **Expansión de la base de datos**
   - Solicitud de actualización del modelo de datos según un ERD en formato Mermaid, generación de script SQL y migraciones Prisma.
5. **Verificación y visualización de la base de datos**
   - Pruebas con PGAdmin, exportación de esquema y visualización en dbdiagram.io.
6. **Limpieza y conversión de SQL a DBML**
   - Conversión del esquema SQL a formato DBML para su uso en dbdiagram.io.

## Inconvenientes y soluciones

1. **docker-compose no encontrado**
   - *Problema:* El comando `docker-compose` no estaba instalado.
   - *Solución:* Se instaló usando `sudo apt install docker-compose`.

2. **Permisos insuficientes para Docker**
   - *Problema:* Error `Permission denied` al usar Docker.
   - *Solución:* Se usó `sudo` y se recomendó agregar el usuario al grupo `docker`.

3. **Conflicto de puerto 5432**
   - *Problema:* El puerto 5432 ya estaba en uso por otro contenedor.
   - *Solución:* Se identificó y detuvo el contenedor en conflicto usando `docker ps` y `docker stop`.

4. **Errores de permisos en npm install**
   - *Problema:* Error EACCES al instalar dependencias en frontend/backend.
   - *Solución:* Se corrigieron los permisos con `sudo chown -R $USER:$USER ...` y se eliminaron carpetas problemáticas.

5. **Prisma: DATABASE_URL no encontrada**
   - *Problema:* Prisma no encontraba la variable de entorno para la conexión a la base de datos.
   - *Solución:* Se creó el archivo `.env` en la carpeta backend con la variable correcta.

6. **Prisma: credenciales inválidas**
   - *Problema:* Error de autenticación al conectar a PostgreSQL.
   - *Solución:* Se revisaron y sincronizaron las variables de entorno en `.env` y `docker-compose.yml`.

7. **Variables de entorno no leídas por Docker Compose**
   - *Problema:* Docker Compose no leía las variables del archivo `.env`.
   - *Solución:* Se verificó la ubicación y formato del archivo `.env` en la raíz del proyecto.

8. **pg_dump no instalado o versión incompatible**
   - *Problema:* No se podía exportar el esquema por falta de `pg_dump` o por incompatibilidad de versión.
   - *Solución:* Se instaló el cliente de PostgreSQL 17 para coincidir con la versión del servidor.

9. **dbdiagram.io: errores de sintaxis SQL**
   - *Problema:* dbdiagram.io no aceptaba el SQL exportado.
   - *Solución:* Se convirtió el esquema a formato DBML compatible.

---

## Visualización del modelo ERD en dbdiagram.io

### Pasos para generar el diagrama:

1. **Exporta el esquema de la base de datos:**
   ```sh
   pg_dump -U postgres -h localhost -d mydatabase --schema-only > estructura.sql
   ```
2. **Limpia el SQL:**
   - Extrae solo los bloques `CREATE TABLE` y las claves foráneas.
3. **Convierte a formato DBML:**
   - Usa el siguiente ejemplo como plantilla para dbdiagram.io:

```dbml
Table Company {
  id int [pk]
  name varchar
  createdAt timestamp
  updatedAt timestamp
}

Table Employee {
  id int [pk]
  companyId int
  name varchar
  email varchar
  role varchar
  is_active boolean
  createdAt timestamp
  updatedAt timestamp
}

Table Position {
  id int [pk]
  companyId int
  interviewFlowId int
  title varchar
  description text
  status varchar
  is_visible boolean
  location varchar
  job_description text
  requirements text
  responsibilities text
  salary_min float
  salary_max float
  employment_type varchar
  benefits text
  company_description text
  application_deadline timestamp
  contact_info varchar
  createdAt timestamp
  updatedAt timestamp
}

Table InterviewFlow {
  id int [pk]
  description text
  createdAt timestamp
  updatedAt timestamp
}

Table InterviewStep {
  id int [pk]
  interviewFlowId int
  interviewTypeId int
  name varchar
  order_index int
  createdAt timestamp
  updatedAt timestamp
}

Table InterviewType {
  id int [pk]
  name varchar
  description text
  createdAt timestamp
  updatedAt timestamp
}

Table Candidate {
  id int [pk]
  firstName varchar
  lastName varchar
  email varchar
  phone varchar
  address varchar
  createdAt timestamp
  updatedAt timestamp
}

Table Application {
  id int [pk]
  positionId int
  candidateId int
  application_date timestamp
  status varchar
  notes text
  createdAt timestamp
  updatedAt timestamp
}

Table Interview {
  id int [pk]
  applicationId int
  interviewStepId int
  employeeId int
  interview_date timestamp
  result varchar
  score int
  notes text
  createdAt timestamp
  updatedAt timestamp
}

Table Education {
  id int [pk]
  institution varchar
  title varchar
  startDate timestamp
  endDate timestamp
  candidateId int
}

Table WorkExperience {
  id int [pk]
  company varchar
  position varchar
  description text
  startDate timestamp
  endDate timestamp
  candidateId int
}

Table Resume {
  id int [pk]
  filePath varchar
  fileType varchar
  uploadDate timestamp
  candidateId int
}

Ref: Employee.companyId > Company.id
Ref: Position.companyId > Company.id
Ref: Position.interviewFlowId > InterviewFlow.id
Ref: InterviewStep.interviewFlowId > InterviewFlow.id
Ref: InterviewStep.interviewTypeId > InterviewType.id
Ref: Application.positionId > Position.id
Ref: Application.candidateId > Candidate.id
Ref: Interview.applicationId > Application.id
Ref: Interview.interviewStepId > InterviewStep.id
Ref: Interview.employeeId > Employee.id
Ref: Education.candidateId > Candidate.id
Ref: WorkExperience.candidateId > Candidate.id
Ref: Resume.candidateId > Candidate.id
```

4. **Pega el DBML en [dbdiagram.io](https://dbdiagram.io/)**
   - El diagrama se generará automáticamente mostrando tablas y relaciones.

---

Este archivo documenta los principales prompts, problemas, soluciones y el proceso para visualizar el modelo ERD del proyecto. 
