# Organización de carpeta de Google Drive

Esta carpeta propone una estructura lista para replicar en Google Drive y mantener documentos clínicos, PRD, entregables técnicos y material de validación ordenados por flujo de trabajo.

## Estructura

```text
Google_Drive/
├── 00_Inbox/
├── 01_PRDs/
│   └── Protocolos_Clinicos/
│       └── TEP_Agudo/
│           └── PRD-TEP-AGUDO-2N-EJECUTABLE.md
├── 02_Implementacion/
│   └── Motor_Reglas/
├── 03_Validacion_Clinica/
├── 04_Recursos/
└── 99_Archivo/
```

## Uso de cada carpeta

- `00_Inbox/`: recepción temporal de archivos nuevos antes de clasificarlos.
- `01_PRDs/`: documentos de producto, protocolos ejecutables y especificaciones funcionales aprobadas o en revisión.
- `02_Implementacion/`: entregables técnicos derivados de los PRD, como reglas, pseudocódigo, scripts, integraciones y documentación para desarrollo.
- `03_Validacion_Clinica/`: revisiones clínicas, matrices de validación, aprobaciones, minutas y evidencia de revisión por expertos.
- `04_Recursos/`: bibliografía, anexos, plantillas, imágenes, tablas auxiliares y materiales de referencia.
- `99_Archivo/`: documentos obsoletos, versiones reemplazadas o material que debe conservarse pero no está activo.

## Convención de nombres

Usar nombres descriptivos, sin espacios, con guiones medios o bajos:

```text
TIPO-TEMA-VERSION-ESTADO.ext
```

Ejemplos:

- `PRD-TEP-AGUDO-2N-2026-v1.md`
- `MATRIZ-VALIDACION-TEP-AGUDO-v1.xlsx`
- `REGLAS-CDS-TEP-AGUDO-v1.json`

## Flujo recomendado

1. Subir archivos nuevos a `00_Inbox/`.
2. Revisar propietario, estado, versión y confidencialidad.
3. Mover el archivo a la carpeta definitiva según su propósito.
4. Mantener una sola versión vigente en carpetas activas.
5. Enviar versiones reemplazadas a `99_Archivo/` con fecha o sufijo de versión.

