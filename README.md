# Toolkit DevOps Template

Este repositorio centraliza workflows para la automatización de procesos, integraciones y despliegues con AWS Cloud. Contiene workflows reutilizables diseñados para ser implementados en diferentes proyectos.

## Estructura del Repositorio

- **[docs/](docs/)**: Documentación detallada de cada workflow
  - [Frontend Orchestrator Workflow](docs/frontend-orchestrator-workflow.md)
  - [Utils: Read Config Workflow](docs/utils-read-config-workflow.md)
  - [Deploy SPA AWS Workflow](docs/deploy-spa-aws-workflow.md)
  - [Node Unit Tests & Qodana Workflow](docs/node-unit-tests-qodana-workflow.md)

- **[uses/](uses/)**: Ejemplos de implementación para proyectos
  - [frontend-workflow.yml](uses/frontend CI-CD/frontend-workflow.yml): Ejemplo de workflow principal
  - [workflow-config.json](uses/frontend CI-CD/workflow-config.json): Ejemplo de configuración

- **[.github/workflows/](.github/workflows/)**: Definiciones de los workflows
  - [frontend-orchestrator-workflow.yml](.github/workflows/frontend-orchestrator-workflow.yml)
  - [utils-read-config-workflow.yml](.github/workflows/utils-read-config-workflow.yml)
  - [deploy-spa-aws-workflow.yml](.github/workflows/deploy-spa-aws-workflow.yml)
  - [node-unit-tests-qodana-workflow.yml](.github/workflows/node-unit-tests-qodana-workflow.yml)

## Workflows Disponibles

### 1. Frontend Orchestrator Workflow
Workflow orquestador que coordina el proceso completo de CI/CD para aplicaciones frontend.

### 2. Utils: Read Config Workflow
Workflow de utilidad que lee la configuración del proyecto y determina el entorno de destino.

### 3. Deploy SPA AWS Workflow
Workflow para el despliegue de aplicaciones de una sola página (SPA) a AWS.

### 4. Node Unit Tests & Qodana Workflow
Workflow para ejecutar pruebas unitarias y análisis de calidad de código.

## Implementación en tu Proyecto

Para implementar estos workflows en tu proyecto, sigue las instrucciones detalladas en la [documentación de ejemplos](uses/frontend CI-CD/README.md).

### Requisitos Principales

1. Archivo `.github/workflows/frontend-workflow.yml` que llame al workflow principal
2. Archivo `workflow-config.json` en la raíz del proyecto con la configuración específica
3. Secretos configurados en el repositorio de GitHub:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `QODANA_TOKEN`

## Documentación Completa

Para obtener información detallada sobre cada workflow, consulta la [documentación completa](docs/README.md).
