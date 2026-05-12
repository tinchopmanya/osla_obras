---
status: legacy_do_not_use_as_truth
legacy_marked_at: 2026-05-12
supersedes: C:\dev\Investigacion_Osla_consolidada\OK\verticales\osla_obras\product.md
reason: OSLA canonical documentation moved to consolidated OK tree after V3/saneamiento audit.
do_not_use_for: product_truth, roadmap_truth, implementation_scope, claims, data_rights
---

# AGENTS.md — OslaObras

## Identidad del proyecto

- **Nombre**: OslaObras (Contractor Safety & Compliance UY)
- **Ranking InvestigaVert**: — Score 4.0, Dark Horse fuera del top 3
- **AshRise project_id**: `obras`
- **Puerto Postgres**: 5442
- **DB**: obras / user: obras
- **ICP primario**: Tecnico Prevencionista habilitado CTHPA
- **ICP secundario**: Constructoras medianas UY (30-200 empleados)
- **Pricing target**: USD 100-150/prevencionista/mes
- **Estado validacion**: CONFIRMADO — metodo v3, 5/5 gates pass

## Que es

Herramienta de trabajo del Tecnico Prevencionista para gestionar cumplimiento IGTSS en las N obras que asesora simultaneamente. Genera Planes de Seguridad, mantiene Libro de Obra digital paralelo al fisico obligatorio, automatiza inducciones con registro cronologico, e integra con Registro Nacional de Obras del MTSS.

## Problema que resuelve

Un prevencionista que asesora 8-12 obras simultaneamente pasa 4-6 hs/obra solo en armado documental (Plan de Seguridad, Libro de Obra, inducciones, actas). Fiscalizaciones IGTSS aumentando: 9000 procedimientos en 2025, +16 inspectores para 2026. Sancion: clausura de obra, multas, responsabilidad penal empresarial (Ley 19.196).

## Contexto 2026 que refuerza la tesis

- Uruguay: 40.000+ accidentes laborales anuales, 42 mortales en 2025
- MTSS aumento fiscalizacion, preve incorporar mas inspectores
- BSE trabajando en observatorio de siniestralidad laboral en tiempo real
- Preocupaciones explicitas: trabajo en altura y vuelco de tractores (construccion + rural)
- Ley 19.196 de responsabilidad penal empresarial vigente

## Que valor le aporta el nucleo Osla

- **Documentos + evidencia**: Plan de Seguridad, Libro digital, actas, fotos, audios, inducciones -> evidence-first
- **Workflow**: seguimiento multi-obra, alertas por induccion pendiente, visita vencida, obra sin movimiento
- **Regulatory watch**: monitoreo IMPO/MTSS para cambios normativos (discovery + source_snapshots)
- **Identidad**: obra, prevencionista, constructora, subcontratista, tecnico firmante -> entity model
- **Timeline y auditoria**: trazabilidad cronologica ante accidente o fiscalizacion

## Que NO le aporta el nucleo

- Procurement score/ranking
- ARCE/OCDS como fuente de valor directa

## Fuentes de datos

### Entrada
- Datos de obra cargados por el prevencionista
- Fotos y audio desde obra (app mobile/PWA)
- Documentos escaneados (permisos municipales, registros previos)

### Fuentes externas
- Normativa MTSS/IGTSS: scraping gub.uy + IMPO
- Monitor siniestralidad BSE (publico) para alertas por zona
- Padron CTHPA (si convenio con el Colegio)
- Registro Nacional de Obras MTSS (pre-fill + export, no API)

### Almacenamiento
- Postgres (metadata, relaciones, cumplimiento)
- S3-compatible (documentos, fotos, audio)
- Firmas digitales con timestamps

### IA
- Claude Sonnet para generacion de Plan de Seguridad
- Claude Haiku para clasificacion de riesgos en fotos
- Opus opcional para informes de accidente complejos

## Modelos ML defensibles

