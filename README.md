# Análisis de producción asistencial — CESFAM Tongoy

Análisis interpretativo de las prestaciones registradas entre **julio 2025 y junio 2026**.
Todo el procesamiento es reproducible y trabaja sobre una base **anonimizada** (sin datos personales).

## Privacidad
Antes de cualquier análisis se **eliminan** las columnas identificables: nombre del profesional,
documento, tipo de documento, nombre del paciente y comentario de texto libre. `IDKEY` se convierte
en un hash irreversible usado solo para contar personas/atenciones únicas y **nunca se expone**.
Almacenar estos archivos en repositorios institucionales seguros (Ley N.º 19.628).

## Estructura
```
proyecto_analisis/
├── datos_procesados/
│   ├── base_limpia_anonimizada.csv   # datos limpios, sin PII
│   ├── resultados.json               # todos los agregados del análisis
│   ├── tabla_calidad.json            # calidad por variable
│   └── chequeos.json                 # chequeos de consistencia
├── graficos/                         # 12 gráficos estáticos (PNG)
├── dashboard/
│   ├── dashboard_offline.html        # panel interactivo autocontenido (abrir en navegador)
│   ├── dashboard_artifact.html       # versión publicada como Artifact
│   ├── data.json                     # datos columnares del panel
│   └── _template.html                # plantilla del panel
├── informes/
│   ├── informe.md / auditoria.md     # fuentes en Markdown
│   └── estilo.css
├── exportaciones/
│   ├── informe.docx / informe.html
│   └── auditoria.docx / auditoria.html
├── scripts/                          # 01..06 pipeline reproducible
└── README.md
```

## Ejecución (requiere Python 3 con pandas, openpyxl, ftfy, numpy, matplotlib)
```bash
cd scripts
python3 01_limpieza.py         # carga, anonimiza, normaliza, deriva variables
python3 02_calidad.py          # tabla de calidad y chequeos de consistencia
python3 03_analisis.py         # indicadores y análisis -> resultados.json
python3 04_graficos.py         # gráficos estáticos PNG
python3 05_dashboard_data.py   # dataset columnar del dashboard
python3 06_build_dashboard.py  # ensambla el dashboard (offline + artifact)
# Informes:  pandoc informes/informe.md -o exportaciones/informe.docx --toc
```

## Definiciones clave (unidad de análisis)
- **Registro de prestación:** una fila. Una atención genera varias prestaciones.
- **Prestación:** actividad; volumen = suma de la columna *Cantidad*.
- **Atención:** evento único (misma persona, fecha y horario de apertura/cierre).
- **Persona:** identificador anonimizado; **no** equivale a población inscrita.

## Advertencias
Las cifras son **actividad registrada**, no cobertura ni necesidad poblacional. Sin denominadores
poblacionales ni de dotación no se calculan tasas ni productividad. La duración solo es válida a
nivel de atención (0–240 min). Programa y Sector tienen ~70% de subregistro.
