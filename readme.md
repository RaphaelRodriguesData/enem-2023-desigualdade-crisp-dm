# 📊 Desigualdade e Desempenho em Matemática no ENEM 2023 (SP)

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)
![Scikit-Learn](https://img.shields.io/badge/Scikit--Learn-Machine%20Learning-orange?logo=scikit-learn&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-Data%20Storytelling-yellow?logo=powerbi&logoColor=black)
![CRISP-DM](https://img.shields.io/badge/Metodologia-CRISP--DM-green)

Análise de Ciência de Dados aplicada aos microdados públicos do **ENEM 2023 (Estado de São Paulo)** para investigar como o contexto socioeconômico — renda familiar, escolaridade dos pais e acesso a recursos como computador e internet — se associa à presença na prova e ao desempenho em **Matemática e suas Tecnologias**.

Projeto desenvolvido como **Capstone** da disciplina *CCD210 – Fundamentos de Ciência e Visualização de Dados* do **Centro Universitário FEI**, alinhado aos Objetivos de Desenvolvimento Sustentável da ONU:
* **ODS 4:** Educação de Qualidade
* **ODS 10:** Redução das Desigualdades

---

## 🎯 Principais Conclusões

1. **A desigualdade começa antes da prova:** Entre candidatos de famílias sem renda, a taxa de abstenção chega a **53%** (52,9%). Na faixa de renda mais alta (acima de R$ 26.400), cai para **16%**.
2. **O abismo no desempenho:** Entre os presentes, apenas **5%** (5,2%) dos candidatos sem renda atingem nota acima de 700 em Matemática, contra **59%** (59,4%) na faixa de maior renda — uma proporção mais de 11 vezes maior.
3. **Capital cultural e infraestrutura importam:** Filhos de pais com pós-graduação superam os 700 pontos cerca de **12 vezes mais** do que filhos de pais que nunca estudaram (41% contra 3%). Ter 4 ou mais computadores em casa multiplica por 10 essa chance em relação a quem não possui nenhum (52% contra 5%).
4. **Contexto pesa, mas não é destino:** Modelos treinados exclusivamente com variáveis socioeconômicas explicam **27,1% da variação das notas** ($R^2 = 0,271$). O contexto revela tendências claras em nível de grupo, mas não permite prever ou rotular o potencial individual de um candidato.

---

## 🧭 Metodologia (CRISP-DM)

O projeto seguiu rigorosamente as etapas do framework **CRISP-DM** (com exceção da etapa de *Deployment*):

### 1. Entendimento do Negócio (Framework CONVO)
* **Contexto:** Desigualdade de oportunidades educacionais no acesso ao ensino superior via ENEM 2023 em SP.
* **Necessidade:** Mensurar o grau de associação entre variáveis socioeconômicas, abstenção e desempenho em Matemática.
* **Visão:** Gerar evidências claras combinando análise exploratória, estatística de correlação, modelos simples e *data storytelling*.
* **Resultado:** Relatório técnico, dashboard interativo no Power BI e apresentação executiva.

### 2. Entendimento e Preparação dos Dados
A partir da base pública do INEP (1,78 GB, 76 colunas), realizou-se a leitura otimizada em blocos (*chunks* de 200.000 linhas) filtrando candidatos de São Paulo (`SG_UF_PROVA = SP`):

```text
590.767 candidatos inscritos em SP
 ├── 178.902 ausentes ou eliminados (30,28% — não imputados por padrão MAR/MNAR)
 └── 411.865 presentes na prova de Matemática
      ├── 1.817 notas iguais a zero removidas (outliers / provas anuladas)
      └── 82.148 treineiros removidos (IN_TREINEIRO = 1)
           └── 327.900 registros na base final (83,4% ≤ 700 | 16,6% > 700)
```

* **Feature Engineering:** Aplicação de *Label Encoding* ordinal para faixas de renda e quantidade de bens (`Q006` a `Q025`) e *One-Hot Encoding* para escolaridade e ocupação dos pais (`Q001` a `Q004`), preservando categorias como `"Não sei"` fora da escala numérica (totalizando **49 atributos preditores**).
* **Prevenção de Data Leakage:** Divisão estratificada de treino e teste (80/20, `random_state=42`) com normalização *Min-Max* ajustada exclusivamente sobre o conjunto de treino para o modelo KNN.

### 3. Modelagem e Avaliação

| Abordagem / Modelo | Tarefa | Métricas no Conjunto de Teste | Baseline de Comparação |
| :--- | :--- | :--- | :--- |
| **Correlação de Spearman** | Associação ordinal | Renda (`0,44`), Computador (`0,38`), Esc. Pai (`0,35`), Esc. Mãe (`0,34`) | — |
| **Regressão Linear** | Previsão da nota contínua | **$R^2 = 0,271$** \| **MAE = 88,0** \| RMSE = 107,7 | Prever a média (MAE = 105,2) |
| **KNN ($k = 5$)** | Classificação (`Nota > 700`) | **Precisão = 0,44** \| **Recall = 0,23** \| **F1 = 0,30** | Chute classe majoritária (Recall = 0) |

---

## 📁 Estrutura do Repositório

```text
├── notebooks/
│   └── ProjetoFEiCapstone.ipynb    # Pipeline completo em Python (ETL, EDA e Modelos)
├── dashboard/
│   └── dashboard_enem_2023.pbix    # Painel interativo desenvolvido no Power BI
├── docs/
│   ├── Relatorio_Capstone_CCD210.pdf
│   └── Apresentacao_ENEM_2023.pdf
├── assets/                         # Gráficos exportados para documentação
└── README.md
```

---

## 🚀 Como Reproduzir

1. **Baixe os dados oficiais:** Acesse o portal de [Dados Abertos do INEP](https://www.gov.br/inep/pt-br/acesso-a-informacao/dados-abertos/microdados/enem) e faça o download dos **Microdados do ENEM 2023** (`MICRODADOS_ENEM_2023.csv`). *(O arquivo bruto não está hospedado neste repositório devido ao tamanho de 1,78 GB).*
2. **Instale as dependências:**
   ```bash
   pip install pandas numpy scikit-learn matplotlib seaborn jupyter
   ```
3. **Execute o notebook:** Abra `notebooks/ProjetoFEiCapstone.ipynb` (no Jupyter ou Google Colab), aponte o caminho para o arquivo CSV baixado e execute as células sequencialmente. Todas as sementes aleatórias estão fixadas em `random_state=42`.

---

## ⚠️ Limitações e Considerações Éticas

* **Associação não implica causalidade:** A presença de bens (como computador ou número de banheiros) atua como *proxy* de condição socioeconômica, não como causa direta da nota.
* **Viés de seleção por abstenção:** Como 53% dos inscritos sem renda faltaram à prova, a desigualdade observada entre os presentes pode estar subestimada.
* **Uso ético:** Os modelos descrevem padrões agregados de grupos populacionais e **não devem ser utilizados** para julgar, classificar ou selecionar indivíduos.

---

## 👥 Autor

* **Raphael Rodrigues de Oliveira** — [LinkedIn](https://www.linkedin.com/in/raphael-rodrigues-de-oliveira-9311422a0/)

*Centro Universitário FEI — São Bernardo do Campo, SP*
