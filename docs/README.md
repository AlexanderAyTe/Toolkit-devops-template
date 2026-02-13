# Documentación de Workflows

Esta carpeta contiene la documentación detallada de los workflows disponibles en este repositorio. Estos workflows están diseñados para ser reutilizables y configurables para diferentes proyectos, centralizando la automatización de procesos, integraciones y despliegues con AWS Cloud.

## Workflows Disponibles

### 1. [Frontend Orchestrator Workflow](frontend-orchestrator-workflow.md)
Workflow orquestador que coordina el proceso completo de CI/CD para aplicaciones frontend. Gestiona la configuración, pruebas de calidad y despliegue.

### 2. [Utils: Read Config Workflow](utils-read-config-workflow.md)
Workflow de utilidad que lee la configuración del proyecto desde el archivo `workflow-config.json` y determina el entorno de destino basado en la rama actual.

### 3. [Deploy SPA AWS Workflow](deploy-spa-aws-workflow.md)
Workflow para el despliegue de aplicaciones de una sola página (SPA) a AWS. Realiza el build de la aplicación, sube los archivos a un bucket S3 y actualiza la distribución CloudFront.

### 4. [Node Unit Tests & Qodana Workflow](node-unit-tests-qodana-workflow.md)
Workflow para ejecutar pruebas unitarias y análisis de calidad de código utilizando Qodana para proyectos Node.js.

## Ejemplos de Uso

En la carpeta [uses](../uses/) encontrarás ejemplos de cómo implementar estos workflows en tus proyectos:

- `frontend-workflow.yml`: Ejemplo de workflow principal para CI/CD
- `workflow-config.json`: Ejemplo de configuración para los workflows
- `README.md`: Instrucciones detalladas sobre cómo usar los ejemplos

## Secretos y Variables

### Secretos Requeridos

| Secreto | Descripción | Requerido por |
|---------|-------------|---------------|
| `AWS_ACCESS_KEY_ID` | Clave de acceso para AWS | deploy-spa-aws-workflow |
| `AWS_SECRET_ACCESS_KEY` | Clave secreta para AWS | deploy-spa-aws-workflow |
| `QODANA_TOKEN` | Token para el análisis de código con Qodana | node-unit-tests-qodana-workflow |

### Variables Opcionales

| Variable | Descripción | Valor por defecto |
|----------|-------------|-------------------|
| `AWS_REGION` | Región de AWS | us-east-1 |
| `ORG_MIN_COVERAGE` | Cobertura mínima requerida | 80 |
| `ORG_MAX_DUPLICATION` | Duplicación máxima permitida | 5 |
| `ORG_QODANA_SEVERITY` | Nivel de severidad para Qodana | high |

## Configuración del Proyecto

Para utilizar estos workflows, tu proyecto debe tener un archivo `workflow-config.json` en la raíz con la configuración específica para cada entorno (dev, staging, prod). Consulta el [ejemplo de configuración](../uses/frontend%20CI-CD/workflow-config.json) para más detalles.
