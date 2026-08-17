<img width="601" height="702" alt="IMG_1054" src="https://github.com/user-attachments/assets/e58f7311-6a9b-4db3-b513-2c2ea86da478" />

<img width="607" height="769" alt="IMG_1053" src="https://github.com/user-attachments/assets/56dec4ae-8ffe-4bb3-8ae2-ff231551afd8" />



# 📊 LinkedIn Content Performance & Traction Analytics Pipeline

![Status](https://img.shields.io/badge/STATUS-COMPLETED-brightgreen)
![Python](https://img.shields.io/badge/PYTHON-3.10%2B-blue)
![Pandas](https://img.shields.io/badge/PANDAS-ETL-150458)
![SQL](https://img.shields.io/badge/SQL-DUCKDB%20%2F%20MOTHERDUCK-FFF000)
![Looker Studio](https://img.shields.io/badge/BI-LOOKER%20STUDIO-4285F4)

> **Desafio Real de Negócio & Growth:** Projeto analítico end-to-end desenvolvido para diagnosticar o comportamento de alcance e retenção de publicações no LinkedIn. O fluxo contempla a ingestão de métricas brutas de 50 postagens, tratamento e higienização em Python/Pandas, modelagem analítica via SQL/MotherDuck e criação de um Dashboard Executivo no Looker Studio para comprovar matematicamente o formato de maior tração (Imagem vs. Vídeo).

---

## 🎯 1. O Problema de Negócio (Business Case)

> 📨 **Diagnóstico de Performance de Conteúdo**  
> **De:** Estratégia de Conteúdo & Growth  
> **Para:** Calebe Valério (Data Analyst / Analytics Engineer)  
> **Assunto:** Otimização de Alcance Orgânico: Imagem vs. Vídeo  
>
> Olá, Calebe!  
>
> Nossas publicações no LinkedIn apresentam oscilações drásticas de alcance e engajamento. Precisamos entender com base em dados concretos — e não em achismos — quais fatores geram tração real: postagens em imagem (estáticas) ou vídeos explicativos?  
>
> Sua missão é estruturar um pipeline completo de dados para limpar o histórico bruto de 50 postagens, modelar consultas analíticas em SQL e construir um painel executivo com indicadores de conversão, engajamento e alcance.  
>
> **Requisitos do Pipeline:**
> 1. Ingerir o histórico bruto de métricas de postagens (CSV/Excel).
> 2. Tratar datas, validar consistência de métricas de interação e calcular taxas de engajamento via **Python (Pandas)**.
> 3. Modelar e criar Views SQL no **MotherDuck/DuckDB** para consumo analítico.
> 4. Desenvolver um Dashboard no **Looker Studio** com comparativos diretos de formato, retenção e alcance.

---

## ⚙️ 2. Pipeline de Dados & Arquitetura

1. **Ingestão & ETL (Python / Pandas):** Carga do dataset bruto de 50 publicações, conversão de formatos de data/hora, tratamento de valores ausentes e criação de métricas derivadas (Taxa de Engajamento e Interações Totais).
2. **Armazenamento & Modelagem (SQL / MotherDuck):** Criação da tabela fato (`fato_posts_linkedin`) e estruturação de Views analíticas de benchmark entre formatos e evolução temporal.
3. **Business Intelligence & Storytelling (Looker Studio):** Construção de dashboard interativo com filtros de formato, permitindo comparar diretamente o retorno por publicação.

---

## 🐍 3. Amostra dos Dados Tratados (Pandas DataFrame)

| id_post | data_publicacao | formato_midia | impressoes | curtidas | comentarios | compartilhamentos | taxa_engajamento_pct |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `001` | `2025-03-10` | Imagem / Print | 320 | 12 | 2 | 0 | 4.38% |
| `002` | `2025-03-15` | Vídeo Explicativo | 1.850 | 84 | 26 | 9 | 6.43% |
| `003` | `2025-03-22` | Imagem / Print | 290 | 9 | 1 | 0 | 3.45% |
| `004` | `2025-04-02` | Vídeo Explicativo | 2.140 | 115 | 38 | 14 | 7.80% |

---

## 📬 Diagnóstico Executivo & Principais Insights

**De:** Calebe Valério (Data Analyst)  
**Para:** Estratégia de Conteúdo & Growth  
**Assunto:** Conclusão do Diagnóstico de Tração (Imagem vs. Vídeo)  

Prezados, apresento os resultados da análise consolidada de performance após a higienização dos dados e modelagem SQL:

* **Superioridade do Formato Vídeo (+459% de Alcance):** Publicações em vídeo entregaram uma média de impressões e engajamento significativamente superior às publicações estáticas em imagem, comprovando que o algoritmo do LinkedIn prioriza tempo de tela e retenção.
* **Volume de Interações Qualificadas:** O formato em vídeo gerou uma proporção de comentários 4,8x maior, ampliando o alcance viral de segundo nível (conexões das conexões).
* **Taxa Média de Engajamento:** Enquanto posts estáticos mantiveram uma taxa média de engajamento de ~3,8%, os posts em vídeo superaram a marca de 6,5% a 7,8%.
* **Storytelling & Metodologia:** O uso de vídeos curtos demonstrando a execução prática de projetos foi o principal catalisador para converter visualizações casuais em novos seguidores e conexões estratégicas.

---

## 💾 4. Análise e Views SQL (MotherDuck / DuckDB)

```sql
-- 1. View Comparativa: Imagem vs. Vídeo (Métricas Médias)
CREATE OR REPLACE VIEW my_db.vw_benchmark_formato AS
SELECT 
    formato_midia,
    COUNT(id_post) AS total_posts,
    ROUND(AVG(impressoes), 0) AS media_impressoes,
    ROUND(AVG(curtidas), 1) AS media_curtidas,
    ROUND(AVG(comentarios), 1) AS media_comentarios,
    ROUND(AVG(taxa_engajamento_pct), 2) AS media_taxa_engajamento
FROM my_db.fato_posts_linkedin
GROUP BY formato_midia
ORDER BY media_impressoes DESC;

-- 2. View de Ranking dos Top Posts por Tração
CREATE OR REPLACE VIEW my_db.vw_top_posts_tracao AS
SELECT 
    id_post,
    data_publicacao,
    formato_midia,
    impressoes,
    (curtidas + comentarios + compartilhamentos) AS total_interacoes,
    taxa_engajamento_pct
FROM my_db.fato_posts_linkedin
ORDER BY impressoes DESC
LIMIT 10;

-- 3. View de Evolução Mensal de Alcance Orgânico
CREATE OR REPLACE VIEW my_db.vw_evolucao_mensal_alcance AS
SELECT 
    STRFTIME(CAST(data_publicacao AS TIMESTAMP), '%Y-%m') AS mes_ano,
    formato_midia,
    SUM(impressoes) AS total_impressoes,
    SUM(curtidas + comentarios) AS total_engajamento
FROM my_db.fato_posts_linkedin
GROUP BY mes_ano, formato_midia

````
<img width="1133" height="744"
 alt="IMG_1020" src="https://github.com/user-attachments/assets/3d8b5022-02da-4692-bd26-c6208de3f341" />


---

Aplicando os insights que a própria análise revelou, o engajamento e as impressões saltaram mais de +459% em relação ao formato antigo.
Quando você resolve um problema real com dados, o resultado aparece na prática.
Assista ao video para ver a estrutura dos dados e me conte nos comentários: qual formato você achava que dava mais tração?
