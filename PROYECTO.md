# tenerifIA — Estado del proyecto

Última actualización: 2026-06-29

## Stack

- **Web:** HTML + CSS + JS estático (sin framework)
- **Scoring leads:** Groq API — llama-3.3-70b-versatile (gratis)
- **CRM:** Airtable — base `app29s2sq6gMxQlB3`, tabla `tbl49QwMbTzDJWuQ2`
- **Emails:** EmailJS — service `service_n1k7brx`
- **Dominio comprado:** tenerifia.com (pendiente de desplegar)

## Credenciales

Ver `.env` (no está en git).

EmailJS:
- Public Key: `EYPupeL_C7KOLLEK9`
- Template lead (→ cliente): `template_eyifhlr`
- Template notificación (→ Marco): `template_qqtfgfc`

## Flujo al enviar el formulario

1. Groq analiza el lead → devuelve scoring JSON
2. Airtable guarda lead + scoring
3. EmailJS envía confirmación al lead + notificación a Marco (tenerifiaa@gmail.com)

## Probar en local

```bash
cd "/Users/stce/Desktop/PROYECTOS IA/Tenerifia"
python3 -m http.server 8080
```

Abrir: http://localhost:8080

## Pendiente

- [ ] Email con dominio propio (`hola@tenerifia.com`) para que no vaya a spam
  - Opción gratuita: Zoho Mail (zoho.com/mail) → plan gratis con dominio propio
  - Luego conectar en EmailJS en vez de Gmail
- [ ] Desplegar en Netlify → apuntar tenerifia.com
- [ ] (Opcional) Airtable Automations para otras automatizaciones futuras
