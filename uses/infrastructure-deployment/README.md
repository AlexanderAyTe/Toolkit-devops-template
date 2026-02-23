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

- Determinación de entorno por rama:
  - `main` → entorno `prod`
  - `staging` → entorno `staging`
  - Cualquier otra rama → entorno `dev`
- Ejecución manual: al usar `workflow_dispatch`, puedes seleccionar el `environment` (`dev`, `staging`, `prod`).
- `stackName` en `workflow-config.json` se usa como prefijo; el orquestador agrega el sufijo del entorno automáticamente: `stackName-<env>`.
- `templateFile` en `workflow-config.json` debe apuntar a tu plantilla principal de CloudFormation (por ejemplo: `infrastructure/template.yml`).
- Archivos de parámetros por entorno son opcionales (si existen, se aplican de forma automática): `infrastructure/params/dev.json`, `staging.json`, `prod.json`.
- Puedes personalizar capacidades y tiempo de espera desde el archivo de workflow que llama al orquestador:
  - `capabilities: "CAPABILITY_IAM,CAPABILITY_NAMED_IAM"`
  - `timeout: 10` (minutos)
- Para proyectos más complejos, considera crear workflows separados por dominios de infraestructura o stacks.

## Recomendación: extensión cfn-lint para CloudFormation

Para mejorar la calidad de tus plantillas CloudFormation, te recomendamos instalar en tu editor la extensión del linter `cfn-lint` (AWS CloudFormation Linter).

- ¿Qué es? Valida plantillas YAML/JSON contra el esquema de CloudFormation y buenas prácticas. Detecta propiedades inválidas, tipos incorrectos, referencias rotas, parámetros no usados y más, antes de desplegar.
- Beneficios: feedback temprano, reducción de fallos en despliegues, reglas actualizadas con los últimos recursos de AWS y posibilidad de personalizar reglas.
- Dónde obtenerlo: disponible como extensión para VS Code ("cfn-lint") y otros editores populares. También puede ejecutarse por línea de comando.

Instalación rápida del CLI:

```bash
# Con pip (recomendado)
pip install cfn-lint

# En macOS con Homebrew
brew install cfn-lint
```

Uso básico:

```bash
# Validar una plantilla específica
cfn-lint infrastructure/template.yaml

# Validar recursivamente todas las plantillas del proyecto
cfn-lint .
```

Sugerencia: si usas VS Code, instala la extensión "cfn-lint" y asegúrate de que el binario `cfn-lint` esté disponible en tu PATH para obtener diagnósticos en tiempo real al editar tus plantillas.
