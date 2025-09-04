# **Calidad de Datos**

**Responsabilidades:**
* Identificar datos faltantes y errores
* Proponer estrategias de limpieza
* Documentar problemas de calidad


```python
# calidad_datos.py
import pandas as pd
import matplotlib.pyplot as plt
```


```python
#Conectar a drive
from google.colab import drive
drive.mount('/content/drive')
```

    Drive already mounted at /content/drive; to attempt to forcibly remount, call drive.mount("/content/drive", force_remount=True).
    


```python
# Leer los datos
df = pd.read_excel('/content/drive/MyDrive/Python/JEFAB_2024.xlsx')
```


```python
# Explorar estructura básica
print("=== INFORMACIÓN GENERAL ===")
print(f"Total de registros: {len(df)}")
print(f"Total de columnas: {len(df.columns)}")
```

    === INFORMACIÓN GENERAL ===
    Total de registros: 6423
    Total de columnas: 231
    

# **Preguntas a responder:**

1. ¿Qué columnas tienen más datos faltantes?
2. ¿Hay registros duplicados?
3. ¿Qué problemas de encoding se detectan?

# **1. ¿Qué columnas tienen más datos faltantes?**


```python
#Analisis de datos faltantes
print("=== ANÁLISIS DE DATOS FALTANTES ===")
missing_data = df.isnull().sum()
missing_percent = (missing_data / len(df)) * 100
print("Top 10 columnas con más datos faltantes:")
missing_info = pd.DataFrame({
'Columna': missing_data.index,
'Datos_Faltantes': missing_data.values,
'Porcentaje': missing_percent.values
}).sort_values('Datos_Faltantes', ascending=False)
print(missing_info.head(10))

```

    === ANÁLISIS DE DATOS FALTANTES ===
    Top 10 columnas con más datos faltantes:
                                       Columna  Datos_Faltantes  Porcentaje
    213  NUMERO_PERSONAS_APORTE_SOSTENIMIENTO2             3928   61.155223
    212               NUMERO_HABITAN_VIVIENDA2             3808   59.286938
    31                            NUMERO_HIJOS             3217   50.085630
    32                          HIJOS_EN_HOGAR             3200   49.820956
    216                       EDAD_RANGO_PADRE             1939   30.188385
    17                              EDAD_PADRE             1939   30.188385
    215                       EDAD_RANGO_MADRE              889   13.840884
    13                              EDAD_MADRE              885   13.778608
    7                                    EDAD2               13    0.202398
    214                             EDAD_RANGO               13    0.202398
    

Es evidente que hay variables críticas con más del 50% de datos faltantes. Por lo cual se hara un análisis profundo con los profesionales para identificar las variables necesarias para el estudio y de manera adicional revisar si los valores faltantes realmente son faltantes o son categorias que quedaron vacias por que no aplican

# **2. ¿Hay registros duplicados?**




```python
# Análisis de duplicados
print(f"\n=== ANÁLISIS DE DUPLICADOS ===")
print(f"Registros duplicados: {df.duplicated().sum()}")

```

    
    === ANÁLISIS DE DUPLICADOS ===
    Registros duplicados: 0
    

Esto significa que no existen filas repetidas que nos puedan generar sesgos o inflar frecuencias en posteriores análisis estadísticos

# **3. ¿Qué problemas de encoding se detectan?**


```python
#Análisis de tipos de datos
print(f"\n=== TIPOS DE DATOS ===")
print(df.dtypes.value_counts())

```

    
    === TIPOS DE DATOS ===
    int64      153
    object      66
    float64     12
    Name: count, dtype: int64
    


