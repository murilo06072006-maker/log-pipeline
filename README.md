# Log Pipeline — ETL + Alertas (Python)

## Objetivo
Construir um pipeline defensivo que:
- Ingesta logs (Linux + Windows)
- Normalize eventos para um formato comum
- Aplique regras simples (detections) e gere alertas
- Exporte relatórios (JSON/CSV)
- Seja reproduzível (README + teste + CI)

## Por que isso existe (valor pro SOC)
SOC depende de **dados limpos**. Sem normalização, todo alerta vira ruído.  
Este projeto prova capacidade de: parsing, dados, automação e pensamento de detecção.

## Stack
- Python 3.12+
- pytest (teste mínimo)
- GitHub Actions (CI)
- Logs de:
  - Linux: `auth.log` (SSH falhas)
  - Windows: eventos 4625 (falha de login) exportados para JSONL

## Arquitetura
![Arquitetura](docs/architecture.png)

Pipeline:
1) input logs → 2) parse → 3) normalize → 4) rules → 5) report/alerts

## Estrutura do repo (esperada)
- `src/` código
- `data/` logs de entrada (exemplos)
- `out/` saída (reports)
- `tests/` testes
- `docs/` diagrama + prints

## Como rodar
### 1) Ambiente Python
~~~powershell
python -m venv .venv
.\.venv\Scripts\activate
pip install -r requirements.txt
pip install pytest
~~~

### 2) Adicionar dados de teste
Coloque:
- `data/linux_auth.log`
- `data/windows_security_4625.jsonl`

### 3) Executar
~~~powershell
python .\src\log_pipeline\main.py
~~~

Saída esperada:
- `out/report.json` gerado
- stdout com resumo (events_total, alerts_total)

### 4) Rodar testes
~~~powershell
pytest -q
~~~

## Como gerar os dados (LAB)
### Linux: auth.log
~~~bash
sudo tail -n 500 /var/log/auth.log > /tmp/linux_auth.log
~~~

### Windows: exportar 4625 para JSONL
~~~powershell
$events = Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625; StartTime=(Get-Date).AddDays(-2)} -MaxEvents 200
$events |
  ForEach-Object {
    [pscustomobject]@{
      TimeCreated = $_.TimeCreated.ToString("o")
      Id          = $_.Id
      Message     = $_.Message
      IpAddress   = ($_.Properties | ForEach-Object { $_.Value } | Where-Object { $_ -match '^\d+\.\d+\.\d+\.\d+$' } | Select-Object -First 1)
    } | ConvertTo-Json -Compress
  } |
  Set-Content -Encoding UTF8 .\data\windows_security_4625.jsonl
~~~

## Evidências (prints obrigatórios)
Salvar em `docs/screenshots/`:
- pipeline_run.png (terminal com execução)
- report_json.png (conteúdo do report)
- ci_green.png (GitHub Actions passando)

## Definition of Done (v1.0)
- [ ] Pipeline roda do zero com logs de exemplo
- [ ] Normalização mínima aplicada (campos padronizados)
- [ ] 2+ regras implementadas (ex.: brute force ssh / win 4625)
- [ ] `out/report.json` gerado
- [ ] Teste smoke + CI passando
- [ ] README ensinando a reproduzir
- [ ] Tag `v1.0`

## Roadmap
- v0.1: parsing básico linux
- v0.3: parsing windows 4625
- v0.6: regras + relatório
- v1.0: CI + docs + evidências
- v1.1+: enrichment (GeoIP), mais log sources, tuning