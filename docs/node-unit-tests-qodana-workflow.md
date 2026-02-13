# Workflow de Calidad: Tests Unitarios y Qodana

## Descripción
Este workflow se encarga de ejecutar pruebas unitarias y análisis de calidad de código utilizando Qodana para proyectos Node.js. Verifica la cobertura de código, detecta duplicaciones y vulnerabilidades.

## Ubicación
`.github/workflows/node-unit-tests-qodana-workflow.yml`

## Entradas
| Nombre | Tipo | Requerido | Valor por defecto | Descripción |
|--------|------|-----------|-------------------|-------------|
| node-version | string | Sí | - | Versión de Node.js a utilizar |
| disabled-test | boolean | No | false | Deshabilita la ejecución de pruebas |
| working-directory | string | No | "." | Directorio de trabajo para ejecutar comandos |

## Secretos Requeridos
- **QODANA_TOKEN**: Token para el análisis de código con Qodana

## Variables Opcionales
- **ORG_MIN_COVERAGE**: Cobertura mínima requerida (por defecto: 80)
- **ORG_MAX_DUPLICATION**: Duplicación máxima permitida (por defecto: 5)
- **ORG_QODANA_SEVERITY**: Nivel de severidad para Qodana (por defecto: 'high')

## Flujo de Trabajo
1. **Checkout del Código**: Obtiene el código fuente
2. **Configuración de Node.js**: Instala la versión especificada de Node.js
3. **Instalación de Dependencias**: Ejecuta `npm ci`
4. **Ejecución de Pruebas**: Ejecuta las pruebas unitarias con cobertura (si no están deshabilitadas)
5. **Definición de Umbrales de Calidad**: Establece los umbrales de calidad basados en variables de la organización
6. **Análisis con Qodana**: Ejecuta el análisis de código con Qodana
7. **Verificación de Quality Gate**: Falla el workflow si no se cumplen los criterios de calidad

## Parámetros de Qodana
- **Cobertura objetivo**: Definido por `ORG_MIN_COVERAGE` o 80% por defecto (0% si las pruebas están deshabilitadas)
- **Duplicación máxima**: Definido por `ORG_MAX_DUPLICATION` o 5% por defecto
- **Severidad**: Definido por `ORG_QODANA_SEVERITY` o 'high' por defecto
- **Exclusiones**: node_modules, build, dist, coverage, archivos de prueba

## Ejemplo de Uso
Este workflow está diseñado para ser llamado por otros workflows:

```yaml
jobs:
  quality:
    uses: AYTECOL/devops-toolkit-central-templates/.github/workflows/node-unit-tests-qodana-workflow.yml@main
    with:
      node-version: "22.12.0"
      disabled-test: false
      working-directory: "."
    secrets: inherit
```

## Notas Adicionales
- Si `disabled-test` es `true`, la cobertura requerida se establece en 0%
- El workflow fallará si no se cumplen los criterios de calidad (cobertura, duplicación, vulnerabilidades)
- Se espera que los resultados de cobertura estén en `coverage/lcov.info`