```python
#Problemas de encoding
problemas_encoding = {}
for col in df.select_dtypes(include=["object"]).columns:
    rare_chars = set("".join(df[col].dropna().astype(str))).difference(
        set("abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 áéíóúÁÉÍÓÚñÑ.,;:-_()[]{}!?@%&/")
    )
    if rare_chars:
        problemas_encoding[col] = rare_chars

print("\nProblemas de encoding detectados en columnas de texto:")
for col, chars in problemas_encoding.items():
    print(f"- {col}: {chars}")
```

    
    Problemas de encoding detectados en columnas de texto:
    - UNIDAD: {'Ã'}
    - CUERPO: {'Ã', '‰'}
    - NIVEL_EDUCATIVO: {'“', 'Ã', '‰'}
    - FRECUENCIA_VISITA_FAMILIAR: {'‘', 'Ã'}
    - ESPECIFICACION_CASADO: {'“', 'Ã'}
    - CASADO_FUERZA_MILITAR: {'Ã', '‰'}
    - TIPO_RELACION_PAREJA: {'“', 'Ã'}
    - CONOCIMIENTO_SERVICIO_EDUCATIVO: {'³', '\xad', '±', 'Ã', '©', '“', '¡'}
    - PRINCIPAL_NECESIDAD_FAMILIAR: {'“', '‘', 'Ã', '‰'}
    - DONDE_DENUNCIA: {'“', 'Ã'}
    - CONSULTA_TOMA_DECISIONES: {'³', '\xad', 'Â', '±', '\xa0', 'Ã', '©', '“', '¡', 'º'}
    - METODO_RESOLUCION_PROBLEMAS_FAMILIAR: {'Ã'}
    - PERSONA_APOYO_PROBLEMAS: {'‘', '\xad', '±', 'Ã', '¡', '©', '“', '³', 'º'}
    - INTEGRANTE_RED_APOYO: {'‘', 'Ã'}
    - PARENTESCO_FALLECIMIENTO: {'‘', '\xad', '±', '·', 'Ã', '“', 'â', '¡', 'º'}
    - ACTIVIDADES_FAC_FORTALECIMIENTO_FAMILIAR: {'‘', '\xad', '¦', '±', 'Ã', '‰', '¡', '©', '“', '€', 'â', '³', '+', 'º'}
    - EVENTOS_SIGNIFICATIVOS_FAMILIA: {'‘', '\xad', '±', 'Ã', '‰', '©', '³'}
    - PERSONA_A_CARGO: {'‘', '\xad', '±', 'Ã', '¡', '“', '³', 'º'}
    - NOMBRE_DISCAPACIDAD: {'Ã'}
    - PARENTESCO_DISCAPACIDAD: {'‘', 'Ã'}
    - NOMBRE_DISCAPACIDAD_MIEMBRO_FAMILIAR: {'Ã'}
    - NOMBRE_INSTITUCION_DISCAPACIDAD_FAMILIAR: {'“', '‘', 'Ã'}
    - ESTADO_FINANCIERO_VIVIENDA: {'¡', 'Ã'}
    - MIEMBROS_COMPARTE_VIVIENDA: {'“', '‘', 'Ã'}
    - BASE: {'Ã'}
    - Mun: {'‘', 'Ã', '‰'}
    - Depto: {'Ã', '‰'}
    - MunDep: {'‘', 'Ã', '‰'}
    

La base está conformada principalmente por:

* 153 columnas de tipo entero (int64)
* 66 columnas categóricas (object)
* 12 columnas numéricas decimales (float64)

Predominan las variables enteras y categóricas esto refleja que gran parte de la información corresponde a conteos y clasificaciones (edad, hijos, rangos, etc.), lo cual es consistente con bases de encuestas sociodemográficos.

Y se tomaran medidas de Normalización para las variables categoricas que presentan problemas de encoding

# **SOLUCIONES**


# Imputacion de datos faltantes


```python
# ==============================
# SOLUCIONES
# ==============================

# Imputación de faltantes
# Identificar variables con muchos faltantes

# Eliminar variables con +50% de faltantes (excepto las que queremos conservar)
umbral = 0.5
variables_candidatas = missing_info[missing_info["Porcentaje"] > umbral*100]["Columna"].tolist()

# Excepciones: no eliminar variables que sí tienen sentido aunque tengan vacíos
excepciones = ["NUMERO_HIJOS", "HIJOS_EN_HOGAR", "HIJOS"]
variables_a_eliminar = [col for col in variables_candidatas if col not in excepciones]

df_solved.drop(columns=[c for c in variables_a_eliminar if c in df_solved.columns], inplace=True)

print("\nColumnas eliminadas por alto porcentaje de faltantes:")
print(variables_a_eliminar)
```

    
    Columnas eliminadas por alto porcentaje de faltantes:
    ['NUMERO_PERSONAS_APORTE_SOSTENIMIENTO2', 'NUMERO_HABITAN_VIVIENDA2']
    

