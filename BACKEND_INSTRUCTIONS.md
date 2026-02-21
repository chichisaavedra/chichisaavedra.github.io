# 🧠 Guía de Implementación del Backend para DocMind

## 📌 Contexto del Proyecto
**DocMind** es una plataforma inteligente de gestión documental diseñada para automatizar la organización de archivos mediante Inteligencia Artificial. La aplicación permite a los usuarios subir documentos (PDFs, Excel, Imágenes), procesarlos automáticamente usando modelos avanzados (Claude 3.5 Sonnet y GPT-4o Vision), y organizarlos en carpetas basadas en reglas inteligentes.

**Objetivo Final:** Convertirse en el "cerebro" documental de profesionales y pequeñas empresas, eliminando la carga manual de clasificar y resumir información.

---

## 👤 Perfil del Usuario (Dueño del Proyecto)
El dueño de este proyecto es una persona con **conocimientos limitados en programación**. 
> **⚠️ NOTA PARA LA IA/DESARROLLADOR:** Por favor, utiliza un lenguaje sencillo, claro y paciente. Explica los conceptos técnicos ("API Keys", "Database", "Environment Variables") como si estuvieras enseñando a un principiante. No asumas que el usuario sabe dónde pegar el código o cómo configurar un servidor.

---

## 🛠️ Requerimientos Técnicos del Backend

### 1. Autenticación y Seguridad (Prioridad Máxima)
El usuario valora la seguridad por encima de todo. Necesitamos:
- **Inicio de Sesión Real:** Implementar un sistema robusto (preferiblemente usando **Supabase Auth** o **Firebase**) que permita registro por email/contraseña y OAuth (Google).
- **Protección de Datos:** Los documentos de un usuario **NUNCA** deben ser accesibles por otro. Implementar RLS (Row Level Security) en la base de datos.
- **Cifrado:** Asegurar que los archivos en el Storage estén protegidos.

### 2. Procesamiento de IA (Modelo Híbrido y Diferenciación de Planes)
La app utiliza un sistema de "Slots de IA" con una lógica específica según el plan del usuario para optimizar costos y fomentar el upgrade.

#### **Plan FREE:**
- **Documentos (PDF/DOCX/PPT/XLS):** Usar **DeepSeek V3** (vía API compatible con OpenAI). Es más económico para procesar usuarios gratuitos.
- **Imágenes:** Usar **GPT-4o Vision** con un **LÍMITE ESTRICTO de 3 imágenes por mes**.
- **Velocidad:** Implementar un **delay artificial de 7 segundos** en el procesamiento para incentivar el upgrade a planes pagos.
- **Nota:** No mencionar "DeepSeek" en el frontend, referirse como "IA Estándar".

#### **Plan PRO/BUSINESS:**
- **Documentos:** Usar **Claude 3.5 Sonnet** (Anthropic) para máxima precisión y análisis profesional.
- **Imágenes:** Usar **GPT-4o Vision** sin límites mensuales.
- **Velocidad:** Procesamiento inmediato, sin delays artificiales.

#### **Lógica del Worker (Pseudocódigo):**
1. Verificar plan del usuario (`free` vs `pro`).
2. Si es `free` y el archivo es imagen, verificar `images_used_this_month < 3`.
3. Si es `free`, aplicar `sleep(7000)` antes o durante el procesamiento.
4. Seleccionar modelo: `deepseek-chat` para Free Docs, `claude-3-5-sonnet` para Pro Docs, `gpt-4o` para imágenes.
5. Descontar slot de IA y (si aplica) incrementar contador de imágenes.

### 3. Almacenamiento y Organización
- **Storage:** Configurar un bucket de almacenamiento para los archivos físicos.
- **Base de Datos:** Guardar la metadata de los archivos (nombre, tamaño, fecha, resumen de IA, carpeta, subcarpeta, etiquetas).
- **Reglas Automáticas:** Implementar la lógica que, al subir un archivo, verifique si cumple con alguna "Regla" (ej: "Si el nombre contiene 'Factura', mover a la carpeta 'Contabilidad'").

### 4. Integración de Pagos
- **Stripe:** Preparar la infraestructura para suscripciones (Plan Pro de $7/mes). El backend debe validar el estado de la suscripción para habilitar los límites superiores.

---

## 🔑 Guía de API Keys para el Usuario
Necesito que guíes al usuario paso a paso para obtener y configurar:
1. **Anthropic API Key:** [Instrucciones aquí](https://console.anthropic.com/)
2. **OpenAI API Key:** [Instrucciones aquí](https://platform.openai.com/)
3. **Supabase/Firebase Credentials:** Explicar qué es una URL de proyecto y una Anon Key.
4. **Stripe Secret Key:** Explicar el modo "Test" vs "Live".

---

## 📂 Estructura Actual del Frontend
El frontend ya está construido con **React + Tailwind CSS**.
- `src/App.tsx`: Maneja el estado global y la navegación.
- `src/components/MainLayout.tsx`: Contiene el buscador y la estructura principal.
- `src/components/views/Dashboard.tsx`: Donde ocurre la magia de la subida y visualización.
- `src/lib/mockData.ts`: **IMPORTANTE:** Aquí hay datos falsos que deben ser reemplazados por llamadas reales a la API.

---

## 🚀 Próximos Pasos Sugeridos
1. Configurar el servidor (Express.js o integración directa con Supabase).
2. Crear las tablas en la base de datos (`profiles`, `files`, `folders`, `rules`).
3. Implementar el endpoint de subida de archivos con procesamiento de IA en segundo plano (Webhooks o Edge Functions).
4. Conectar el buscador del Header con una consulta real a la base de datos.

---
*Documento generado para facilitar la transición al desarrollo de backend.*
