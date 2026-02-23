# Ejemplos de Uso de Workflows

Este directorio contiene ejemplos de cómo implementar los workflows de CI/CD en proyectos frontend. Los archivos de ejemplo son:

- `frontend-workflow.yml`: Ejemplo de workflow principal para CI/CD
- `workflow-config.json`: Ejemplo de configuración para los workflows
- `qodana.yml`: Ejemplo de configuración de Qodana (debes copiarlo a la raíz de tu repo)

## Implementación en tu Proyecto

### 1. Configuración del Workflow

Para implementar el pipeline CI/CD en tu proyecto, debes crear un archivo `.github/workflows/frontend-workflow.yml` con el siguiente contenido (basado en el ejemplo):

```yaml
name: Pipeline CI/CD

on:
  push:
    branches: [main, staging, develop]
  pull_request:
    branches: [main, staging, develop]

jobs:
  execute:
    uses: AYTECOL/devops-toolkit-central-templates/.github/workflows/frontend-orchestrator-workflow.yml@main
    with:
      node-version: "22.12.0" # Ajusta a la versión de Node.js que necesites
      disabled-test: false # Cambiar a true si no hay tests
      disabled-deploy: false # Cambiar a true si la infra no existe
    secrets: inherit
```

También puedes usar el workflow orquestador con opciones personalizadas:

```yaml
name: Build Frontend

on:
  push:
    branches: [develop]
  pull_request:
    branches: [develop]

jobs:
  build:
    uses: AYTECOL/devops-toolkit-central-templates/.github/workflows/frontend-orchestrator-workflow.yml@main
    with:
      node-version: "22.12.0"
      disabled-test: false
      disabled-deploy: true
    secrets: inherit
```

### 2. Configuración del Proyecto (workflow-config.json)

Crea un archivo `workflow-config.json` en la raíz de tu proyecto con la siguiente estructura mínima requerida por los workflows:

```json
{
  "AppName": "nombre-de-tu-app",
  "Support": "email@tuempresa.com",
  "Package": "dist",
  "stackName": "stack-infra-base",
  "region": "us-east-1",
  "dev": {
    "bucketName": "bucket-nombre-dev"
  },
  "staging": {
    "bucketName": "bucket-nombre-staging"
  },
  "prod": {
    "bucketName": "bucket-nombre-prod"
  }
}
```

Notas:
- `Package` es la carpeta donde queda el build de la app (por ejemplo: `dist`, `build`).
- `stackName` se define a nivel raíz y el workflow automáticamente le agrega el sufijo del entorno (`-dev`, `-staging`, `-prod`).
- `region` es opcional; si no se define, se usa `us-east-1` por defecto.

### 3. Scripts de build en package.json (nomenclatura estándar)

El orquestador resuelve el script de build automáticamente según la rama. Debes definir en tu `package.json` los siguientes scripts con estos NOMBRES estándar (el contenido del script lo ajusta el equipo según su build system: Vite, Next, Angular, React, etc.):

```json
{
  "scripts": {
    "build:dev": "vite build --mode development",
    "build:staging": "vite build --mode staging",
    "build:prod": "vite build --mode production"
  }
}
```

Mapeo de rama a entorno y script ejecutado:
- Rama `main` → entorno `prod` → ejecuta `npm run build:prod`
- Rama `staging` → entorno `staging` → ejecuta `npm run build:staging`
- Cualquier otra rama → entorno `dev` → ejecuta `npm run build:dev`

Importante: el estándar requerido para el despliegue son SOLO los nombres `build:dev`, `build:staging` y `build:prod`. El contenido de cada script puede variar según tu proyecto.

### 4. Archivo de Qodana

Para el análisis de calidad con Qodana, coloca un archivo `qodana.yml` en la raíz del repositorio. Puedes copiar el ejemplo provisto en este repositorio desde `uses/frontend CI-CD/qodana.yml`.

## Secretos Requeridos

Para que los workflows funcionen correctamente, debes configurar los siguientes secretos en tu repositorio de GitHub:

| Secreto | Descripción | Requerido por |
|---------|-------------|---------------|
| `AWS_ACCESS_KEY_ID` | Clave de acceso para AWS | deploy-spa-aws-workflow |
| `AWS_SECRET_ACCESS_KEY` | Clave secreta para AWS | deploy-spa-aws-workflow |
| `QODANA_TOKEN` | Token para el análisis de código con Qodana | node-unit-tests-qodana-workflow |

## Variables Opcionales

Puedes configurar las siguientes variables a nivel de organización o repositorio:

| Variable | Descripción | Valor por defecto |
|----------|-------------|-------------------|
| `AWS_REGION` | Región de AWS | us-east-1 |
| `ORG_MIN_COVERAGE` | Cobertura mínima requerida | 80 |
| `ORG_MAX_DUPLICATION` | Duplicación máxima permitida | 5 |
| `ORG_FAIL_THRESHOLD` | Umbral de fallo de Qodana (máx. número de problemas permitidos; 0 no falla por cantidad) | 0 |
| `ORG_QODANA_SEVERITY` | Nivel de severidad para Qodana | high |

## Requisitos de Infraestructura

Para que el despliegue funcione correctamente, debes tener:

1. Un bucket S3 configurado para hosting estático
2. Una distribución CloudFront asociada al bucket S3
3. Un stack de CloudFormation que incluya un output llamado 'CloudFrontDistributionId'

### Resolución del nombre del bucket (frontend)

Para el despliegue del frontend, el workflow puede obtener el nombre del bucket S3 de dos maneras:
- Desde `workflow-config.json`: usando la propiedad `bucketName` del entorno actual.
- Desde el stack de CloudFormation: leyendo el output con clave `BucketNameFrontend`.

Prioridad: si el output `BucketNameFrontend` existe y tiene valor, se usará ese bucket. Si no existe o está vacío, se usará el `bucketName` definido en `workflow-config.json`.

## Personalización

- **Deshabilitar Tests**: Si tu proyecto no tiene tests, cambia `disabled-test: true` en el workflow.
- **Deshabilitar Despliegue**: Si aún no tienes infraestructura, cambia `disabled-deploy: true` en el workflow.
- **Cambiar Versión de Node.js**: Ajusta el valor de `node-version` según las necesidades de tu proyecto.

## Comportamiento por Entorno

El workflow determina automáticamente el entorno basado en la rama:
- Rama `main` → entorno `prod`
- Rama `staging` → entorno `staging`
- Cualquier otra rama → entorno `dev`
