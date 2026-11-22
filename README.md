# 📐 TutorMates DUA — Cuaderno Privado de Gestión Docente de Matemáticas (3º ESO)

Aplicación web privada diseñada exclusivamente para **profesores de Matemáticas de Educación Secundaria**, concebida para automatizar la burocracia administrativa, planificar secuenciaciones de aula realistas, gestionar sustituciones imprevistas y generar material adaptado al **Diseño Universal para el Aprendizaje (DUA)**, garantizando un cumplimiento riguroso del **Reglamento General de Protección de Datos (RGPD)**.

---

## 🛡️ 1. Arquitectura de Privacidad y Cumplimiento RGPD (GDPR)

La protección del menor y de los datos académicos sensibles en el entorno educativo es un imperativo legal y ético. Esta aplicación implementa el principio de **Privacidad desde el Diseño y por Defecto** (*Privacy by Design and by Default*, Art. 25 del RGPD UE 2016/679 y Ley Orgánica 3/2018 LOPDGDD):

1. **Anonimización Radical por Códigos Estancos (`ALU_01` a `ALU_15`):**
   - El sistema opera únicamente con identificadores numéricos ficticios.
   - En ningún momento se almacenan, leen ni transmiten nombres, apellidos, DNI, correos electrónicos, fechas de nacimiento ni direcciones del alumnado.
2. **Cero Exposición de Datos Personales (PII) a Modelos de Lenguaje:**
   - Al interactuar con la API de Google Gemini (`google-genai`), los prompts y contextos contienen exclusivamente códigos alfanuméricos y observaciones pedagógicas objetivas.
   - Las empresas proveedoras de IA nunca reciben datos de carácter personal que permitan identificar o reidentificar a un estudiante.
   - **Detección Dinámica de Modelos:** La aplicación consulta dinámicamente `client.models.list()` para seleccionar de forma automática un modelo *Flash* vigente y compatible con `generateContent` (ej. `gemini-2.5-flash`), evitando errores 404 por modelos descatalogados.
3. **Custodia de la Matriz de Reidentificación:**
   - La correspondencia entre el código anónimo (`ALU_02`) y la identidad del estudiante permanece exclusivamente en el cuaderno de calificaciones oficial del docente o en los sistemas de gestión académica institucional del centro (e.g., Séneca, Raíces, Ítaca), desconectada por completo de esta herramienta en la nube.
4. **Borradores Administrativos Seguros:**
   - Las notificaciones oficiales de absentismo y comunicaciones con familias generadas por IA emplean marcadores y códigos anónimos, permitiendo al docente realizar la inserción final del nombre del alumno en su cliente de correo interno o procesador de textos local.

---

## 🔒 2. Mecanismo de Seguridad: Control de Acceso Exclusivo Docente

Para evitar de manera estricta que el alumnado o terceros ajenos al equipo docente puedan visualizar las calificaciones, notas pedagógicas o pruebas de evaluación:

- **Bloqueo del Lado del Servidor (*Server-side Gate*):** Antes de renderizar cualquier pestaña, componente o dato del aula, la aplicación valida la credencial contra `st.secrets["TEACHER_PASSWORD"]`.
- **Detención de Flujo Inmediata (`st.stop()`):** Si la contraseña no es introducida o no coincide, el script de Streamlit se detiene inmediatamente. No se carga el dataset en memoria ni se ejecuta ninguna llamada a la API.
- **Gestión Segura de Credenciales:** La contraseña docente y la clave de la API de Gemini se administran mediante el gestor de *Secrets* de Streamlit (nunca en el código fuente ni en repositorios públicos).
- **Cierre de Sesión:** Dispone de un botón específico en la barra lateral para cerrar la sesión en ordenadores de aulas compartidas o salas de profesorado.

---

## 🚀 3. Funcionalidades de Automatización Docente

La aplicación organiza el trabajo en 5 pestañas profesionales:

### 📋 Pestaña 1: Pasar Lista y Burocracia
- **Mini-Dashboard Estadístico del Aula:**
  - Tres tarjetas métricas dinámicas (`st.metric`):
    - *Nota Media del Grupo:* Cálculo instantáneo del rendimiento global del aula.
    - *% Alumnos en Riesgo:* Porcentaje y conteo exacto de estudiantes con Nota Media < 5.0 o más de 4 faltas acumuladas.
    - *Total Faltas Acumuladas:* Sumatorio de ausencias con media por alumno.
