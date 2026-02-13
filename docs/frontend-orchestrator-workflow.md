# Frontend Orchestrator Workflow

## Descripción
Este workflow orquesta el proceso completo de CI/CD para aplicaciones frontend. Coordina la configuración, pruebas de calidad y despliegue de aplicaciones de una sola página (SPA) a AWS.

## Ubicación
`.github/workflows/frontend-orchestrator-workflow.yml`

## Entradas
| Nombre | Tipo | Requerido | Valor por defecto | Descripción |
|--------|------|-----------|-------------------|-------------|
| node-version | string | Sí | - | Versión de Node.js a utilizar |
| disabled-test | boolean | No | false | Deshabilita la ejecución de pruebas |
| disabled-deploy | boolean | No | false | Deshabilita el despliegue |
| secret-qodana | string | No | "" | Token para Qodana (opcional) |

## Secretos Requeridos
- **AWS_ACCESS_KEY_ID**: Clave de acceso para AWS
- **AWS_SECRET_ACCESS_KEY**: Clave secreta para AWS
- **QODANA_TOKEN**: Token para el análisis de código con Qodana

## Variables Opcionales
- **AWS_REGION**: Región de AWS (por defecto: 'us-east-1')
- **ORG_MIN_COVERAGE**: Cobertura mínima requerida (por defecto: 80)
- **ORG_MAX_DUPLICATION**: Duplicación máxima permitida (por defecto: 5)
- **ORG_QODANA_SEVERITY**: Nivel de severidad para Qodana (por defecto: 'high')

## Flujo de Trabajo
1. **Configuración**: Lee la configuración del proyecto desde `workflow-config.json`
2. **Calidad (CI)**: Ejecuta pruebas unitarias y análisis de código con Qodana
3. **Despliegue (CD)**: Despliega la aplicación a AWS S3 y actualiza la distribución CloudFront

## Ejemplo de Uso
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
      node-version: "22.12.0"
      disabled-test: false
      disabled-deploy: false
    secrets: inherit
```

## Notas Adicionales
- Este workflow requiere un archivo `workflow-config.json` en la raíz del proyecto
- El despliegue solo se ejecuta si las pruebas de calidad son exitosas
- Se puede deshabilitar el despliegue para entornos que no tienen infraestructura configurada
