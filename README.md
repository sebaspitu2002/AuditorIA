# Auditor Inteligente de Expedientes Inmobiliarios

Aplicación web para agentes inmobiliarios de España: sube los PDFs de una vivienda
(Nota Simple, certificado catastral, recibo del IBI) y recibe un informe semáforo con
discrepancias y documentos faltantes.

## Estructura

- `backend/`: API en Python (FastAPI), extracción con IA y motor de cruce
- `frontend/`: web en Next.js + TypeScript + Tailwind
- `docs/`: guía del proyecto ([docs/GUIA.md](docs/GUIA.md))

## Configuración

Copia `.env.example` a `.env` y rellena tus claves. El archivo `.env` nunca se sube a Git.
