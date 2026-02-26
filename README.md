# SegundaentregaIA
1. Introducción

Nombre del Proyecto
“IA para la Diversificación y Optimización de Ventas en un Emprendimiento de Moda Estacional”

Presentación del problema

El emprendimiento de bikinis “Chicas Chic” experimenta una caída drástica de ventas durante 8–9 meses del año, debido a la fuerte estacionalidad del producto.

Esta baja demanda afecta directamente:
•	La estabilidad financiera del negocio.
•	La capacidad de sostener costos fijos durante el año.
•	La competitividad, ya que otras marcas están incorporando líneas como activewear o loungewear.
•	La posibilidad de generar contenido y mantener presencia digital relevante.

Relevancia del problema:

Resolver este desafío no solo estabiliza ingresos, sino que abre oportunidades para crear una unidad de negocio rentable y activa todo el año, apoyada en herramientas de IA que permiten investigar, crear, validar y automatizar sin grandes inversiones iniciales.

3. Desarrollo de la propuesta de solución
   
La solución propuesta consiste en utilizar IA generativa para:
1.	Analizar la audiencia y el mercado para detectar oportunidades.
2.	Diseñar una nueva unidad de negocio que no dependa del clima.
3.	Producir contenido visual y textual con IA para validar la propuesta.
4.	Optimizar la comunicación y ventas mediante prompts entrenados.
5.	Reducir costos operativos en diseño, publicidad y testeo de productos.

Nueva unidad de negocio propuesta: ACTIVEWEAR & BEACHWEAR TODA TEMPORADA

Justificación:

•	Se adapta al público actual (mujeres jóvenes, activas y digitales).
•	Tiene demanda todo el año.
•	Requiere baja inversión inicial: diseño previo con IA antes de producir.
•	Mantiene la identidad estética del emprendimiento.

Prompts a aplicar en etapas posteriores

1.	Análisis de mercado y perfiles de cliente
2.	Generación de ideas de nuevos productos
3.	Copywriting con estructura AIDA
4.	Generación de imágenes estilo editorial
5.	Chatbot comercial con respuestas a objeciones
  
3. Justificación de la viabilidad del proyecto
   
 Viabilidad técnica
 
•	No requiere modelos avanzados ni entrenamiento personalizado.
•	Los prompts funcionan con modelos disponibles en el curso o APIs económicas.
•	Los outputs (texto e imagen) permiten validar ideas antes de invertir.

 Tiempo estimado
•	3–4 semanas, incluyendo iteración y pruebas con usuarios reales.
 Recursos necesarios
 
•	API de IA (texto + imagen).
•	GitHub para versionado y presentación.
•	Conexión para ejecutar las consultas.

 Razón de elección
•	El problema es real y crítico para el negocio.
•	La IA permite simular campañas, crear contenido, generar diseños y automatizar procesos, reduciendo costos y riesgos.
•	Es un proyecto escalable a otros emprendimientos de moda.

5. Objetivos
Objetivo general
Desarrollar una solución basada en IA que permita diversificar el negocio, disminuir la estacionalidad y optimizar ventas mediante análisis, diseño y automatización.
Objetivos específicos
•	Crear una nueva línea de productos rentable todo el año.
•	Generar contenido visual y textual profesional utilizando IA.
•	Diseñar un sistema automático de atención y ventas.
•	Optimizar las consultas a la API para reducir costos.
•	Documentar el proceso en GitHub para trabajo reproducible.

6. Metodología
1.	Análisis exploratorio (prompts + estudios de mercado con IA).
2.	Diseño de ideas de negocio (prompts de creatividad y diseño).
3.	Validación visual (generación de imágenes).
4.	Automatización inicial mediante chatbot prompt-based.
5.	Optimización de consultas: 
o	Agrupar prompts por función.
o	Evitar repeticiones.
o	Guardar outputs para reutilizarlos.

6. Herramientas y tecnologías
•	Modelos LLM para análisis, textos y estrategias.
•	Modelos de imagen para diseño visual.
•	Técnicas de prompting: 
o	Role prompting (actúa como…).
o	Structured prompting (pautar outputs en listas/tablas).
o	Iterative prompting.
o	Few-shot prompting para mejorar coherencia del chatbot.

7. Implementación: Codigo usado en Colab
   
!pip install -q google-genai==1.63.0

from google import genai
from google.colab import userdata
import os, re

# 1) Leer secreto o variable de entorno
API_KEY = userdata.get('GOOGLE_API_KEY') or os.environ.get('GOOGLE_API_KEY')

if not API_KEY:
    raise RuntimeError(
        "Falta GOOGLE_API_KEY. Cargalo en Secrets (Settings > Secrets) o en variable de entorno."
    )

# 2) Sanitizar: quitar espacios, saltos de línea y quedarnos con la primera línea válida
API_KEY = API_KEY.strip()

for line in API_KEY.splitlines():
    line = line.strip()
    if line:
        API_KEY = line
        break

# Validación simple de formato 
if ("\n" in API_KEY) or ("\r" in API_KEY):
    raise ValueError("La API key contiene saltos de línea. Editá el Secret y dejala en una sola línea.")

if not API_KEY.startswith("AIza"):
    print(" Aviso: La API key no empieza con 'AIza'. Verificá que sea una clave de Gemini API.")

# 3) Crear cliente y probar
client = genai.Client(api_key=API_KEY)
MODEL_TEXT = "gemini-3-flash-preview"  # podés cambiar a "gemini-1.5-flash" si tu cuenta no tiene acceso preview

resp = client.models.generate_content(
    model=MODEL_TEXT,
    contents="Decí 'OK' si estás funcionando."
)
print("Test del modelo:", getattr(resp, "text", str(resp)))


Pruebas: 

# Si abriste un runtime nuevo, descomenta estas dos líneas para instalar/usar el cliente:
# !pip install -q google-genai==1.63.0
# from google import genai

# Comprobar que el cliente y el modelo existen en la sesión:
try:
    client
    MODEL_TEXT
except NameError as e:
    raise RuntimeError(
        "No se encontró 'client' o 'MODEL_TEXT' en esta sesión. "
        "Primero corré 01_init_gemini.ipynb (instalar, leer Secret y crear cliente)."
    ) from e

# Contexto que usarán los casos de prueba
PROJECT_CONTEXT = """
Marca: X Bikinis (Argentina). Problema: estacionalidad (8–9 meses de baja).
Estrategia: nueva unidad 'ACTIVEWEAR & BEACHWEAR TODO EL AÑO' validada con IA.
Audiencia: mujeres jóvenes digitales. Objetivo: estabilizar ingresos y optimizar costos.
"""

# Helper sencillo para consultas
def _ask(prompt: str, model: str = None) -> str:
    if model is None:
        model = MODEL_TEXT
    resp = client.models.generate_content(model=model, contents=prompt)
    return getattr(resp, "text", str(resp))
