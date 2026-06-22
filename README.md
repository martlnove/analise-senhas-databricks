# Análise de Senhas Vazadas - PySpark / Databricks

Versão distribuída da análise de **14 milhões de senhas** do dataset *rockyou*, desenvolvida em **PySpark** no **Databricks**. O projeto porta uma análise originalmente feita em pandas/scikit-learn para o ambiente Spark, demonstrando processamento de dados em escala e o fluxo de trabalho em uma plataforma Lakehouse.

> Versão original (pandas) com a análise completa e o dashboard: [analise-senhas-clusterizacao](https://github.com/martlnove/analise-senhas-clusterizacao)

---

## Objetivo

Reproduzir, no ambiente distribuído do Databricks, a análise de força de senhas vazadas - desde o processamento dos 14 milhões de registros até a clusterização e os modelos de classificação. O foco é demonstrar o uso de **PySpark**, **Delta Lake** e a integração entre processamento distribuído e Machine Learning.

---

## Arquitetura

O projeto separa o que roda de forma distribuída do que roda sobre amostra, refletindo um padrão comum em produção:

| Etapa | Onde roda | Tecnologia |
|---|---|---|
| Carga, limpeza, feature engineering | 14M registros (distribuído) | PySpark |
| Rotulagem por regras | 14M registros (distribuído) | PySpark |
| Persistência intermediária | Tabelas Delta | Delta Lake |
| Clusterização (K-Means) | Amostra de 500 mil | scikit-learn |
| Modelos supervisionados | Amostra de 500 mil | scikit-learn |

O processamento pesado é distribuído em Spark; o treino de modelos roda sobre amostra representativa - abordagem usual quando o volume de dados é processado em escala mas o treino do modelo dispensa o dataset completo.

---

## Notebooks

### `01_carga_limpeza_features.ipynb`
- Configuração do ambiente (schema e Volume no Unity Catalog)
- Carregamento dos 14 milhões de senhas
- Limpeza (nulos e duplicatas) de forma distribuída
- Feature engineering com funções nativas do Spark (9 atributos por senha)
- Persistência como tabela Delta

### `02_rotulagem_clusterizacao.ipynb`
- Rotulagem por regras manuais sobre os 14M (PySpark)
- Extração de amostra representativa
- Clusterização K-Means (scikit-learn) com mapeamento de clusters para força
- Comparação entre os dois métodos de rotulagem
- Persistência da amostra rotulada como tabela Delta

### `03_modelos_ml.ipynb`
- Treino e comparação de três modelos (Rede Neural, Random Forest, Regressão Logística)
- Avaliação nos dois cenários de rotulagem
- Análise crítica de *label leakage*

---

## Principais conceitos demonstrados

- **PySpark:** DataFrames, funções de coluna nativas, transformações distribuídas
- **Delta Lake:** persistência de tabelas Delta entre etapas do pipeline
- **Unity Catalog / Volumes:** armazenamento no ambiente serverless da Free Edition
- **Integração Spark + scikit-learn:** processamento distribuído de dados + treino de modelo sobre amostra
- **Feature engineering em escala:** extração de atributos via `regexp_replace`/`length` em vez de funções linha a linha

---

## Stack

`Databricks` · `PySpark` · `Delta Lake` · `Python` · `scikit-learn`

---

## Observações

- Desenvolvido no **Databricks Free Edition** (ambiente serverless). Algumas operações foram adaptadas às restrições desse ambiente (por exemplo, o treino de modelos com scikit-learn sobre amostra, em vez do Spark MLlib).
- O dataset `rockyou.txt` não é versionado (arquivo grande e sensível). Ver o repositório original para instruções de obtenção.
- Análise crítica de *label leakage* documentada no notebook de modelos - os altos índices de acurácia refletem a sobreposição entre features e rótulos, não a qualidade preditiva real.

---

## Autor

**Lucas Souza Silveira Martins**
Bacharelando em Sistemas de Informação - Faculdade Dom Bosco de Porto Alegre
[LinkedIn](https://www.linkedin.com/in/lucasmartinsdev9) · [GitHub](https://github.com/martlnove)
