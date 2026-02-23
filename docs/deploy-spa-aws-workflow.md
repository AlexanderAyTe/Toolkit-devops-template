# Workflow de Despliegue: SPA en AWS

## Descripción
Este workflow se encarga del despliegue de aplicaciones de una sola página (SPA) a AWS. Realiza el build de la aplicación, sube los archivos a un bucket S3 y actualiza la distribución CloudFront para invalidar la caché.

## Ubicación
`.github/workflows/deploy-spa-aws-workflow.yml`

## Entradas
| Nombre | Tipo | Requerido | Descripción |
|--------|------|-----------|-------------|
| node-version | string | Sí | Versión de Node.js a utilizar |
| env | string | Sí | Entorno de despliegue (dev, staging, prod) |
| dist-path | string | Sí | Ruta al directorio de distribución (build) |
| s3-bucket-name | string | Sí | Nombre del bucket S3 donde se desplegará |
| stack-name | string | Sí | Nombre del stack de CloudFormation |
| build-script | string | Sí | Script de build a ejecutar (ej: "build:dev") |

## Secretos Requeridos
- **AWS_ACCESS_KEY_ID**: Clave de acceso para AWS
- **AWS_SECRET_ACCESS_KEY**: Clave secreta para AWS

## Variables Opcionales
- **AWS_REGION**: Región de AWS (por defecto: 'us-east-1')

## Flujo de Trabajo
1. **Configuración de Node.js**: Instala la versión especificada de Node.js
2. **Build de la Aplicación**: Ejecuta el script de build especificado
3. **Configuración de AWS**: Configura las credenciales de AWS
4. **Sincronización con S3**: Sube los archivos al bucket S3 y elimina archivos obsoletos
5. **Invalidación de CloudFront**: Crea una invalidación para actualizar la caché de CloudFront

## Requisitos de Infraestructura
- Un bucket S3 configurado para hosting estático
- Una distribución CloudFront asociada al bucket S3
- Un stack de CloudFormation que incluya un output llamado 'CloudFrontDistributionId'

## Ejemplo de Uso
Este workflow está diseñado para ser llamado por otros workflows:

```yaml
jobs:
  deploy:
    uses: AYTECOL/devops-toolkit-central-templates/.github/workflows/deploy-spa-aws-workflow.yml@main
    with:
      node-version: "22.12.0"
      env: "prod"
      dist-path: "dist"
      s3-bucket-name: "mi-bucket-prod"
      stack-name: "mi-stack-prod"
      build-script: "build:prod"
    secrets: inherit
```

## Notas Adicionales
- Resolución del bucket S3 (frontend): el workflow primero intenta obtener el nombre del bucket desde el output del stack de CloudFormation con la clave `BucketNameFrontend`. Si este output no existe o no tiene valor, usa el valor provisto en la entrada `s3-bucket-name` (normalmente proveniente de `workflow-config.json`).
- Se espera que el stack de CloudFormation tenga un output llamado `CloudFrontDistributionId` para poder realizar la invalidación.
- Se utiliza la opción `--delete` en la sincronización con S3 para eliminar archivos obsoletos.
- La invalidación de CloudFront afecta a todos los archivos (`/*`).
