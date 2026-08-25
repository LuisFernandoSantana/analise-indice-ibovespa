# Previsão Direcional do IBOVESPA (D+1)

Projeto de **classificação binária** para prever se o **IBOVESPA** vai fechar em **alta (↑) ou baixa (↓)** no próximo pregão (**D+1**), usando dados históricos e engenharia de atributos com indicadores técnicos.

> **Objetivo do desafio:** prever a tendência do IBOVESPA no dia seguinte com base em dados históricos, avaliando no **último mês (30 pregões)** e buscando **acurácia mínima de 75%**.



## Contexto do Problema

A volatilidade do IBOVESPA gera incerteza para tomada de decisão. A proposta aqui é construir um modelo que, dado o comportamento histórico recente do índice, indique se o fechamento do próximo pregão tende a subir ou cair.

Este modelo pode ser usado como **insumo para dashboards internos** e análises quantitativas.



##  Dados

- **Fonte (pública):**
  - link: https://br.investing.com/indices/bovespa-historical-data  
  - Neste projeto usamos **Yahoo Finance via `yfinance`** pela praticidade de aquisição via API.
- **Frequência:** diária
- **Período utilizado:** ~10 anos

Estrutura típica (OHLCV):
- `data`, `abertura (Open)`, `maxima (High)`, `minima (Low)`, `fechamento (Close)`, `volume (Volume)`

### Análise exploratória (distribuição dos fechamentos)
<p align="center">
  <img src="img/grafico%201.png" alt="Distribuição dos fechamentos do IBOVESPA" width="950">
</p>

### Tendência anual do fechamento médio (últimos 10 anos)
<p align="center">
  <img src="img/grafico%202.png" alt="Tendência anual do fechamento médio do IBOVESPA" width="950">
</p>


## Target (Variável Alvo)

Criamos uma variável binária `tendencia` comparando o fechamento do dia **D** com o fechamento do dia **D+1**:

- `tendencia = 1` quando ocorre **queda** no D+1 (fechamento amanhã < fechamento hoje)
- `tendencia = 0` caso contrário (**alta** ou manutenção)

> Observação: a codificação 0/1 é apenas convenção. O importante é representar “sobe vs desce” respeitando a ordem temporal.



## Engenharia de Atributos (Features)

Foram criadas features para capturar **tendência, momentum** e dinâmica recente do mercado, como:

- **Retorno / variação diária**
- **Médias móveis** (ex.: MM20, SMA5, SMA10)
- **RSI (ex.: RSI_7)**
- **Lags** (defasagens do comportamento recente)

Essas variáveis ajudam o modelo a capturar acelerações, reversões e padrões recorrentes.


## Preparação e Validação Temporal

Para evitar **data leakage** e respeitar a natureza sequencial da série:

- **Treino:** histórico até antes do último mês
- **Teste:** **últimos 30 pregões** (conforme exigido no desafio)

A divisão é feita mantendo a ordem cronológica (sem embaralhar).


## Modelos

### Baseline
- **Random Forest** como referência inicial.

### Modelo final
- **XGBoost (XGBClassifier)**, por:
- ótimo desempenho em dados tabulares
- capacidade de capturar relações não-lineares
- controles de regularização para reduzir overfitting


## Resultados (Teste: 30 pregões)

- **Acurácia:** **73,33%** (22 acertos em 30)
- **ROC AUC:** ~**0,73**
- **Features de maior contribuição:** destaque para **RSI_7** e **MM20** (conforme análise de importância)

> Resultado ficou **próximo da meta (75%)**, mas abaixo do requisito mínimo.

### Matriz de Confusão (XGBoost)
<p align="center">
  <img src="img/grafico%203.png" alt="Matriz de Confusão - XGBoost" width="520">
</p>

### Importância das Features (XGBoost)
<p align="center">
  <img src="img/grafico%204.png" alt="Importância das Features - XGBoost" width="950">
</p>


## Estrutura do Repositório

```text
├─ img/
│  ├─ grafico 1.png
│  ├─ grafico 2.png
│  ├─ grafico 3.png
│  └─ grafico 4.png
├─ notebooks/
│  └─ Tech_Challenge_IBOVESPA.ipynb
├─ docs/
│  ├─ apresentacao.pdf (ou .pptx)
│  └─ imagens/ (curva ROC, matriz confusão, etc.)
├─ README.md
└─ requirements.txt
