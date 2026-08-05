---
name: clima-local
description: Consulta la temperatura y el estado del clima actual en Alcalá de Henares, España, y lo muestra de forma breve. Úsala cuando el usuario pida "el clima", "el tiempo" o "la temperatura" de su ciudad.
---

# Clima local (Alcalá de Henares)

Consulta el clima actual de Alcalá de Henares y responde de forma breve (1-2 líneas).

## Pasos

1. Usa `WebFetch` sobre `https://www.clima.com/espana/madrid/alcala-de-henares` con un prompt que pida extraer: temperatura actual, estado del cielo (despejado/nublado/lluvia/etc.), viento y humedad.
   - Si `WebFetch` falla o no devuelve datos útiles, usa `WebSearch` con la consulta `temperatura y clima actual Alcalá de Henares España ahora` como alternativa.
2. Responde al usuario en español, en una o dos líneas, con el formato:

   **Alcalá de Henares ahora:** <temperatura>°C, <estado del cielo>, viento <viento> km/h, humedad <humedad>%.

3. Incluye al final una sección `Sources:` con el enlace de la página consultada, en formato markdown.

No añadas pronóstico a varios días ni información adicional salvo que el usuario la pida explícitamente.