1. **Risk Scoring por obra** (GBM): predice probabilidad de accidente basado en tipo de obra, historial contratista, zona, temporada. Features: siniestralidad BSE zona, historial incumplimientos, tipo de actividad (altura, excavacion, demolicion).
2. **Anomaly Detection documental** (Isolation Forest): detecta obras con patron de documentacion irregular — inducciones retrasadas, visitas espaciadas, hallazgos sin cierre.
3. **NLP clasificacion de hallazgos** (BERT fine-tuned): clasifica automaticamente hallazgos de visita por gravedad y norma aplicable (Decreto 283/96, 125/014).
4. **Benchmark contratista** (scoring comparativo): scoring de cumplimiento por contratista vs industria. Util para constructoras eligiendo subcontratistas.

## Acceso a datos compartidos (via FDW)

```sql
-- Identidad de empresas constructoras
SELECT * FROM shared.entity WHERE ...;

-- Normativa vigente
SELECT * FROM shared.normativa_snapshots WHERE ...;

-- Cotizacion para normalizar montos
SELECT * FROM shared.fx_rates WHERE currency_code = 'USD';
```

## Stack tecnico

- Backend: FastAPI (Python)
- Frontend web: Next.js 15 + Tailwind
- Mobile: PWA (React, cero instalacion, UX simple para prevencionistas >45 anios)
- DB: Postgres 16 (puerto 5442)
- Storage: S3-compatible
- Firma digital: pyHanko o DocuSign API
- OCR: Docling para documentos existentes
- ML: scikit-learn + LightGBM + MLflow

## Reglas de boundary

1. Este proyecto tiene **repo, base de datos, storage y colas propios**.
2. No leer ni escribir directo en la base de otra vertical.
3. Si hace falta compartir datos, hacerlo solo por FDW read-only desde shared-db, API interna read-only, o export materializado curado.
4. No tratar tablas de otras verticales como contenedor universal.
5. Reusar patrones del nucleo reusable, no arrastrar semantica de otras verticales donde no aplique.

## Reuso permitido del nucleo

Se puede heredar o reimplementar de forma compatible:
- source discovery / triage
- source snapshots + raw storage + hashing
- capa documental / OCR / text extraction
- extraccion estructurada con evidencia
- identity resolution
- alerts / tasks / decisions / audit trail
- product boundary y adapters/view-models
- tests smoke y contratos read-only

## Reglas para agentes

1. **No escribir en shared-db** — solo leer via FDW
2. **Normativa MTSS es fuente de verdad** — Decretos 283/96, 125/014, Ley 19.196
3. **Templates Plan de Seguridad co-validados con prevencionistas** — no inventar
4. **Evidence-first**: cada anotacion deja timestamp + geolocalizacion + evidencia
5. **Deterministic-first**: no usar LLM en la hot path si existe opcion determinista
6. **No afirmar incumplimientos legales sin documento o norma fuente**

## Variables de entorno requeridas

```
ASHRISE_BASE_URL=http://localhost:8080
ASHRISE_TOKEN=<token>
ASHRISE_PROJECT_ID=obras
```

## Documentación del producto

- **[docs/Producto.md](docs/Producto.md)** — Documento completo del producto: visión, ICP, propuesta de valor, fuentes de datos, modelos ML, competencia, arquitectura, roadmap y métricas. Se actualiza cuando una investigación impacta esta vertical.
- **[docs/Investigaciones.md](docs/Investigaciones.md)** — Log cronológico de investigaciones procesadas que impactan esta vertical: si fortalecen o debilitan la tesis, y qué se actualizó en Producto.md como consecuencia.
- **[docs/ROADMAP.md](docs/ROADMAP.md)** — Milestones técnicos de implementación.

## Contrato AshRise

Al iniciar sesion: leer AGENTS.md -> docs/ROADMAP.md -> GET /state/obras -> GET /handoffs/obras?status=open
Al cerrar: emitir ashrise-close con run + state_update.
