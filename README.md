# Prueba técnica ingeniero de datos

## Contexto

S&P Global (Platts) publica mensualmente un reporte Excel con proyecciones de precios de hidrocarburos Cada archivo corresponde a un mes de reporte y contiene precios históricos y proyectados para múltiples indicadores (Brent, WTI, diesel, gasolinas, etc.).

El objetivo de esta prueba es construir un pipeline que extraiga, transforme y cargue esos datos en una base de datos estructurada, garantizando calidad e integridad en cada paso.

---

## Archivos de entrada

 | Archivo | Descripción |
 |---|---|
 | `data/hidrocarburos.xlsx` | Catálogo oficial de hibrocarburos |
 | SharePoint: [Documentos compartidos/datos_prueba_tecnica](https://ecopetrol.sharepoint.com/sites/EquipoHubdeAnaltica/Documentos%20compartidos/Forms/AllItems.aspx?id=%2Fsites%2FEquipoHubdeAnaltica%2FDocumentos%20compartidos%2Fdatos%5Fprueba%5Ftecnica&viewid=e7423c3d%2D64a2%2D465d%2D9e1f%2Daf5985d21842&newTargetListUrl=%2Fsites%2FEquipoHubdeAnaltica%2FDocumentos%20compartidos&viewpath=%2Fsites%2FEquipoHubdeAnaltica%2FDocumentos%20compartidos%2FForms%2FAllItems%2Easpx) | Archivos mensuales Platts con formato platts_<month>_<yyyy>.xlsx hoja donde estan los datos **Price Monthly_US** |
 | Fabric: [lakehouse bronce](https://app.powerbi.com/groups/9318b82a-d3a7-4899-8f0d-3e4e8d850de0/lakehouses/f750ef21-25fa-4f51-8577-9b8808de9ce1?experience=power-bi&clientSideAuth=0&selectedPath=Files%2Fprueba_tecnica%2F35.+North_America_Refined_Products_Short-Term_Outlook_-_March_2026.xlsx&extensionScenario=openArtifact) | Archivos mensuales Platts con formato platts_<month>_<yyyy>.xlsx hoja donde estan los datos **Price Monthly_US** |

**Notas**:
- El catálogo es la fuente donde **se encuentran los nombres, códigos y unidades** de los hidrocarburos.
- Para el caso de SharePoint la carpeta contiene un solo archivo. **El nombre del archivo contiene la fecha del reporte**.
- Para el caso de Fabric el archivo se encuentra en el workspace **WS-FB-NGOLD-ExOp-VCF** en el lakehouse **LKH001_MaestroEjecucionPptal_BRONCE** y **El nombre del archivo contiene la fecha del reporte**.
 ---

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
 Leer el catálogo y los archivos mensuales desde SharePoint o Fabric. Las credenciales ya están configuradas en `get_secrets_from_vault`.

 **2. Exploración de calidad**
 Examinar los datos crudos e identificar los problemas de calidad presentes.
 Documentar los hallazgos.

 **3. Procesamiento**
 Transformar los datos crudos al esquema de las tablas destino,
 resolviendo los problemas identificados en el paso anterior.

 **4. Carga**
Crear la base de datos y cargar los datos procesados en las tablas destino.
El nombre de la base de datos debe seguir el siguiente formato:
`hidrocarburos_<nombre>_<apellido>`
Ejemplo: `hidrocarburos_juan_garcia`

**5. Validaciones**
Verificar que la carga fue exitosa desde el punto de vista de ingeniería de datos.
Las validaciones **_deben realizarse en SQL_**.

 ---

## Consideraciones de negocio
- Los valores en `cpg` deben convertirse a `$/Bbl` (1 barril = 42 galones)
- No hardcodear información que ya está disponible en el catálogo
- Los tipos de datos deben estar alineados entre pandas y Spark antes de cargar

---

## Instrucciones de entrega

[Url Repositorio](https://ecopetrolad.visualstudio.com/HUBAnalitica/_git/SoporteHUB)

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

 4. Al finalizar, hacer push de la rama al repositorio:
    ```
    git push origin <nombre-apellido>
    ```

 No se deben hacer merges a `main` o `dev`. La revisión se realizará directamente sobre cada rama.

 ---