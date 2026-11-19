# Diplomado en Big Data e Inteligencia Artificial   
## Sistema de segmentación de usuarios   
## FASTPAY   
### Integrantes grupo 6:   
### Maria Dolores Robalino   
### Roxana Oñate Lazo   
### Gerardo Villalba   

**1.	Problema y datos**
El proyecto desarrolla una propuesta de segmentación de usuarios para FASTPAY a partir de sus perfiles demográficos, económicos y de comportamiento transaccional. El propósito es identificar grupos con características similares, reconocer perfiles atípicos y traducir los resultados en acciones diferenciadas de servicio, fidelización y monitoreo. Se trata de un enfoque no supervisado: los algoritmos de clustering no predicen directamente si un usuario cometerá fraude.
La base de trabajo contiene 775 perfiles de usuario, construidos a partir de 2.996 transacciones limpias descargadas desde el servicio del proyecto. El diagnóstico reporta ausencia de valores nulos y de valores negativos en las variables numéricas revisadas. Se consideran variables como edad, ingreso mensual, antigüedad, número de transacciones, monto medio, actividad nocturna, uso de dispositivos nuevos, distancia media e intentos medios.
•	Se identificó una correlación elevada entre monto medio y monto máximo (aproximadamente 0,85); para evitar redundancia, monto máximo se excluyó de las variables de clustering y se conservó para interpretación.
•	Se construyeron tres variables derivadas: ratio_monto_ingreso, intensidad_transaccional e indice_riesgo_operativo.
•	La variable fraudes no se incorporó como característica de agrupamiento; se utilizó posteriormente para describir la incidencia de fraude por segmento.
•	Las variables categóricas de provincia se transformaron mediante variables indicadoras y las características se estandarizaron con StandardScaler.

**2.	Modelo comparados (tabla de métricas)**

Se compararon tres métodos de agrupamiento y un método complementario de detección de anomalías. K-Means se seleccionó como solución operativa; Ward se utilizó como contraste jerárquico y DBSCAN para explorar agrupaciones basadas en densidad. Isolation Forest se aplicó por separado para marcar usuarios con perfiles atípicos.
Método	Resultado	Lectura
K-Means	8 grupos · silueta 0,1879	Modelo principal seleccionado por facilidad de asignación de nuevos usuarios y lectura de centroides.
Jerárquico Ward	8 grupos · silueta 0,2010	Obtuvo una silueta ligeramente mayor; se conserva como contraste.
DBSCAN	5 clusters · 32 usuarios ruido (4,1%)	Útil para detectar densidades y observaciones aisladas; no se eligió como segmentación principal.
Isolation Forest	39 anomalías (5,03%)	Detector complementario de perfiles atípicos; no es un algoritmo de segmentación.

La búsqueda de K-Means evaluó valores de k entre 2 y 11 y seleccionó k = 8 por la mayor silueta observada en esa búsqueda (0,2016). En el ajuste final, la silueta reportada fue 0,1879. La concordancia entre K-Means y Ward fue ARI = 0,754, lo que muestra una similitud apreciable entre las particiones, aunque no prueba por sí sola que los grupos estén claramente separados.
La silueta final de K-Means y Ward (aproximadamente 0,19–0,20) indica una separación limitada y posible superposición entre perfiles. Por ello, los ocho grupos deben interpretarse como segmentos operativos útiles para explorar diferencias, no como categorías naturales perfectamente delimitadas. La proyección PCA en dos dimensiones se utilizó para visualizar la distribución de los grupos.

**3.	Qué explica el resultado (variables, segmentos, incertidumbre)**

Los promedios siguientes describen cada grupo obtenido con K-Means. El porcentaje representa la participación de usuarios en la base. La columna de fraude corresponde al promedio de fraudes por usuario en el segmento, no a una probabilidad individual.
Segmento	Usuarios	Base	Ingreso medio
(USD)	Monto medio
(USD)	Transac.
medias	Riesgo
operativo	Fraudes
medios
0	59	7,6%	888,14	94,34	4,08	0,43	0,56
1	73	9,4%	1.616,40	215,39	3,77	0,52	0,74
2	202	26,1%	910,81	68,49	3,89	0,32	0,36
3	33	4,3%	912,75	90,79	3,97	0,32	0,27
4	43	5,5%	898,70	60,98	3,63	0,35	0,14
5	259	33,4%	884,58	64,30	3,92	0,24	0,29
6	59	7,6%	955,99	78,40	3,37	0,30	0,36
7	47	6,1%	947,12	83,65	4,09	0,34	0,45

