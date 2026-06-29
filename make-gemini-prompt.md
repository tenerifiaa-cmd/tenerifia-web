# Prompt para Gemini — Scoring de leads tenerifIA

## Contexto
Este prompt se usa en Make para analizar cada lead que llega del formulario web
y devolver un JSON estructurado con la valoración del lead.

---

## Prompt (copiar tal cual en Make)

```
Eres un experto en ventas de servicios de automatización e IA para negocios.
Trabajas para tenerifIA, una agencia de inteligencia artificial en Tenerife (España)
que ofrece: chatbots, automatizaciones con Make/n8n, agentes de IA y consultoría.
Precio medio de un proyecto: entre 500€ y 8.000€ según complejidad.

Analiza el siguiente lead y devuelve ÚNICAMENTE un JSON válido, sin texto adicional.

DATOS DEL LEAD:
- Nombre: {{nombre}}
- Empresa: {{empresa}}
- Sector: {{sector}}
- Tamaño del equipo: {{equipo}}
- Mensaje / Qué quiere automatizar: {{mensaje}}
- Canal preferido: {{canal}}
- Disponibilidad: {{disponibilidad}}

CRITERIOS DE VALORACIÓN:
- Puntuación alta (7-10): empresa real con problema concreto, equipo de +2 personas, sector con presupuesto (hostelería, inmobiliaria, retail, salud)
- Puntuación media (4-6): autónomo con problema interesante o empresa pequeña
- Puntuación baja (1-3): mensaje vago, parece estudiante, competidor, o spam
- Lead no real: mensaje sin sentido, sin empresa, email genérico tipo gmail sin nombre

RESPONDE SOLO CON ESTE JSON (sin markdown, sin explicaciones fuera del JSON):

{
  "puntuacion": <número del 1 al 10>,
  "temperatura": "<Caliente | Templado | Frío>",
  "presupuesto_estimado": "<Alto +5.000€ | Medio 1.000-5.000€ | Bajo -1.000€ | Desconocido>",
  "tamano_proyecto": "<Grande | Medio | Pequeño>",
  "probabilidad_cierre": "<Alta | Media | Baja>",
  "es_lead_real": <true | false>,
  "prioridad_contacto": "<Urgente | Normal | Baja>",
  "razonamiento": "<Explicación en 1-2 frases de por qué esa puntuación>",
  "siguiente_accion": "<Qué debería hacer Marco para cerrar este lead>"
}
```

---

## Configuración del módulo HTTP en Make

- **URL:** `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=TU_API_KEY`
- **Método:** POST
- **Header:** `Content-Type: application/json`
- **Body (JSON):**

```json
{
  "contents": [{
    "parts": [{
      "text": "EL PROMPT DE ARRIBA CON LAS VARIABLES MAPEADAS"
    }]
  }],
  "generationConfig": {
    "responseMimeType": "application/json",
    "temperature": 0.3
  }
}
```

- **Dónde está la respuesta de Gemini en Make:**
  `{{body.candidates[].content.parts[].text}}` → parsear como JSON

---

## Campos a añadir en Airtable (además de los del formulario)

| Campo            | Tipo Airtable      | Viene de          |
|------------------|--------------------|-------------------|
| IA Score         | Number             | puntuacion        |
| Temperatura      | Single select      | temperatura       |
| Presupuesto est. | Single select      | presupuesto_estimado |
| Tamaño proyecto  | Single select      | tamano_proyecto   |
| Prob. cierre     | Single select      | probabilidad_cierre |
| Lead real        | Checkbox           | es_lead_real      |
| Prioridad        | Single select      | prioridad_contacto |
| Razonamiento IA  | Long text          | razonamiento      |
| Siguiente acción | Single line text   | siguiente_accion  |
| Estado           | Single select      | Manual (Nuevo / Contactado / Propuesta / Cerrado) |

---

## Flujo completo del escenario Make

1. **Webhooks → Custom webhook** — recibe el JSON del formulario
2. **HTTP → Make an API call** — llama a Gemini con el prompt + datos del lead
3. **Tools → Parse JSON** — parsea la respuesta de Gemini
4. **Airtable → Create a record** — guarda todos los campos (formulario + análisis IA)
5. **Email → Send an email** — notificación a Marco con el resumen del lead y la puntuación

---

## Ejemplo de notificación por email

**Asunto:** 🔥 Lead nuevo — Score {{puntuacion}}/10 — {{nombre}} ({{empresa}})

**Cuerpo:**
```
Nuevo lead en tenerifIA

👤 {{nombre}} — {{empresa}} ({{sector}})
📊 Score IA: {{puntuacion}}/10 — {{temperatura}}
💰 Presupuesto estimado: {{presupuesto_estimado}}
⚡ Prioridad: {{prioridad_contacto}}

🧠 Análisis: {{razonamiento}}
➡️ Qué hacer: {{siguiente_accion}}

Canal preferido: {{canal}}
Disponibilidad: {{disponibilidad}}
Mensaje: {{mensaje}}
```
