#DESAFIO 1 - Ciência e Governança de Dados
##Análise Socioeconômica: Segurança Pública e Educação em Minas Gerais (2019-2024)

## Descrição do Projeto

Este projeto realiza uma análise exploratória de dados (AED) com o objetivo de investigar as relações entre indicadores de segurança pública (criminalidade violenta) e educação (básica e superior) nos municípios do estado de Minas Gerais, Brasil, durante o período de 2019 a 2024. A análise busca identificar tendências temporais, disparidades regionais e calcular correlações entre os fenômenos, controlando pelo tamanho da população e considerando o Índice de Desenvolvimento Humano Municipal (IDHM) como fator socioeconômico de referência.

## Fontes e Escolha dos Dados

Os dados utilizados foram selecionados por serem fatores que são geralmente mencionados como impactos socioeconômico de um estado ou país. Esses dados foram retirados de fontes do Governo Federal ou Estadual, sendo todos fontes oficiais.

1.  **Criminalidade:**
    * **Fonte:** Secretaria de Estado de Justiça e Segurança Pública (SEJUSP-MG), via Portal de Dados Abertos de Minas Gerais.
    * **Dados:** Registros agregados mensais de crimes violentos (homicídios, roubos, estupros, etc.) por município.
    * **Escolha:** Fonte oficial primária para estatísticas criminais no estado. Dados que abrangem o período desejado (2019-2024).

2.  **Educação Básica:**
    * **Fonte:** INEP, via Microdados do Censo Escolar do Brasil.
    * **Dados:** Informações anuais por escola, incluindo número de matrículas por etapa (infantil, fundamental, médio), modalidades distintas (EJA, Profissional, Especial), além de dados demográficos (sexo, cor/raça, idade) dos alunos.
    * **Escolha:** Base de dados mais completa que possui as matrículas usadas para análise. Foi filtrado apenas para o estado de Minas Gerais durante a análise.

3.  **Educação Superior:**
    * **Fonte:** INEP, via Microdados do Censo da Educação Superior.
    * **Dados:** Informações anuais por curso/IES, incluindo número de ingressantes, matriculados e concluintes, além de dados demográficos e modalidade (Presencial/EAD).
    * **Escolha:** Base de dados oficial e detalhada sobre o ensino superior no Brasil, posteriormente filtrado para Minas Gerais para a análise.

4.  **População:**
    * **Fonte:** DATASUS (utilizando estimativas do IBGE).
    * **Dados:** Estimativas anuais da população residente por município de Minas Gerais.
    * **Escolha:** Necessário para calcular taxas (por 100 mil habitantes) e normalizar os dados, removendo o viés do tamanho do município nas análises de correlação.

5.  **IDH (Índice de Desenvolvimento Humano):**
    * **Fonte:** Atlas do Desenvolvimento Humano no Brasil (PNUD / Ipea / FJP). 
    * **Dados:** Índice de Desenvolvimento Humano Municipal (IDHM) e seus componentes (Renda, Longevidade, Educação). Foi utilizado o dado de referência mais completo disponível (2021) como indicador estrutural do município.
    * **Escolha:** Indicador socioeconômico padrão para medir o nível de desenvolvimento municipal.

**Metodologia de Aquisição:** Os dados foram baixados diretamente dos portais oficiais mencionados, predominantemente em formato `.csv`.

## Metodologia e Principais Passos do Notebook

O notebook `Desafio_1.ipynb` segue uma estrutura lógica de análise de dados:

1.  **Configuração:** Importação das bibliotecas necessárias (Pandas, Seaborn, Matplotlib, Numpy).
2.  **Carregamento e Limpeza Inicial:** Leitura dos diversos arquivos CSV e Excel, tratamento inicial de problemas comuns (encoding `latin-1`, `skiprows/skipfooter`, `low_memory=False`), e verificação da estrutura e tipos de dados (`.head()`, `.info()`, `.isnull().sum()`).
3.  **Análise Exploratória - Criminalidade:**
    * Análise inicial apenas do ano de 2024, analisando a estrutura dos dados armazenados e vendo a variação de crimes neste ano.
    * Boxplots por região para analizar a distribuição de crimes para analisar.
    * Criação da coluna `regiao` (Belo Horizonte, RMBH, Interior) baseada na classificação da RMBH e código/nome do município, para melhorar a análise.
    * Carregamento e concatenação dos arquivos anuais.
    * Agregação de dados por ano e município.
    * Visualização da tendência temporal geral (gráfico de linhas).
    * Análise da distribuição espacial (boxplots por região e ano).
    * Cálculo e análise de estatísticas descritivas (`.describe()`) por região e ano.
4.  **Análise Exploratória - Educação Básica:**
    * Carregamento e concatenação dos arquivos anuais.
    * Limpeza específica: tratamento de padrões de valores nulos (`NaN`) identificados, fazendo a remoção de linhas na Ed. Básica. 
    * Análise temporal detalhada (gráficos de linha) da evolução das matrículas:
        * Geral.
        * Por Sexo.
        * Por Tipo de Ensino (Infantil, Fundamental, Médio, EJA, etc.).
