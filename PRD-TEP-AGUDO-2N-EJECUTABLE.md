# PRD-TEP-AGUDO-2N (AHA/ACC 2026)

> **Propósito:** protocolo ejecutable para sospecha, diagnóstico, estratificación y manejo de tromboembolismo pulmonar (TEP) agudo en entorno clínico digital (IDSOERAP + DictaClínica).
>
> **Audiencia:** equipos clínicos entrenados. No sustituye juicio médico ni protocolos institucionales.

---

## 1) Variables mínimas de entrada (data contract)

### 1.1 Identificación y contexto
- `patient_id`
- `edad`
- `sexo_al_nacer`
- `embarazo_activo` (sí/no)
- `peso_kg`
- `creatinina_mg_dl`
- `clearance_creatinina_ml_min`

### 1.2 Signos/síntomas y estabilidad
- `disnea_subita` (sí/no)
- `dolor_pleuritico` (sí/no)
- `sincopa` (sí/no)
- `hemoptisis` (sí/no)
- `fc`
- `pas`
- `pam`
- `sat_o2`
- `uso_vasopresores` (sí/no)
- `paro_cardiaco_inminente_o_reciente` (sí/no)

### 1.3 Riesgo tromboembólico
- `cirugia_reciente`
- `inmovilizacion`
- `cancer_activo`
- `historia_vte`
- `estrógenos`
- `viaje_prolongado`
- `tvp_clinica`

### 1.4 Pruebas
- `wells_score`
- `geneva_score`
- `perc_completo_negativo` (sí/no)
- `ddimero_ng_ml_feu`
- `troponina_positiva` (sí/no)
- `bnp_ntprobnp_elevado` (sí/no)
- `vd_disfuncion_eco` (sí/no)
- `vd_disfuncion_angio_tac` (sí/no)
- `angio_tac_tep_confirmado` (sí/no)
- `gammagrafia_vq_compatible` (sí/no)

### 1.5 Seguridad terapéutica
- `sangrado_activo_mayor` (sí/no)
- `contraindicacion_trombolisis` (sí/no)
- `plaquetas`
- `hb`
- `uso_anticoagulante_previo` (sí/no)

---

## 2) Motor de decisión IF–THEN (ejecutable)

## 2.1 Gate de entrada (sospecha clínica)

```text
IF (disnea_subita OR dolor_pleuritico OR taquicardia(fc>100) OR hipoxemia_inexplicada(sat_o2<92%) OR factores_riesgo_vte>=1)
THEN activar_ruta_tep = TRUE
ELSE activar_ruta_tep = FALSE
```

Si `activar_ruta_tep = FALSE`, terminar flujo y documentar causa alternativa.

## 2.2 Probabilidad pretest

```text
IF pretest_clinico_bajo AND perc_completo_negativo = TRUE
THEN descartar_tep_sin_imagen = TRUE
ELSE solicitar_ddimero = TRUE
```

Regla de D-dímero:

```text
IF edad <= 50: umbral_ddimero = 500 ng/mL FEU
IF edad > 50: umbral_ddimero = edad x 10 ng/mL FEU

IF ddimero <= umbral_ddimero AND estabilidad_hemodinamica = TRUE
THEN descartar_tep_sin_imagen = TRUE
ELSE avanzar_a_imagen = TRUE
```

## 2.3 Diagnóstico por imagen

```text
IF avanzar_a_imagen = TRUE AND sin_contraindicacion_contraste
THEN solicitar_angio_tac

IF contraind_contraste = TRUE
THEN solicitar_gammagrafia_vq

IF inestabilidad_hemodinamica = TRUE
THEN priorizar_eco_cardiaca_cabecera + decisión de reperfusión sin demoras
```

Confirmación:

```text
IF angio_tac_tep_confirmado = TRUE OR gammagrafia_vq_compatible = TRUE
THEN dx_tep_confirmado = TRUE
ELSE dx_tep_confirmado = FALSE
```

---

## 3) Clasificación AHA/ACC 2026 (A–E)

