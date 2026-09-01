# AVDS

AVDS é um repositório-esqueleto para gerir coletas experimentais, equipamentos e técnicas, com foco em integração contínua de novos aparelhos e na geração prática de documentação/relatórios (paperwork). O objetivo é fornecer uma API e uma estrutura modular para registrar dispositivos, executar ensaios, armazenar resultados e produzir relatórios/exports prontos para inclusão em papers.

Principais requisitos orientadores
- Fácil adicionar novos drivers/equipamentos (arquitetura de plugins).
- Interface programática (API) para automação e integração com scripts.
- Geração reproducível de relatórios (PDF/Markdown/LaTeX).
- Reprodutibilidade via Docker e versionamento de dados/metadados.

Stack recomendado (sugestão)
- Backend: Python + FastAPI (rápido para desenvolver APIs e ótimo suporte a pydantic).
- Banco de dados: PostgreSQL (relacional, confiável para metadados experimentais).
- ORM + migrações: SQLAlchemy + Alembic.
- Tarefas em background: Celery ou RQ (para aquisições longas/processamento).
- Frontend (opcional): React + Vite (simples e moderno) ou interface mínima server-rendered.
- Containers: Docker + docker-compose para dev/prod locais.
- Relatórios: Jinja2 -> HTML -> WeasyPrint (PDF) ou Pandoc/LaTeX para papers.

Estado atual deste repositório
- Atualmente contém apenas a licença. Este README propõe o esqueleto inicial e as próximas ações.

Começando (quickstart - desenvolvimento local com Docker)
1. Clone:
   git clone https://github.com/Andres-Felix/AVDS.git
   cd AVDS

2. (opcional) Crie um .env com as variáveis necessárias:
   cp .env.example .env
   # editar .env conforme necessário (DATABASE_URL, SECRET_KEY, ...)

3. Usando Docker (recomendado):
   docker-compose up -d --build
   # aguarde; a API ficará disponível em http://localhost:8000

4. Sem Docker (Python venv):
   python -m venv .venv
   source .venv/bin/activate
   pip install -r requirements.txt
   alembic upgrade head
   uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

URLs úteis (exemplos)
- Swagger / OpenAPI: GET http://localhost:8000/docs
- Health-check: GET http://localhost:8000/health

Modelos de dados (exemplo simplificado)
- Device (id, name, type, connection_info, metadata)
- Experiment (id, name, device_id, protocol, started_at, finished_at, results_json)
- Report (id, experiment_id, template, generated_at, file_url)

Exemplos de uso via curl
- Registrar um dispositivo:
  curl -X POST http://localhost:8000/devices \
    -H "Content-Type: application/json" \
    -d '{"name":"Osciloscópio A","type":"oscilloscope","connection":{"port":"/dev/ttyUSB0"}}'

- Criar um experimento:
  curl -X POST http://localhost:8000/experiments \
    -H "Content-Type: application/json" \
    -d '{"name":"Teste X","device_id":1,"protocol":"protocol.yaml"}'

- Gerar relatório PDF:
  curl -X POST http://localhost:8000/reports \
    -H "Content-Type: application/json" \
    -d '{"experiment_id":1,"template":"paper_template.tpl","format":"pdf"}'

Arquitetura recomendada (alto nível)
- app/
  - api/        # endpoints FastAPI
  - models/     # SQLAlchemy models
  - services/   # lógica de negócios (execução de protocolos, orquestração)
  - drivers/    # drivers de dispositivos (plugins) — cada driver implementa interface comum
  - reports/    # templates e geradores de relatório
  - tasks/      # background jobs (Celery workers)
- migrations/   # Alembic
- tests/        # pytest
- docker/       # Dockerfile, docker-compose.yml
- docs/         # documentação e exemplos de papers

Boas práticas e sugestões específicas para o seu objetivo
- Plugins/Drivers: padronize uma interface (ex.: connect(), configure(), run(), read()) e carregue dinamicamente drivers (entry points ou folder-based).
- Versionamento de experimentos: armazene metadados completos (versão do protocolo, versão do driver, parâmetros) para garantir reprodutibilidade.
- Exports/paperwork: mantenha templates de relatório (Jinja2) e gere PDFs automaticamente; também exporte Markdown/LaTeX para inclusão em papers.
- Testes: crie fixtures que simulam equipamentos (mocks) para testes automatizados sem hardware físico.
- CI/CD: pipeline para executar testes, lint, builds de imagens Docker e geração automática de relatórios de exemplo.
- Documentação viva: mantenha exemplos de protocolos e um diretório `examples/` com dados de referência.

Contribuir
1. Fork ou branch a partir de `main`.
2. Abra PRs com descrições claras e issue relacionada (se aplicável).
3. Adicione testes e atualize docs.
4. Use commit mensagens claras e siga semver.

Próximos passos que eu posso executar para você
- Criar este README.md no repositório (branch + PR).
- Inicializar o esqueleto do projeto (estrutura de diretórios, Dockerfile, .gitignore, requirements, exemplo de endpoint FastAPI e testes).
- Criar um template de relatório e exemplo de geração de PDF.
- Implementar um driver de exemplo (simulado) e um endpoint para registrar/rodar experimentos.

Se quiser que eu commite o README direto, responda "commitar README" e eu criarei um branch e PR com o arquivo. Se preferir primeiro ajustar o texto, diga quais pontos mudar (idioma, mais/menos técnico, ou inclusão de propósito mais específico para AVDS).