- **Analítica Visual Nativa de Aula:**
  - *Gráfico de Dispersión (Scatter Chart):* Correlación directa entre Faltas Acumuladas y Nota Media clasificada por Perfil DUA.
  - *Gráfico de Barras (Bar Chart):* Desglose de faltas individuales por código `ALU_XX` frente al umbral crítico de absentismo (>4 faltas).
- **Pase de Lista Interactivo:** Tabla editable (`st.data_editor`) para marcar la asistencia de hoy, ajustar faltas y redactar observaciones pedagógicas.
- **Exportación en CSV:** Botón para descargar el registro actualizado de la sesión con un solo clic.
- **Redacción Automatizada de Partes Oficiales con IA:**
  - Generación de partes para **Jefatura de Estudios / Comisión de Absentismo**.
  - Citaciones formales para **Familias y Tutores Legales**, cumpliendo con la normativa de absentismo de ESO.
- **Ficha Preparatoria para Entrevistas con Familias (Tutoría Oficial):**
  - Generador inteligente de informes y guías de conversación para reuniones presenciales o telemáticas con los padres y tutores legales.
  - **Selector Enriquecido:** Mapeo instantáneo del estado de cada estudiante (`ALU_XX`) indicando nota media, faltas acumuladas, perfil DUA e indicadores de alerta.
  - **Personalización por Motivo de Reunión:** Permite orientar la entrevista según el objetivo (seguimiento ordinario, riesgo de suspenso, absentismo, atención/desmotivación, pautas DUA para casa o altas capacidades), con opción de incorporar apuntes u observaciones docentes de última hora.
  - **Estructura Pedagógica en 4 Bloques:**
    1. *Informe Conciso del Alumno (Uso Docente):* Diagnóstico global en 3 líneas, radiografía de competencias matemáticas ({nota}/10), impacto del absentismo y síntesis del cuaderno docente.
    2. *Guía de Explicaciones para los Padres:* Traducción de conceptos pedagógicos a un lenguaje cercano y empático, apertura constructiva destacando virtudes y fortalezas, desestigmatización del perfil DUA y comunicación asertiva sobre actitud y hábitos.
    3. *Compromisos Compartidos (Pacto Familia-Centro):* 3 pautas realistas para el hogar (rutinas de estudio sin pantallas, revisión de agenda, descanso) y 2 compromisos de seguimiento del profesor en el aula.
    4. *Preguntas Estratégicas para la Familia:* Cuestiones clave para indagar la dinámica del alumno en casa (autonomía ante los deberes, frustración, motivación).
  - **Doble Exportación Formal:** Descarga con un solo clic en **Microsoft Word (`.docx`)** con cabecera institucional oficial para llevarla impresa a la entrevista, y en **texto plano (`.txt`)** para archivo rápido en notas de tutoría.

### 📅 Pestaña 2: Planificación de Aula
Organizada en dos sub-pestañas:
- **Sub-pestaña '📅 Distribución de Horas y Temario':**
  - Cálculo automático de sesiones netas descontando festivos y días no lectivos.
  - Secuenciación didáctica sesión por sesión (estructura 10-30-10 min) con adaptaciones DUA.
  - Inclusión obligatoria de sesiones de dudas, simulacro cooperativo, examen y retroalimentación post-evaluación.
- **Sub-pestaña '🚨 Modo Guardia Express':**
  - Generador de fichas de guardia autocontenidas de **50 minutos** listas para usar ante una sustitución imprevista.
  - Incluye:
    1. Instrucciones organizativas rápidas para el profesor/a de guardia (5 min).
    2. Actividad de activación o calentamiento en pizarra (5-10 min).
    3. Cuatro ejercicios graduados en dificultad (básico, comprensión, problema aplicado y reto).
    4. **Hoja de soluciones explicadas paso a paso en lenguaje accesible**, redactada específicamente para que un docente de cualquier otra especialidad (Historia, Inglés, Educación Física, etc.) pueda escribir y corregir los ejercicios en la pizarra con total tranquilidad.
  - **Descarga real en Word (`.docx`):** Exportación con cabecera formal institucional.