#Ajuste de reglas semanticas


```python
# Reglas semánticas
# Tratamiento semántico de HIJOS
if "HIJOS" in df_solved.columns:
    df_solved["HIJOS"] = df_solved["HIJOS"].astype(str).str.upper().str.strip()

    if "NUMERO_HIJOS" in df_solved.columns and "HIJOS_EN_HOGAR" in df_solved.columns:
        # Si NO tiene hijos -> asignar 0
        df_solved.loc[df_solved["HIJOS"] == "NO", ["NUMERO_HIJOS", "HIJOS_EN_HOGAR"]] = 0

        # Si dijo que SÍ pero dejó vacío -> imputar con moda
        for col in ["NUMERO_HIJOS", "HIJOS_EN_HOGAR"]:
            mask = (df_solved["HIJOS"] == "SÍ") & (df_solved[col].isna())
            if mask.sum() > 0:
                moda = df_solved.loc[df_solved["HIJOS"] == "SÍ", col].mode()
                if not moda.empty:
                    df_solved.loc[mask, col] = moda[0]

print("\nReglas aplicadas a HIJOS/NUMERO_HIJOS/HIJOS_EN_HOGAR.")

```

    
    Reglas aplicadas a HIJOS/NUMERO_HIJOS/HIJOS_EN_HOGAR.
    


```python
# Imputación del restante
for col in df_solved.columns:
    if df_solved[col].isnull().sum() > 0:
        if df_solved[col].dtype in ["float64", "int64"]:
            df_solved[col].fillna(df_solved[col].mean(), inplace=True)
        else:
            moda = df_solved[col].mode()
            if not moda.empty:
                df_solved[col].fillna(moda[0], inplace=True)

print("Imputación aplicada (media para numéricas, moda para categóricas).")

```

    Imputación aplicada (media para numéricas, moda para categóricas).
    

# Seleccion de variables relevantes para el objeto de estudio


