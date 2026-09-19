<h1 align="center">Econometric Studio</h1>

<p align="center">
  <strong>Ambiente integrado de análise econométrica</strong><br>
  Desktop app com engine Python/R, 15+ modelos econométricos e interface gráfica.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-0.1.0-blue" alt="Version">
  <img src="https://img.shields.io/badge/rust-1.77+-orange?logo=rust" alt="Rust">
  <img src="https://img.shields.io/badge/python-3.10+-yellow?logo=python" alt="Python">
  <img src="https://img.shields.io/badge/tauri-2.0-purple?logo=tauri" alt="Tauri">
  <img src="https://img.shields.io/badge/license-MIT-green" alt="License">
  <img src="https://img.shields.io/badge/platform-Windows-lightgrey" alt="Platform">
</p>

<p align="center">
  <a href="#modelos-econometricos">Modelos</a> •
  <a href="#como-usar">Como Usar</a> •
  <a href="#exemplos">Exemplos</a> •
  <a href="#instalacao">Instalação</a> •
  <a href="#arquitetura">Arquitetura</a>
</p>

---

## O que é o Econometric Studio?

O **Econometric Studio** é um aplicativo desktop que integra uma interface gráfica moderna com um motor de cálculo econômico baseado em Python e R. Ele permite estimar, diagnosticar e visualizar modelos econométricos sem necessidade de programar.

**Principais características:**

- Interface gráfica com painéis dedicados para cada modelo
- Execução via CLI para automação e reprodutibilidade
- Dados de exemplo incluídos para teste imediato
- Suporte a CSV, Excel e Parquet
- portable — não requer instalação (extrair e executar)

---

## Modelos Econométricos

### Regressão

| Modelo | Descrição | Worker | Exemplo |
|--------|-----------|--------|---------|
| **OLS** | Mínimos Quadrados Ordinários com erros robustos (HC0–HC3, HAC) | `ols.py` | `y ~ x1 + x2` |
| **Logística** | Regressão logística binária com odds ratios e AUC | `logit.py` | `y ~ x1 + x2` |
| **IV/2SLS** | Variáveis Instrumentais (Two-Stage Least Squares) | `iv.py` | Dep: `y`, Endog: `x1`, Instruments: `z1, z2` |
| **Painel** | Efeitos Fixos, Efeitos Aleatórios e Diferenças Primeiras | `panel.py` | `y ~ x1 + x2` com entity/time |

### Séries Temporais

| Modelo | Descrição | Worker | Exemplo |
|--------|-----------|--------|---------|
| **ADF** | Teste Augmented Dickey-Fuller para raiz unitária | `adf.py` | Coluna `y` (300 obs) |
| **ARIMA** | AutoRegressive Integrated Moving Average com previsão | `arima.py` | `y`, ordem `[1,1,1]` |
| **Cointegração** | Teste de Johansen e VECM | `coint.py` | `y1`, `y2` (300 obs) |
| **VAR** | Vetores Autoregressivos com impulso-resposta | `var.py` | `y1`, `y2`, `y3` |
| **GARCH** | Modelos de volatilidade condicional (GARCH, EGARCH) | `garch.py` | Coluna `r` (retornos) |

### Causalidade

| Modelo | Descrição | Worker | Exemplo |
|--------|-----------|--------|---------|
| **DiD** | Diferença-em-Diferenças com tendências paralelas | `did.py` | `id`, `year`, `treated`, `y` |
| **RDD** | Regressão Descontínua com kernel e bandwidth | `rdd.py` | `y`, `score` (cutoff=0) |
| **PSM** | Pareamento por Escore de Propensão (Nearest Neighbor) | `psm.py` | `y`, `treated`, `x1`, `x2` |

### Machine Learning

| Modelo | Descrição | Worker | Exemplo |
|--------|-----------|--------|---------|
| **Ridge** | Regressão com regularização L2 | `ml.py` | `y ~ x1 + x2` |
| **Lasso** | Regressão com regularização L1 (seleção de variáveis) | `ml.py` | `y ~ x1 + x2` |
| **ElasticNet** | Regularização L1 + L2 combinada | `ml.py` | `y ~ x1 + x2` |
| **Random Forest** | Floresta aleatória para regressão/classificação | `ml.py` | `y ~ x1 + x2` |
| **Gradient Boosting** | Gradient Boosting para regressão/classificação | `ml.py` | `y ~ x1 + x2` |

### Diagnósticos e Utilitários

| Função | Descrição | Worker |
|--------|-----------|--------|
| **Diagnósticos** | Breusch-Pagan, Jarque-Bera, VIF, RESET, Durbin-Watson | `diagnostics.py` |
| **Descrição** | Estatísticas descritivas e resumo do dataset | `describe.py` |
| **Qualidade** | Detecção de outliers, missings, duplicatas, escalas | `quality.py` |
| **Pipeline** | Encadeamento de modelos via JSON | `pipeline.py` |

---

## Como Usar

### 1. Via Interface Gráfica (Desktop)

1. Execute `EconometricStudio.exe`
2. Navegue até o painel do modelo desejado
3. Carregue um arquivo CSV/Excel
4. Configure os parâmetros e clique em **Estimar**

### 2. Via CLI (Automação)

```bash
# OLS
python python/workers/ols.py '{"path":"examples/OLS/exemplo.csv","formula":"y ~ x1 + x2"}'

# ARIMA com previsão
python python/workers/arima.py '{"path":"examples/TimeSeries/serie.csv","column":"y","order":[1,1,1],"steps":10}'

# GARCH(1,1)
python python/workers/garch.py '{"path":"examples/GARCH/returns.csv","column":"r","model":"GARCH","p":1,"q":1}'

# DiD
python python/workers/did.py '{"path":"examples/DifferenceInDifferences/did.csv","id":"id","year":"year","treated":"treated","y":"y"}'
```

