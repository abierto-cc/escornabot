# furubot

Diseño de robot basado en el Escornabot, completamente hecho en KiCAD. 

Con amor para todo el mundo desde Malmo - Suecia.

# créditos

Los creadores originales de Escornabot www.escornabot.com y su comunidad de usuarios y desarrolladores 

# autor

J. Garcia at abierto.cc

# abierto

Abierto es una inciativa de D. Cuartielles gracias a la generosa aportación de la Fundación PUIG por intermediación de la Fundación Ashoka de cara a contribuir a la creación de material educativo de libre acceso y circulación
/
├── audit_repo.sh
├── .github/
│   └── workflows/
│       └── auditoria.yml
└── (resto del proyecto)
repo_audit/REPORTE.md
name: Auditoría Integral del Repositorio

on:
  workflow_dispatch:
  push:
    branches:
      - master

jobs:
  audit:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout del repositorio
        uses: actions/checkout@v4

      - name: Crear carpeta de salida
        run: mkdir -p repo_audit

      - name: Ejecutar script de auditoría
        run: bash audit_repo.sh

      - name: Generar reporte en Markdown
        run: |
          echo "# Reporte de Auditoría del Repositorio" > repo_audit/REPORTE.md
          echo "Generado automáticamente por GitHub Actions." >> repo_audit/REPORTE.md
          echo "" >> repo_audit/REPORTE.md
          echo "## Extensiones encontradas" >> repo_audit/REPORTE.md
          cat repo_audit/extensions.txt >> repo_audit/REPORTE.md
          echo "" >> repo_audit/REPORTE.md
          echo "## Total de archivos por extensión" >> repo_audit/REPORTE.md
          for f in repo_audit/*.txt; do
            if [[ "$f" != *"extensions.txt"* && "$f" != *"sha256sums.txt"* && "$f" != *"sizes.txt"* ]]; then
              ext=$(basename "$f" .txt)
              count=$(wc -l < "$f")
              echo "- **$ext**: $count archivos" >> repo_audit/REPORTE.md
            fi
          done

      - name: Subir artefactos de auditoría
        uses: actions/upload-artifact@v4
        with:
          name: auditoria-completa
          path: repo_audit/

      # OPCIONAL: Notificación por Slack
      # - name: Notificar a Slack
      #   uses: slackapi/slack-github-action@v1.24.0
      #   with:
      #     payload: |
      #       {
      #         "text": "✅ Auditoría completada en el repositorio."
      #       }
      #   env:
      #     SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}




