# Spec: WhatsApp Integration (via Twilio)

**Objective**  
Deliver messages (text or audio URL) reliably to WhatsApp and confirm delivery status back to Supabase.

**Endpoints & Contracts**
- **Outbound**: POST to Twilio WhatsApp API with:
  - `To=whatsapp:+521XXXXXXXXXX`
  - `Body` (texto) **o** `MediaUrl` (audio mp3).
- **Inbound webhook**: Receives user replies -> stores in `conversations`.
- **Status callback**: Updates message status (`queued|sent|delivered|failed`) in `conversations`.

**Inputs**
- `lead_phone_e164`, `message_text?`, `media_url?`, `cliente_id`, `conversation_id`.

**Outputs**
- `message_sid` (Twilio) persistido.
- Update `status` + `delivered_at` when applicable.

**Success Criteria**
1. **Reliability**: ≥ 99% success rate (except carrier/WhatsApp downtime).
2. **Latency**: outbound request ≤ 1s; status update ≤ 3s desde callback.
3. **Observability**: logs estructurados con `sid`, `cliente_id`, `latencia`, `error_code?`.

**Error Handling**
- Retries exponenciales: 3 intentos (1s, 4s, 10s).
- Mapear `error_code` de Twilio a `failure_reason` (tabla `delivery_errors`).
- Si falla `MediaUrl`, fallback a mensaje de texto corto:  
  “Tuvimos un problema con el audio, aquí va el resumen: …”

**Security**
- Validar firma Twilio en webhooks.
- No exponer API keys en logs.
