# Ejemplo de Despliegue de Infraestructura con CloudFormation

Este ejemplo muestra cómo implementar el workflow de despliegue de infraestructura con CloudFormation en tu proyecto.

## Estructura de Archivos

Para implementar este workflow, necesitarás la siguiente estructura de archivos en tu proyecto:

```
├── .github/
│   └── workflows/
│       └── infrastructure-workflow.yml  # Workflow principal
├── infrastructure/
│   ├── template.yaml                   # Plantilla CloudFormation
│   └── params/                         # Parámetros por entorno
│       ├── dev.json
│       ├── staging.json
│       └── prod.json
└── workflow-config.json                # Configuración del workflow
```

## Pasos para la Implementación

1. **Crear el archivo de workflow**

   Crea el archivo `.github/workflows/infrastructure-workflow.yml` en tu proyecto con el contenido del [ejemplo proporcionado](./infrastructure-workflow.yml).

2. **Configurar el archivo workflow-config.json**

   Crea o actualiza el archivo `workflow-config.json` en la raíz de tu proyecto siguiendo el [ejemplo proporcionado](./workflow-config.json). Asegúrate de personalizar los nombres de los stacks y otros parámetros según tus necesidades.

3. **Crear la plantilla CloudFormation**

   Crea tu plantilla CloudFormation en `infrastructure/template.yaml`. Esta plantilla definirá los recursos de AWS que deseas crear.

4. **Crear archivos de parámetros**

   Crea archivos de parámetros para cada entorno en `infrastructure/params/`:
   
   - `dev.json`: Parámetros para el entorno de desarrollo
   - `staging.json`: Parámetros para el entorno de staging
   - `prod.json`: Parámetros para el entorno de producción

## Ejemplo de Archivo de Parámetros

Los archivos de parámetros deben seguir el formato de CloudFormation:

```json
[
  {
    "ParameterKey": "EnvironmentType",
    "ParameterValue": "Development"
  },
  {
    "ParameterKey": "InstanceType",
    "ParameterValue": "t2.micro"
  },
  {
    "ParameterKey": "MaxCapacity",
    "ParameterValue": "2"
  },
  {
    "ParameterKey": "MinCapacity",
    "ParameterValue": "1"
  }
]
```

## Secretos Requeridos

Asegúrate de configurar los siguientes secretos en tu repositorio de GitHub:

- `AWS_ACCESS_KEY_ID`: Clave de acceso para AWS
- `AWS_SECRET_ACCESS_KEY`: Clave secreta para AWS

## Variables Opcionales

Puedes configurar las siguientes variables en tu repositorio de GitHub:

- `AWS_REGION`: Región de AWS (por defecto: 'us-east-1')

## Ejecución del Workflow

El workflow se ejecutará automáticamente cuando:

1. Se realice un push a las ramas `main`, `staging` o `develop` que incluya cambios en el directorio `infrastructure/`.
2. Se active manualmente desde la interfaz de GitHub Actions, seleccionando el entorno deseado.

## Notas Adicionales

- El workflow determina automáticamente el entorno basado en la rama:
  - `main` → entorno `prod`
  - `staging` → entorno `staging`
  - Cualquier otra rama → entorno `dev`
- Puedes personalizar el workflow según tus necesidades específicas.
- Para proyectos más complejos, considera crear workflows separados para diferentes componentes de infraestructura.
