# SAAI Backend — Sistema de Análisis y Automatización de Inventario

Backend serverless multi-tenant para la gestión de tiendas. Desarrollado con **AWS Lambda + Python + DynamoDB** y desplegado mediante **Serverless Framework**.

---

## ✨ Características

- **Multi-tenant**: cada tienda opera en su propio espacio aislado
- **Autenticación JWT** con 3 roles: `worker`, `admin`, `saai`
- **Gestión completa**: productos, ventas, gastos, usuarios y tiendas
- **Analítica automática** cada 4 horas vía EventBridge
- **Machine Learning** (Holt-Winters) para predicción de demanda
- **Notificaciones en tiempo real** con WebSockets + SNS
- **Reportes Excel** almacenados en S3

---

## 🛠 Stack Tecnológico

| Capa | Tecnología |
|---|---|
| Runtime | Python 3.10 |
| Framework | Serverless Framework v3 |
| Compute | AWS Lambda |
| Base de datos | AWS DynamoDB |
| Almacenamiento | AWS S3 |
| Mensajería | AWS SNS |
| Colas | AWS SQS |
| Tiempo real | AWS API Gateway WebSocket |
| ML | Holt-Winters (contenedor Docker en ECR) |
| Auth | JWT (Lambda Authorizer) |

---

## Instalación y Despliegue

### Prerrequisitos

- Node.js ≥ 18
- Python ≥ 3.9
- Serverless Framework v3 (`npm install -g serverless`)
- AWS CLI configurado (credenciales de AWS Academy)
- Docker (para funciones ML)

### 1. Clonar e instalar dependencias

```bash
git clone <repo-url>
cd SAAI
npm install## Notas AWS Academy

- Se usa el rol `LabRole` preexistente (sin creación de roles IAM propios)
- SES no está disponible, el módulo de correo de bienvenida está deshabilitado
- Las credenciales de sesión expiran; renovar antes de cada deploy
pip install -r requirements.txt
```

### 2. Configurar variables de entorno

```bash
cp .env.example .env
# Edita .env con tu org de Serverless y tu cuenta AWS
```

> **Variables clave en `.env`:**
> - `SERVERLESS_ORG` — tu organización en [app.serverless.com](https://app.serverless.com)
> - `AWS_ACCOUNT_ID` — ID de tu cuenta AWS Academy

### 3. Desplegar

```bash
# Entorno de desarrollo
npm run deploy-dev

# Entorno de producción
npm run deploy-prod
```

### 4. Seed inicial (ejecutar una sola vez)

Después del primer deploy, crear el usuario SAAI raíz:

```bash
curl -X POST https://<api-id>.execute-api.us-east-1.amazonaws.com/dev/setup/seed-usuario-saai
```

---

## Estructura del Proyecto

```
SAAI/
├── auth/               # Login y Lambda Authorizer JWT
├── productos/          # CRUD productos
├── ventas/             # Registro y listado de ventas
├── gastos/             # CRUD gastos (solo admin)
├── usuarios/           # CRUD usuarios de tienda
├── tiendas/            # Gestión de tiendas (solo saai)
├── analytics/          # Analítica y métricas por tienda
├── reportes/           # Generación de reportes Excel en S3
├── notificaciones/     # Listado de notificaciones
├── websockets/         # Conexiones WebSocket en tiempo real
├── welcome/            # Automatizaciones post-registro de tienda
├── ml/                 # Predicción de demanda con Holt-Winters
├── setup/              # Seed de datos iniciales
├── utils/              # Utilidades compartidas
├── constants.py        # Constantes del sistema
├── serverless.yml      # Infraestructura como código
├── requirements.txt    # Dependencias Python
├── .env.example        # Plantilla de variables de entorno
└── README.md
```

---

## Roles del Sistema

| Rol | Descripción | Permisos |
|---|---|---|
| `worker` | Trabajador de tienda | Productos, Ventas |
| `admin` | Administrador de tienda | Todo lo anterior + Gastos, Usuarios, Analítica, Reportes |
| `saai` | Super administrador | Gestión de todas las tiendas |

---

## Endpoints Principales

| Método | Ruta | Rol mínimo |
|---|---|---|
| `POST` | `/login` | Público |
| `GET/POST` | `/productos` | worker |
| `GET/POST` | `/ventas` | worker |
| `GET/POST` | `/gastos` | admin |
| `GET/POST` | `/usuarios` | admin |
| `GET` | `/analitica` | admin |
| `POST` | `/reportes/{tipo}` | admin |
| `GET/POST` | `/tiendas` | saai |
| `GET` | `/predicciones` | admin |
| `GET` | `/notificacion` | worker |

> La colección Postman completa está en `SAAI_Backend_Complete.postman_collection.json`.

---

## Testing y Utilidades

```bash
# Ejecutar tests
npm run test

# Ver logs de una función
npm run logs -- --function LoginUsuario

# Info del despliegue actual
npm run info

# Eliminar stack
npm run remove
```

---
