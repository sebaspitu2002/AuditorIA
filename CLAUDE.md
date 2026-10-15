# Auditor Inteligente de Expedientes Inmobiliarios

Aplicación web para agentes inmobiliarios de España: el agente sube los PDFs de una
vivienda (Nota Simple, certificado catastral, recibo del IBI), una IA extrae los datos,
un motor de reglas detecta discrepancias y documentos faltantes, y el agente paga ~9 €
para desbloquear el informe completo en formato semáforo (rojo / amarillo / verde).

La guía completa del proyecto, fase por fase, está en `docs/GUIA.md`. Léela antes de
proponer cualquier tarea nueva y respeta el orden de las fases.

## Sobre mí (el desarrollador)

- Soy principiante en programación. Explícame brevemente qué haces y por qué,
  con lenguaje sencillo, sobre todo cuando introduzcas un concepto nuevo.
- Avanza en pasos pequeños que yo pueda comprobar. Al terminar cada paso, dime
  exactamente qué comando ejecutar para probarlo y qué debería ver.
- Antes de instalar librerías o crear muchos archivos, dime qué vas a hacer.
- Si hay varias formas de hacer algo, elige la más simple para un MVP.
- Respóndeme siempre en español.

## Stack

- **Backend:** Python 3.12+, FastAPI, Pydantic, SQLAlchemy + Alembic, PostgreSQL (Docker en local)
- **Extracción:** PyMuPDF (`fitz`) para el texto; LLM vía librería `openai` (GPT-4o-mini o DeepSeek)
- **Cruce:** reglas en Python puro + `rapidfuzz` para comparar nombres; tests con `pytest`
- **Frontend:** Next.js + TypeScript + Tailwind CSS
- **Pagos:** Stripe Checkout + webhook
- **Almacenamiento de PDFs:** carpeta local en desarrollo; Cloudflare R2 / S3 en producción

## Estructura

```
backend/    API FastAPI, extracción, cruce, base de datos
frontend/   App Next.js
docs/       Guía y documentación
```

## Comandos

(Rellenar a medida que existan)

- Backend: `cd backend` → activar `.venv` → `uvicorn main:app --reload`
- Tests: `cd backend && pytest`
- Frontend: `cd frontend && npm run dev`
- Base de datos: `docker compose up -d`

## Reglas del proyecto (no negociables)

1. **Nunca** escribir claves de API ni secretos en el código: van en `.env`, y `.env` en `.gitignore`.
   Mantener un `.env.example` sin valores reales.
2. **Nunca** subir PDFs reales de clientes al repositorio. Los documentos de prueba
   (anonimizados) van en una carpeta ignorada por Git.
3. La salida de la IA **siempre** se valida con modelos Pydantic; si no cumple, se reintenta una vez.
4. En la primera versión solo se aceptan PDFs nativos. Si el texto extraído sale vacío
   (PDF escaneado), devolver un error claro. Nada de OCR todavía.
5. El motor de cruce (Fase 2) es código determinista **sin IA**, con tests para los casos límite.
6. El detalle del informe se bloquea **en el backend**: la API solo lo devuelve si el
   expediente está pagado, y "pagado" lo marca únicamente el webhook de Stripe
   (firma verificada, idempotente). Nunca ocultarlo solo con CSS.
7. Los PDFs no se guardan en la base de datos, sino en almacenamiento de archivos.
8. Los requisitos documentales por Comunidad Autónoma deben contrastarse con fuentes
   oficiales; no los inventes. Si no estás seguro, márcalo como pendiente de verificar.
9. Nada de funciones fuera del MVP (OCR, WhatsApp, suscripciones...) hasta la Fase 7.

## Git

- Una rama por fase: `fase-0-entorno`, `fase-1-extraccion`, `fase-2-cruce`...
- Conventional Commits en español, en presente, una idea por commit:
  `feat(extraccion): extractor de texto con PyMuPDF`, `fix(cruce): ...`, `test(...)`, `chore: ...`, `docs: ...`
- La guía sugiere commits para cada fase; úsalos como referencia.
- No hagas `git push` ni cambies de rama sin preguntarme.

## Estado actual

- Fase actual: **0 — Preparar el entorno**
- Hecho: carpetas `backend/`, `frontend/`, `docs/`
- Siguiente: README, `.gitignore`, `.env.example`, primer commit
