# 🚀 Automatización B2B — Calificación de Posibles Clientes (Leads)

Flujo de automatización end-to-end desarrollado en **n8n** que recibe posibles clientes B2B vía Webhook, evalúa su *fit* comercial mediante Inteligencia Artificial (**NVIDIA NIM**) con salida estructurada garantizada, registra el contacto segmentado en **Airtable** y envía automáticamente una cotización personalizada o una respuesta de nutrición por **Gmail**.

---

## 🧠 Arquitectura del Workflow

1. **Ingesta (`Recibir lead`)**: Webhook `POST` (`/lead-b2b`) que captura nombre, email, empresa, presupuesto estimado y descripción del proyecto.
2. **Evaluación Comercial con IA (`Evaluar fit comercial`)**: Agente impulsado por **NVIDIA NIM** (`nvidia/nemotron-3-nano-omni-30b-a3b-reasoning`) acoplado a un **Structured Output Parser** (`Formato de evaluación`) que devuelve un JSON estricto con:
   - `lead_score` (1 a 100)
   - `qualification_status` (`QUALIFIED` / `NOT_QUALIFIED`)
   - `recommended_package`
   - `quote_amount`
   - `email_body`
3. **Enrutamiento Condicional (`¿Lead calificado?`)**: Nodo `If` que bifurca la ejecución según el estado de calificación del posible cliente.
4. **Rama `true` — Posibles Clientes Prioritarios**:
   - **Airtable (`Guardar lead Prioritario`)**: Crea el registro en la tabla `CRM - Leads B2B` con estado priorizado, puntaje, paquete sugerido y monto cotizado.
   - **Gmail (`Enviar cotización personalizada`)**: Envía la propuesta comercial redactada por la IA directamente al posible cliente.
5. **Rama `false` — Posibles Clientes En Nutrición**:
   - **Airtable (`Guardar lead En Nutrición`)**: Registra el contacto en el CRM para seguimiento futuro.
   - **Gmail (`Enviar respuesta estándar`)**: Envía un correo de agradecimiento y contacto inicial.

---

## 🛠️ Stack Tecnológico

- **Orquestación**: n8n Cloud
- **Modelo LLM**: NVIDIA NIM API (`nemotron-3-nano-omni-30b-a3b-reasoning`)
- **Base de Datos / CRM**: Airtable
- **Notificaciones / Correo**: Gmail API

---

## 📋 Estructura de Payload Esperado (Webhook `POST`)

```json
{
  "name": "Nombre del Contacto",
  "email": "contacto@empresa.com",
  "company": "Empresa S.A.",
  "budget": 50000,
  "project_description": "Descripción de la necesidad comercial y alcance del proyecto."
}
