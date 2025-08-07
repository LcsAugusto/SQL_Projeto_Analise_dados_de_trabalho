📄 Conteúdo do README.md
# Introdução
Este projeto investiga o mercado de trabalho para Analistas de Dados, com foco em três frentes principais:

- Cargos com os maiores salários

- Habilidades mais demandadas

- Convergência entre alta demanda e alta remuneração

A análise é baseada em dados reais de vagas, com consultas SQL estruturadas para extrair insights relevantes, especialmente no contexto de vagas remotas no Brasil.

As consultas completas estão disponíveis neste repositório: [projeto_sql folder](/projeto_sql/)

# Contexto

Este projeto foi desenvolvido com o objetivo de entender melhor o mercado de trabalho para **Analistas de Dados**, identificando as habilidades mais valorizadas em termos de **demanda** e **remuneração**. A proposta é oferecer uma base sólida que ajude profissionais a se posicionarem melhor e encontrarem oportunidades mais alinhadas com o cenário atual.

Os dados utilizados foram extraídos do [curso de SQL de Luke Barousse](https://lukebarousse.com/sql), que fornece uma base rica com informações sobre cargos, salários, localizações e competências técnicas.

### Perguntas que este projeto busca responder

1. Quais são os cargos de analista de dados mais bem remunerados?
2. Quais habilidades são exigidas para esses cargos com maiores salários?
3. Quais habilidades estão em maior demanda para analistas de dados?
4. Quais habilidades estão associadas a salários mais altos?
5. Quais são as habilidades mais vantajosas para aprender atualmente?


# Ferramentas Utilizadas

A execução deste projeto envolveu o uso estratégico de ferramentas amplamente adotadas no mercado de dados. O objetivo foi simular um ambiente profissional de análise de dados, desde a coleta e manipulação até a geração de insights relevantes para decisões estratégicas.

- **SQL (Structured Query Language)**  
  Utilizada como principal linguagem de consulta para explorar grandes volumes de dados relacionados a vagas, habilidades e salários. Foi essencial para aplicar filtros, junções, agregações e gerar análises interpretáveis.

- **PostgreSQL**  
  Banco de dados relacional robusto e amplamente utilizado no mercado. Permitiu estruturar as tabelas, aplicar comandos avançados de SQL e testar queries complexas com eficiência e precisão.

- **Visual Studio Code (VS Code)**  
  Ambiente de desenvolvimento escolhido para escrever e executar as consultas. Com suporte a extensões de SQL e Git, proporcionou uma experiência fluida e integrada para análise e versionamento.

- **Git & GitHub**  
  Ferramentas indispensáveis para controle de versão, organização e colaboração em projetos técnicos. Utilizei o GitHub para documentar o projeto, compartilhar os scripts e demonstrar domínio de boas práticas de versionamento e publicação de código.



#  Análise

Cada consulta SQL desenvolvida neste projeto teve como objetivo responder perguntas estratégicas sobre o mercado de trabalho para analistas de dados. A seguir, apresento como cada aspecto foi explorado:

## 1. Cargos de Analista de Dados com Salários Mais Altos

Para identificar as funções mais bem remuneradas, filtrei as vagas com base na média salarial anual (`salary_year_avg`), considerando apenas posições com título "Data Analyst" e com dados salariais disponíveis. Também restringi a análise a oportunidades remotas e localizadas no Brasil, de modo a refletir o cenário atual e acessível.

Essa consulta destaca as melhores oportunidades em termos de remuneração, permitindo aos profissionais direcionarem seus esforços para cargos com maior retorno financeiro.

```sql
SELECT  
    job_id AS id_vaga,
    job_title AS titulo_da_vaga,
    job_location AS localizacao,
    job_schedule_type AS tipo_de_contrato,
    salary_year_avg AS media_salarial_anual,
    job_posted_date AS data_da_postagem,
    name AS empresa
FROM
    job_postings_fact
LEFT JOIN company_dim ON company_dim.company_id = job_postings_fact.company_id
WHERE
    job_title_short = 'Data Analyst'AND
    salary_year_avg IS NOT NULL AND
    (job_location LIKE '%Brazil%'OR
    job_country LIKE '%Brazil'
)
ORDER BY
    salary_year_avg DESC
LIMIT 10
```
---

### A análise dos principais empregos de analista de dados no Brasil em 2023:

- **São Paulo é o principal polo de oportunidades:**  
  Mais da metade das vagas com maiores salários estão localizadas no estado de São Paulo, especialmente na capital e em cidades como Cotia e Campinas. Essa concentração reforça a região como o principal hub de oportunidades para profissionais de dados no Brasil, beneficiando-se de uma maior oferta de empresas inovadoras e multinacionais com cultura data-driven.

- **Experiência e perfil técnico elevam o salário:**  
  Os cargos com maiores salários costumam requerer níveis intermediários a avançados, refletidos em títulos como "Mid-Level Data Analyst", "Product Data Analyst" e "AML Specialist". Além disso, conhecimentos específicos em ferramentas e linguagens como Python, análise de produto, e especializações em áreas regulatórias (AML – Anti-Money Laundering) impactam positivamente a remuneração.

- **Remuneração está alinhada à complexidade e especialização das funções:**  
  O mercado valoriza não apenas o tempo de experiência, mas também a capacidade analítica avançada, domínio de ferramentas de visualização, manipulação de grandes volumes de dados e aplicação de técnicas estatísticas e de machine learning. Profissionais que apresentam essas habilidades tendem a alcançar salários mais competitivos.

- **Contratos full-time dominam as melhores oportunidades:**  
  As vagas com salários mais elevados são predominantemente para posições em regime integral, o que sugere que o mercado valoriza o comprometimento e a dedicação plena dos analistas para entregas de maior impacto estratégico.


![Ranking de Salários Anuais para Vagas de Analista de Dados no Brasil – 2023](imagens\analise_1.png)

*Gráfico de barras visualizando os salários mais altos para o cargo de analistas de dados no Brasil; o ChatGPT gerou este gráfico a partir dos resultados da minha consulta SQL*

## 2. Habilidades Mais Requisitadas nas Vagas de Analista de Dados com Maiores Salários
A análise abaixo revela as competências técnicas mais valorizadas entre as 10 vagas de Analista de Dados com os maiores salários no Brasil.

Objetivo: fornecer um mapa claro de quais habilidades técnicas os profissionais devem dominar para se posicionarem entre os cargos mais bem pagos da área.

```sql

WITH top_vagas AS (

    SELECT  
        job_id AS id_vaga,
        job_title AS titulo_da_vaga,
        salary_year_avg AS media_salarial_anual
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON company_dim.company_id = job_postings_fact.company_id
    WHERE
        job_title_short = 'Data Analyst'AND
        salary_year_avg IS NOT NULL AND
        (job_location LIKE '%Brazil%' OR
        job_country LIKE '%Brazil%'
)
    ORDER BY
        salary_year_avg DESC
)

SELECT
    top_vagas.*,
    skills AS habilidades
FROM top_vagas
    INNER JOIN skills_job_dim ON top_vagas.id_vaga = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    media_salarial_anual DESC
```

---

### Principais Resultados:
- **SQL lidera a lista** — confirmando seu papel essencial na manipulação de dados estruturados.

- **Python é quase tão frequente quanto SQL** — seu uso vai de automações até análise preditiva.

- **Power BI e Tableau** — ferramentas de visualização continuam fundamentais para transformar dados em decisões.

- **Conhecimentos em nuvem (Azure) e big data (Databricks, Snowflake)** — indicam que empresas estão migrando para arquiteturas modernas e escaláveis.

- **Ferramentas como R, Excel, Looker e SAS** — reforçam a demanda por um perfil técnico versátil, que saiba trabalhar com diferentes plataformas e linguagens.

![Habilidades Mais Requisitadas nas Vagas de Data Analyst com Maiores Salários](imagens\analise_2.png)

*Gráfico de barras mostrando as habilidades mais requisitadas nas vagas de Data Analyst com maiores salários; o ChatGPT gerou este gráfico a partir dos resultados da minha consulta SQL*

## 3. Habilidades com Maior Demanda para Analistas de Dados

Em um mercado de trabalho cada vez mais competitivo, entender quais habilidades técnicas são mais requisitadas pode ser o diferencial para conquistar uma vaga como Analista de Dados. Esta análise foca exclusivamente nas vagas remotas para analistas de dados no Brasil, buscando identificar as competências técnicas mais mencionadas nas descrições de emprego.

```sql
SELECT
    skills AS habilidades,
    COUNT(skills_job_dim.job_id) AS demanda
FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home = TRUE AND 
    (job_location LIKE '%Brazil%' OR
    job_country LIKE '%Brazil%'
)
GROUP BY
    skills
ORDER BY
    demanda DESC
LIMIT 5
```
---

### Top 5 Habilidades Mais Requisitadas

| Competências | Demanda |
|--------------|---------|
| SQL          | 93      |
| Python       | 67      |
| Tableau      | 36      |
| AWS          | 27      |
| Power BI     | 26      |

### Principais Resultados:

- **1. SQL lidera a demanda**
com 93 menções, SQL é a habilidade mais comum entre as vagas remotas de analista de dados. Isso reforça seu papel essencial na extração e manipulação de dados em bancos relacionais.

- **2. Python é a linguagem de programação mais valorizada**
com 67 ocorrências, Python aparece como a principal linguagem além do SQL. Seu uso é amplamente difundido em análise de dados, automação, estatística e machine learning.

- **3. Visualização de dados é uma competência crítica**
Tableau (36) e Power BI (26) ocupam lugar de destaque entre as ferramentas exigidas. Isso mostra que o mercado valoriza analistas que sabem comunicar dados de forma visual e estratégica.

- **4. Cloud computing é um diferencial crescente**
AWS, com 27 menções, reforça a importância de se familiarizar com ambientes de nuvem para análises escaláveis, especialmente em equipes distribuídas e produtos digitais.

## 4. Tendência Salarial das Habilidades Técnicas: De 2023 até o Futuro

O objetivo foi analisar as habilidades técnicas mais bem remuneradas nas vagas de Analista de Dados. Comparar com as tendências que se consolidaram até 2025, e projetar o que deve ganhar destaque nos próximos anos.

Essa análise serve como um radar estratégico para profissionais de dados, revelando onde estão (e estarão) as oportunidades mais lucrativas.
```sql
 SELECT
    skills AS habilidades,
    ROUND( AVG(salary_year_avg), 0) AS media_salarial
FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    salary_year_avg IS NOT NULL
   -- job_work_from_home = TRUE AND 
GROUP BY
    skills
ORDER BY
    media_salarial DESC
LIMIT 25
```
---

### O que o banco de dados de 2023 já mostrava:
Com base nas habilidades com maior média salarial, já era possível notar as seguintes tendências emergentes:

| Habilidade     | Média Salarial (USD) | Interpretação 2023                              |
|----------------|----------------------|-------------------------------------------------|
| solidity       | 179,000              | Alta remuneração em blockchain                 |
| hugging face   | 123,950              | Ascensão de NLP e IA aplicada                  |
| airflow        | 116,387              | Orquestração de pipelines                      |
| terraform      | 146,734              | Integração entre dados e infraestrutura        |
| keras          | 127,013              | Forte presença de deep learning                |
| pytorch        | 125,226              | Confirma o interesse em IA                     |
| datarobot      | 155,486              | Adoção de AutoML                               |
| couchbase      | 160,515              | Interesse em bancos NoSQL distribuídos         |
| dplyr          | 147,633              | Forte uso de R, mas com tendência de migração  |
| vmware         | 147,500              | Valorização de ambientes virtualizados         |ção

---

### O que se confirmou em 2025:
Com base em observações atuais de 2025, essas tendências se concretizaram:

| Tendência 2023               | Situação Atual 2025                                                    |
|------------------------------|------------------------------------------------------------------------|
| Hugging Face / NLP           | Amplamente adotado para automação, resumo e análise de texto          |
| Airflow / Terraform          | Padrão para pipelines e infraestrutura em times de dados               |
| LangChain / OpenAI APIs      | Comuns em automação de relatórios e atendimento via IA                |
| dbt + Snowflake / BigQuery   | Stack padrão em empresas modernas de médio e grande porte             |
| Ferramentas com R (dplyr)    | Em queda, com migração para pandas / Python                           |
| AutoML (ex: DataRobot)       | Em uso crescente, especialmente em plataformas de citizen data science |

### Projeções para os próximos anos (2026+):

![Projeções para os próximos anos (2026+)](imagens\analise_4.png)

*Gráfico de barras visualizando as projeções de tecnologias para o cargo de analistas de dados no Brasil para os próximos anos; o ChatGPT gerou este gráfico a partir dos resultados da minha consulta SQL*

---

 ### Conclusão Estratégica

- Em 2023, o mercado já sinalizava para automação inteligente, IA e cloud pipelines como diferencial técnico.

- Até 2025, essas tecnologias se consolidaram, não apenas como tendências, mas como requisitos profissionais.

- Para os próximos anos, o diferencial não será apenas técnico, mas comunicativo, estratégico e ético.
## 5. Como Identificar as Melhores Habilidades para Aprender em Análise de Dados

Diante de tantas ferramentas, linguagens e plataformas no universo de Análise de Dados, fica difícil saber por onde começar — ou onde focar. A melhor forma de decidir é com base em dados: entender quais habilidades estão em alta demanda e oferecem melhores salários.

### Esta análise cruza duas métricas essenciais:

- **Demanda:** o número de vagas que exigem determinada habilidade.

- **Salário médio:** quanto, em média, as vagas com essa habilidade estão oferecendo.

O foco está em vagas remotas com salário especificado, garantindo uma amostra mais confiável e atual do mercado. O objetivo é mostrar quais tecnologias proporcionam segurança no emprego e bom retorno financeiro, guiando o desenvolvimento de carreira.


```sql
SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY
    skills_dim.skill_id, skills_dim.skills
HAVING
    COUNT(skills_job_dim.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```

---

### Principais Resultados
1. **Python é inegociável:**
A linguagem Python domina o cenário de análise de dados, aparecendo com a maior demanda entre todas as habilidades (236 menções) e um salário médio superior a $101 mil anuais. Seu uso vai desde análise exploratória até automação, machine learning e integração com APIs.

Se você está começando na área de dados, Python é a primeira ferramenta que deve dominar.

2. **Ferramentas de Business Intelligence continuam fortes:**
Duas ferramentas se destacam:
Tableau, com 230 vagas, e Looker, com 49 vagas, mas oferecendo salário médio mais alto. Elas mostram que saber transformar dados em visualizações impactantes ainda é uma das competências mais valiosas no mercado. Além disso, muitas dessas ferramentas exigem pouco código — o que as torna acessíveis a perfis não técnicos também.

3. **Cloud e data warehouses são o novo padrão técnico
ferramentas como:** Snowflake, Azure, BigQuery, AWS, Redshift. Estão entre as mais bem pagas, mesmo com menor volume de menções. Isso sinaliza que empresas que adotam infraestrutura moderna estão dispostas a pagar mais por profissionais que dominam essas tecnologias.

A mensagem é clara: dominar cloud + data warehouse garante acesso às vagas mais bem remuneradas.

4. **Engenharia de dados impulsiona salários**:
Embora a análise foque em Data Analysts, ferramentas como Hadoop, Spark, SSIS, Java, SQL Server e SSRS aparecem com destaque.
Isso indica uma convergência entre analista e engenheiro de dados, especialmente em times menores, onde o profissional precisa atuar em várias frentes — da extração até a visualização.

Ter noções de engenharia de dados pode desbloquear oportunidades com maior remuneração e impacto técnico.

5. **Habilidades menos comuns com alto retorno**
Algumas habilidades aparecem com menor frequência, mas salários acima da média, como:

- **Go (Golang):** $115 mil de média com 27 ocorrências

- **Confluence:** $114 mil, usado na documentação e gestão de times ágeis

- **Hadoop:** $113 mil, ainda relevante em ambientes com Big Data

Essas tecnologias oferecem nichos com alta remuneração e menos concorrência. Ideais para profissionais com foco técnico ou que queiram se diferenciar rapidamente.

---

### Conclusão
Se você quer fazer escolhas inteligentes de aprendizado, os dados mostram um caminho claro:

- Comece pelo essencial: Python, SQL, Tableau/Looker

- Aprofunde em infraestrutura moderna: Snowflake, BigQuery, Azure, AWS

- Diferencie-se por engenharia ou cloud: Aprenda ferramentas como Airflow, Spark, Redshift

-   Observe tendências emergentes: Go, Confluence, ferramentas de documentação e DevOps

Além disso, desenvolva comunicação com dados, storytelling e empatia com o negócio — as chamadas soft skills que já aparecem como tendência para 2026+.


# O que eu aprendi


Ao longo desta jornada de aprendizado em Análise de Dados, percebi que evoluir nessa área vai muito além de memorizar comandos SQL. É sobre **aprender a pensar como um analista** — alguém que enxerga os dados não apenas como tabelas, mas como **peças de um quebra-cabeça real**, que precisa ser resolvido com raciocínio estruturado, curiosidade constante e decisões bem informadas.

---

###  Construção de Pensamento Analítico

Um dos maiores aprendizados foi desenvolver uma **mentalidade analítica orientada a perguntas**. Em vez de apenas executar consultas, passei a me questionar:

- O que essa informação realmente significa?  
- Como ela se conecta com o problema de negócio?  
- Quais variáveis são realmente relevantes nesta análise?  
- O que estou assumindo sem perceber?

Essa mudança de postura me ensinou a **desconstruir problemas complexos**, explorar hipóteses e testar diferentes abordagens até chegar a respostas mais sólidas — mesmo que, às vezes, a melhor resposta seja: *"precisamos de mais dados"*.

---

###  Aprendizado Técnico com Propósito

Durante este projeto, mergulhei no uso do SQL com foco em **aplicações reais e contextualizadas**:

- Aprendi a **unir dados de diferentes fontes** usando JOINs com intenção clara, conectando informações entre vagas, empresas e habilidades.
- Dominei o uso de **filtragens, agregações e ordenações** para extrair métricas acionáveis como média salarial e volume de demanda por habilidade.
- Apliquei **CTEs (Common Table Expressions)** para estruturar raciocínios complexos de forma mais clara e reutilizável.
- Trabalhei com **dados incompletos ou ruidosos**, o que me levou a refletir sobre **qualidade da informação**, **tratamento de exceções** e **impacto de filtros mal definidos**.

Esse processo reforçou que saber escrever uma query é apenas uma parte do trabalho. O verdadeiro diferencial está em **formular perguntas certas** e compreender **os limites da análise**.

---

###  Meu Olhar como Iniciante na Área

Por estar iniciando na área de dados, esse projeto me trouxe mais do que conhecimento técnico: ele me deu **clareza sobre o meu caminho** e sobre o que realmente significa atuar como analista.

Descobri que:

- Saber SQL é essencial, mas é apenas o primeiro passo.  
- Um bom analista precisa entender **o contexto do negócio e o impacto das suas análises**.  
- Ter dúvidas faz parte — e o importante é saber transformá-las em **boas perguntas analíticas**.

Também percebi o peso das chamadas **soft skills**, como:

- Saber comunicar insights de forma simples e clara.  
- Estruturar o raciocínio lógico e defender decisões com base em dados.  
- Documentar e apresentar análises de forma compreensível para diferentes públicos.

---

###  Próximos Passos

Quero continuar evoluindo em áreas que complementam o que construí até aqui:

- Aprofundar o uso de **ferramentas de BI e visualização** como Power BI, Looker e Tableau.  
- Explorar **engenharia de dados moderna**, com ferramentas como dbt, Airflow e pipelines escaláveis.  
- Praticar **storytelling com dados**, pensando em apresentações mais executivas e tomadas de decisão.  
- Acompanhar **tendências como IA generativa** e automação de análises com LLMs.  
- Conectar análises técnicas com **visão estratégica de negócio**.

---


Esse projeto me ajudou a enxergar o papel do analista de dados de forma muito mais completa. Me proporcionou **base técnica**, **maturidade lógica** e, acima de tudo, **clareza sobre onde estou e onde quero chegar**.

Ainda tenho muito a aprender — mas agora, sei exatamente **como e por que continuar aprendendo**.



# Conclusões

##  Insights Finais

A análise deste projeto permitiu identificar padrões e tendências essenciais para quem atua (ou deseja atuar) na área de Análise de Dados.

---

###  Empregos de Analista de Dados com Maiores Salários

- Os cargos mais bem pagos que permitem **trabalho remoto** oferecem uma ampla faixa salarial — o mais alto chega a **$650.000**!
- Esses empregos geralmente exigem **nível intermediário ou avançado**, além de domínio em áreas específicas como análise regulatória, produto e automação.
- O regime **full-time predomina nas melhores oportunidades**, reforçando a importância da dedicação exclusiva.

---

###  Habilidades para os Empregos Mais Bem Pagos

- As funções mais bem remuneradas **exigem proficiência em SQL**, Python e ferramentas de visualização como Tableau e Power BI.
- **Habilidades em cloud (Azure, Databricks, Snowflake)** indicam a valorização de arquiteturas modernas de dados.
- Conhecimentos de nicho, como **AML (Anti-Money Laundering)** e plataformas específicas, aparecem ligados a altos salários.

---

###  Habilidades com Maior Demandada

- O **SQL** lidera como a habilidade mais requisitada, com dezenas de menções nas descrições de vaga — um requisito essencial para a maioria das funções.
- **Python**, **Tableau**, **AWS** e **Power BI** também aparecem com destaque, formando a base técnica esperada do analista moderno.
- Visualização e cloud computing são **competências críticas** para o futuro do cargo.

---

###  Habilidades Associadas a Salários Mais Altos

- Habilidades como **SVN** e **Solidity** estão associadas às maiores médias salariais (acima de $150.000), mostrando o **valor de conhecimentos especializados e nichados**.
- Ferramentas como **Couchbase**, **Golang**, **Terraform** e **DataRobot** também aparecem entre as mais lucrativas, mesmo com baixa demanda.
- Isso revela nichos técnicos com **alta recompensa e pouca concorrência**.

---

###  Habilidades com Melhor Custo-Benefício

- **SQL** lidera tanto em **demanda** quanto em **média salarial**, sendo a habilidade mais vantajosa para maximizar retorno no mercado.
- Tecnologias como **Python**, **Looker**, **Snowflake**, **Azure** e **BigQuery** oferecem um equilíbrio excelente entre **volume de vagas** e **boa remuneração**.
- Profissionais que unem essas competências estão mais preparados para oportunidades de impacto e crescimento.

---

###  Tendências Confirmadas até 2025

- O uso de **LLMs, Hugging Face, OpenAI APIs e LangChain** se consolidou, saindo da experimentação e entrando no fluxo de trabalho de equipes de dados.
- **Airflow, dbt, Terraform** e stacks modernos de cloud são agora padrão em médias e grandes empresas.
- A exigência por conhecimento em **infraestrutura e engenharia de dados** cresceu mesmo em vagas de analista, mostrando uma fusão de papéis.

---

###  Projeções para os Próximos Anos

- A expectativa é que o diferencial deixe de ser somente técnico. Profissionais precisarão dominar também:
  - **Storytelling com dados**
  - **Ética e Explainable AI**
  - **Integração com IA generativa**
- As habilidades mais promissoras incluem:
  - **LLM Automation e AutoML**
  - **Cloud-first Data Pipelines**
  - **Comunicação analítica com foco no negócio**

---


##  Considerações Finais

Este projeto foi muito mais do que um exercício técnico com SQL — foi uma jornada de descoberta, crescimento e autoconhecimento como iniciante na área de dados. A cada consulta que escrevi, a cada insight que organizei, fui entendendo que ser analista de dados é sobre muito mais do que saber "puxar dados". É sobre **fazer perguntas melhores, entender o contexto, e gerar valor real com as respostas**.

Ao explorar o mercado de trabalho para analistas de dados, pude ver com clareza como certas habilidades se destacam — tanto em termos de **demanda** quanto de **retorno financeiro**. Isso me ajudou a organizar minha própria rota de aprendizado com mais propósito.

Percebi que, mesmo em início de carreira, já é possível **tomar decisões estratégicas**: escolher onde investir tempo, quais ferramentas dominar e que tipo de profissional quero me tornar.

Além das habilidades técnicas que refinei com SQL, o que mais ganhei foi **visão de futuro**. Aprendi a olhar para os dados com criticidade, a desconfiar de respostas rápidas, e a entender que a melhor análise é aquela que **combina lógica com impacto humano**.

Concluo esse projeto com a certeza de que estou no caminho certo — e com ainda mais vontade de continuar aprendendo. O mercado de dados está em constante evolução, e eu também quero evoluir com ele.

**Aprendizado contínuo, adaptação e curiosidade serão minhas ferramentas daqui para frente.**

## *Observações*

- O arquivo `job_postings_fact.csv`, localizado na pasta `job_postings_fact_csv/`, possui mais de 100 MB e excede o limite padrão de arquivos suportados pelo GitHub.
- Para contornar essa limitação, foi utilizado o [Git LFS (Large File Storage)](https://git-lfs.github.com/) para armazenar o arquivo corretamente no repositório.
- Para clonar o repositório com o arquivo incluído, é necessário ter o Git LFS instalado. Utilize os comandos abaixo:

```bash
git lfs install
git clone https://github.com/LcsAugusto/SQL_Projeto_Analise_dados_de_trabalho.git
```
 Caso o Git LFS não esteja instalado, o arquivo CSV será clonado como um ponteiro de texto (.gitattributes), e não como o arquivo real.