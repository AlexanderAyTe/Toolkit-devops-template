# Ejemplos de Uso de Workflows

Este directorio contiene ejemplos de cómo implementar los workflows de CI/CD en proyectos frontend. Los archivos de ejemplo son:

- `frontend-workflow.yml`: Ejemplo de workflow principal para CI/CD
- `workflow-config.json`: Ejemplo de configuración para los workflows

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

### 2. Configuración del Proyecto

Crea un archivo `workflow-config.json` en la raíz de tu proyecto con la siguiente estructura:

```json
{
  "AppName": "nombre-de-tu-app",
  "Package": "dist",
  "TrouxUUID": "ID-de-tu-proyecto",
  "Support": "email@tuempresa.com",
  "Testing": {
    "Path": "test/",
    "lcovReportPath": "coverage/lcov.info"
  },
  "dev": {
    "bucketName": "bucket-nombre-dev",
    "stackName": "stack-nombre-dev"
  },
  "staging": {
    "bucketName": "bucket-nombre-staging",
    "stackName": "stack-nombre-staging"
  },
  "prod": {
    "bucketName": "bucket-nombre-prod",
    "stackName": "stack-nombre-prod"
  }
}
```

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
| `ORG_QODANA_SEVERITY` | Nivel de severidad para Qodana | high |

## Requisitos de Infraestructura

Para que el despliegue funcione correctamente, debes tener:

1. Un bucket S3 configurado para hosting estático
2. Una distribución CloudFront asociada al bucket S3
3. Un stack de CloudFormation que incluya un output llamado 'CloudFrontDistributionId'

## Personalización

- **Deshabilitar Tests**: Si tu proyecto no tiene tests, cambia `disabled-test: true` en el workflow.
- **Deshabilitar Despliegue**: Si aún no tienes infraestructura, cambia `disabled-deploy: true` en el workflow.
- **Cambiar Versión de Node.js**: Ajusta el valor de `node-version` según las necesidades de tu proyecto.

## Comportamiento por Entorno

El workflow determina automáticamente el entorno basado en la rama:
- Rama `main` → entorno `prod`
- Rama `staging` → entorno `staging`
- Cualquier otra rama → entorno `dev`
