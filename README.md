# Fraud Predict - Detecção de Fraude em Contratos de Empréstimo

## Empresa

**CredMais Financeira** (fictícia) é uma fintech de crédito pessoal que concede empréstimos consignados e com garantia, atuando em todo o território nacional. A empresa opera com alto volume de contratações mensais e precisa identificar rapidamente contratos com indícios de fraude para reduzir prejuízo financeiro e proteger sua carteira de crédito.

## Problema de negócio

A área de Risco da CredMais identificou um aumento nos casos de fraude em contratos de empréstimo - situações em que dados cadastrais inconsistentes, padrões de pagamento atípicos ou renegociações recorrentes indicam possível fraude na tomada do crédito. A análise manual de todos os contratos é inviável dado o volume de operações, e a empresa precisa de um método automatizado para priorizar quais contratos devem passar por revisão manual da equipe de Risco.

## Objetivo

Desenvolver um modelo de Machine Learning capaz de prever a variável `Possivel_Fraude`, classificando contratos de empréstimo como fraude ou não fraude a partir de dados cadastrais do cliente e do histórico de pagamento do contrato, permitindo à equipe de Risco atuar de forma preventiva e priorizada.

## Dados

- Dataset sintético com registros de contratos de empréstimo (`data/dados_coletados10k.csv`), contendo:
  - **Dados cadastrais do cliente**: idade, sexo, estado civil, escolaridade, UF, renda, patrimônio
  - **Dados do contrato**: valor do empréstimo, juros, prazo, prazo restante, data de contratação
  - **Histórico de pagamento**: parcelas pagas em dia/atraso, dias em atraso, renegociações, saldo devedor, total pago
  - **Variável alvo**: `Possivel_Fraude` (Sim/Não)

## Metodologia

1. **Análise Exploratória (EDA)** - distribuição das variáveis, relação com a variável alvo e identificação de desbalanceamento da classe fraude (`analysis/fraud_predict_model.ipynb`)
2. **Tratamento de dados** - criação de faixas (etária, salarial, prazo, dias em atraso) para facilitar o encoding e reduzir dimensionalidade
3. **Codificação de variáveis categóricas** com Label Encoding
4. **Balanceamento da classe alvo** com SMOTE, dado o desbalanceamento natural entre contratos fraudulentos e não fraudulentos
5. **Normalização** das variáveis preditoras (MinMaxScaler)
6. **Treinamento e comparação de modelos** - Random Forest, SVM e KNN, com otimização de hiperparâmetros via GridSearchCV
7. **Deploy** - scripts para treinar o modelo (`deploy/gerar_modelo_fraude.py`) e gerar previsões em novos dados (`deploy/gerar_previsoes_fraudes.py`), com o modelo serializado em `deploy/modelo_treinado_fraude.pk`

## Resultado

O modelo **Random Forest** foi o de melhor desempenho, atingindo **99,23% de acurácia média em validação cruzada** (GridSearchCV) e **99,04% de acurácia em teste**, sem sinais de overfitting relevante entre treino e teste.

As variáveis mais relevantes para a previsão de fraude foram:
- Quantidade de parcelas em atraso (`QT_Parcelas_Atraso`)
- Quantidade total de parcelas pagas (`QT_Total_Parcelas_Pagas`)
- Total pago pelo cliente (`Total_Pago`)

Com o modelo em produção, a CredMais passa a gerar automaticamente uma lista priorizada de contratos com maior probabilidade de fraude (`predict/previsoes_fraude.xlsx`) a partir de novos dados (`predict/novos_dados.csv`), direcionando o esforço da equipe de Risco para os casos de maior risco.

## Ferramentas utilizadas

- **Linguagem**: Python
- **Manipulação e análise de dados**: pandas, numpy
- **Visualização**: matplotlib, seaborn
- **Machine Learning**: scikit-learn (RandomForestClassifier, SVC, KNeighborsClassifier, GridSearchCV, LabelEncoder, MinMaxScaler, StandardScaler)
- **Balanceamento de classes**: imbalanced-learn (SMOTE)
- **Serialização do modelo**: joblib
- **Ambiente**: Jupyter Notebook

## Estrutura do repositório

```
fraud-predict/
├── analysis/
│   └── fraud_predict_model.ipynb   # EDA, tratamento e treinamento dos modelos
├── data/                            # Datasets utilizados no treinamento
├── deploy/
│   ├── gerar_modelo_fraude.py       # Script de treinamento e serialização do modelo
│   └── modelo_treinado_fraude.pk    # Modelo treinado (Random Forest)
├── predict/
│   ├── gerar_previsoes_fraudes.py   # Script para gerar previsões em novos dados
│   ├── novos_dados.csv              # Novos contratos a serem avaliados
│   └── previsoes_fraude.xlsx        # Resultado das previsões
└── img/                              # Gráficos gerados durante a EDA
```