Decision de negocio:
•	El segmento 1 presenta el mayor ingreso medio (USD 1.616,40), el mayor monto medio (USD 215,39) y el mayor promedio de fraudes (0,74 por usuario). El análisis del cuaderno indica que reúne 54 de los 290 fraudes observados (18,6%) con el 9,4% de los usuarios.
•	El segmento 0 combina un promedio de riesgo operativo elevado (0,43) y 0,56 fraudes por usuario; requiere atención reforzada.
•	El segmento 4 registra el menor promedio de fraudes (0,14) y un monto medio relativamente bajo (USD 60,98).
•	Isolation Forest identificó 39 usuarios (5,03% de la base). En ellos se contabilizaron 27 de los 290 fraudes observados (9,3%). La concentración es superior a su peso en la base, pero el resultado muestra una capacidad limitada para capturar el total de fraudes.
•	El porcentaje de anomalías dentro de cada segmento fue: segmento 0: 8,47%; 1: 12,33%; 2: 2,48%; 3: 12,12%; 4: 9,30%; 5: 0,39%; 6: 11,86%; y 7: 8,51%.
El usuario señalado como más anómalo fue U0544, con score 0,6167. En el análisis SHAP del cuaderno, las variables destacadas fueron provincia (Manabí), distancia media, monto medio y ratio monto/ingreso. Estos resultados deben entenderse como explicación del comportamiento atípico según el modelo, no como evidencia de fraude ni como relación causal.

**4.	Recomendaciones con cifras**

|Segmento|Lectura del perfil|Acción|propuesta|
|1	Mayor ingreso, monto medio y promedio de fraude.	Verificación reforzada y monitoreo continuo; considerar revisión adicional para transacciones de monto elevado.
|0	Riesgo operativo y fraude medio elevados.	Verificación adicional y revisión de señales operativas.
|7	Perfil mixto; promedio de fraude 0,45.	Monitoreo medio y seguimiento de comportamiento; evitar incentivos automáticos sin revisar riesgo.
|5	Mayor grupo (33,4%); menor riesgo operativo promedio y baja proporción de anomalías.	Fidelización y cashback, sujeto a controles habituales.
|4	Menor promedio de fraude.	Campañas de reactivación de bajo costo.
|2	Grupo numeroso (26,1%) y anomalías relativamente bajas.	Campañas de reactivación y ofertas generales de bajo costo.
|3	Grupo pequeño y proporción de anomalías elevada (12,12%).	Monitoreo focalizado antes de definir incentivos.
|6	Ingreso medio algo superior al promedio y anomalías de 11,86%.	Oferta personalizada condicionada a controles de riesgo; no aumentar límites automáticamente.

El cuaderno calcula un escenario económico ilustrativo para la revisión de anomalías: costo de revisión de USD 117 (39 × USD 3), ahorro estimado de USD 2.430 (27 × 60% × USD 150) y beneficio neto de USD 2.313. Estas cifras dependen de supuestos definidos para el ejercicio (60% de detección y USD 150 de pérdida por fraude) y no constituyen ahorro observado ni garantía de prevención.

**5.	Limitaciones y próximos pasos**

•	La calidad de separación de los segmentos es moderada-baja según la silueta; conviene evaluar estabilidad, tamaños de grupo y otras métricas antes de considerar definitiva la solución.
•	Existe una diferencia entre la silueta máxima de la búsqueda (0,2016) y la silueta del K-Means final (0,1879), asociada a que el ajuste final utiliza una configuración distinta de inicializaciones. Para una versión definitiva, conviene repetir la búsqueda y el ajuste con la misma configuración.
•	Las reglas de acción generadas automáticamente en el código no coinciden completamente con la recomendación narrativa final, especialmente para el segmento 7. Se debe unificar esa lógica antes de implementar.
•	Isolation Forest detecta atipicidad, no fraude. Se recomienda usarlo como señal para revisión, no como criterio automático de bloqueo.
•	La interpretación SHAP debe validarse respecto de la salida exacta explicada por el modelo; los signos invertidos no deben asumirse como explicación correcta sin comprobar la reconstrucción del score.
•	Como siguiente paso, se recomienda probar un modelo probabilístico como Gaussian Mixture Models y comparar la solución con métricas adicionales y análisis de estabilidad, sin reemplazar los resultados actuales hasta contar con evidencia.