### 🎯 Pestaña 3: Diseñador de Ejercicios y Exámenes DUA
- **Atención a la Diversidad Multinivel:** Generación simultánea de versiones adaptadas para:
  - *Refuerzo / Nivel Inicial:* Andamiaje paso a paso y coeficientes sencillos.
  - *Estándar / Nivel Medio:* Competencias curriculares de 3º de ESO.
  - *TDAH:* Enunciados concisos, palabras clave en negrita y división en pasos breves sin sobrecarga cognitiva.
  - *Altas Capacidades:* Retos deductivos, pensamiento lateral y generalización algebraica.
  - *Discalculia Leve:* Apoyo visual estructurado sin distractores aritméticos complejos.
- **Tipografía Matemática LaTeX:** Fórmulas limpias renderizadas con `$ ... $` y `$$ ... $$`.
- **Exportación a Microsoft Word (`.docx`) con Ecuaciones Nativas (OMML):**
  - Conversión profesional de Markdown a `.docx` nativo usando **Pandoc (`pypandoc`)** con la extensión `tex_math_dollars`.
  - Todas las fórmulas LaTeX se transforman automáticamente en **ecuaciones editables de Microsoft Word (OMML)**, en lugar de texto plano o imágenes.
  - Cabecera oficial: *Departamento de Matemáticas*, Curso, Fecha, Grupo y espacio para *Nombre del alumno/a* y *Calificación*.
  - Persistencia en `st.session_state` para evitar que el contenido desaparezca al pulsar el botón de descarga.

### 🤖 Pestaña 4: Asistente Docente con Contexto del Aula
- **Chatbot Pedagógico Contextualizado:** Acceso en tiempo real al dataset completo de los 15 alumnos anónimos.
- **Tutoría entre Iguales:** Recomendaciones de agrupamientos cooperativos complementando perfiles fuertes (Altas Capacidades) con alumnos que necesitan apoyo.
- **Detección de Riesgo Académico:** Identificación de alumnos en riesgo de evaluación negativa y planes de rescate.
- **Botones de Consulta Rápida:** Atajos para resolver dudas frecuentes sobre agrupamientos o adaptaciones.

### 📜 Pestaña 5: Normativa y Plan de Convivencia
- **Asistente Oficial Basado en Evidencia Documental:**
  - Precarga automática de archivos PDF depositados en la carpeta `documentos_centro/` mediante `pypdf`.
  - Resumen visual de documentos y páginas indexadas en memoria con `@st.cache_data`.
  - Posibilidad de subir un documento PDF complementario temporal para consultas específicas.
  - **Citación Obligatoria de Fuente y Página:** Cada respuesta de la IA referencia explícitamente el nombre del documento y el número de página (`[Nombre.pdf, pág. X]`).
  - Respuestas taxativas basadas únicamente en la normativa interna (si un procedimiento no consta en el texto, el asistente informa de ello sin inventar).

---

## 📦 4. Estructura del Proyecto

```plaintext
tutor-mates/
├── .gitignore             # Exclusión de credenciales y temporales
├── packages.txt           # Paquetes del sistema operativo para Streamlit Cloud (pandoc)
├── README.md              # Documentación técnica, pedagógica y legal
├── requirements.txt       # Dependencias Python (streamlit, google-genai, pandas, pypdf, pypandoc)
├── clase_3eso.csv         # Registro anonimizado de 15 alumnos (DUA)
├── documentos_centro/     # Carpeta para depositar los reglamentos y planes de convivencia (PDF)
└── app.py                 # Aplicación principal de Streamlit
```

---

## ⚙️ 5. Despliegue en Streamlit Cloud

Para desplegar esta herramienta privada en tu cuenta de **Streamlit Cloud**:

1. Sube estos archivos a tu repositorio en **GitHub**.
2. Entra en [share.streamlit.io](https://share.streamlit.io/) y selecciona tu repositorio.
3. En la sección **App Settings > Secrets**, añade las siguientes variables:

```toml
TEACHER_PASSWORD = "TuClaveSecretaDocente2026"
GEMINI_API_KEY = "AIzaSy..."
```

4. Haz clic en **Deploy**. La aplicación estará lista y protegida para tu uso exclusivo.

---

## 💻 6. Ejecución Local (Opcional)

Si en el futuro deseas ejecutar la aplicación en tu entorno local:

1. Crea el archivo `.streamlit/secrets.toml`:
   ```toml
   TEACHER_PASSWORD = "TuClaveSecretaDocente2026"
   GEMINI_API_KEY = "AIzaSy..."
   ```
2. Instala las dependencias:
   ```bash
   pip install -r requirements.txt
   ```
3. Ejecuta Streamlit:
   ```bash
   streamlit run app.py
   ```
