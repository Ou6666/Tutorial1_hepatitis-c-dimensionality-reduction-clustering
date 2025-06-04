# Tutorial 1: Aprendizagem Não Supervisionada - Redução de Dimensionalidade e Clustering no Dataset HCV

**Aluno:** Ouhao Wu / Nº:123542 / Grupo: ANNS-9

---

## Visão Geral do Projeto

Este projeto explora o dataset HCV (Hepatitis C Virus) utilizando técnicas de aprendizagem não supervisionada. O objetivo principal é aplicar métodos de redução de dimensionalidade para visualização e simplificação dos dados, seguido pela aplicação de algoritmos de clustering para identificar agrupamentos naturais de indivíduos com base em seus perfis laboratoriais e demográficos. A análise visa descobrir estruturas inerentes aos dados sem utilizar as informações de diagnóstico ('Category') durante o treinamento dos modelos não supervisionados, usando-as posteriormente para interpretação e validação dos resultados.

---

## Índice

*   [1. Introdução](#1-introdução)
    *   [1.1. Contexto do Dataset](#11-contexto-do-dataset)
    *   [1.2. Objetivos da Análise](#12-objetivos-da-análise)
*   [2. Metodologia](#2-metodologia)
    *   [2.1. Carregamento e Exploração Inicial dos Dados](#21-carregamento-e-exploração-inicial-dos-dados)
    *   [2.2. Pré-processamento e Limpeza de Dados](#22-pré-processamento-e-limpeza-de-dados)
    *   [2.3. Redução de Dimensionalidade](#23-redução-de-dimensionalidade)
    *   [2.4. Clustering](#24-clustering)
*   [3. Resultados e Discussão](#3-resultados-e-discussão)
    *   [3.1. Visualização Pós-Redução de Dimensionalidade](#31-visualização-pós-redução-de-dimensionalidade)
    *   [3.2. Análise dos Clusters](#32-análise-dos-clusters)
    *   [3.3. Comparação das Técnicas](#33-comparação-das-técnicas)
*   [4. Conclusões](#4-conclusões)
*   [5. Como Executar](#5-como-executar)
*   [6. Dependências](#6-dependências)

---

## 1. Introdução

### 1.1. Contexto do Dataset

O dataset utilizado é o **HCV dataset (hcvdat0.csv)**, contendo dados de 615 indivíduos. As informações incluem:
*   **Dados Demográficos:** Idade, Sexo.
*   **Resultados de 10 Testes Laboratoriais Sanguíneos:** ALB, ALP, ALT, AST, BIL, CHE, CHOL, CREA, GGT, PROT.
*   **Categoria de Diagnóstico (usada para interpretação):**
    *   '0=Blood Donor' (Doadores de Sangue saudáveis)
    *   '0s=suspect Blood Donor' (Suspeitos de Doadores de Sangue)
    *   '1=Hepatitis' (Pacientes com Hepatite C)
    *   '2=Fibrosis' (Pacientes com Fibrose Hepática)
    *   '3=Cirrhosis' (Pacientes com Cirrose Hepática)

O dataset apresenta valores ausentes ('NA') e foi originalmente utilizado em estudos sobre o uso de machine learning em diagnósticos laboratoriais (Hoffmann et al., 2018).

### 1.2. Objetivos da Análise

1.  Realizar o pré-processamento adequado dos dados, incluindo tratamento de valores ausentes e codificação de variáveis categóricas.
2.  Aplicar técnicas de redução de dimensionalidade (PCA, t-SNE, UMAP) para visualizar a estrutura dos dados em um espaço de menor dimensão.
3.  Utilizar algoritmos de clustering (K-Means, DBSCAN, Clustering Hierárquico) para identificar grupos intrínsecos nos dados.
4.  Analisar e interpretar os clusters formados, comparando-os com as categorias de diagnóstico originais para extrair insights sobre os perfis dos pacientes.
5.  Documentar todo o processo e resultados no Jupyter Notebook fornecido.

---

## 2. Metodologia

### 2.1. Carregamento e Exploração Inicial dos Dados
Os dados foram carregados do arquivo `hcvdat0.csv`. A exploração inicial incluiu a verificação das dimensões, tipos de dados, estatísticas descritivas, contagem de valores ausentes e distribuição das variáveis, especialmente 'Category' e 'Sex'.

### 2.2. Pré-processamento e Limpeza de Dados
As seguintes etapas foram realizadas:
*   **Separação da Variável Alvo:** A coluna 'Category' foi separada para uso posterior na interpretação dos clusters.
*   **Codificação de Variáveis Categóricas:** A variável 'Sex' foi transformada usando One-Hot Encoding (convertida para `Sex_m`).
*   **Tratamento de Valores Ausentes:** Valores ausentes nas colunas de testes laboratoriais (ALB, ALP, ALT, CHOL, PROT) foram imputados utilizando a mediana de cada coluna.
*   **Feature Scaling:** Todas as features numéricas foram padronizadas (StandardScaler) para terem média zero e desvio padrão unitário, garantindo que todas as features contribuam de forma equitativa para os algoritmos.

### 2.3. Redução de Dimensionalidade
Foram aplicadas três técnicas para reduzir a dimensionalidade dos dados processados, principalmente para fins de visualização em 2D:
*   **Análise de Componentes Principais (PCA):** Utilizada para identificar as direções de maior variância e como baseline.
*   **t-SNE (t-distributed Stochastic Neighbor Embedding):** Técnica não linear eficaz na visualização de estruturas locais e agrupamentos. Parâmetros: `perplexity=55`, `n_iter=1550`.
*   **UMAP (Uniform Manifold Approximation and Projection):** Outra técnica não linear que busca preservar tanto a estrutura local quanto global dos dados. Parâmetros: `n_neighbors=15`, `min_dist=0.1`.

### 2.4. Clustering
Três algoritmos de clustering foram aplicados aos dados padronizados (`scaled_features`):
*   **K-Means:**
    *   O número ótimo de clusters (k) foi avaliado usando o Método do Cotovelo (WCSS) e o Coeficiente de Silhueta.
    *   `k=2` foi escolhido com base no pico do Coeficiente de Silhueta.
*   **DBSCAN (Density-Based Spatial Clustering of Applications with Noise):**
    *   Os parâmetros `eps` e `min_samples` foram estimados. `min_samples` foi definido como `2*D` (onde D é a dimensionalidade) e `eps` foi escolhido a partir do gráfico k-distance.
    *   Parâmetros usados: `eps=3.00`, `min_samples=24`.
*   **Clustering Hierárquico Aglomerativo:**
    *   O método de ligação 'ward' foi utilizado.
    *   O número de clusters foi determinado pela análise do dendrograma, resultando em `k=3`.

---

## 3. Resultados e Discussão

### 3.1. Visualização Pós-Redução de Dimensionalidade
*   **PCA:** Mostrou considerável sobreposição entre as categorias, indicando que a variância linear principal não separa bem os grupos. Os dois primeiros componentes explicaram aproximadamente 36.3% da variância.
*   **t-SNE:** Ofereceu uma melhor separação visual dos grupos em comparação com o PCA, começando a distinguir os estágios mais avançados da doença (Fibrose, Cirrose), mas com foco na estrutura local.
*   **UMAP:** Forneceu a visualização mais clara e com clusters mais compactos, separando bem os grupos de Fibrose e Cirrose e equilibrando melhor a estrutura local e global.

As visualizações com t-SNE e UMAP sugeriram que existem estruturas não lineares nos dados que podem diferenciar os grupos de pacientes.

### 3.2. Análise dos Clusters
Os resultados do clustering foram projetados no espaço 2D do PCA para visualização e comparados com as categorias originais através de tabelas de contingência.

*   **K-Means (k=2):**
    *   Cluster 0: Agrupou a vasta maioria dos 'Blood Donor'.
    *   Cluster 1: Agrupou a maioria dos 'suspect Blood Donor', 'Hepatitis', 'Fibrosis' e 'Cirrhosis'.
    *   Este resultado indica uma boa separação entre doadores saudáveis e indivíduos com alguma condição ou suspeita.

*   **DBSCAN (eps=3.00, min_samples=24):**
    *   Cluster 0: Identificou um núcleo denso correspondente principalmente aos 'Blood Donor'.
    *   Cluster -1 (Ruído): Classificou a maioria dos indivíduos das outras categorias ('suspect Blood Donor', 'Hepatitis', 'Fibrosis', 'Cirrhosis') e alguns 'Blood Donor' atípicos como ruído.
    *   Mostrou-se eficaz em isolar o grupo mais homogêneo e denso.

*   **Clustering Hierárquico (k=3, Ward):**
    *   Cluster 0: Conseguiu isolar a maioria dos casos de 'Cirrhosis'.
    *   Cluster 1: Agrupou a maioria dos 'Blood Donor' restantes, 'Hepatitis', 'Fibrosis' e 'suspect Blood Donor'.
    *   Cluster 2: Formado predominantemente por 'Blood Donor', sugerindo um possível subgrupo dentro dos indivíduos saudáveis.
    *   Este método foi o único que conseguiu destacar um cluster específico para um dos estágios mais avançados da doença (Cirrose).

### 3.3. Comparação das Técnicas
(Consulte a "Tabela de Comparação Final das Técnicas Aplicadas" no notebook para um resumo detalhado.)
*   **Visualização:** UMAP foi superior para revelar a estrutura dos dados.
*   **Clustering:**
    *   K-Means (k=2) foi eficaz na separação binária Doador vs. Não-Doador.
    *   DBSCAN isolou o núcleo dos Doadores, tratando os demais como outliers/ruído.
    *   Hierárquico (k=3) ofereceu o insight mais clinicamente relevante ao isolar a maioria dos casos de Cirrose.

---

## 4. Conclusões

A análise não supervisionada do dataset HCV demonstrou que:
1.  Técnicas de redução de dimensionalidade não lineares como **UMAP e t-SNE são essenciais** para visualizar a separação potencial entre os diferentes grupos de pacientes, superando a PCA.
2.  A distinção entre **Doadores de Sangue e Não-Doadores (incluindo pacientes e suspeitos) é a estrutura mais proeminente** nos dados, capturada de forma robusta pelo K-Means (com k=2) e pelo DBSCAN (que isolou os doadores como o principal cluster denso).
3.  O **Clustering Hierárquico (com k=3 e método Ward) foi o mais promissor em começar a distinguir estágios específicos da doença**, isolando um cluster enriquecido com pacientes com Cirrose.
4.  A separação automática e clara entre todos os estágios da doença (Hepatite, Fibrose, Cirrose) permanece desafiadora apenas com os métodos de clustering e parâmetros testados, sugerindo que, embora existam padrões, eles podem ser sutis ou exigir abordagens mais especializadas ou, eventualmente, modelos supervisionados para uma classificação precisa.

Este estudo ressalta o potencial da aprendizagem não supervisionada para gerar hipóteses e descobrir padrões em dados médicos complexos.

---

## 5. Como Executar

1.  **Clone o repositório:**
    ```bash
    git clone https://github.com/seu-usuario/seu-repositorio.git
    cd seu-repositorio
    ```
2.  **Crie um ambiente virtual (recomendado) e instale as dependências:**
    ```bash
    python -m venv venv
    source venv/bin/activate  # No Windows: venv\Scripts\activate
    pip install -r requirements.txt
    ```
3.  **Execute o Jupyter Notebook:**
    Abra e execute o arquivo `tutorial_hcv_unsupervised.ipynb` (ou o nome que tu deste ao seu notebook) em um ambiente Jupyter (Jupyter Lab ou Jupyter Notebook).

    O dataset `hcvdat0.csv` deve estar no mesmo diretório que o notebook, ou o caminho no notebook deve ser ajustado.

---

## 6. Dependências

As principais bibliotecas utilizadas neste projeto são:

*   pandas
*   numpy
*   matplotlib
*   seaborn
*   scikit-learn
*   plotly
*   umap-learn

