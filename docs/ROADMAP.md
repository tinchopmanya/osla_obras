---
status: legacy_do_not_use_as_truth
legacy_marked_at: 2026-05-12
supersedes: C:\dev\Investigacion_Osla_consolidada\OK\verticales\osla_obras\product.md
reason: OSLA canonical documentation moved to consolidated OK tree after V3/saneamiento audit.
do_not_use_for: product_truth, roadmap_truth, implementation_scope, claims, data_rights
---

# ROADMAP — OslaObras (Contractor Safety & Compliance UY)

## Estado actual

- Validacion: CONFIRMADO (metodo v3, 5/5 gates pass)
- Producto: en diseno
- Dark horse fuera del top 3 — mejor apuesta si cluster comex se traba

## Entidades canonicas

- `obra`: id, nombre, direccion, geo, tipo, constructora_id, prevencionista_id, fecha_inicio, fecha_fin_estimada, estado
- `prevencionista`: id, nombre, cthpa_matricula, constructoras[], obras_activas[]
- `constructora`: id, rut, razon_social, tamano_empleados, historial_siniestralidad
- `subcontratista`: id, rut, razon_social, rubros[], scoring_cumplimiento
- `visita`: id, obra_id, prevencionista_id, fecha, tipo, hallazgos[], fotos[], audio_url
- `hallazgo`: id, visita_id, descripcion, gravedad, norma_aplicable, estado, fecha_cierre
- `induccion`: id, obra_id, trabajador_id, fecha, firma_digital, contenido_cubierto[]
- `plan_seguridad`: id, obra_id, version, fecha_generacion, estado_aprobacion, contenido_json
- `libro_obra`: id, obra_id, entradas[], firmas[], estado

## Milestone 1 — Fundacion (semanas 1-6)

- [ ] Schema Postgres: obras, prevencionistas, constructoras, subcontratistas
- [ ] API CRUD basica FastAPI
- [ ] PWA shell con navegacion multi-obra
- [ ] Template Plan de Seguridad v1 (co-validado con prevencionista senior)
- [ ] Modulo inducciones con firma digital y timestamp
- [ ] Scraping normativa MTSS/IGTSS + IMPO

## Milestone 2 — Libro de Obra Digital (semanas 7-12)

- [ ] Libro de Obra digital: entradas cronologicas, fotos, hallazgos
- [ ] Workflow visitas: programacion, ejecucion, cierre de hallazgos
- [ ] Alertas: induccion pendiente, visita vencida, hallazgo sin cierre
- [ ] Upload fotos desde PWA con geolocalizacion automatica
- [ ] Integracion Registro Nacional de Obras MTSS (pre-fill + export)

## Milestone 3 — IA + ML (semanas 13-20)

- [ ] Claude Sonnet: generacion automatica Plan de Seguridad desde datos de obra
- [ ] Claude Haiku: clasificacion de riesgos en fotos de obra
- [ ] Risk Scoring por obra (GBM): probabilidad de accidente
- [ ] Anomaly Detection documental (Isolation Forest): patrones irregulares
- [ ] NLP clasificacion hallazgos (BERT): gravedad + norma aplicable
- [ ] Golden dataset: 50 planes + 200 hallazgos clasificados para eval

## Milestone 4 — Multi-empresa + Benchmark (semanas 21-28)

- [ ] Vista multi-empresa para prevencionistas con 8-12 obras
- [ ] Benchmark contratista: scoring cumplimiento vs industria
- [ ] Dashboard constructora: estado de cumplimiento across obras
- [ ] Paquete listo para inspeccion IGTSS (1-click export)
- [ ] Monitor siniestralidad BSE por zona (alertas)

## Milestone 5 — Escala (semanas 29+)

- [ ] Integracion FDW shared-db (entity, normativa, fx_rates)
- [ ] API publica para constructoras grandes
- [ ] Expansion: rural/agro (vuelco tractores, silos)
- [ ] Mobile offline-first para obras sin conectividad

## Criterio de exito primera fase

- 3+ prevencionistas validaron pain y pricing en entrevistas
- Template de Plan de Seguridad co-validado con prevencionista senior
- AshRise responde y guarda contexto del proyecto
- Evidencia y lineage visibles
- Base aislada de otras verticales

## Pipeline ML

| Modelo | Input | Output | Accuracy target |
|--------|-------|--------|-----------------|
| Risk Scoring obra | tipo obra, historial, zona, temporada | probabilidad accidente 0-100 | AUC >0.80 |
| Anomaly Detection doc | patrones documentacion | obras con patron irregular | precision >85% |
| NLP hallazgos | texto hallazgo | gravedad + norma | F1 >0.82 |
| Benchmark contratista | historial cumplimiento | score 0-100 vs industria | correlacion >0.70 |
