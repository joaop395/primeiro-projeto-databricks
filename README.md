# 🌩️ Weather-Ops: Monitoramento Preditivo de Infraestrutura com Databricks

Este projeto demonstra a implementação de um pipeline de dados para monitoramento de ativos críticos, utilizando variáveis climáticas para prever riscos operacionais. O projeto foi desenvolvido no ecossistema **Databricks** utilizando **PySpark** e **Spark SQL**.

## 🎯 Objetivo
Simular um cenário de **NOC (Network Operations Center)** onde dados meteorológicos reais (via AccuWeather) são processados para gerar alertas automáticos de manutenção e segurança física de equipamentos (ex: superaquecimento, umidade excessiva e riscos de neve).

## 🛠️ Tecnologias Utilizadas
* **Databricks** (Plataforma de Dados)
* **PySpark** (Processamento Distribuído)
* **Spark SQL** (Análise de Dados)
* **Unity Catalog / Delta Sharing** (Governança e Ingestão)
* **GitHub** (Versionamento de Código)

## 🚀 Desafios Técnicos e Resolução (Troubleshooting)
Durante o desenvolvimento, foram superados desafios reais de engenharia de dados:
1.  **Restrições de Segurança (DBFS):** Enfrentei o erro `SQLSTATE: 56038` devido à desativação do acesso root ao DBFS. A solução foi migrar a estratégia de ingestão para **Unity Catalog Volumes** e **Delta Sharing**, garantindo conformidade com as melhores práticas de segurança de 2026.
2.  **Inconsistência de Schema:** Utilizei comandos de inspeção (`printSchema`) para tratar o erro `UNRESOLVED_COLUMN`, adaptando a lógica de negócio aos atributos reais fornecidos pelo provedor de dados (AccuWeather).

## 📊 Arquitetura de Dados
O projeto segue uma estrutura simplificada de camadas:
* **Bronze:** Dados brutos acessados via `samples.accuweather`.
* **Silver:** Filtragem de colunas essenciais (`temperature`, `humidity_relative`) e limpeza de tipos.
* **Gold:** Aplicação de regras de negócio via **SQL CASE Statements** para definição de status operacionais (Crítico, Alerta, Normal).

## 📈 Exemplo de Lógica de Alerta (SQL)
```sql
SELECT 
    datetime_valid_local,
    CASE 
        WHEN temperature > 90 THEN 'CRÍTICO: Superaquecimento'
        WHEN humidity_relative > 85 THEN 'ALERTA: Risco de Curto'
        ELSE 'Operação Normal'
    END as status_operacional
FROM v_monitoramento_ativos
