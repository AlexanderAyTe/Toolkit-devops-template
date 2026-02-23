# Toolkit DevOps Template

Este repositorio centraliza workflows para la automatización de procesos, integraciones y despliegues con AWS Cloud. Contiene workflows reutilizables diseñados para ser implementados en diferentes proyectos.

## Estructura del Repositorio

- **[docs/](docs/)**: Documentación detallada de cada workflow
  - [Frontend Orchestrator Workflow](docs/frontend-orchestrator-workflow.md)
  - [Utils: Read Config Workflow](docs/utils-read-config-workflow.md)
  - [Node Unit Tests & Qodana Workflow](docs/node-unit-tests-qodana-workflow.md)
  - [Deploy SPA AWS Workflow](docs/deploy-spa-aws-workflow.md)
  - [CloudFormation Orchestrator Workflow](docs/cloudformation-orchestrator-workflow.md)
  - [Deploy CloudFormation Workflow](docs/deploy-cloudformation-workflow.md)

- **[uses/](uses/)**: Ejemplos de implementación para proyectos
  - **Frontend CI/CD**:
    - [frontend-workflow.yml](uses/frontend CI-CD/frontend-workflow.yml): Ejemplo de workflow principal
    - [workflow-config.json](uses/frontend CI-CD/workflow-config.json): Ejemplo de configuración
    - [qodana.yml](uses/frontend CI-CD/qodana.yml): Ejemplo de configuración de Qodana para análisis estático
  - **Infrastructure Deployment**:
    - [infrastructure-workflow.yml](uses/infrastructure-deployment/infrastructure-workflow.yml): Ejemplo de workflow para despliegue de infraestructura
    - [workflow-config.json](uses/infrastructure-deployment/workflow-config.json): Ejemplo de configuración

- **[.github/workflows/](.github/workflows/)**: Definiciones de los workflows
  - [frontend-orchestrator-workflow.yml](.github/workflows/frontend-orchestrator-workflow.yml)
  - [utils-read-config-workflow.yml](.github/workflows/utils-read-config-workflow.yml)
  - [deploy-spa-aws-workflow.yml](.github/workflows/deploy-spa-aws-workflow.yml)
  - [node-unit-tests-qodana-workflow.yml](.github/workflows/node-unit-tests-qodana-workflow.yml)
  - [deploy-cloudformation-workflow.yml](.github/workflows/deploy-cloudformation-workflow.yml)

## Workflows Disponibles

### 1. Frontend Orchestrator Workflow
Workflow orquestador que coordina el proceso completo de CI/CD para aplicaciones frontend.

### 2. Utils: Read Config Workflow
Workflow de utilidad que lee la configuración del proyecto y determina el entorno de destino.

### 3. Deploy SPA AWS Workflow
Workflow para el despliegue de aplicaciones de una sola página (SPA) a AWS.

### 4. Node Unit Tests & Qodana Workflow
Workflow para ejecutar pruebas unitarias y análisis de calidad de código.

### 5. Deploy CloudFormation Infrastructure Workflow
Workflow para desplegar infraestructura utilizando AWS CloudFormation. Permite crear nuevos stacks o actualizar stacks existentes.

## Implementación en tu Proyecto

Puedes implementar estos workflows en tu proyecto siguiendo las instrucciones detalladas en la documentación de ejemplos:

- [Implementación de CI/CD para Frontend](uses/frontend%20CI-CD/README.md)
- [Implementación de Despliegue de Infraestructura](uses/infrastructure-deployment/README.md)

#### Inicio Rápido (Frontend)

Para un inicio rápido en un proyecto frontend, copia y ajusta estos archivos de ejemplo:
- Copia `uses/frontend CI-CD/frontend-workflow.yml` a `.github/workflows/frontend-workflow.yml`
- Copia `uses/frontend CI-CD/workflow-config.json` a `./workflow-config.json`
- Copia `uses/frontend CI-CD/qodana.yml` a `./qodana.yml` (colócalo en la raíz del repositorio)

Luego revisa la documentación del workflow de pruebas y Qodana: [Node Unit Tests & Qodana Workflow](docs/node-unit-tests-qodana-workflow.md).

### Requisitos Principales

#### Para CI/CD de Frontend:
1. Archivo `.github/workflows/frontend-workflow.yml` que llame al workflow principal
2. Archivo `workflow-config.json` en la raíz del proyecto con la configuración específica
3. Archivo `qodana.yml` en la raíz del repositorio (puedes usar el ejemplo en `uses/frontend CI-CD/qodana.yml`)
4. Secretos configurados en el repositorio de GitHub:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `QODANA_TOKEN`

#### Para Despliegue de Infraestructura:
1. Archivo `.github/workflows/infrastructure-workflow.yml` que llame al workflow de despliegue
2. Archivo `workflow-config.json` en la raíz del proyecto con la configuración específica
3. Plantillas CloudFormation en el directorio `infrastructure/`
4. Secretos configurados en el repositorio de GitHub:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`

## Documentación Completa

Para obtener información detallada sobre cada workflow, consulta la [documentación completa](docs/README.md).