```python
#  Eliminación explícita de variables irrelevantes
variables_irrelevantes = ["CUERPO", "MADRE_VIVE_SI","MADRE_VIVE_NO","EDAD_MADRE","PADRE_VIVE_SI","PADRE_VIVE_NO","EDAD_PADRE",
    "HERMANOS_SI","HERMANOS_NO","ESPECIFICACION_CASADO","CASADO_FUERZA_MILITAR","TIPO_RELACION_PAREJA",
    "USO_FAMILIAR_SERVICIO_EDUCATIVO_FAC","CONOCIMIENTO_SERVICIO_EDUCATIVO","SERV_EDU_UBICACION",
    "SERV_EDU_NO_RESPONDE","SERV_EDU_NO_CONOCE","SERV_EDU_TODAS","SERV_EDU_REQ_ING","SERV_EDU_GRAD_OFERT",
    "SERV_EDU_CAL_EDU","SERV_EDU_BENEFICIOS","SERV_EDU_CAL_SERV","SERV_EDU__RECON_REG","SERV_EDU_INSTRUC_MIL",
    "SERV_EDU_HORARIOS","SERV_EDU_RESULT_ACAD","MALTRATO_INTRAFAMILIAR_SI","MALTRATO_INTRAFAMILIAR_NO",
    "DENUNCIA_MALTRATO_SI","DENUNCIA_MALTRATO_NO","DENUNCIA_MALTRATO_NO_RESP","DONDE_DENUNCIA_FISC",
    "DONDE_DENUNCIA_ICBF","DONDE_DENUNCIA_COM_FAM","DONDE_DENUNCIA_TRIB_CUND","DECIS_PAREJA","DECIS_AMBOS PADRES",
    "DECIS_FAMILIA","DECIS_HIJOS","DECIS_TERCEROS","DECIS_TECNOLOGIA","DECIS_ESPIRITUAL","DECIS_YO_SOLO",
    "PERS_APOYO_PAREJA","PERS_APOYO_AMBOS PADRES","PERS_APOYO_FAM","PERS_APOYO_AMIG","PERS_APOYO_CREER",
    "PERS_APOYO_TECNOL","PERS_APOYO_NO_ACUD_NADIE","PERS_APOYO_PROFES","PERS_APOYO_CAPELLAN","PERS_APOYO_PSIC_BACOP",
    "INTEGRANTE_RED_APOYO_AMIGOS","INTEGRANTE_RED_APOYO_COMP_TRAB","INTEGRANTE_RED_APOYO_VECINOS",
    "INTEGRANTE_RED_APOYO_FAMIL","INTEGRANTE_RED_APOYO_NO_RESPONDE","INTEGRANTE_RED_APOYO_INSTITUCIONES",
    "ACT_FAM_TIEMPO_LIBRE_RECREAT","ACT_FAM_TIEMPO_LIBRE_DEPORT","ACT_FAM_TIEMPO_VISIT_FAM","ACT_FAM_TIEMPO_APREND",
    "ACT_FAM_TIEMPO_NO_COMPARTE","PARENTESCO_FALLECIMIENTO","PARENTESCO_FALLECIMIENTO_ESPOSO",
    "PARENTESCO_FALLECIMIENTO_ABUELO","PARENTESCO_FALLECIMIENTO_ABUELA","PARENTESCO_FALLECIMIENTO_PADRE",
    "PARENTESCO_FALLECIMIENTO_MADRE","PARENTESCO_FALLECIMIENTO_AMBOS_PAD","PARENTESCO_FALLECIMIENTO_TIO",
    "PARENTESCO_FALLECIMIENTO_TIA","PARENTESCO_FALLECIMIENTO_PRIMO","PARENTESCO_FALLECIMIENTO_PRIMA",
    "PARENTESCO_FALLECIMIENTO_HERMANO","PARENTESCO_FALLECIMIENTO_HERMANA","PARENTESCO_FALLECIMIENTO_HIJO",
    "PARENTESCO_FALLECIMIENTO_HIJA","PARENTESCO_FALLECIMIENTO_SUEGRO","PARENTESCO_FALLECIMIENTO_SUEGRA",
    "PARENTESCO_FALLECIMIENTO_NIETO","PARENTESCO_FALLECIMIENTO_NIETA","PARENTESCO_FALLECIMIENTO_SOBRINO",
    "PARENTESCO_FALLECIMIENTO_SOBRINA","PARENTESCO_FALLECIMIENTO_BISABUELO","PARENTESCO_FALLECIMIENTO_BISABUELA",
    "PARENTESCO_FALLECIMIENTO_MULTIPLE","PARENTESCO_FALLECIMIENTO_MASCOTAS","PARENTESCO_FALLECIMIENTO_NUERA",
    "PARENTESCO_FALLECIMIENTO_NO_RESPONDE","MOTIVO_FALLECIMIENTO_FAMILIAR","APOYO_FAMILIAR_PSICOLOGICO_INSTITUCIONAL",
    "ACTIVIDADES_FAC_FORTALECIMIENTO_FAMILIAR","ACT_FAC_FORT_FAM_ACT_INT","ACT_FAC_FORT_FAM_INV_FAM_ACT",
    "ACT_FAC_FORT_FAM_EMP_COMUNICACION","ACT_FAC_FORT_FAM_TIEMP_LIBR","ACT_FAC_FORT_FAM_CAPACIT",
    "ACT_FAC_FORT_FAM_NO_APLICA","ACT_FAC_FORT_FAM_FORT_ESP","ACT_FAC_FORT_FAM_FORT_FAM_PROF",
    "ACT_FAC_FORT_FAM_NINGUNA","ACT_FAC_FORT_FAM_OTROS","EVENT_SIGN_FAM_CEL_ESP","EVENT_SIGN_FAM_CEL_RELIG",
    "EVENT_SIGN_FAM_CEL_ACAD","EVENT_SIGN_FAM_TRAD_FAM","EVENT_SIGN_FAM_NINGUNO","PERS_A_CARG_ESPOSA",
    "PERS_A_CARG_ESPOSO","PERS_A_CARG_HIJOS","PERS_A_CARG_AMBOS_PADRE","PERS_A_CARG_MADRE","PERS_A_CARG_HERMANOS",
    "PERS_A_CARG_ABUELO","PERS_A_CARG_TIO","PERS_A_CARG_SOBRIN","DISC_SI","DISC_NO","DISC_NO_RESP",
    "NOMBRE_DISCAPACIDAD","MIEMBRO_FAMILIAR_DISCAPACITADO","MIEM_FAM_DISC_SI","MIEM_FAM_DISC_NO",
    "PARENTESCO_DISCAPACIDAD","NOMBRE_DISCAPACIDAD_MIEMBRO_FAMILIAR","DEPENDENCIA_MIEMBRO_FAMILIAR_DISCAPACITADO",
    "DEP_MIEMBRO_FAMILIAR_DISCAPACITADO_SI","DEP_MIEMBRO_FAMILIAR_DISCAPACITADO_NO",
    "DEP_MIEMBRO_FAMILIAR_DISCAPACITADO_NO_RES","APOYO_FAMILIAR_DISCAPACITADO","APOYO_FAM_DISC_EMOC",
    "APOYO_FAM_DISC_COG","APOYO_FAM_DISC_INSTRU","APOYO_FAM_DISC_MAT","APOYO_FAM_DISC_NO_RESP",
    "PERTENENCIA_PROGRAMA_DISCAPACIDAD_MIEMBRO_FAMILIAR","PERT_PROG_DISCAP_MIEMB_FAM_SI",
    "PERT_PROG_DISCAP_MIEMB_FAM_NO","PERT_PROG_DISCAP_MIEMB_FAM_NO_RESP","NOMBRE_INSTITUCION_DISCAPACIDAD_FAMILIAR",
    "VIV_PROPIA_SI","VIV_PROP_NO","HABITA_VIVIENDA_FAMILIAR_SI","HABITA_VIVIENDA_FAMILIAR_NO",
    "BENEFICIO_VIVIENDA_FISCAL_SI","BENEFICIO_VIVIENDA_FISCAL_NO","BENEFICIO_VIVIENDA_FISCAL_NO_RESP",
    "BENEFICIO_BARRACA_UNIDAD","BENEFICIO_BARRACA_UNIDAD_SI","BENEFICIO_BARRACA_UNIDAD_NO",
    "BENEFICIO_BARRACA_UNIDAD_NO_RESPONDE","VIVE_EN_ARRIENDO_SI","VIVE_EN_ARRIENDO_NO",
    "VIVE_EN_ARRIENDO_NO_RESPONDE","EDAD_RANGO_MADRE","EDAD_RANGO_PADRE","MunDep","MPIO_CDPMP","Shape_Leng",
    "ORIG_FID","latitude","longitude","ObjectId","x","y"]

variables_existentes = [col for col in variables_irrelevantes if col in df_solved.columns]
df_solved.drop(columns=variables_existentes, inplace=True)

```

    
    Columnas eliminadas explícitamente:
    []
    

