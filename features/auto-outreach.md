# Spec: Auto Outreach (Lead Activation)

**Objective**  
Automatically initiate a sales conversation with selected leads within 10 seconds after activation, using client-specific prompts and response mode (text/audio).

**Context**  
- Leads are selected in Lead Management and inserted into `conversations` (Supabase).
- Clients have custom prompts in `clientes` (es-ES), system is multi-tenant.
- Messages are sent via WhatsApp (Twilio) with IA-generated responses (OpenAI).

**Inputs**
- `lead_id`, `lead_phone` (E.164), `cliente_id`
- `modo_respuesta`: `"texto"` | `"audio"`
- `prompt_inicial` (from `clientes`) + pricing/services list
- Optional: `idioma_preferido` (default: es-MX)

**Outputs**
- First outbound message sent to WhatsApp **≤10s** after activation.
- Message logged in `conversations` with fields:
  - `last_message`, `modo_respuesta`, `cliente_id`, `status="outbound_sent"`, timestamps.
- If `audio`, store file in bucket `audios` and send media URL.

**Success Criteria**
1. **Timeliness**: 95% of activations envían el primer mensaje ≤10s.
2. **Personalization**: mensaje incluye `nombre_del_negocio` y uno de los **servicios relevantes**.
3. **Sales-first tone**: oferta proactiva (no preguntar “¿cómo te ayudo?”).
4. **Token control**: ≤ 1200 chars (texto) o ≤ 20s (audio).
5. **Tracing**: todo request/response queda trazado (ids, latencias).

**Non-Goals**
- No reintentos automáticos si Twilio falla (se maneja en service-level spec).
- No nurturing largo (eso es otro feature).

**Rules**
- Si `prompt_inicial` falta, usar **plantilla de contingencia** mínima (ver Policy).
- Si `modo_respuesta="audio"`, generar TTS con voz MX neutra.
- Detectar idioma del lead si existe historial y ajustar saludo.

**Examples**
- See `tests/auto-outreach-examples.md`.

**Open Questions**
- ¿Límites de rate por cliente? (definir en service policy)
- ¿Fallback a SMS si WhatsApp falla? (fuera de alcance aquí)
