# 📐 TutorMates DUA — Cuaderno Privado de Gestión Docente de Matemáticas (3º ESO)

Aplicación web privada diseñada exclusivamente para **profesores de Matemáticas de Educación Secundaria**, concebida para automatizar la burocracia administrativa, planificar secuenciaciones de aula realistas y generar material adaptado al **Diseño Universal para el Aprendizaje (DUA)**, garantizando un cumplimiento riguroso del **Reglamento General de Protección de Datos (RGPD)**.

---

## 🛡️ 1. Arquitectura de Privacidad y Cumplimiento RGPD (GDPR)

La protección del menor y de los datos académicos sensibles en el entorno educativo es un imperativo legal y ético. Esta aplicación implementa el principio de **Privacidad desde el Diseño y por Defecto** (*Privacy by Design and by Default*, Art. 25 del RGPD UE 2016/679 y Ley Orgánica 3/2018 LOPDGDD):

1. **Anonimización Radical por Códigos Estancos (`ALU_01` a `ALU_15`):**
   - El sistema opera únicamente con identificadores numéricos ficticios.
   - En ningún momento se almacenan, leen ni transmiten nombres, apellidos, DNI, correos electrónicos, fechas de nacimiento ni direcciones del alumnado.
2. **Cero Exposición de Datos Personales (PII) a Modelos de Lenguaje:**
   - Al interactuar con la API de Google Gemini (`google-genai`), los prompts y contextos contienen exclusivamente códigos alfanuméricos y observaciones pedagógicas objetivas.
   - Las empresas proveedoras de IA nunca reciben datos de carácter personal que permitan identificar o reidentificar a un estudiante.
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

La aplicación organiza el trabajo en 4 pestañas especializadas:

### 📋 Pestaña 1: Pasar Lista y Burocracia
- **Pase de Lista Interactivo:** Tabla editable (`st.data_editor`) donde el profesor puede marcar asistencia diaria, registrar faltas acumuladas y actualizar notas u observaciones.
- **Detección Automática de Absentismo:** Alerta visual y automática cuando un alumno supera las **4 faltas acumuladas**, activando el protocolo de riesgo escolar.
- **Exportación en CSV:** Botón para descargar el registro actualizado de la sesión con un solo clic.
- **Redacción Automatizada de Partes Oficiales:** Generación inmediata con IA de:
  - Partes oficiales para **Jefatura de Estudios / Comisión de Absentismo**.
  - Correos formales de citación para **Familias y Tutores Legales**, fundamentados en la normativa de evaluación continua y orientación de ESO.

### 📅 Pestaña 2: Distribución de Horas y Temario
- **Cálculo de Sesiones Netas:** Descuenta de forma automática festivos, jornadas de huelga o salidas escolares del total de sesiones disponibles antes del examen.
- **Secuenciación Didáctica Realista (Sesión por Sesión):**
  - Estructuración de cada clase de 50 minutos: activación inicial (10 min), trabajo guiado/autónomo (30 min) y ticket de salida/cierre (10 min).
  - Medida específica DUA incorporada en cada sesión lectiva.
  - Inclusión obligatoria de sesiones de **dudas, simulacro cooperativo, examen formal y coevaluación/feedback post-examen**.

### 🎯 Pestaña 3: Diseñador de Ejercicios y Exámenes DUA
- **Atención a la Diversidad Multinivel:** Genera simultáneamente actividades adaptadas a los perfiles seleccionados:
  - *Refuerzo / Nivel Inicial:* Andamiaje paso a paso y coeficientes sencillos.
  - *Estándar / Nivel Medio:* Competencias curriculares de 3º de ESO.
  - *TDAH:* Enunciados concisos, palabras clave en negrita y división en pasos breves sin sobrecarga cognitiva.
  - *Altas Capacidades:* Retos de pensamiento lateral, formulación algebraica de conjeturas y problemas no rutinarios.
  - *Discalculia Leve:* Apoyo visual estructurado sin distractores aritméticos complejos.
- **Tipografía Matemática LaTeX:** Fórmulas limpias y profesionales renderizadas con `$ ... $` y `$$ ... $$`.
- **Solucionarios y Rúbricas:** Resoluciones completas paso a paso y criterios de calificación para el profesorado.

### 🤖 Pestaña 4: Asistente Docente con Contexto del Aula
- **Chatbot Pedagógico Contextualizado:** El agente tiene acceso en tiempo real a los 15 perfiles anónimos de `clase_3eso.csv`.
- **Tutoría entre Iguales:** Capacidad para recomendar agrupamientos óptimos y parejas cooperativas complementando perfiles fuertes (ej. Altas Capacidades) con perfiles que requieran andamiaje.
- **Detección de Riesgo Académico:** Identifica alumnos en umbral de suspenso y propone planes de choque individualizados.
- **Atajos de Consulta Rápida:** Botones preconfigurados para resolver consultas organizativas frecuentes.

---

## 📦 4. Estructura del Proyecto

```plaintext
tutor-mates/
├── .gitignore             # Exclusión de archivos sensibles y temporales
├── README.md              # Documentación técnica, pedagógica y legal
├── requirements.txt       # Dependencias necesarias para Streamlit Cloud
├── clase_3eso.csv         # Dataset simulado con 15 alumnos anonimizados (DUA)
└── app.py                 # Aplicación principal de Streamlit
```

---

## ⚙️ 5. Despliegue en Streamlit Cloud

Para desplegar esta herramienta privada en tu cuenta de **Streamlit Cloud**:

1. Sube estos archivos a un repositorio privado de **GitHub**.
2. Entra en [share.streamlit.io](https://share.streamlit.io/) y selecciona tu repositorio.
3. En la sección **Advanced settings > Secrets**, añade las siguientes dos variables:

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