# Resolucion de encoding


```python
# Resolucion de encoding
import unicodedata

# --- Función para normalizar tildes y ñ ---
def normalizar_tildes_enn(texto):
    if pd.isna(texto):
        return texto
    texto = str(texto)

    # 1. Normalizar caracteres Unicode
    texto = unicodedata.normalize("NFC", texto)

    # 2. Correcciones comunes de encoding roto
    reemplazos = {
        "Ã¡": "á", "Ã©": "é", "Ã­": "í", "Ã³": "ó", "Ãº": "ú",
        "Ã": "Á", "Ã‰": "É", "Ã": "Í", "Ã“": "Ó", "Ãš": "Ú",
        "Ã±": "ñ", "Ã‘": "Ñ",
        "â": "-", "â": "-", "â": "-",   # guiones
        "â": "'", "â": "'",               # comillas simples
        "â": '"', "â": '"',               # comillas dobles
        "Â": ""                              # caracteres de relleno
    }

    for malo, bueno in reemplazos.items():
        texto = texto.replace(malo, bueno)

    return texto

# --- Aplicar a todas las columnas de texto ---
for col in df.select_dtypes(include=["object"]).columns:
    df[col] = df[col].apply(normalizar_tildes_enn)

print("Normalización aplicada: tildes y ñ corregidas.")

```

    ✅ Normalización aplicada: tildes y ñ corregidas.
    


