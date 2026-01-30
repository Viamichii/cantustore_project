# CantuStore — Data Pipeline (Databricks) + SQL

Este repositório contém a resolução de um teste técnico com:
- Pipeline em **Databricks Free** (camadas **Bronze → Silver → Gold**)
- Respostas analíticas sobre **carrinhos abandonados**

Este projeto é para o arquivo **ProvaDados**, e os arquivos utilizados (CSV e Parquet) foram baixados deste link:
https://corpcantuinc-my.sharepoint.com/personal/wilian_selzlein_cantustore_com_br/_layouts/15/guestaccess.aspx?e=zbngLN)&share=Eu_z6X6MAd9Jt-pZSNkod5EBpMgW_PckxPLjSjLkF4w_ZA

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

- `questao_1/` → resposta da Questão 1 (notebook e tabelas finais)
- `questao_2/` → resposta da Questão 2 (notebook, tabelas e TXT top 50)
- `dbultils.ipynb` e `rascunho.ipynb` → rascunhos locais (ignorado no git)

---

## ▶️ Como rodar no Databricks Free

1) Faça upload dos arquivos para o Volume RAW:
- `workspace.cantustore.raw_prova_dados`

2) Rode os notebooks na ordem:
- `questao_1/questao_1.ipynb`
- `questao_2/questao_2.ipynb`

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

Em alguns casos, preferi manter a resposta em formato de tabela completa, sem aplicar filtros adicionais, para facilitar uso posterior em BI.

---

## 📌 Entregáveis (Questão 1)
As tabelas de apoio foram criadas com idempotência:
- `times`, `jogos`, `comissoes`, `colaboradores`
Usando:
- `CREATE TABLE IF NOT EXISTS`
- `MERGE INTO` (para rodar várias vezes sem duplicar)

As respostas finais estão em:
- `questao_1/questao_1.ipynb`
- `questao_1/README.md`

---

## 📌 Entregáveis (Questão 2)
- Relatório diário: qtd carrinhos abandonados, itens abandonados, valor não faturado
- Export `.txt` com top 50 carrinhos abandonados por `p_totalprice`

O TXT segue o layout:
`carts.PK|carts.createdTS|carts.p_totalprice|user.p_uid|payment|modes.p_code|paymentinfos.p_installments|cmssitelp.p_name|addresses.p_postalcode|sum(cartentries.p_quantity)|count(cartentries.PK)`

Arquivo gerado em:
- `questao_2/top50_abandoned.txt`

---

## Autor
Victor Amichi  
GitHub: https://github.com/Viamichii
