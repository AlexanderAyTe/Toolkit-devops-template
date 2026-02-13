# Deploy CloudFormation Infrastructure Workflow

## Descripción
Este workflow permite desplegar infraestructura utilizando AWS CloudFormation. Puede crear nuevos stacks o actualizar stacks existentes, validar plantillas, y esperar a que se complete la operación.

## Ubicación
`.github/workflows/deploy-cloudformation-workflow.yml`

## Entradas
| Nombre | Tipo | Requerido | Valor por defecto | Descripción |
|--------|------|-----------|-------------------|-------------|
| env | string | Sí | - | Entorno de despliegue (dev, staging, prod) |
| template-file | string | Sí | - | Ruta al archivo de plantilla CloudFormation |
| parameters-file | string | No | - | Ruta al archivo de parámetros CloudFormation (opcional) |
| stack-name | string | Sí | - | Nombre del stack CloudFormation |
| capabilities | string | No | CAPABILITY_IAM | Capacidades de CloudFormation (separadas por comas, ej: CAPABILITY_IAM,CAPABILITY_NAMED_IAM) |
| timeout | number | No | 30 | Tiempo de espera en minutos para la creación/actualización del stack |
| wait-for-completion | boolean | No | true | Si se debe esperar a que se complete la operación del stack |

## Secretos Requeridos
- **AWS_ACCESS_KEY_ID**: Clave de acceso para AWS
- **AWS_SECRET_ACCESS_KEY**: Clave secreta para AWS

## Variables Opcionales
- **AWS_REGION**: Región de AWS (por defecto: 'us-east-1')

## Flujo de Trabajo
1. **Validación**: Valida la plantilla CloudFormation
2. **Verificación**: Comprueba si el stack existe para determinar si crear o actualizar
3. **Despliegue**: Crea o actualiza el stack CloudFormation
4. **Espera**: Espera a que se complete la operación (si está habilitado)
5. **Resultados**: Recupera y muestra las salidas del stack

## Ejemplo de Uso
```yaml
name: Deploy Infrastructure

on:
  push:
    branches: [main, staging, develop]
    paths:
      - 'infrastructure/**'

jobs:
  setup:
    uses: AYTECOL/devops-toolkit-central-templates/.github/workflows/utils-read-config-workflow.yml@main

  deploy-infra:
    needs: setup
    uses: AYTECOL/devops-toolkit-central-templates/.github/workflows/deploy-cloudformation-workflow.yml@main
    with:
      env: ${{ needs.setup.outputs.target_env }}
      template-file: 'infrastructure/template.yaml'
      parameters-file: 'infrastructure/params/${{ needs.setup.outputs.target_env }}.json'
      stack-name: ${{ needs.setup.outputs.stack }}
      capabilities: 'CAPABILITY_IAM,CAPABILITY_NAMED_IAM'
      timeout: 45
      wait-for-completion: true
    secrets: inherit
```

## Configuración del Proyecto
Para utilizar este workflow, tu proyecto debe tener:

1. Un archivo `workflow-config.json` en la raíz con la configuración específica para cada entorno:
```json
{
  "dev": {
    "stackName": "mi-proyecto-dev"
  },
  "staging": {
    "stackName": "mi-proyecto-staging"
  },
  "prod": {
    "stackName": "mi-proyecto-prod"
  }
}
```

2. Plantillas CloudFormation en el proyecto (por ejemplo, en un directorio `infrastructure/`)
3. Opcionalmente, archivos de parámetros para cada entorno

## Notas Adicionales
- El workflow detecta automáticamente si debe crear un nuevo stack o actualizar uno existente
- Proporciona información detallada sobre el progreso y los resultados del despliegue
- Maneja errores y tiempos de espera de manera adecuada
- Las salidas del stack CloudFormation se muestran en los logs para referencia