### 3. Via Pipeline (Encadeamento)

```json
{
  "name": "analise-completa",
  "dataset": "examples/OLS/exemplo.csv",
  "steps": [
    {"id": "ols", "worker": "ols.py", "args": {"formula": "y ~ x1 + x2"}},
    {"id": "diag", "worker": "diagnostics.py", "args": {"formula": "y ~ x1 + x2"}},
    {"id": "ml", "worker": "ml.py", "args": {"formula": "y ~ x1 + x2", "model": "Lasso"}}
  ]
}
```

---

## Exemplos

Todos os exemplos estão em `examples/` com dados sintéticos prontos para uso.

### Regressão Linear (OLS)

```bash
# Arquivo: examples/OLS/exemplo.csv (200 obs, y ~ 2·x1 + 1.5·x2 + ε)
python python/workers/ols.py '{"path":"examples/OLS/exemplo.csv","formula":"y ~ x1 + x2"}'
```

### Variáveis Instrumentais (IV)

```bash
# Arquivo: examples/IV/iv.csv (500 obs, x1 endógena, z1/z2 instrumentos)
python python/workers/iv.py '{"path":"examples/IV/iv.csv","y":"y","exog":["x2"],"endog":["x1"],"instruments":["z1","z2"]}'
```

### Séries Temporais (ARIMA)

```bash
# Arquivo: examples/TimeSeries/serie.csv (300 obs)
python python/workers/arima.py '{"path":"examples/TimeSeries/serie.csv","column":"y","order":[1,1,1],"steps":12}'
```

### Volatilidade (GARCH)

```bash
# Arquivo: examples/GARCH/returns.csv (200 obs, retornos financeiros)
python python/workers/garch.py '{"path":"examples/GARCH/returns.csv","column":"r","model":"GARCH","p":1,"q":1}'
```

### Diferença-em-Diferenças (DiD)

```bash
# Arquivo: examples/DifferenceInDifferences/did.csv (400 obs, 40 unidades × 10 períodos)
python python/workers/did.py '{"path":"examples/DifferenceInDifferences/did.csv","id":"id","year":"year","treated":"treated","y":"y"}'
```

### Regressão Descontínua (RDD)

```bash
# Arquivo: examples/RDD/rdd.csv (1300 obs, cutoff em score=0)
python python/workers/rdd.py '{"path":"examples/RDD/rdd.csv","y":"y","running":"score","cutoff":0}'
```

### Pareamento (PSM)

```bash
# Arquivo: examples/PSM/psm.csv (850 obs, tratamento binário)
python python/workers/psm.py '{"path":"examples/PSM/psm.csv","y":"y","treated":"treated","covariates":["x1","x2"]}'
```

### Machine Learning (Lasso)

```bash
# Arquivo: examples/OLS/exemplo.csv (reutilizado)
python python/workers/ml.py '{"path":"examples/OLS/exemplo.csv","formula":"y ~ x1 + x2","model":"Lasso","test_size":0.2}'
```

---

## Instalação

### Portátil (Recomendado)

1. Baixe `EconometricStudio-0.1.0-Portable.zip`
2. Extraia em uma pasta de sua escolha
3. Execute `EconometricStudio.exe`

> **Requisito:** Python 3.10+ instalado no sistema. Para instalar as dependências:
> ```bash
> pip install -r resources/python/requirements.txt
> ```

### Instalador NSIS

1. Baixe `Econometric Studio_0.1.0_x64-setup.exe`
2. Execute o instalador
3. O app estará disponível no Menu Iniciar

### Desenvolvimento

```bash
# Clone o repositório
git clone https://github.com/seu-usuario/econometric-studio.git
cd econometric-studio

# Instale dependências do frontend
cd frontend && npm install

# Execute em modo dev (hot-reload)
cd ../src-tauri && npm run tauri dev

# Compile o exe
cargo tauri build
```

---

## Estrutura do Projeto

```
econometric-studio/
├── frontend/              # React + TypeScript + Vite
│   ├── src/
│   │   ├── components/    # Painéis de cada modelo
│   │   ├── services/      # API bridge (Tauri invoke)
│   │   └── layouts/       # Shell e navegação
│   └── package.json
├── src-tauri/             # Core Rust + Tauri
│   ├── src/
│   │   ├── commands.rs    # Comandos Tauri (API boundary)
│   │   ├── analysis.rs    # Motor de análise (spawn workers)
│   │   ├── runtime.rs     # Resolução de Python/R
│   │   └── runtime/       # Diagnósticos de runtime
│   └── Cargo.toml
├── python/                # Engine de cálculo
│   ├── workers/           # 17 scripts de modelo
│   └── requirements.txt
├── examples/              # Dados de teste (10 modelos)
├── resources/             # Bundled resources (exe)
├── docs/                  # Documentação
└── README.md
```

---

## Requisitos

| Componente | Versão |
|------------|--------|
| Python | 3.10+ |
| Node.js | 18+ (para desenvolvimento) |
| Rust | 1.77+ (para compilação) |
| Tauri | 2.0 |

### Dependências Python

```
numpy>=1.26
pandas>=2.0
scipy>=1.11
statsmodels>=0.14
scikit-learn>=1.3
arch>=6.0
linearmodels>=5.0
```

---

## Licença

MIT License — Erick de S.C. Araújo

---

<p align="center">
  <img src="https://img.shields.io/badge/built%20with-Rust%20%7C%20React%20%7C%20Tauri-purple" alt="Built with">
</p>
