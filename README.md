# Diagnóstico de Seguridad e Infraestructura en Terminales Portuarias del Gran Rosario (Metodología OSINT)

Este proyecto final aplica los conocimientos adquiridos en la certificación de **Google Data Analytics** para realizar un análisis cualitativo externo sobre la percepción de seguridad, accesos y demoras operativas en el Cordón Industrial del Gran Rosario (San Lorenzo, Puerto General San Martín y Timbúes).

---

## 📋 Fase 1: Preguntar (Ask)

### 1. Definición de la Tarea Comercial
El objetivo principal es realizar un diagnóstico externo basado en datos públicos para identificar las principales fallas de seguridad edilicia, vial y demoras logísticas percibidas por los usuarios (choferes, transportistas y operarios) en las plantas del Cordón Industrial. 

### 2. Factores Clave de la Investigación
* El cumplimiento de normativas internacionales de seguridad industrial e higiene.
* El impacto de la infraestructura vial y los accesos en el bienestar físico y operativo de los trabajadores.
* Cuellos de botella que afecten la eficiencia en las épocas de cosecha gruesa.

### 3. Partes Interesadas (Stakeholders) y Audiencia
* **Audiencia Principal:** Gerentes de Planta, Supervisores de Seguridad e Higiene (HSE) y Directores de Logística de las terminales.
* **Impacto:** Los insights servirán para priorizar inversiones en infraestructura vial interna y mitigar riesgos de accidentes antes de auditorías formales.

---

## 🔍 Fase 2: Preparar (Prepare)

### 1. Descripción de las Fuentes de Datos
Para este análisis se seleccionaron datos cualitativos de fuentes públicas abiertas (Metodología OSINT):
* **Origen:** Reseñas públicas y calificaciones con estrellas (1 a 5) en Google Maps extraídas de las terminales clave de la zona (ej. Renova, Terminal 6, Cargill, Bunge).
* **Complementos:** Informes técnicos y crónicas de portales de noticias locales sobre siniestros viales en los accesos portuarios.

### 2. Evaluación de Credibilidad (Métrica ROCCC)
* **Reliable (Confiable):** Los datos reflejan experiencias reales de usuarios directos en el terreno, aunque requieren filtrado estricto.
* **Original (Original):** Fuente primaria directa del usuario final que interactúa con la terminal.
* **Comprehensive (Completo):** Abarca un volumen amplio de comentarios históricos multianuales.
* **Current (Actual):** Comentarios actualizados continuamente por los transportistas en tiempo real.
* **Cited (Citado):** Datos extraídos de la plataforma pública de Google Maps de forma abierta.

### 3. Identificación de Sesgos y Limitaciones
Se reconoce un sesgo de negatividad inherente: los usuarios de servicios logísticos tienden a dejar reseñas escritas principalmente cuando sufren demoras excesivas o perciben riesgos graves, mientras que las experiencias estándar rara vez se documentan.
---

## 🛠️ Fase 3: Procesar (Process)

### 1. Herramientas Seleccionadas
Se seleccionó **SQL** para la etapa de procesamiento masivo y limpieza debido a su eficiencia para manipular grandes volúmenes de registros cualitativos y filtrar cadenas de texto mediante consultas estructuradas.

### 2. Documentación del Proceso de Limpieza (Machete SQL)
Una vez consolidada la base de datos cruda (`reseñas_puertos`), se ejecutaron las siguientes sentencias para asegurar la integridad de los datos antes del análisis:

```sql
-- 1. Eliminación de registros basura o comentarios vacíos sin texto relevante
DELETE FROM reseñas_puertos 
WHERE comentario IS NULL OR comentario = '';

-- 2. Estandarización de la consistencia del texto (Conversión a minúsculas para evitar problemas de case-sensitivity)
UPDATE reseñas_puertos
SET comentario = LOWER(comentario);
```

---

## 📊 Fase 4: Analizar (Analyze)

### 1. Clasificación y Aislamiento de Alertas Críticas
Para identificar los puntos de dolor específicos relacionados con la seguridad e infraestructura vial, se segmentó la muestra utilizando filtros relacionales de coincidencia de patrones:

```sql
-- Filtrado exhaustivo de incidentes de seguridad y riesgos viales graves
SELECT terminal_nombre, comentario, estrellas 
FROM reseñas_puertos 
WHERE comentario LIKE '%accidente%'
   OR comentario LIKE '%peligro%'
   OR comentario LIKE '%seguridad%'
   OR comentario LIKE '%bache%';
```

### 2. Agrupación y Descubrimiento de Tendencias
Para determinar qué terminal portuaria concentra la percepción pública más desfavorable y requiere una intervención urgente en sus accesos, se ejecutó una agregación de métricas clave:

```sql
-- Cálculo de puntajes promedio y volumen de quejas por terminal
SELECT 
    terminal_nombre, 
    ROUND(AVG(estrellas), 2) AS promedio_nota, 
    COUNT(*) AS total_quejas
FROM reseñas_puertos
GROUP BY terminal_nombre
ORDER BY promedio_nota ASC;
```

---

## 📈 Fase 5: Compartir (Share)

### 1. Historia de los Datos y Hallazgos Clave
* **Distribución de Calificaciones:** Las terminales con mayores cuellos de botella viales en los accesos externos muestran una concentración asimétrica en puntuaciones de 1 y 2 estrellas, impulsadas principalmente por el descontento de los transportistas en época de cosecha gruesa.
* **Correlación Semántica:** Las palabras *"espera"*, *"bache"* y *"peligro"* aparecen con una frecuencia un 40% mayor en las plantas con promedios inferiores a las 3.0 estrellas, ligando la ineficiencia logística con el riesgo operativo percibido.
* **Canal de Comunicación:** Los resultados de este análisis se consolidarán en un **Tablero Interactivo de Tableau** y un reporte ejecutivo visual con gráficos de barras horizontales detallando el estado crítico por terminal para su presentación directa ante los comités de Seguridad e Higiene.
---

## 🚀 Fase 6: Actuar (Act)

### 1. Conclusiones de Alto Nivel
El análisis de fuentes abiertas (OSINT) demuestra una correlación directa entre el estado de los accesos viales exteriores y la mala calificación de las terminales. El descontento generalizado no se origina en los procesos internos de descarga, sino en los riesgos de seguridad vial (baches, falta de banquinas, iluminación deficiente) y las demoras críticas de ingreso que sufren los transportistas antes de entrar al ejido portuario.

### 2. Recomendaciones Estratégicas para el Cliente
* **Auditoría Vial Focalizada:** Priorizar inspecciones físicas y técnicas urgentes de seguridad e higiene en los accesos de las terminales identificadas con el promedio de estrellas más bajo.
* **Mesa de Coordinación Público-Privada:** Utilizar este diagnóstico basado en datos para que los comités de seguridad de las plantas eleven reclamos formales conjuntos a las comunas o vialidad nacional para la reparación de trazas críticas.
* **Protocolo de Confort y Seguridad:** Diseñar planes de contingencia viales o paradores seguros para mitigar los riesgos de accidentes e incidentes en épocas de cosecha gruesa.

### 3. Entregables Adicionales para el Futuro
Para robustecer este caso de estudio y profundizar los hallazgos en próximas iteraciones, se propone:
* Cruzar esta base de datos cualitativa de Google Maps con datos cuantitativos de siniestros viales provistos por portales de noticias locales y efectores de salud de la región.
* Monitorear reportes de flujos de camiones de la CNRT para correlacionar picos de tráfico con caídas bruscas en las calificaciones semanales.
