# GTEL Talento — Sistema de Reclutamiento

Plataforma de selección de personal para candidatos, reclutadores y administradores de GTEL.

| Información | Detalle |
|---|---|
| Curso | Curso Integrador II — Desarrollo de Páginas Web |
| Docente | Marisbel Toledo |
| Arquitectura | React → API REST Spring Boot → MySQL |

### Integrantes

| Integrante |

| Bartolome Angelo Prado Misaico |
| Rivera Bautista Brian Alexis |
| Menendez Reinoso Oscar Sebastian |
| Leon Soles Leonardo Francisco |
| Mendoza Malca, Mayco Joel Carlos |
| Mendoza Núñez Adrihan Daniel |

### Estado del proyecto


### Contenido

- [Despliegue rápido con Docker](#despliegue-rápido-con-docker): ejecución multi-contenedor sin dependencias locales.
- [Frontend](#frontend): instalación, configuración y ejecución de React.
- [Backend](#backend): MySQL, configuración y ejecución de Spring Boot.
- [Credenciales de prueba](#6-credenciales-de-prueba).
- [Verificación por correo](#10-verificación-de-acceso-por-correo-gmail-smtp).
- [Administración y permisos](#11-administración-y-permisos).
- [Trabajo colaborativo](#12-trabajo-colaborativo).

# Despliegue rápido con Docker

Para ejecutar la arquitectura completa (Frontend React con Nginx, Backend Spring Boot y Base de Datos MySQL 8) sin necesidad de instalar Java, Maven, Node.js ni MySQL en el equipo:

1. Asegurarse de tener **Docker Desktop** abierto y en ejecución.
2. Abrir una terminal en el directorio del proyecto y ejecutar:
   ```bash
   docker compose up -d
   ```
3. Acceso a los servicios:
   - **Frontend Web:** [http://localhost:3000](http://localhost:3000)
   - **Swagger UI (Documentación API):** [http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html)
   - **MySQL 8:** Puerto 3307 (Usuario: `gtel_user`, Contraseña: `GtelSecure2026*`, Base de datos: `gtel_talento`)
4. Para detener los contenedores:
   ```bash
   docker compose down
   ```

---
# Frontend

Interfaz web para consultar ofertas, registrar candidatos, iniciar sesión y acceder a las pantallas de postulaciones y reclutamiento.

## Stack técnico

Versiones declaradas en `frontend/package.json`; `package-lock.json` fija las dependencias instaladas con `npm ci`.

| Tecnología | Versión declarada | Función |
|---|---|---|
| React | ^19.2.8 | Componentes y estado |
| Vite | ^8.2.2 | Desarrollo y compilación |
| React Router | ^7.18.3 | Navegación |
| Tailwind CSS | ^4.3.3 | Estilos |
| Lucide React | ^1.43.0 | Iconos |
| Recharts | ^3.10.1 | Gráficos |
| ESLint | ^10.9.0 | Revisión del código |

## Prerrequisitos

| Herramienta | Versión de referencia | Descarga oficial |
|---|---|---|
| Node.js | 22.x, desde 22.12 | [Node.js](https://nodejs.org/en/download) |
| npm | Incluido con Node.js | [Node.js](https://nodejs.org/en/download) |
| Git | 2.x | [Git](https://git-scm.com/downloads) |
| VS Code | Estable; el proyecto no fija versión | [VS Code](https://code.visualstudio.com/Download) |

Los comandos de esta guía usan PowerShell en Windows. En Linux/macOS, usar `npm` y `mvn` sin `.cmd`.

## 1. Obtener el proyecto e instalar dependencias

Si todavía no lo clonaste, utiliza el comando del apartado **Backend → 1. Clonar el repositorio**. No necesitas clonarlo dos veces.

Desde la raíz del repositorio:

```powershell
cd frontend
npm.cmd ci
```

## 2. Configurar la API

Crear opcionalmente `frontend/.env.local`:

```dotenv
VITE_API_URL=http://localhost:8080
VITE_DATA_MODE=demo
```

Son los valores predeterminados. Registro y login utilizan el backend incluso con los demás módulos en modo `demo`. Cambiar a `api` no implementa los endpoints pendientes. No guardar secretos en variables `VITE_*`, porque son públicas.

## 3. Levantar el frontend

Desde `frontend/`:

```powershell
npm.cmd run dev
```

Abrir http://localhost:5173 o la dirección indicada por Vite. El backend debe estar iniciado para registrarse e ingresar. Si cambia el puerto del frontend, ajustar también los orígenes CORS del backend.

## 4. Pruebas y compilación

```powershell
npm.cmd run lint
node --test --test-concurrency=1 tests/*.test.mjs
npm.cmd run build
```

La compilación genera `dist/`. Los recorridos actuales están en `tests/admin-browser.mjs` y `tests/email-otp-browser.mjs`; sus requisitos se indican en el apartado de administración. `tests/browser-flows.mjs` conserva escenarios demo anteriores y requiere adaptación antes de reutilizarlo.

## 5. Estructura del frontend

```text
frontend/
├── src/
│   ├── components/      # Componentes, formularios y modales
│   ├── context/         # Estado de autenticación
│   ├── hooks/           # Lógica reutilizable
│   ├── layouts/         # Estructuras de página
│   ├── pages/           # Pantallas públicas, candidato, reclutador y admin
│   ├── routes/          # Navegación y rutas protegidas
│   ├── services/        # Comunicación con API y servicios demo
│   └── utils/           # Funciones auxiliares
├── public/              # Archivos estáticos
├── scripts/             # Herramientas de desarrollo
├── tests/               # Pruebas automatizadas
├── package.json         # Dependencias y comandos
└── vite.config.js       # Configuración de Vite
```

# Backend

API REST de GTEL Talento. Gestiona el registro, la autenticación por correo y contraseña, la emisión de tokens JWT y las consultas de reclutamiento. Utiliza MySQL para persistir los datos y organiza el código por funcionalidades.

## Stack técnico

| Tecnología | Versión | Función |
|---|---|---|
| Java | 21 | Lenguaje y runtime |
| Spring Boot | 3.5.5 | Servidor y configuración |
| Spring Web | Gestionada por Spring Boot | API REST |
| Spring Data JPA / Hibernate | Gestionada por Spring Boot | Persistencia y validación del esquema |
| Spring Security / BCrypt | Gestionada por Spring Boot | Autorización y hash de contraseñas |
| JJWT | 0.12.6 | Firma y validación de JWT |
| Spring Mail | Gestionada por Spring Boot | Envío de códigos por SMTP |
| MySQL Connector/J | Gestionada por Spring Boot | Conexión JDBC |
| JUnit / Mockito | Gestionada por Spring Boot | Pruebas |
| H2 | Gestionada por Spring Boot | Base en memoria para pruebas |

Las dependencias Java se descargan mediante Maven según `backend/pom.xml`; no se instalan manualmente.

## Prerrequisitos

| Herramienta | Versión requerida o de referencia | Descarga oficial |
|---|---|---|
| JDK | 21; seleccionar JDK 21 en la página | [Oracle JDK](https://www.oracle.com/java/technologies/downloads/#java21) |
| Apache Maven | 3.9.x como referencia | [Maven](https://maven.apache.org/download.cgi) |
| MySQL Server | 8.x para la instalación de referencia; SQL requiere al menos 8.0.13 | [MySQL Community](https://dev.mysql.com/downloads/) |
| DBeaver Community | Versión compatible con MySQL 8; no fijada por el proyecto | [DBeaver](https://dbeaver.io/download/) |
| VS Code | Versión estable; no fijada por el proyecto | [VS Code](https://code.visualstudio.com/Download) |
| Extension Pack for Java | Versión compatible con tu VS Code | [Extensión Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) |
| Spring Boot Extension Pack | Versión compatible con tu VS Code | [Extensiones Spring](https://marketplace.visualstudio.com/items?itemName=vmware.vscode-boot-dev-pack) |
| Git | 2.x | [Git](https://git-scm.com/downloads) |

Las versiones de herramientas auxiliares son referencias, no una declaración de que todas se hayan probado juntas. DBeaver es un cliente: no reemplaza a MySQL Server. Cada integrante configura su propia instalación local.

Comprobar que Java y Maven estén disponibles en `PATH` y que Maven utilice Java 21:

```powershell
java -version
mvn.cmd -version
git --version
```

## 1. Clonar el repositorio

```powershell
git clone https://github.com/AngeloPradoM/IntegradorII-DesarrolloWeb-FullStack-GTEL.git
cd IntegradorII-DesarrolloWeb-FullStack-GTEL
```

Repositorio: [IntegradorII-DesarrolloWeb-FullStack-GTEL](https://github.com/AngeloPradoM/IntegradorII-DesarrolloWeb-FullStack-GTEL).

## 2. Crear la base de datos en DBeaver

### 2.1. Conectar MySQL

1. Iniciar el servicio MySQL y abrir DBeaver.
2. Seleccionar **New Database Connection** (ícono de conexión).
3. Elegir **MySQL → Next**.
4. Completar los datos siguientes.

| Campo | Valor |
|---|---|
| Host | `localhost` |
| Port | `3306` |
| Username | `root`, o tu usuario MySQL local |
| Password | Contraseña de tu MySQL local |

5. En **Driver properties**, configurar `allowPublicKeyRetrieval=true` y `useSSL=false` para esta conexión de desarrollo local.
6. Seleccionar **Test Connection** y descargar el driver si DBeaver lo solicita. La conexión debe resultar satisfactoria.
7. Seleccionar **Finish**.

### 2.2. Crear la base de datos

Abrir un editor SQL asociado a la conexión y ejecutar:

```sql
CREATE DATABASE IF NOT EXISTS gtel_talento
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;
```

Ejecutar la sentencia con `Ctrl + Enter` o la acción **Execute SQL Statement**. Actualizar la conexión con **Refresh** y verificar que aparezca `gtel_talento`.

### 2.3. Crear las tablas y roles

Abrir [backend/database/schema/gtel_talento.sql](backend/database/schema/gtel_talento.sql) mediante **SQL Editor → Open SQL Script**, asociarlo a la conexión y ejecutar el script completo con **Execute SQL Script**.

El script incluye `CREATE DATABASE IF NOT EXISTS`, por lo que admite la base vacía creada en el paso anterior. Crea las tablas y los roles `CANDIDATO`, `RECLUTADOR`, `ADMIN` y `TECNICO`. Las cuentas de desarrollo se habilitan explícitamente como se indica abajo.

**Aplicar solo en una base sin tablas.** No volver a ejecutar el script inicial sobre una BD con datos. La migración histórica `001_postulante_telefono.sql` tampoco se aplica si ya existe la columna `telefono`.

```sql
USE gtel_talento;
SHOW TABLES;
SELECT id, nombre FROM roles;
```

### 2.4. Si ya tienes una base de datos

Conservar los datos y revisar qué cambios faltan antes de ejecutar SQL. Los scripts son manuales; no se aplican automáticamente al iniciar Spring Boot.

| Script | Cuándo aplicarlo |
|---|---|
| `001_postulante_telefono.sql` | Solo si falta `postulantes.telefono` |
| [002_email_otp.sql](backend/database/migrations/002_email_otp.sql) | Solo si falta la tabla `auth_email_challenges` |
| [003_admin_access.sql](backend/database/migrations/003_admin_access.sql) | Una vez, para incorporar ADMIN, `usuarios.auth_version` y `usuarios.otp_exempt` |
| [007_support_tickets.sql](backend/database/migrations/007_support_tickets.sql) | Una vez, para incorporar el rol TECNICO, su cuenta local de prueba y las tablas de tickets en una BD existente |
| [008_notification_schema_compatibility.sql](backend/database/migrations/008_notification_schema_compatibility.sql) | Después de la 007, para adaptar instalaciones existentes al esquema actual de notificaciones y evitar que el envío de avisos revierta un ticket |
| [009_support_ticket_lifecycle.sql](backend/database/migrations/009_support_ticket_lifecycle.sql) | Después de la 008, normaliza los estados, relaciona la trazabilidad y crea el archivo resumido para la depuración semanal |

El esquema inicial actualizado incluye estos cambios. No ejecutar las migraciones sobre una instalación nueva creada con ese esquema. Si una migración se aplicó parcialmente, revisar las columnas existentes antes de continuar.

## 3. Configurar el proyecto

`backend/src/main/resources/application.properties` define la configuración compartida: puerto, URL de MySQL, importación del archivo local y validación del esquema. Las credenciales privadas se configuran en `backend/config/local.properties`, que está excluido de Git.

Si no existe, copiar `backend/config/local.properties.example` como `backend/config/local.properties`. Si ya existe, editarlo conservando su clave JWT.

```properties
spring.datasource.username=root
spring.datasource.password=TU_PASSWORD_AQUI
jwt.secret=TU_CLAVE_ALEATORIA_PRIVADA_DE_AL_MENOS_32_BYTES
otp.hash-secret=OTRA_CLAVE_ALEATORIA_PRIVADA_DE_AL_MENOS_32_BYTES
spring.mail.username=TU_REMITENTE@gmail.com
spring.mail.password=TU_CONTRASENA_DE_APLICACION
app.security.test-access-enabled=false
```

Sustituir `TU_PASSWORD_AQUI` por la contraseña de MySQL. Dejarla vacía únicamente si ese usuario MySQL realmente no tiene contraseña.

Generar una clave JWT con Node.js (instalado para el frontend) y copiar el resultado a `jwt.secret`:

```powershell
node -e "console.log(require('crypto').randomBytes(48).toString('hex'))"
```

Ejecutar el generador dos veces: una para `jwt.secret` y otra para `otp.hash-secret`. Deben ser claves distintas y conservarse entre reinicios. Configurar el remitente siguiendo el apartado 10; para usar las cuentas de prueba, seguir el apartado 6.

JWT identifica las solicitudes autenticadas. Se emite tras validar el código de correo o tras autenticar una cuenta de prueba autorizada. No publicar el archivo local ni copiar claves privadas al README.

| Configuración | Valor predeterminado / uso |
|---|---|
| `DB_URL` | JDBC hacia `localhost:3306/gtel_talento` |
| `DB_USERNAME` | `root` |
| `DB_PASSWORD` | Contraseña MySQL si se utiliza variable de entorno |
| `JWT_SECRET` | Alternativa a `jwt.secret` local |
| `SERVER_PORT` | `8080` |
| `FRONTEND_URL` | `http://localhost:5173,http://127.0.0.1:5173` |
| `JWT_EXPIRATION_MS` | `3600000` (una hora) |
| `JPA_DDL_AUTO` | `validate` |

Spring no carga `.env` automáticamente. Usar variables del proceso/IDE o el archivo local anterior. Arrancar desde `backend/` para resolver `./config/local.properties`.

## 4. Levantar el servidor

### Opción A — VS Code con Spring Boot Dashboard

1. Instalar las extensiones Java y Spring indicadas en prerrequisitos.
2. Abrir **File → Open Folder** y seleccionar **la carpeta `backend`**.
3. Esperar que Maven importe el proyecto y descargue las dependencias.
4. Abrir **Spring Boot Dashboard**.
5. Seleccionar ▶ en la aplicación correspondiente a `gtel-talento-backend` / `GtelTalentoApplication`.
6. Comprobar que el log muestre `Started GtelTalentoApplication`.

Abrir directamente `backend/` facilita que el directorio de ejecución coincida con la ruta de configuración local. Si el IDE utiliza otro directorio, ajustar su configuración de ejecución.

### Opción B — Terminal

Desde la raíz del repositorio:

```powershell
cd backend
mvn.cmd spring-boot:run
```

Si Maven no puede acceder a su caché predeterminada:

```powershell
mvn.cmd "-Dmaven.repo.local=$env:USERPROFILE\.m2\repository" spring-boot:run
```

### Verificar

- Servidor: http://localhost:8080
- Salud básica: http://localhost:8080/api/health

**Hibernate no crea tablas:** `ddl-auto=validate` comprueba el esquema creado mediante SQL. Por separado, `DevelopmentAccounts` crea las cuentas de prueba faltantes únicamente cuando `app.security.test-access-enabled=true`. Actualizar DBeaver para consultar los registros.

## 5. Comprobar el funcionamiento

1. Mantener MySQL, backend y frontend encendidos.
2. Registrar un candidato o utilizar las cuentas de prueba previamente creadas.
3. Iniciar sesión con correo y contraseña. El servidor detecta el rol; introducir el código recibido por correo si la cuenta no tiene la excepción de pruebas.
4. Recargar la página para comprobar la recuperación de sesión.
5. Cerrar sesión y verificar que una contraseña incorrecta sea rechazada.

Desde `backend/`, ejecutar pruebas unitarias:

```powershell
mvn.cmd clean test
```

Las pruebas unitarias no crean usuarios en MySQL y no sustituyen la comprobación real de conexión. El cierre de sesión elimina el token del navegador. Editar una cuenta desde el panel invalida sus JWT anteriores; el cierre de sesión por sí solo no revoca el token en el servidor.

| Error | Qué revisar |
|---|---|
| `JWT_SECRET` sin resolver | Configurar `jwt.secret` local y arrancar desde `backend/` |
| `Access denied ... using password: NO` | Falta la contraseña MySQL local |
| Tabla o columna inexistente | En una BD nueva, importar el esquema; en una existente, revisar las migraciones pendientes |
| `otp.hash-secret` sin resolver | Configurar una clave privada distinta de la clave JWT |
| Rol CANDIDATO inexistente | Comprobar los registros de `roles` |
| Puerto 8080 ocupado | Detener la instancia duplicada o configurar otro puerto |
| Frontend no conecta | URL de API, puerto y orígenes CORS |

## 6. Credenciales de prueba

| Tipo de acceso | Rol | Correo | Contraseña de prueba |
|---|---|---|---|
| Administrador | `ADMIN` (3) | `Administrador@gmail.com` | `AdminSecure2026*` |
| Candidato | `CANDIDATO` | `postulante1@gmail.com` | `Postulante2026!` |
| Reclutador | `RECLUTADOR` | `reclutador1@gmail.com` | `Reclutador2026!` |
| Técnico de sistemas | `TECNICO` | `tecnico1@gmail.com` | `Tecnico2026!` |

Estas cuatro cuentas ingresan sin código de verificación cuando el acceso local de pruebas está habilitado. Las demás cuentas mantienen la verificación por correo.

Con `app.security.test-access-enabled=true` en `backend/config/local.properties`, `DevelopmentAccounts` crea o sincroniza las cuentas locales configuradas, incluidas las cuatro documentadas: actualiza su hash BCrypt, rol, estado y excepción OTP. Esta sincronización es exclusiva para desarrollo. El registro público solo crea candidatos.

Los IDs de los roles se consultan en `roles`; no asumir que siempre serán 1 y 2. Son credenciales públicas de desarrollo, no de producción ni de Gmail. Antes de activar correos reales, utilizar direcciones controladas por el equipo.

## 7. Estructura del backend

```text
backend/
├── src/main/java/pe/com/gtel/talento/
│   ├── GtelTalentoApplication.java  # Punto de entrada Spring Boot
│   ├── auth/                       # Autenticación: controller, service, dto
│   ├── admin/                      # Gestión de usuarios: controller, service, dto
│   ├── identity/                   # Usuarios y perfiles: entity, repository
│   ├── recruitment/                # Reclutamiento: controller, service, repository
│   ├── support/                    # Mesa de ayuda: tickets, asignación y autorización
│   ├── mail/                       # Envío SMTP de códigos
│   ├── health/                     # Endpoint de salud
│   ├── config/                     # Configuración Spring y CORS
│   └── security/                   # Firma JWT y filtro de autenticación
├── src/main/resources/
│   └── application.properties      # Configuración compartida
├── src/test/java/                  # Pruebas por módulo
├── config/
│   ├── local.properties            # Configuración privada; no versionar
│   └── local.properties.example    # Plantilla para el equipo
├── database/
│   ├── schema/                     # Instalación inicial de la BD
│   └── migrations/                 # Cambios manuales históricos
├── target/                         # Generado por Maven; no versionar
├── CONTRIBUTING.md                 # Convenciones de colaboración
└── pom.xml                         # Dependencias y configuración Maven
```

Las solicitudes recorren Controller → Service → Repository/JDBC → MySQL. La configuración local, las dependencias descargadas y los archivos compilados no deben subirse a GitHub.

## 8. Tablas MySQL

Esquema: `gtel_talento`, codificación `utf8mb4`.

| Tabla | Función | Relaciones principales |
|---|---|---|
| `roles` | Tipos de acceso | Referenciada por `usuarios` |
| `departamentos` | Áreas de trabajo | Referenciada por requerimientos y vacantes |
| `usuarios` | Cuentas, hash, estado, versión de sesión y excepción OTP | `rol_id` → roles |
| `postulantes` | Perfil profesional del candidato | `usuario_id` → usuarios (único) |
| `requerimientos_personal` | Solicitudes de contratación | Departamento y usuario solicitante |
| `vacantes` | Ofertas laborales | Requerimiento, departamento y reclutador |
| `postulaciones` | Candidaturas a vacantes | Postulante y vacante |
| `postulacion_timeline` | Historial de estados | Postulación y usuario |
| `entrevistas` | Agenda de entrevistas | Postulación |
| `evaluaciones` | Resultados de evaluación | Postulación y usuario evaluador |
| `evaluacion_detalle` | Criterios y puntajes | Evaluación |
| `auditoria` | Registro de acciones | Usuario responsable |
| `auth_email_challenges` | Desafío de acceso, hash del código y límites | Un desafío por usuario; FK con eliminación en cascada |
| `tickets_soporte` | Solicitudes de ayuda, propuesta técnica, autorización y evidencia | Solicitante y técnico → usuarios |
| `ticket_soporte_estados` | Catálogo ordenado de estados y marca de estados terminales | tickets_soporte e historial |
| `ticket_soporte_historial` | Trazabilidad detallada de cada transición del ticket | Ticket, actor y catálogo de estados |
| `tickets_soporte_archivo` | Evidencia resumida conservada después de la depuración semanal | Sin claves foráneas para preservar el registro histórico |
| `ticket_soporte_historial` | Trazabilidad de cambios del ticket | Ticket y actor responsable |

La existencia de estas tablas no significa que todos sus módulos estén integrados con el frontend.

Consultar las cuentas sin exponer los hashes:

```sql
SELECT u.id, u.email, r.nombre AS rol,
       p.nombres, p.apellidos
FROM gtel_talento.usuarios u
JOIN gtel_talento.roles r ON r.id = u.rol_id
LEFT JOIN gtel_talento.postulantes p ON p.usuario_id = u.id
ORDER BY u.id;
```

## 9. Endpoints principales

| Método | Ruta | Función |
|---|---|---|
| GET | `/api/health` | Salud básica |
| POST | `/api/auth/register` | Registro público de candidato |
| POST | `/api/auth/login` | Valida credenciales; solicita OTP o entrega JWT para las cuentas de prueba habilitadas |
| POST | `/api/auth/verify-otp` | Verifica sessionId + otp y entrega JWT una sola vez |
| POST | `/api/auth/resend-otp` | Reenvía código mediante sessionId, con límites |
| GET | `/api/auth/me` | Perfil actual con Bearer token |
| POST | `/api/auth/logout` | Indicación de eliminar token local |
| GET | `/api/recruiter/candidates` | Consulta de candidatos |
| GET | `/api/recruiter/interviews` | Consulta de entrevistas |
| GET | `/api/recruiter/evaluations` | Consulta de evaluaciones |
| GET | `/api/recruiter/dashboard` | Resumen de reclutamiento |
| GET | `/api/admin/users` | Lista paginada de usuarios; solo ADMIN |
| POST | `/api/admin/users` | Crea usuarios; solo ADMIN |
| PUT | `/api/admin/users/{id}` | Edita cuentas y permisos; solo ADMIN |
| GET | `/api/support/tickets` | Lista tickets según el rol autenticado |
| POST | `/api/support/tickets` | Reclutador registra una solicitud de soporte |
| GET | `/api/support/technicians` | Administrador consulta técnicos activos |
| PATCH | `/api/support/tickets/{id}/assignment` | Administrador asigna el responsable técnico |
| PUT | `/api/support/tickets/{id}/proposal` | Técnico remite diagnóstico y solución propuesta |
| PATCH | `/api/support/tickets/{id}/decision` | Administrador autoriza o devuelve la propuesta |
| PATCH | `/api/support/tickets/{id}/status` | Técnico actualiza la pasarela de estados autorizada |
| PUT | `/api/support/tickets/{id}/execution` | Técnico registra la resolución y su evidencia |

Las rutas de reclutamiento permiten RECLUTADOR y ADMIN; `/api/admin/**` permite únicamente ADMIN. Registro devuelve ID de postulante; login y `/me`, ID de usuario. La verificación por correo se realiza en cada nuevo inicio de sesión, salvo las cuatro cuentas de prueba expresamente habilitadas.

## 10. Verificación de acceso por correo (Gmail SMTP)

### Preparar una instalación existente

Ejecutar una sola vez [002_email_otp.sql](backend/database/migrations/002_email_otp.sql) en DBeaver, sobre gtel_talento. El script agrega una tabla y no modifica las cuentas existentes. En una instalación nueva basta el script completo de schema, que ya incluye la tabla. No aplicar ambos sobre las mismas tablas.

Si MySQL indica espera de bloqueo de metadatos, finalizar las transacciones pendientes en DBeaver: confirmar solamente los cambios que se quieran conservar o revertirlos. No desactivar claves foráneas ni cerrar transacciones ajenas.

### Configurar el remitente

En backend/config/local.properties, conservar MySQL y JWT y añadir:

```properties
spring.mail.username=TU_REMITENTE@gmail.com
spring.mail.password=TU_CONTRASENA_DE_APLICACION
otp.hash-secret=OTRA_CLAVE_ALEATORIA_PRIVADA_DE_AL_MENOS_32_BYTES
```

Generar otp.hash-secret del mismo modo que la clave JWT, pero con un valor diferente. Conservarla entre reinicios: cambiarla invalida los códigos pendientes. El remitente es configurable; para cambiarlo basta actualizar sus dos propiedades y reiniciar Spring. No se lee index/.env.

Activar la verificación en dos pasos de Google y generar una contraseña de aplicación en [la cuenta remitente](https://myaccount.google.com/apppasswords); consultar la [guía de Google](https://support.google.com/accounts/answer/185833?hl=es). No usar la contraseña habitual del correo.

El backend utiliza smtp.gmail.com:587 con STARTTLS obligatorio, validación del certificado y tiempos de espera de 5 segundos. Configuración basada en [Gmail SMTP](https://support.google.com/mail/answer/7104828?hl=en) y [Spring Boot Mail](https://docs.spring.io/spring-boot/3.5/reference/io/email.html). Las credenciales nunca se devuelven al navegador ni se incluyen en ejemplos públicos.

### Flujo y límites

1. Registro conserva el flujo actual y no inicia sesión ni envía códigos.
2. Login valida correo y contraseña; el rol se obtiene de MySQL. Las cuentas normales reciben requiresOtp=true, authenticated=false, sessionId, maskedEmail y temporizadores, sin token. Las cuentas de prueba habilitadas reciben sesión inmediata.
3. React muestra el campo de seis dígitos. La sesión pendiente se mantiene en memoria; recargar obliga a iniciar de nuevo.
4. verify-otp consume el código válido y devuelve perfil, verified=true, authenticated=true y JWT.
5. `/me` recupera sesiones válidas, incluidas las cuentas de prueba autorizadas. Los tokens con formato anterior, estado inactivo, rol o versión desactualizados requieren iniciar sesión nuevamente.

| Regla | Valor |
|---|---|
| Código | 6 dígitos generados con SecureRandom |
| Vigencia | Hasta 5 minutos por código |
| Ventana total | 10 minutos; no se prolonga al reenviar |
| Intentos | Máximo 5 por ventana; reenviar o reiniciar login no los restablece |
| Envíos | Inicial + máximo 3 adicionales por ventana |
| Espera entre envíos | 30 segundos, también al repetir login |
| Persistencia | HMAC-SHA256 con clave privada; no se almacena el código legible |
| Concurrencia | Transacciones y bloqueo de fila para impedir consumo doble |
| Error SMTP | Invalida el desafío, mantiene límites y no emite JWT |

Los destinatarios deben ser correos reales controlados por quien prueba. Las cuentas de la sección 6 omiten OTP solo cuando está habilitado el acceso local de pruebas y tienen la marca otp_exempt. No se añadió recuperación de contraseña ni confirmación independiente al registrarse.

### Validación

Ejecutar mvn.cmd test en backend y los comandos de pruebas del frontend. Las pruebas de OTP usan H2 en modo MySQL, transacciones reales y un remitente simulado: comprueban expiración, errores, límites, reenvíos, cambios de rol y consumo concurrente. No sustituyen una comprobación manual en MySQL y navegador. No incluyen bloqueo general de intentos de contraseña por IP; ese endurecimiento queda pendiente para un despliegue público.


## 11. Administración y permisos

El login tiene un solo formulario. Redirige a candidatos a `/mis-postulaciones`, reclutadores a `/reclutador/dashboard`, técnicos a `/tecnico/tickets` y administradores a `/admin`. El panel permite crear y editar usuarios, cambiar roles y activar/desactivar cuentas. ADMIN puede abrir vistas de ambos roles; esto no suplanta a otros usuarios ni completa módulos aún pendientes de integrar.

### Habilitar las pruebas locales

Con el esquema actualizado según el apartado 2, configurar `app.security.test-access-enabled=true` en `backend/config/local.properties`, reiniciar e ingresar con las credenciales del apartado 6. El valor predeterminado es `false`. Las excepciones nunca se deciden desde el navegador.

**Despliegue:** dejar `app.security.test-access-enabled=false` y retirar o cambiar las credenciales públicas de prueba antes de exponer el sistema. Desactivar esta opción invalida también los JWT emitidos con el método de prueba.

| Método | Ruta | Permiso |
|---|---|---|
| GET | `/api/admin/users?search=&page=0` | ADMIN; búsqueda por correo, 25 resultados por página |
| POST | `/api/admin/users` | ADMIN; crear cuenta |
| PUT | `/api/admin/users/{id}` | ADMIN; editar cuenta |

La mesa de ayuda usa `/reclutador/tickets` para solicitar y consultar incidencias, `/tecnico/tickets` para evaluar y ejecutar el trabajo autorizado y `/admin/tickets` para asignar técnicos y decidir propuestas. Aprobar habilita la ejecución; no resuelve automáticamente el caso. `/reclutador/guia-uso` queda creado como página vacía para incorporar el contenido futuro.

El cuerpo de creación/edición usa `email`, `password`, `rol`, `estado`, `nombres`, `apellidos` y `telefono`. La contraseña es obligatoria al crear (12 a 72 caracteres, máximo 72 bytes UTF-8), opcional al editar; nombres y apellidos son obligatorios para candidatos. No se devuelven hashes ni contraseñas. No se permite desactivar o degradar al propio administrador ni quitar el último administrador activo.

Editar una cuenta incrementa `auth_version` y elimina sus desafíos OTP pendientes. Cada petición protegida comprueba rol, estado y versión en MySQL; las sesiones anteriores dejan de servir. El cierre de sesión del navegador sigue siendo local.

### Validación

Ejecutar las pruebas del apartado **Frontend → 4** y **Backend → 5** desde sus respectivas carpetas.

Prueba manual: comprobar el destino de las cuatro cuentas, abrir `/admin` como candidato (debe rechazar), crear/editar una cuenta desde el panel y comprobar MySQL, desactivarla e intentar ingresar. Las cuentas normales deben pedir OTP y conservar el reenvío de 30 segundos. Una contraseña incorrecta nunca omite la validación.

Los recorridos de navegador están en `frontend/tests/admin-browser.mjs` (backend real y cuentas locales) y `frontend/tests/email-otp-browser.mjs` (respuestas HTTP simuladas para OTP). Requieren Vite en 127.0.0.1:5173 y Chrome con perfil desechable y depuración local en el puerto 9238. No ejecutarlos en un perfil personal.

## 12. Trabajo colaborativo

Convención propuesta para las ramas:

| Rama | Uso |
|---|---|
| `main` | Versión estable |
| `develop` | Integración del equipo |
| `feature/<descripcion>` | Nuevas funcionalidades mediante pull request |
| `fix/<descripcion>` | Correcciones mediante pull request |

Compartir el esquema SQL y las plantillas de configuración. Mantener claves JWT, credenciales SMTP, configuración local, dependencias y archivos compilados fuera de Git. Cada integrante configura su propia base local. El laboratorio `index/` permanece separado de la aplicación principal.
