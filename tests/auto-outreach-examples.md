# Test Cases — Auto Outreach

## Case 1: Texto, es-MX, prompt completo
**Input**
- cliente: “Holbox Store”
- servicios: “Lentes blue light, envíos a todo México”
- modo: texto
- lead_phone: +5216560000000

**Expected Output (essentials)**
- Saludo con marca
- Menciona “lentes blue light” y “envíos a todo México”
- CTA: “¿Te cotizo hoy?”
- Longitud ≤ 900 chars
- Registro en conversations + status outbound_sent

## Case 2: Audio, fallback policy
**Input**
- cliente: “Unicorn AI”
- servicios: no disponibles (prompt faltante)
- modo: audio
- lead_phone: +5216560000001

**Expected Output**
- Genera texto de contingencia (policy) → TTS → mp3 en `audios/`
- Envía `MediaUrl` por WhatsApp
- Si falla media, fallback a texto de contingencia