```python
# Corrección puntual solo para "DIILOGO"
for col in df_solved.select_dtypes(include=["object"]).columns:
    df_solved[col] = df_solved[col].str.replace(r'^\s*DIILOGO\s*$', 'DIÁLOGO', regex=True)
```

# Obtencion de Data Limpia, lista para análisis


```python
# ==============================
# 8. Exportar dataset limpio
# ==============================
output_path = '/content/drive/MyDrive/Python/JEFAB_2024_limpio.xlsx'
df_solved.to_excel(output_path, index=False)
print(f"\nArchivo limpio exportado en: {output_path}")
```

    
    Archivo limpio exportado en: /content/drive/MyDrive/Python/JEFAB_2024_limpio.xlsx
    


```python
print("\n=== AUDITORÍA ESTADÍSTICA (BASE DEPURADA) ===")

# 1. Inconsistencias lógicas
print("\n[1] INCONSISTENCIAS LÓGICAS")
if "HIJOS" in df.columns and "NUMERO_HIJOS" in df.columns:
    inconsistencias_hijos = df[(df["HIJOS"].str.upper() == "NO") & (df["NUMERO_HIJOS"].fillna(0) > 0)]
    print(f"- Registros con HIJOS = NO pero NUMERO_HIJOS > 0: {len(inconsistencias_hijos)}")

if "ESTADO_CIVIL" in df.columns and "RELACION_PAREJA_ESTABLE" in df.columns:
    inconsistencias_pareja = df[(df["ESTADO_CIVIL"].str.upper() == "SOLTERO") &
                                (df["RELACION_PAREJA_ESTABLE"].str.upper() == "SI")]
    print(f"- Registros SOLTERO pero con pareja estable: {len(inconsistencias_pareja)}")

# 2. Outliers numéricos
print("\n[2] OUTLIERS NUMÉRICOS")
if "EDAD2" in df.columns:
    out_edad = df[(df["EDAD2"] < 10) | (df["EDAD2"] > 100)]
    print(f"- EDAD2 fuera de rango [10-100]: {len(out_edad)}")
if "NUMERO_HIJOS" in df.columns:
    out_hijos = df[(df["NUMERO_HIJOS"] < 0) | (df["NUMERO_HIJOS"] > 20)]
    print(f"- NUMERO_HIJOS fuera de rango [0-20]: {len(out_hijos)}")
if "MIEMBROS_COMPARTE_VIVIENDA" in df.columns:
    out_viv = df[df["MIEMBROS_COMPARTE_VIVIENDA"] > 20]
    print(f"- MIEMBROS_COMPARTE_VIVIENDA > 20: {len(out_viv)}")

# 3. Estrato
print("\n[3] REVISIÓN ESTRATO")
if "ESTRATO" in df.columns:
    estratos_invalidos = df[~df["ESTRATO"].isin([1,2,3,4,5,6])]
    print(f"- Estratos fuera de rango (1-6): {len(estratos_invalidos)}")

# 4. Categorías duplicadas o inconsistentes
print("\n[4] INCONSISTENCIA EN CATEGORÍAS")
for col in ["SEXO", "GENERO", "NIVEL_EDUCATIVO", "ESTADO_CIVIL", "TIPOLOGIA_FAMILIAR"]:
    if col in df.columns:
        unicos = df[col].dropna().unique()
        normalizados = [str(x).strip().upper() for x in unicos]
        if len(set(normalizados)) < len(unicos):
            print(f"- {col}: {len(unicos)} categorías → {len(set(normalizados))} tras normalizar")

# 5. Valores sospechosos de no respuesta
print("\n[5] POSIBLES CÓDIGOS 'NO RESPONDE/NO APLICA'")
codigos_sospechosos = ["NO RESP", "NO APLICA", "99", "999", "NS", "NA"]
for col in df.select_dtypes(include=["object"]).columns:
    if df[col].dropna().astype(str).str.upper().isin(codigos_sospechosos).any():
        print(f"- {col}: contiene valores como {set(df[col].dropna().astype(str).str.upper()) & set(codigos_sospechosos)}")

# 6. Validación geográfica
print("\n[6] VALIDACIÓN GEOGRÁFICA")
if "Depto" in df.columns:
    print(f"- Departamentos distintos: {df['Depto'].nunique()}")
if "Mun" in df.columns:
    print(f"- Municipios distintos: {df['Mun'].nunique()}")

```

    
    === AUDITORÍA ESTADÍSTICA (BASE DEPURADA) ===
    
    [1] INCONSISTENCIAS LÓGICAS
    - Registros con HIJOS = NO pero NUMERO_HIJOS > 0: 0
    - Registros SOLTERO pero con pareja estable: 0
    
    [2] OUTLIERS NUMÉRICOS
    - EDAD2 fuera de rango [10-100]: 0
    - NUMERO_HIJOS fuera de rango [0-20]: 0
    - MIEMBROS_COMPARTE_VIVIENDA > 20: 0
    
    [3] REVISIÓN ESTRATO
    - Estratos fuera de rango (1-6): 0
    
    [4] INCONSISTENCIA EN CATEGORÍAS
    
    [5] POSIBLES CÓDIGOS 'NO RESPONDE/NO APLICA'
    - RELACION_AMBOS PADRES: contiene valores como {'NO APLICA'}
    - RELACION_HERMANOS: contiene valores como {'NO APLICA'}
    - FRECUENCIA_VISITA_FAMILIAR: contiene valores como {'NO APLICA'}
    - ESPECIFICACION_CASADO: contiene valores como {'NO APLICA'}
    - TIPO_RELACION_PAREJA: contiene valores como {'NO APLICA'}
    - RELACION_HIJOS: contiene valores como {'NO APLICA'}
    - RESPONSABILIDAD_ACADEMICA_BIENESTAR_HIJOS: contiene valores como {'NO APLICA'}
    - PRINCIPAL_NECESIDAD_FAMILIAR: contiene valores como {'NO APLICA'}
    - METODO_RESOLUCION_PROBLEMAS_FAMILIAR: contiene valores como {'NO APLICA'}
    - ASIGNACION_TAREAS_MIEMBROS_HOGAR: contiene valores como {'NO APLICA'}
    - NOMBRE_INSTITUCION_DISCAPACIDAD_FAMILIAR: contiene valores como {'NO APLICA'}
    
    [6] VALIDACIÓN GEOGRÁFICA
    - Departamentos distintos: 12
    - Municipios distintos: 13
    