### 3.1 Variables de clasificación
- **Inestabilidad hemodinámica:** PAS < 90 mmHg sostenida, caída > 40 mmHg, vasopresor, hipoperfusión.
- **Biomarcadores:** troponina y/o BNP/NT-proBNP elevados.
- **Ventrículo derecho (VD):** disfunción por eco o angio-TAC.

### 3.2 Algoritmo de categoría

```text
IF dx_tep_confirmado = FALSE -> SIN CATEGORÍA (reevaluar diagnóstico)

IF hallazgo_incidental_sin_sintomas = TRUE -> CATEGORÍA A

ELSE IF estable_hemodinamicamente = TRUE
    AND troponina_positiva = FALSE
    AND bnp_ntprobnp_elevado = FALSE
    AND vd_disfuncion_eco = FALSE
    AND vd_disfuncion_angio_tac = FALSE
THEN CATEGORÍA B

ELSE IF estable_hemodinamicamente = TRUE
    AND (troponina_positiva OR bnp_ntprobnp_elevado OR vd_disfuncion_eco OR vd_disfuncion_angio_tac)
THEN CATEGORÍA C

ELSE IF (hipotension_transitoria OR empeoramiento_clinico_progresivo OR lactato_alto_sin_shock_franco)
THEN CATEGORÍA D

ELSE IF (shock_persistente OR vasopresores OR colapso_hemodinamico OR paro_cardiaco)
THEN CATEGORÍA E
```

---

## 4) Manejo por categoría (órdenes automáticas)

## 4.1 Órdenes comunes inmediatas (todas las categorías confirmadas)
1. Monitorización continua (FC, PA, SatO2, diuresis).
2. Laboratorios base: BH, TP/INR, TPT, creatinina, troponina, BNP/NT-proBNP, gasometría si hipoxemia.
3. Evaluar contraindicaciones de anticoagulación/reperfusión.
4. Activar checklist de sangrado.
5. Considerar activación de **PERT** (obligatorio en D/E; recomendado en C).

## 4.2 Anticoagulación inicial (preferencias)

```text
IF categoría in [B, C, D] AND sin shock franco AND clearance_creatinina adecuado
THEN preferir LMWH (ej. enoxaparina 1 mg/kg SC c/12h)

IF shock / inestabilidad (E) OR procedimiento invasivo inminente OR falla renal severa
THEN preferir heparina no fraccionada IV titulada por TPTa/anti-Xa
```

> Nota operativa: ajustar dosis por función renal, peso extremo y protocolo local.

## 4.3 Categorías A–B
- Considerar manejo ambulatorio si:
  - estabilidad completa,
  - bajo riesgo de sangrado,
  - red de soporte y seguimiento garantizado.
- Anticoagulación de mantenimiento preferente: **DOAC** si no hay contraindicaciones.
- Alta con plan de alarma + cita temprana (48–72 h).

## 4.4 Categoría C
- Hospitalización.
- Anticoagulación terapéutica plena.
- Vigilancia estrecha por deterioro (signos cada 2–4 h al inicio).
- Revalorar reperfusión si progresa a D/E.

## 4.5 Categoría D
- Unidad de cuidados intermedios o críticos.
- Activar PERT en tiempo real.
- Evaluar terapia de reperfusión (trombólisis sistémica o dirigida por catéter / trombectomía) según riesgo-beneficio.

## 4.6 Categoría E
- Reanimación hemodinámica inmediata.
- Terapia de reperfusión urgente:
  - trombólisis sistémica si no contraindicada,
  - o alternativa mecánica/quirúrgica (trombectomía, embolectomía).
- Considerar ECMO venoarterial como puente en shock refractario.

---

## 5) Reglas duras terapéuticas (guardrails)

