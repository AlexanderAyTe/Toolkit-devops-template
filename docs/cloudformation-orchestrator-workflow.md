# CloudFormation Orchestrator Workflow

## Descripción
Workflow orquestador para despliegue de infraestructura con AWS CloudFormation. Resuelve configuración común (entorno, región, rutas, nombres de stack) y delega el despliegue al workflow de CloudFormation.

Última actualización: 2026-02-22

## Ubicación
`.github/workflows/cloudformation-orchestrator-workflow.yml`

## Entradas
| Nombre | Tipo | Requerido | Valor por defecto | Descripción |
|--------|------|-----------|-------------------|-------------|
| capabilities | string | Sí | - | Capacidades de CloudFormation (ej.: `CAPABILITY_IAM,CAPABILITY_NAMED_IAM`) |
| wait-for-completion | boolean | Sí | - | Si se debe esperar a que finalice la operación del stack |
| timeout | number | Sí | - | Tiempo máximo de espera (minutos) para la creación/actualización |

## Secretos Requeridos
- **AWS_ACCESS_KEY_ID**: Clave de acceso para AWS
- **AWS_SECRET_ACCESS_KEY**: Clave secreta para AWS

## Variables Opcionales
- **AWS_REGION**: Región de AWS (si no se define en `workflow-config.json`, se usa `us-east-1`)

## Flujo de Trabajo
1. **Setup** (utils-read-config):
   - Determina el entorno (`dev`, `staging`, `prod`) según la rama o `workflow_dispatch`.
   - Lee `region`, `stackName`, y `templateFile` de `workflow-config.json`.
   - Expone salidas: `region`, `target_env`, `template_file_infra`, `stack` (con sufijo por entorno) y más.
2. **Deploy Infra** (deploy-cloudformation):
   - Valida la plantilla.
   - Crea o actualiza el stack según corresponda.
   - Usa `parameters-file` basado en el entorno (`infrastructure/params/<env>.json`).

## Ejemplo de Uso
```yaml
name: Deploy Infrastructure

on:
  push:
    branches: [main, staging, develop]
    paths:
      - 'infrastructure/**'
  pull_request:
    branches: [main, staging, develop]
    paths:
      - 'infrastructure/**'
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy to'
        required: true
        default: 'dev'
        type: choice
        options: [dev, staging, prod]

jobs:
  execute:
    uses: AYTECOL/devops-toolkit-central-templates/.github/workflows/cloudformation-orchestrator-workflow.yml@main
    with:
      capabilities: "CAPABILITY_IAM,CAPABILITY_NAMED_IAM"
      wait-for-completion: true
      timeout: 10
    secrets: inherit
```

## Configuración del Proyecto
Asegúrate de tener un archivo `workflow-config.json` en la raíz del repositorio con al menos:

```json
{
  "stackName": "mi-aplicacion-infra",
  "region": "us-east-1",
  "templateFile": "infrastructure/template.yml"
}
```

Y archivos de parámetros por entorno (opcional) en `infrastructure/params/`:
- `dev.json`
- `staging.json`
- `prod.json`

## Notas Adicionales
- El orquestador utiliza el valor de `environment` si se ejecuta por `workflow_dispatch`; en otros casos lo resuelve por rama (`main`→`prod`, `staging`→`staging`, resto→`dev`).
- El nombre real del stack utilizado es `stackName-<env>` (por ejemplo `mi-aplicacion-infra-dev`).
- Si CloudFormation indica "No updates are to be performed", el flujo lo trata como éxito.
