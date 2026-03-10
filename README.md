# Telecom-x2-Desafio-Alura

## Sobre o projeto

Esse projeto analisa dados de uma empresa de telecomunicações pra entender quais fatores levam clientes a cancelar o serviço (churn). A ideia é usar os dados pra prever quais clientes têm mais chance de sair, e a partir disso pensar em estratégias pra reter essas pessoas.

O dataset veio de um JSON com dados de clientes, incluindo informações pessoais, tipo de contrato, serviços contratados e valores cobrados.

---

## Estrutura do projeto

├── TelecomX_EDA.ipynb # notebook parte 1 - limpeza e análise exploratória

├── TelecomX_Modelagem.ipynb # notebook parte 2 - preparação, modelagem e conclusão

├── TelecomX.csv # dados tratados (saída da parte 1)

├── TelecomX_processado.csv # dados com encoding aplicado (saída da parte 2)

├── TelecomX_importancia_variaveis.csv # importância das variáveis do random forest

└── README.md

---

## Dados utilizados

Os dados foram carregados de um JSON hospedado no GitHub. Depois de tratar, o dataset ficou com as seguintes informações:

**Variáveis numéricas:**
- `customer.tenure` — tempo como cliente (em meses)
- `account.Charges.Monthly` — valor da cobrança mensal
- `account.Charges.Total` — valor total cobrado

**Variáveis categóricas:**
- `customer.gender` — gênero do cliente
- `customer.SeniorCitizen` — se é idoso ou não
- `customer.Partner` — se tem parceiro(a)
- `customer.Dependents` — se tem dependentes
- `phone.PhoneService` — se tem serviço de telefone
- `phone.MultipleLines` — se tem múltiplas linhas
- `internet.InternetService` — tipo de serviço de internet
- `internet.OnlineSecurity`, `internet.OnlineBackup`, `internet.DeviceProtection`, `internet.TechSupport`, `internet.StreamingTV`, `internet.StreamingMovies` — serviços adicionais
- `account.Contract` — tipo de contrato (mensal, anual, dois anos)
- `account.PaperlessBilling` — se usa fatura digital
- `account.PaymentMethod` — forma de pagamento
- `Churn` — se o cliente saiu ou não (variável alvo)

---

## O que foi feito

### Parte 1 - Limpeza e análise exploratória

- Carregamento do JSON e expansão dos campos aninhados com `pd.json_normalize`
- Remoção de linhas com Churn vazio
- Conversão de `account.Charges.Total` pra numérico (tava como texto)
- Strings vazias substituídas por NaN
- Análise da distribuição do churn, gasto mensal, tempo de contrato
- Gráficos de barras, boxplots e mapa de nulos

### Parte 2 - Preparação e modelagem

**Preparação dos dados:**
- Remoção da coluna `customerID` (identificador único, não ajuda no modelo)
- Nulos em `account.Charges.Total` preenchidos com a mediana
- Encoding da variável alvo: `Churn` → Yes=1, No=0
- One-hot encoding nas variáveis categóricas com `pd.get_dummies(drop_first=True)`
- Separação em treino (70%) e teste (30%) com estratificação

**Normalização:**
- Aplicada só nos dados usados pela regressão logística (StandardScaler)
- O random forest não precisa de normalização porque é baseado em árvores

**Modelos criados:**

| Modelo | Normalização | Por que escolhi |
|---|---|---|
| Regressão Logística | Sim | Simples, interpretável, bom baseline. Os coeficientes mostram o peso de cada variável |
| Random Forest | Não | Captura relações não lineares, mostra importância das variáveis, não precisa normalizar |

Os dois modelos usam `class_weight='balanced'` porque as classes são desbalanceadas (~73% ficaram, ~27% saíram).

**Métricas avaliadas:**
- Acurácia
- Precisão
- Recall
- F1-Score
- Matriz de confusão

---

## Alguns insights da análise

- Clientes com **contrato mensal** têm muito mais chance de sair do que quem tem contrato anual ou de dois anos
- **Clientes novos** (poucos meses) saem mais. Depois de um tempo a chance diminui bastante
- Quem paga **mais por mês** tende a sair mais
- O método de pagamento por **boleto eletrônico** está associado a maior evasão
- Clientes **sem suporte técnico** e **sem backup online** também saem mais

Exemplos de gráficos gerados:

- Distribuição do churn (barras e pizza)
- Boxplot de tenure e gasto mensal por churn
- Heatmap de correlação entre variáveis
- Scatter plot de tenure vs total gasto colorido por churn
- Barras comparando as métricas dos dois modelos
- Importância das variáveis (coeficientes da regressão e feature importance do random forest)

---

## Como rodar

### Requisitos

O projeto foi feito no Google Colab, então não precisa instalar nada além do que já vem no ambiente. Mas caso queira rodar local, as bibliotecas usadas são:

- pandas
- numpy
- requests
- matplotlib
- seaborn
- scikit-learn

### Conclusão
Os dois modelos tiveram resultados parecidos. O recall é a métrica mais importante nesse caso porque a gente quer identificar o máximo de clientes que vão sair pra poder agir antes.

Os principais fatores que influenciam o churn são o tipo de contrato, tempo como cliente, gasto mensal e método de pagamento. Com base nisso, algumas estratégias de retenção seriam:

Oferecer incentivos pra migrar de contrato mensal pra anual
Acompanhar melhor os clientes nos primeiros meses
Revisar os planos mais caros
Incentivar pagamento por débito automático ao invés de boleto
