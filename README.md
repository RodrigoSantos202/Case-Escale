# Case-Escale

Case-Escale Rodrigo dos Santos Ramos
# Resumo

Esse diretorio foi criado a fim de participar do processo seletivo na Escale para vaga de Data Analyst. 
# Estrututa

1. Conexao com banco de dados 

2. Extrair Informações da fonte de dados

3. transformação dos dados 

4. ilustracao dos graficos

# Conexao com fonte de dados 

Para conectar a fonte de dados criei a seguinte função.
```
DB_HOST = '****'
DB_NAME = '****'
DB_PORT = '****'
DB_USER = '****'
DB_PASS = '****'
SPARK_APP ='****'

def tableReader(tableName):
  global DB_HOST
  global DB_NAME
  global DB_PORT
  global DB_USER
  global DB_PASS
  tempDf = spark.read \
            .format("jdbc") \
            .option("url", f"jdbc:postgresql://{DB_HOST}:{DB_PORT}/{DB_NAME}") \
            .option("dbtable", tableName) \
            .option("user", DB_USER) \
            .option("password", DB_PASS) \
            .option("driver", "org.postgresql.Driver") \
            .load()
  return tempDf
  ```
 # Processo de extração dos dados  
 ```
attendacesDf = tableReader("attendances")
attendancesCallsDf = tableReader("attendances_calls")
lineMKTFinalDf = tableReader("lines_mkt_final")
callHistoryQueueDf = tableReader("call_history_queue")
telephonyTypeDf = tableReader("telephony_types")
 ```
 # Transformação dos dados - ETL
 
 Para o processo de ETL, realizei tratamentos dos dados e criação das tabelas fato e dimensão.
 
# Dimensão

•	dim_attendance:  Agrupamento entre as tabelas attendances_calls e Attendances.

•	dim_line_mkt: Sua origem foi a tabela Lines_mkt_final, porem nessa nova estrutura, ela passou a conter apenas as colunas que achei importante para realizar os indicadores.

•	dim_calendar: Tabela de calendário.

# Fato

•	Tabela fact_call:  Agrupamento entre as tabelas Telephony_types e Calls_history_queue, onde trouxe para dentro do fato, as colunas relevantes. 

Após o processamento, a modelagem dimensional ficou na seguinte estrutura

![dm](https://github.com/RodrigoSantos202/Case-Escale/blob/98ee1a36e61d98a0de0e695354ae4a890b03762f/dm.PNG)

# Case respostas
 
  1.	Qual foi o número de ligações por dia?
  	
      A figura abaixo ilustra o resultado obtido
      
 ![dm](https://github.com/RodrigoSantos202/Case-Escale/blob/3e6bfcb6e372bc4590ac436b8047cb7b75f262f1/q1.PNG)
 
 ```
 SELECT  DC.DATE,
        COUNT(FC.CALL_ID) CALL_QUANTITY
FROM    DIM_CALENDAR DC
LEFT JOIN
        FACT_CALL FC
ON      FC.CREATED_AT = DC.DATE
WHERE   DC.YEAR = 2020
AND     DC.MONTH = 6
GROUP BY 
        DC.DATE
ORDER BY 
        DC.DATE
 ```
