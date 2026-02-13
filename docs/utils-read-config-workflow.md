# Workflow de Utilidad: Leer Configuración

## Descripción
Este workflow es una utilidad que lee la configuración del proyecto desde el archivo `workflow-config.json` y determina el entorno de destino basado en la rama actual. Proporciona valores de configuración como salidas que pueden ser utilizadas por otros workflows.

## Ubicación
`.github/workflows/utils-read-config-workflow.yml`

## Entradas
Este workflow no requiere entradas.

## Salidas
| Nombre | Descripción |
|--------|-------------|
| target_env | Entorno de destino (dev, staging, prod) basado en la rama actual |
| bucket | Nombre del bucket S3 para el entorno actual |
| stack | Nombre del stack de CloudFormation para el entorno actual |
| package_dir | Directorio de salida del build (desde workflow-config.json) |
| build_script | Script de build a ejecutar (build:dev, build:staging, build:prod) |

## Requisitos
- Archivo `workflow-config.json` en la raíz del proyecto con la siguiente estructura:
```json
{
  "Package": "dist",
  "dev": {
    "bucketName": "bucket-front-dev",
    "stackName": "stack-front-dev"
  },
  "staging": {
    "bucketName": "bucket-front-staging",
    "stackName": "stack-front-staging"
  },
  "prod": {
    "bucketName": "bucket-front-prod",
    "stackName": "stack-front-prod"
  }
}
```

## Lógica de Determinación del Entorno
- Si la rama es `main` → entorno `prod`
- Si la rama es `staging` → entorno `staging`
- Para cualquier otra rama → entorno `dev`

## Ejemplo de Uso
Este workflow está diseñado para ser llamado por otros workflows:

```yaml
jobs:
  setup:
    uses: AYTECOL/devops-toolkit-central-templates/.github/workflows/utils-read-config-workflow.yml@main

  deploy:
    needs: setup
    # Ahora puedes usar los valores de salida
    # Por ejemplo: ${{ needs.setup.outputs.target_env }}
```

## Notas Adicionales
- Este workflow simplifica la configuración de despliegues multi-entorno
- Centraliza la configuración en un solo archivo JSON
- Automatiza la selección del entorno basado en la rama Git