```text
RULE-01: No retrasar anticoagulación si la sospecha clínica es alta y el sangrado mayor no está presente.
RULE-02: Preferir LMWH sobre HNF en pacientes estables sin shock.
RULE-03: Preferir DOAC sobre warfarina para mantenimiento cuando elegible.
RULE-04: NO usar DOAC en embarazo.
RULE-05: NO priorizar DOAC en TEP masivo inestable.
RULE-06: Duración mínima de anticoagulación: 3 meses; extender a 6 meses o más según riesgo persistente/recurrencia.
RULE-07: Vigilar sangrado mayor y documentar balance riesgo-beneficio en trombólisis.
```

---

## 6) Contraindicaciones y alertas médico-legales

### 6.1 Alertas rojas automáticas
- `sangrado_activo_mayor = TRUE` → bloquear órdenes de trombólisis hasta validación médica.
- `embarazo_activo = TRUE` → bloquear sugerencia DOAC.
- `plaquetas < 50,000` o descenso abrupto compatible con HIT → alerta hematología.
- `dx_tep_confirmado = FALSE` + `orden_reperfusion = TRUE` → hard-stop de seguridad.

### 6.2 Trazabilidad requerida
- Registrar hora de:
  - sospecha inicial,
  - primera anticoagulación,
  - confirmación por imagen,
  - activación PERT,
  - decisión de reperfusión.

---

## 7) Integración IDSOERAP + DictaClínica

## 7.1 Entradas automáticas desde nota clínica
- Parseo NLP de síntomas gatillo (disnea súbita, dolor pleurítico, síncope, hemoptisis).
- Detección de factores de riesgo VTE.
- Extracción de signos vitales para bandera de inestabilidad.

## 7.2 Salidas automáticas
1. **Categoría A–E sugerida** con justificación estructurada.
2. **Set de órdenes** según categoría.
3. **Texto para nota clínica** editable:
   - “Paciente con TEP agudo clasificado como categoría X…”
4. **Checklist de seguridad** (sangrado, embarazo, función renal).
5. **Plan de seguimiento** y educación al alta.

## 7.3 Interoperabilidad con otros PRD
- `PRD-DLP`: ajustar estatina y riesgo CV global post-evento.
- `PRD-DM`: ajustar control glucémico durante hospitalización.
- `PRD-HTA`: objetivos tensionales y antihipertensivos al egreso.

---

## 8) Follow-up obligatorio (>= 1 año)

### 8.1 Calendario sugerido
- 48–72 h (si ambulatorio)
- 2–6 semanas
- 3 meses
- 6 meses
- 12 meses

### 8.2 Objetivos de seguimiento
- Adherencia y tolerancia a anticoagulación.
- Evaluación de recurrencia tromboembólica.
- Detección de secuelas: disnea persistente, limitación funcional.
- Tamizaje de **CTEPH** cuando haya sospecha clínica.

---

## 9) KPI del sistema

- `tiempo_sospecha_a_anticoagulacion`
- `tiempo_sospecha_a_imagen`
- `% pacientes con categorización A-E documentada`
- `% activación PERT en D/E`
- `tasa_sangrado_mayor`
- `mortalidad intrahospitalaria por TEP`
- `% pacientes con seguimiento >= 12 meses`

---

## 10) Plantilla de nota (salida lista para usar)

```text
Diagnóstico: Tromboembolismo pulmonar agudo confirmado por [angio-TAC/gammagrafía].
Clasificación AHA/ACC 2026: Categoría [A/B/C/D/E], sustentada por [hemodinámica, biomarcadores, VD].
Plan terapéutico inicial: [LMWH/HNF], dosis ajustada por [peso/función renal].
¿Reperfusión?: [sí/no], modalidad [sistémica/catéter/quirúrgica], justificación clínica [texto].
Seguridad: riesgo de sangrado [bajo/intermedio/alto], contraindicaciones [ninguna/listar].
Seguimiento: control en [fecha], duración estimada de anticoagulación [3–6 meses o extendida].
```

---

## 11) Estado de implementación

- **Versión:** `PRD-TEP-AGUDO-2N-2026.v1`
- **Estado:** listo para pasar a motor de reglas / CDS.
- **Próximo entregable recomendado:** tablas de dosificación por fármaco, peso y función renal, homologadas a protocolo institucional.
