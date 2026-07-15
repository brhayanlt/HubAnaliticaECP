# Prueba técnica ingeniero de datos

## Contexto

S&P Global (Platts) publica mensualmente un reporte Excel con proyecciones de precios de hidrocarburos Cada archivo corresponde a un mes de reporte y contiene precios históricos y proyectados para múltiples indicadores (Brent, WTI, diesel, gasolinas, etc.).

El objetivo de esta prueba es construir un pipeline que extraiga, transforme y cargue esos datos en una base de datos estructurada, garantizando calidad e integridad en cada paso.

---

## Archivos de entrada

| Archivo | Descripción |
|----------|-------------|
| `data/hidrocarburos.xlsx` | Catálogo oficial de hidrocarburos. |
| Fuente externa | Archivo de proyecciones de precios de S&P Global (Platts) disponible en la URL suministrada a continuación. La información requerida se encuentra en la hoja **Price Monthly_US**. El nombre del archivo contiene la fecha de reporte y deberá utilizarse para identificar el período correspondiente. |

### URL de la fuente externa
https://github.com/brhayanlt/platts-test-data/raw/refs/heads/main/35.%20North_America_Refined_Products_Short-Term_Outlook_-_March_2026.xlsx


> **Importante**
>
> El archivo de Platts debe ser consumido desde Python utilizando el enlace compartido. No se permite descargar el archivo manualmente ni incluirlo dentro del repositorio.

## Tablas destino

### `hub.catalogo_hidrocarburos`

 | Columna | Tipo | Descripción |
 |---|---|---|
 | `id` | STRING | PK — código mnemónico de S&P Global |
 | `nombre_reporte` | STRING | Nombre en el reporte Platts |
 | `nombre_equivalente` | STRING | Nombre estandarizado interno |
 | `unidades` | STRING | Unidades originales (`cpg` o `$/Bbl`) |

### `hub.precios_hidrocarburos`

 | Columna | Tipo | Descripción |
 |---|---|---|
 | `id` | STRING | FK → `catalogo_hidrocarburos.id` |
 | `fecha_proyeccion` | DATE | Fecha de proyección del precio (último día del mes) |
 | `valor_proyeccion` | DOUBLE | Precio en `$/Bbl` |
 | `fuente` | STRING | Fuente del dato |
 | `fecha_reportada` | DATE | Fecha en la que fue generado el reporte (último día del mes) |
 | `tipo_proyeccion` | STRING | `Real` si `fecha_proyeccion <= fecha_reportada`, `proyeccion` si `fecha_proyeccion > fecha_reportada` |

 ---

## Tareas

 **1. Cargue de datos**
 Leer el catálogo y el archivo mensuales desde la fuente externa.

 **2. Exploración de calidad**
 Examinar los datos crudos e identificar los problemas de calidad presentes.
 Documentar los hallazgos.

 **3. Procesamiento**
 Transformar los datos crudos al esquema de las tablas destino,
 resolviendo los problemas identificados en el paso anterior.

 **4. Carga**
crear una base de datos SQLite denominada `platts.db` a partir de los archivos procesados. Se espera que la solución:
1. Cree las tablas necesarias.
2. Cargue la información procesada.


**5. Validaciones**

Verificar que la carga fue exitosa desde el punto de vista de ingeniería de datos.
Las validaciones deben realizarse utilizando SQL.
Como mínimo se debe validar:

1. Número de indicadores cargados.
2. Número de períodos de proyección cargados por fecha de reporte.
3. Número de registros por indicador.
4. Existencia de registros duplicados para la combinación: (fecha_reporte, fecha_proyeccion, id).
5. Ausencia de valores nulos en campos obligatorios.

Adicionalmente, el candidato puede proponer e implementar validaciones complementarias que considere relevantes.

## Consideraciones de negocio
- Los valores en `cpg` deben convertirse a `$/Bbl` (1 barril = 42 galones).
- No hardcodear información que ya está disponible en el catálogo.
- Los tipos de datos deben definirse explícitamente durante la transformación y mantenerse consistentes durante la carga a SQLite.
- Se debe garantizar que los campos de fecha de reporte y fecha de proyección permitan realizar análisis temporales posteriores.

---

## Instrucciones de entrega

[Url Repositorio](https://github.com/brhayanlt/HubAnaliticaECP)

 1. Clonar el repositorio:
    ```
    git clone <url-repositorio>
    ```

 2. Crear una rama desde `dev` con el siguiente formato:
    ```
    <nombre-apellido>
    ```
    Ejemplo: `juan-garcia`

3. Desarrollar la prueba sobre esa rama.

4. La solución debe incluir como mínimo los siguientes entregables:

   - Notebook (.ipynb) completamente ejecutable.
   - Base de datos SQLite (platts.db) generada durante la ejecución de la solución.
   - Cualquier archivo adicional requerido para ejecutar la solución.

5. Publicar los cambios y entregables en la rama creada dentro del repositorio.

No se deben hacer merges a `main` o `dev`. La revisión se realizará directamente sobre cada rama.

 ---