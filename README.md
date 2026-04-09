# SCISE

SCISE es una plataforma web para el control de ingreso y salida de equipos. El repositorio esta dividido en dos aplicaciones:

- `SCISE-Backend`: API REST construida con FastAPI
- `SCISE-Frontend`: interfaz web construida con Astro

Este README esta pensado para que un docente o evaluador pueda levantar el proyecto, preparar la base de datos y ejecutar pruebas funcionales basicas.

## 1. Requisitos

### Backend
- Python 3.12 recomendado
- PostgreSQL 14 o superior
- PowerShell o terminal compatible

### Frontend
- Node.js 20 o superior
- npm 10 o superior

## 2. Estructura del proyecto

```text
SCISE-Project/
  README.md
  .env.backend.example
  .env.frontend.example
  database/
    scise_init.sql
  SCISE-Backend/
  SCISE-Frontend/
```

## 3. Variables de entorno

### Backend
1. Copiar `.env.backend.example` dentro de `SCISE-Backend/` con el nombre `.env`
2. Ajustar valores reales de PostgreSQL y del usuario administrador inicial

Cadena de conexion actual de la base de datos del proyecto:

```env
DATABASE_URL=postgresql://neondb_owner:npg_l9KBV2MTgPjQ@ep-green-waterfall-adkwuw2c-pooler.c-2.us-east-1.aws.neon.tech/neondb?sslmode=require&channel_binding=require
```

Notas:
- esta base corresponde a Neon PostgreSQL
- requiere conexion SSL
- si se usa esta conexion, debe colocarse en el archivo `.env` del backend

### Frontend
1. Copiar `.env.frontend.example` dentro de `SCISE-Frontend/` con el nombre `.env`
2. Verificar que `PUBLIC_API_BASE_URL` apunte al backend correcto

## 4. Preparar la base de datos

Hay dos formas.

### Opcion A - Recomendada: usar Alembic

1. Crear la base de datos vacia en PostgreSQL, por ejemplo:

```sql
CREATE DATABASE scise_db;
```

2. Configurar `DATABASE_URL` en el archivo `.env` del backend.

3. Ejecutar migraciones:

```powershell
cd SCISE-Backend
.\.venv\Scripts\Activate.ps1
alembic upgrade head
```

4. Ejecutar el seed inicial:

```powershell
python -m app.db.seed
```

Esto crea los roles iniciales y el usuario administrador.

### Opcion B - Alternativa: usar el script SQL

Si no se desea correr Alembic, se puede ejecutar el archivo:

- `database/scise_init.sql`

El script incluye:

- estructura base de tablas
- indices
- roles iniciales
- un administrador de prueba

Credenciales del admin del SQL:

- Correo: `admin@scise.sena.edu.co`
- Contrasena: `CambiarEstoInmediatamente123!`

Aun asi, para mantener el proyecto alineado con el flujo real del backend, la opcion recomendada sigue siendo `alembic upgrade head` + `python -m app.db.seed`.

## 5. Levantar el backend

Desde `SCISE-Backend`:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

Rutas utiles:
- API: `http://localhost:8000`
- Swagger: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`
- Health: `http://localhost:8000/health`

## 6. Levantar el frontend

Desde `SCISE-Frontend`:

```powershell
npm install
npm run dev
```

Ruta local:
- Frontend: `http://localhost:4321`

## 7. Credenciales de prueba

El usuario administrador inicial se crea con los datos configurados en el `.env` del backend.

Valores de prueba sugeridos:
- Correo: `admin@scise.sena.edu.co`
- Contrasena: `CambiarEstoInmediatamente123!`

## 8. Tecnologias esenciales usadas

### Frontend
- Astro
- TypeScript
- CSS modular
- `@zxing/browser` para lectura de carnet por camara

Se eligio este stack porque permite una interfaz modular, rapida y facil de mantener.

### Backend
- FastAPI
- SQLAlchemy
- Alembic
- Pydantic

Se uso porque facilita exponer una API clara, validada y mantenible, separando rutas, logica y acceso a datos.

### Base de datos
- PostgreSQL

Se eligio porque el proyecto trabaja con relaciones claras entre usuarios, roles, estudiantes, equipos, movimientos y auditoria.

## 9. Pruebas funcionales recomendadas

### Prueba 1 - Inicio de sesion
1. Abrir `http://localhost:4321/auth/login`
2. Iniciar sesion con el administrador
3. Verificar acceso a dashboard, reportes, usuarios y configuracion

### Prueba 2 - Crear estudiante
1. Ir a `Gestionar Usuarios`
2. Abrir `Nuevo Usuario`
3. Registrar un estudiante con documento, carnet, correo y telefono
4. Confirmar que aparezca en la tabla

### Prueba 3 - Registrar equipo
1. En `Gestionar Usuarios`, abrir `Registrar Equipo`
2. Identificar al estudiante por documento o carnet
3. Registrar un equipo nuevo
4. Confirmar que aparezca en la tabla de equipos

### Prueba 4 - Registrar ingreso
1. Ir a `Registrar Ingreso`
2. Buscar estudiante por documento o usar camara
3. Seleccionar un equipo asociado
4. Registrar el ingreso
5. Confirmar que el movimiento quede visible en dashboard y reportes

### Prueba 5 - Registrar salida
1. Ir a `Registrar Salida`
2. Buscar el mismo estudiante
3. Verificar que aparezcan solo equipos con ingreso activo
4. Registrar la salida
5. Confirmar el movimiento en historial

### Prueba 6 - Reportes
1. Entrar a `Panel de Administracion`
2. Filtrar por tipo y rango de fechas
3. Revisar historial
4. Exportar CSV o XLSX

### Prueba 7 - Configuracion
1. Entrar a `Configuracion`
2. Verificar gestion de usuarios del sistema
3. Verificar auditoria de accesos

## 10. Notas importantes

- Para probar la camara, usar `localhost` y conceder permisos en el navegador.
- El frontend usa `PUBLIC_API_BASE_URL`, por defecto `http://localhost:8000/api/v1`.
- Si se cambia el puerto del backend, se debe actualizar tambien el `.env` del frontend.
- El flujo recomendado para entornos locales es:
  1. levantar PostgreSQL
  2. correr migraciones o SQL
  3. ejecutar seed
  4. levantar backend
  5. levantar frontend

## 11. Estado funcional del proyecto

El sistema ya cuenta con:
- autenticacion por roles
- dashboard
- ingreso de equipos
- salida de equipos
- gestion de estudiantes y equipos
- reportes con filtros y exportacion CSV/XLSX
- configuracion y auditoria
- escaneo de carnet por camara en flujos clave

## 12. Recomendacion para evaluacion docente

Para una prueba completa del producto:
1. iniciar sesion como administrador
2. crear un estudiante
3. registrar un equipo asociado
4. realizar un ingreso
5. realizar una salida
6. validar el resultado en reportes y configuracion
