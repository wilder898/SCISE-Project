# SCISE

SCISE es una plataforma web para el control de ingreso y salida de equipos. El proyecto esta dividido en dos aplicaciones:

- `SCISE-Backend`: API REST construida con FastAPI
- `SCISE-Frontend`: interfaz web construida con Astro

Este README esta pensado para que cualquiera pueda levantar el proyecto, preparar la base de datos y ejecutar pruebas funcionales basicas.

## 1. Requisitos

### Backend
- Python 3.12 recomendado
- PostgreSQL 14 o superior
- PowerShell o terminal compatible

### Frontend
- Node.js 20 o superior
- npm 10 o superior

## 2. Estructura del proyecto

```
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

El script incluye tambien:

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

Valores deprueba:
- Correo: `admin@scise.sena.edu.co`
- Contrasena: `Admin123*`

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