# **Principales hallazgos**

* **Datos faltantes**

Se identificaron variables con altos porcentajes de valores vacíos (superiores al 50%). Estas fueron eliminadas al no aportar información significativa.

En otras variables, la ausencia de datos fue interpretada como no aplicabilidad (ejemplo: NUMERO_HIJOS vacío cuando HIJOS = No). En estos casos, se normalizó con imputaciones lógicas (ej. asignar 0).

* **Registros duplicados**

No se encontraron duplicados en el identificador principal (ID), asegurando la unicidad de cada caso.

Problemas de codificación (encoding)

Se detectaron caracteres extraños derivados de la mezcla UTF-8/Latin-1 (ejemplo: "MAESTRA").

Se implementó un proceso de normalización de texto que corrige tildes, ñ y caracteres no válidos, preservando la legibilidad de las categorías cualitativas.

* **Depuración de variables**

Se priorizaron variables asociadas a los ejes de análisis demográfico, educativo, familiar y de entorno socioeconómico.

Se descartaron campos administrativos, redundantes o con poca variabilidad.

Valor agregado estadístico

El tratamiento de los faltantes y la normalización semántica permiten garantizar la consistencia en los análisis posteriores.

La reducción de dimensiones facilita la construcción de indicadores y modelos más interpretables.

Se establece un marco confiable para análisis descriptivos e inferenciales, con especial foco en la estructura familiar, el nivel educativo y las condiciones socioeconómicas.
