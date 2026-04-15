# 📊 Facebook Connector Python

Conector Python para a **Facebook Ads API** que extrai dados de campanhas publicitárias, realiza transformações (ETL) e persiste os resultados em um banco de dados. Ideal para times de marketing e dados que precisam automatizar a coleta e análise de métricas de anúncios no Facebook.

---

## 🚀 Funcionalidades

- **Extração de entidades**: coleta campanhas, conjuntos de anúncios (adsets) e anúncios diretamente da Facebook Ads API.
- **Extração de insights**: busca métricas de desempenho segmentadas por dispositivo (reach, clicks, impressions, spend, frequency, actions).
- **Transformações (ETL)**:
  - Normalização e padronização de nomes de campanhas com extração de padrões via regex.
  - Expansão do campo `targeting` dos adsets (geo_locations, regiões, cidades, faixas etárias e interesses).
  - Normalização de texto (remoção de acentos, lowercase).
  - Tipagem correta das colunas para carga no banco de dados.
- **Carga no banco de dados**: insere as entidades e insights processados em um banco de dados configurável.

---

## 🗂️ Estrutura do Projeto

```
facebook-connector-python/
│
├── aplicacao.py              # Ponto de entrada principal — orquestra o pipeline ETL completo
├── fb_entidades.py           # Funções de extração: campanhas, adsets e anúncios
├── fb_insights.py            # Funções de extração de insights da API
├── fb_etl.py                 # Transformações dos dados extraídos
├── fb_database_management.py # Funções de carga no banco de dados
├── configuracao.py           # Variáveis de configuração (tokens, IDs) — não versionado
├── playground.ipynb          # Notebook para testes e exploração interativa
└── .gitignore
```

---

## ⚙️ Pré-requisitos

- Python 3.8+
- Conta de desenvolvedor no [Meta for Developers](https://developers.facebook.com/)
- Access Token com permissões de leitura na conta de anúncios
- Banco de dados configurado (PostgreSQL, MySQL ou similar)

---

## 📦 Instalação

```bash
# Clone o repositório
git clone https://github.com/ebbonfimm/facebook-connector-python.git
cd facebook-connector-python

# Instale as dependências
pip install facebook-business pandas
```

---

## 🔐 Configuração

Crie um arquivo `configuracao.py` na raiz do projeto com suas credenciais:

```python
ACCESS_TOKEN = "seu_access_token_aqui"
APP_SECRET   = "seu_app_secret_aqui"
APP_ID       = "seu_app_id_aqui"
AD_ACCOUNT   = ["act_123456789"]  # Lista de contas de anúncios
```

> ⚠️ **Nunca versione o arquivo `configuracao.py`!** Ele já está incluído no `.gitignore`.

---

## ▶️ Como usar

Execute o pipeline completo rodando:

```bash
python aplicacao.py
```

O fluxo executado é:

```
1. Autenticação na Facebook Ads API
        ↓
2. Extração das entidades (campanhas, adsets, anúncios)
        ↓
3. Transformações ETL
   - Normalização dos nomes de campanha + extração de padrões
   - Expansão do targeting dos adsets (geo, idade, interesses)
        ↓
4. Carga das entidades no banco de dados
        ↓
5. Extração e carga dos insights por dispositivo
```

---

## 🧩 Módulos em Detalhe

### `fb_entidades.py`
Responsável pela extração bruta das três entidades principais da API:
- `obter_campanhas()` — lista todas as campanhas da conta.
- `obter_conjunto_anuncios()` — lista todos os adsets com seus dados de targeting.
- `obter_anuncios()` — lista todos os anúncios ativos.

### `fb_insights.py`
- `obter_insights(breakdown, account)` — retorna métricas de desempenho segmentadas. Atualmente suporta o breakdown por **dispositivo**.

### `fb_etl.py`
Contém três funções de transformação:
- `etl_campaign_name(dataset)` — normaliza nomes de campanha, extrai até 3 padrões via regex `[...]` e classifica campanhas em `padrao_antigo` (antes de 27/06/2022) ou `padrao_novo`.
- `etl_direcionamento(dataset)` — expande o campo `targeting` dos adsets, criando colunas separadas para regiões, cidades, faixa etária e interesses.
- `etl_insights(insights)` — converte o campo `actions` para inteiro, renomeia colunas e aplica tipagem correta.

### `fb_database_management.py`
- `insert_entities(tabela, dataframe)` — insere ou atualiza entidades no banco.
- `inserir_insights(breakdown, dataframe)` — insere métricas de insights no banco.

---

## 📓 Playground

O arquivo `playground.ipynb` é um Jupyter Notebook para exploração interativa dos dados. Útil para testar queries na API, inspecionar respostas e prototipar novas transformações antes de incorporá-las ao pipeline.

---

## 🤝 Contribuindo

Contribuições são bem-vindas! Sinta-se à vontade para abrir uma _issue_ ou enviar um _pull request_.

---

## 📄 Licença

Este projeto não possui licença definida. Entre em contato com o autor para mais informações.
