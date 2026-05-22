# Evaluador WURI IA

Aplicación pública para que hasta 30 participantes escriban ideas o proyectos y reciban una evaluación con IA basada en las 24 categorías WURI 2026 y los tres criterios de evaluación: innovatividad, implementabilidad e impacto.

## Qué incluye

- URL pública para participantes.
- Formulario para describir idea/proyecto.
- Evaluación con IA de 24 categorías WURI: A1-A8, B1-B8, C1-C8.
- Puntajes de 1 a 5 por categoría en tres criterios: innovatividad, implementabilidad e impacto.
- Diagnóstico ejecutivo, riesgos, mejoras y categoría principal sugerida.
- Dashboard de facilitador por código de sesión.
- Exportación CSV de resultados.
- API key protegida en Netlify Functions, no visible para participantes.

## Arquitectura

- `index.html`: frontend público.
- `netlify/functions/evaluate.js`: función serverless que llama a OpenAI y guarda resultados.
- `netlify/functions/results.js`: función para dashboard y exportación CSV.
- `netlify/functions/health.js`: prueba rápida de configuración.
- Netlify Blobs: almacenamiento simple de evaluaciones por sesión.

## Variables de entorno necesarias en Netlify

En **Site configuration > Environment variables**, crea:

| Nombre | Valor | Scope |
|---|---|---|
| `OPENAI_API_KEY` | Tu key de OpenAI, inicia con `sk-proj-...` | Functions |
| `OPENAI_MODEL` | `gpt-5.5` | Functions |
| `ADMIN_PIN` | Un PIN simple para dashboard, por ejemplo `4821` | Functions |

Después de crear o cambiar variables, haz redeploy.

## Despliegue recomendado

No uses solo Netlify Drop para esta app, porque no es un HTML estático: usa Netlify Functions y almacenamiento. Usa una de estas rutas:

### Opción A: GitHub + Netlify

1. Crea un repositorio nuevo en GitHub.
2. Sube todos estos archivos a la raíz del repositorio.
3. En Netlify: **Add new site > Import an existing project**.
4. Conecta el repositorio.
5. Build command: `npm install`.
6. Publish directory: `.`
7. Functions directory: `netlify/functions`.
8. Agrega variables de entorno.
9. Deploy.

### Opción B: Netlify CLI

1. Instala Node.js.
2. En la carpeta del proyecto ejecuta:

```bash
npm install
npx netlify login
npx netlify init
npx netlify deploy --prod
```

3. Agrega las variables de entorno en Netlify.
4. Ejecuta un nuevo deploy.

## Prueba técnica

Abre:

```text
https://TU-SITIO.netlify.app/api/health
```

Debe responder algo como:

```json
{
  "ok": true,
  "hasOpenAIKey": true,
  "model": "gpt-5.5"
}
```

Si `hasOpenAIKey` sale `false`, la API key no quedó bien configurada o falta redeploy.

## Flujo del taller

1. La facilitadora abre la URL pública.
2. Crea un código de sesión, por ejemplo `WURI-8K2M`.
3. Comparte la URL y el código con los participantes.
4. Cada participante escribe su idea y presiona **Evaluar mi idea con IA**.
5. La facilitadora entra a **Dashboard facilitador**, escribe el código y el PIN.
6. Activa **Auto: on** para ver resultados cada 5 segundos.
7. Descarga CSV al final.

## Advertencia importante

Cada evaluación llama a la API de OpenAI y consume créditos. Para 30 personas, configura límites de gasto en la consola de OpenAI antes del taller.
