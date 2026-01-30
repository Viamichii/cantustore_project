# CantuStore — Data Pipeline (Databricks Free) + SQL Challenges

Este repositório contém a resolução de um teste técnico com:
- Pipeline em **Databricks Free** (camadas **Bronze → Silver → Gold**)
- Respostas analíticas sobre **carrinhos abandonados**
- Resolução das **Questões SQL (Parte 1)** com **idempotência** (CREATE IF NOT EXISTS + MERGE)

---

## ✅ Stack
- Databricks Free (Spark / Delta Lake)
- PySpark (ingestão e padronização)
- SQL (análises e desafios)

---

## 🧱 Arquitetura de Dados (Bronze / Silver / Gold)

### Bronze (espelho da fonte)
- Lê arquivos CSV/Parquet da RAW
- Salva em Delta
- Adiciona `_ingest_ts` (timestamp de ingestão)

### Silver (padronização básica)
- Tipagem/limpeza do essencial (datas, ids de join, métricas)
- Tratamento de valores inválidos (ex: `try_cast`)
- Mantém modelo pronto para analytics

### Gold (relatórios e respostas)
- Views/queries para:
  - produtos mais abandonados
  - duplas de produtos
  - aumento mês a mês
  - produtos “novos” (primeiro mês)
  - abandonos por estado
  - relatório diário (abandonados, itens, valor não faturado)
  - export do TXT top50

---

## 📂 Estrutura do Projeto

- `notebooks/` → notebooks exportados do Databricks (ordem de execução)
- `sql/` → queries finais organizadas
- `docs/` → arquitetura e dicionário de dados
- `outputs/` → entregáveis (TXT top50, relatórios)
- `data_sample/` → amostras pequenas para referência (não contém dados pesados)

---

## ▶️ Como rodar no Databricks Free

1) Faça upload dos arquivos para o Volume RAW:
- `workspace.cantustore.raw_prova_dados`

2) Rode os notebooks na ordem:
- `notebooks/01_bronze.py`
- `notebooks/02_silver.py`
- `notebooks/03_gold.sql`
- `notebooks/04_questao_1.sql`

3) Volumes esperados:
- `workspace.cantustore.raw_prova_dados`
- `workspace.cantustore.bronze_prova_dados`
- `workspace.cantustore.silver_prova_dados`
- (opcional) `workspace.cantustore.gold_prova_dados`

---

## 🧠 Regras e Premissas usadas

### Carrinho abandonado
Foi considerado **abandonado** todo carrinho em `tb_carts` que **não possui match** em `tb_paymentinfos` via:
- `tb_carts.p_paymentinfo = tb_paymentinfos.PK`

Uma view base foi criada:
- `vw_base_carts` com `cart_status` = `abandoned` ou `completed`

---

## 📌 Entregáveis (Questão 2)
- Relatório diário: qtd carrinhos abandonados, itens abandonados, valor não faturado
- Export `.txt` com top 50 carrinhos abandonados por `p_totalprice`

O TXT segue o layout:
`carts.PK|carts.createdTS|carts.p_totalprice|user.p_uid|payment|modes.p_code|paymentinfos.p_installments|cmssitelp.p_name|addresses.p_postalcode|sum(cartentries.p_quantity)|count(cartentries.PK)`

Arquivo gerado em:
- `outputs/top50_abandoned.txt`

---

## 🧩 Questões SQL (Parte 1)
As tabelas de apoio foram criadas com idempotência:
- `times`, `jogos`, `comissoes`, `colaboradores`
Usando:
- `CREATE TABLE IF NOT EXISTS`
- `MERGE INTO` (para rodar várias vezes sem duplicar)

As queries finais estão em:
- `sql/questao_1.sql`

---

## Autor
Victor Amichi  
GitHub: https://github.com/Viamichii