5.  **Análise Exploratória - Educação Superior:**   
    * Carregamento e concatenação dos arquivos anuais.
    * Limpeza específica: tratamento de padrões de valores nulos (`NaN`) identificados, fazendo a substituição por 0 nesses valores. 
    * Análise temporal detalhada (gráficos de linha) da evolução das matrículas:
        * Geral.
        * Por Sexo.
        * Por Modalidade (EAD vs. Presencial) 
6.  **Análise Exploratória - Educação Superior:**   
    * Adição da coluna `regiao` aos DataFrames de educação, tratando a diferença entre códigos de município de 6 (SEJUSP) e 7 (INEP) dígitos.
    * Análise da distribuição espacial utilizando boxplots por região e ano, interpretando o nível de agregação escola para Ed. Básica, curso para Ed. Superior.
    * Cálculo e análise de estatísticas descritivas por região e ano.  
7.  **Preparação para Correlação:**
    * Carregamento e limpeza dos dados auxiliares (População e IDH). Padronizando o código do município para 6 dígitos.
    * Agregação final de todos os datasets (Crime, Ed. Básica, Ed. Superior) para o nível município/ano.
    * Junção (`merge`) de todos os DataFrames agregados com População e IDH (utilizando `outer` e `left` joins e tratando `NaN`s resultantes com `dropna` e `fillna(0)`).
    * Cálculo das **Taxas** (por 100 mil habitantes) para normalizar os dados pela população.
8.  **Análise de Correlação Final:**
    * Diagnóstico e remoção de colunas com variância zero (que geram `NaN` na correlação).
    * Cálculo da matriz de correlação (`.corr()`) entre as taxas de criminalidade, taxas de educação e o IDH.
    * Visualização da matriz através de um heatmap.
9.  **Conclusão:** Interpretação dos resultados da correlação e resumo dos principais insights da análise exploratória.

## Principais Insights da Análise Exploratória

* **Tendência Geral:** Observou-se uma queda significativa nos registros de crimes violentos entre 2019 e 2023, com leve aumento em 2024. As matrículas na Educação Básica seguiram tendência similar de queda (2020-2023) e leve recuperação (2024), possivelmente impactadas pela pandemia. Já o Ensino Superior mostrou um aumento expressivo de ingressantes, impulsionado pela "explosão" do EAD.
* **Disparidades Regionais (Crime):** Belo Horizonte apresenta a maior mediana e média de crimes, indicando um patamar de violência mais crítico, a Região Metropolitana possui valores intermediários e o Interior possui os menores indicadores.
* **Disparidades Regionais (Ed. Básica):** A análise por escola revelou uma hierarquia na média (BH > Região Metropolitana > Interior). Uma descoberta crucial foi a fechamento de um grande número de escolas (principalmente no Interior) entre 2021 e 2022 (provavelmente causado pela pandemia), o que levou a um aumento da média/mediana de alunos por escola, mesmo com a queda no total de matrículas.
* **Dinâmica do Ensino Superior:** O EAD foi o motor do crescimento, especialmente entre o público feminino. Houve um aumento massivo na quantidade de cursos ofertados (principalmente no Interior/Região Metropolitana), mas com uma média/mediana baixa de alunos por curso, motivado pela baixa demanda em comparação ao aumento considerável de cursos por conta da "explosão do EAD".
* **Correlação baseada em Taxas:**
    * Após calcular as taxas, a correlação real entre taxa de crime e taxas de educação mostrou-se **positiva e fraca** (≈+0.20), sugerindo um "Efeito Urbano" (centros maiores concentram ambos).
    * A correlação entre **Taxa de Crime e IDH** foi **positiva e fraca (+0.20)**, reforçando o "Efeito Urbano".
    * A correlação entre **IDH e Educação Básica/Média** foi **negativa (-0.18 / -0.33)**, possivelmente devido a fatores demográficos (municípios com maior IDH têm população mais velha).
    * A correlação entre **IDH e Educação Superior** foi **positiva e moderada** (≈+0.40 a +0.46), confirmando a ligação entre desenvolvimento e ensino superior.
    * A **baixa correlação** entre taxas de Ed. Básica e Ed. Superior sugere desafios na transição entre níveis, reforçando que muitos estudantes que acabam o ensino básico não buscam ensino superior.

## Como Executar

1.  Certifique-se de ter Python e Jupyter Notebook instalados.
2.  Instale as bibliotecas necessárias: `pip install pandas matplotlib seaborn numpy openpyxl`
3.  Coloque todos os arquivos de dados (`.csv`) na mesma pasta do notebook `Desafio_1.ipynb`.
4.  Execute as células do notebook sequencialmente.

---

Projeto desenvolvido por Vincent Biazotti Collares, para o Desafio 1 - Zetta lab - Segunda edição.
