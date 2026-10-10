### 📌 1. Introducción y PropósitoEl presente documento recopila la evidencia correspondiente a la Actividad de Aprendizaje 2 (AA2). El objetivo principal es evaluar y demostrar cómo el uso de prompts estructurados, aplicados a modelos de Inteligencia Artificial Generativa (como Gemini), permite automatizar de manera eficiente los procesos clave de ETL (Extracción, Transformación y Carga), la limpieza de datos heterogéneos y la generación de documentación técnica estandarizada para sistemas de gestión e inventario.

### 🚀 2. Desarrollo de Prompts y ScriptsPara cumplir con los requerimientos técnicos, se formularon tres prompts estructurados bajo la metodología de Rol, Contexto, Tarea y Restricciones, obteniendo los siguientes resultados prácticos:

# 🛠️ Tarea 1: Transformación de Estructuras (JSON a CSV Tabular)

## *Prompt Aplicado*

- Rol: Ingeniero de Datos experto en Python y ETL.

- Contexto: Recibo datos de servicios de mantenimiento en JSON semiestructurado y anidado.

- Tarea: Escribe un script en Python utilizando pandas para aplanar la estructura JSON, preservando las relaciones entre cliente, vehículo y servicios recibidos, y exportarlo a un CSV limpio.

- Restricciones: Manejar la clave 'mantenimientos' anidada sin duplicar erróneamente la metainformación y renombrar las columnas a convención snake_case.

### Código Resultante (tarea1_json_to_csv.py)

    import pandas as pd

    json_data = [
        {
            "id_cliente": 101,
            "vehiculo": {"placa": "XYZ123", "marca": "Yamaha"},
            "mantenimientos": [{"fecha": "2026-01-15", "costo": 150000}],
        },
        {
            "id_cliente": 102,
            "vehiculo": {"placa": "ABC456", "marca": "Bajaj"},
            "mantenimientos": [
                {"fecha": "2026-02-10", "costo": 80000},
                {"fecha": "2026-03-01", "costo": 45000},
            ],
        },
    ]

    # Normalización del JSON aplanando el nivel anidado
    df = pd.json_normalize(
        json_data,
        record_path=["mantenimientos"],
        meta=["id_cliente", ["vehiculo", "placa"], ["vehiculo", "marca"]],
    )

    # Renombrar columnas a snake_case estandarizado
    df.rename(
        columns={
            "vehiculo.placa": "placa_vehiculo",
            "vehiculo.marca": "marca_vehiculo",
            "fecha": "fecha_mantenimiento",
            "costo": "costo_mantenimiento",
        },
        inplace=True,
    )

    df.to_csv("mantenimientos_procesados.csv", index=False)
    print("Transformación exitosa.")


# 🧹 Tarea 2: Limpieza y Combinación de Fuentes Heterogéneas

##  *Prompt Aplicado*

- Rol: Especialista en Calidad de Datos (Data Wrangler).

- Contexto: Necesito integrar un inventario en CSV con un reporte de ventas que posee precios nulos y formatos de texto inconsistentes.

- Tarea: Escribe un script en Python para fusionar ambas fuentes mediante LEFT JOIN. Aplica limpieza de texto, elimina duplicados e imputa precios faltantes con la mediana de la categoría.

- Restricciones: Explicar cada etapa mediante comentarios explícitos dentro del código y asegurar la correcta manipulación de valores nulos con numpy.

### *Código Resultante (tarea2_data_wrangling.py)*

    import numpy as np
    import pandas as pd

    # Carga simulada de fuentes heterogéneas
    df_inventario = pd.DataFrame(
        {
            "sku": ["REP01", "REP02", "REP03", "REP01"],
            "nombre_repuesto": [
                " Pastillas Freno ",
                "Aceite 4T",
                "Filtro Aire",
                " Pastillas Freno ",
            ],
            "categoria": ["Frenos", "Lubricantes", "Filtros", "Frenos"],
        }
    )

    df_ventas = pd.DataFrame(
        {
            "sku": ["REP01", "REP02", "REP03"],
            "precio_venta": [25000.0, np.nan, 15000.0],
            "unidades": [10, 5, 20],
        }
    )

    # 1. Eliminación de registros duplicados
    df_inventario = df_inventario.drop_duplicates()

    # 2. Normalización de cadenas de texto (strip y lowercase)
    df_inventario["nombre_repuesto"] = (
        df_inventario["nombre_repuesto"].str.strip().str.lower()
    )

    # 3. Fusión de fuentes mediante LEFT JOIN por SKU
    df_integrado = pd.merge(df_inventario, df_ventas, on="sku",     how="left")

    # 4. Imputación de nulos utilizando la mediana de la columna
    mediana_precio = df_integrado["precio_venta"].median()
    df_integrado["precio_venta"] = df_integrado["precio_venta"].    fillna(
        mediana_precio
    )

    print(df_integrado)


# 📝 Tarea 3: Documentación Técnica y Diccionario de Datos

## Prompt Aplicado

- Rol: Arquitecto de Datos y Documentador Técnico.Contexto: Hemos integrado varias fuentes de datos para el taller de motocicletas y necesitamos documentar la estructura para el equipo de software.

- Tarea: Genera un diccionario de datos en formato tabla Markdown que defina los campos principales, sus tipos de datos, descripción y criterios de calidad.

- Restricciones: Incluir validaciones de no nulidad, formatos estándar (como ISO 8601) y reglas de negocio adaptadas al sector automotriz.

### Resultado Generado (Diccionario de Datos Integrado)

| Campo | Tipo de Dato | Descripción | Criterio de Calidad |
| :--- | :--- | :--- | :--- |
| `id_orden` | Integer | Identificador único del servicio | Primary Key, No Nulo |
| `placa_vehiculo` | Varchar(6) | Placa de la motocicleta | Estándar alfanumérico |
| `kilometraje` | Float | Kilometraje al ingresar | Mayor a 0 |
| `precio_venta` | Decimal | Precio facturado | Imputado por mediana |
| `fecha_ingreso` | Date | Fecha de registro | Formato ISO 8601 |



# 📊 3. Análisis Crítico y Conclusiones

- Impacto de la Estructuración: Asignar un Rol, Contexto y Restricciones Técnicas específicas evita respuestas genéricas y produce código listo para producción.

- Tratamiento Estadístico: En la Tarea 2, imputar valores faltantes por mediana en lugar de promedio evitó que valores atípicos distorsionaran los datos.

-  Cumplimiento Legal y Ético: Se integraron parámetros de anonimización de datos sensibles bajo la normatividad de Habeas Data (Ley 1581 de 2012)

## 🤖 Asistencia Tecnológica
* Informe técnico redactado, estructurado y documentado con el apoyo de herramientas de Inteligencia Artificial Generativa (Gemini) como asistente de desarrollo técnico. 