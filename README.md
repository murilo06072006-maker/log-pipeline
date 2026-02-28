# Log Pipeline — ETL + Alertas (Python)

## Objetivo
Pipeline defensivo para:
- ler logs (Linux auth.log + Windows 4625 exportado)
- normalizar eventos
- aplicar regras simples (detections)
- gerar relatório e alertas (JSON)

## Arquitetura
![Arquitetura](docs/architecture.png)

## Como rodar
1) Criar venv e instalar deps
2) Colocar logs em `data/`
3) Rodar `python src/log_pipeline/main.py`
4) Validar `out/report.json`

## Evidências (prints obrigatórios)
- pipeline_run.png
- report_json.png
- ci_green.png

## Definition of Done (v1.0)
- roda do zero
- gera report
- tem testes + CI
- tag v1.0
