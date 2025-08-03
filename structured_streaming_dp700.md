
# 🧠 Estudo: Structured Streaming com Spark (Foco na DP-700)

Este guia aborda o funcionamento do Structured Streaming no Apache Spark, com foco em ingestão de dados em tempo real, especialmente utilizando **arquivos**, **Kafka** e **Azure Event Hubs**.

---

## 📌 1. O que é Structured Streaming?

Structured Streaming é uma **API de stream unificada** do Apache Spark que permite processar dados **em tempo real** como se fosse um DataFrame contínuo.

- Permite processar arquivos, eventos, mensagens, filas, etc.
- Escreve para sinks como Delta Lake, tabelas, bancos SQL, etc.
- Controla falhas e reprocessamentos com **checkpointing**

---

## 📁 2. Leitura contínua de arquivos (Data Lake)

### ✅ Leitura incremental de arquivos `.json`, `.csv`, `.parquet`:

```python
from pyspark.sql.types import *

schema = StructType([
    StructField("device", StringType(), False),
    StructField("status", StringType(), False)
])

df = spark.readStream     .schema(schema)     .option("maxFilesPerTrigger", 1)     .json("Files/data/")
```

> ⚠️ Arquivos com extensão `.txt` funcionam desde que o conteúdo seja **JSON por linha**.

---

## 💾 3. Escrita contínua para Delta Lake

### ✅ Escrita com controle de estado:

```python
df.writeStream     .format("delta")     .outputMode("append")     .option("checkpointLocation", "Files/delta/checkpoint")     .start("Tables/iotdevicedata")
```

- `checkpointLocation`: controla quais arquivos já foram processados.
- Reiniciar o stream com o mesmo checkpoint evita duplicação.
- Output mode `append` escreve apenas novos dados.

---

## 🔁 4. Leitura de fontes externas (ex: APIs, bancos)

**Structured Streaming não lê diretamente de APIs ou bancos relacionais.**

### Solução:

- Criar um processo (ex: com `foreachBatch`) que:
  - Lê da fonte externa (ex: API)
  - Salva no Lake (JSON ou Delta)
- Structured Streaming lê os dados recém-salvos

---

## 🛰️ 5. Leitura com Kafka ou Event Hubs

### 🔷 Kafka

```python
df = spark.readStream     .format("kafka")     .option("kafka.bootstrap.servers", "localhost:9092")     .option("subscribe", "iot-topic")     .load()
```

### 🔷 Azure Event Hubs (via Kafka interface)

```python
ehConf = {
  "kafka.bootstrap.servers": "namespace.servicebus.windows.net:9093",
  "subscribe": "iot-topic",
  "kafka.security.protocol": "SASL_SSL",
  "kafka.sasl.mechanism": "PLAIN",
  "kafka.sasl.jaas.config": 'org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="<connection-string>";'
}

df = spark.readStream     .format("kafka")     .options(**ehConf)     .load()
```

### 🧪 Conversão de dados:

```python
from pyspark.sql.functions import from_json

parsed = df.selectExpr("CAST(value AS STRING)")     .select(from_json("value", "device STRING, status STRING").alias("data"))     .select("data.*")
```

---

## 📤 6. Escrita para Delta Table (sink)

```python
parsed.writeStream     .format("delta")     .outputMode("append")     .option("checkpointLocation", "/Files/checkpoint/iot")     .start("/Tables/delta/iot_data")
```

---

## 🧠 7. Controle de Offset e Checkpoint

- Spark armazena o progresso com **offsets** (Kafka/EventHub) ou **arquivos lidos** (arquivos).
- Checkpoints são obrigatórios para evitar duplicação em falhas/reinícios.

---

## 📊 8. Output Modes

| Modo        | Descrição |
|-------------|-----------|
| `append`    | Adiciona apenas novos dados |
| `complete`  | Reescreve o resultado completo (usado com agregações) |
| `update`    | Atualiza apenas linhas modificadas |

---

## 📚 9. Diferenças: Kafka vs Event Hubs

| Item | Kafka | Event Hubs |
|------|-------|------------|
| Tipo | Open source | Azure PaaS |
| Gerência | Você gerencia a infra | Azure gerencia |
| Segurança | SSL, ACLs | SAS, RBAC, VNET |
| Conectividade | Apache Kafka API | Compatível com Kafka |

---

## ⚠️ 10. Cuidados e Boas Práticas

- Sempre usar `checkpointLocation`
- Use `withWatermark()` para dados com atraso
- Prefira esquema fixo (`from_json`) para desempenho
- Controle tamanho dos lotes com `.trigger(...)`
- Teste com `rate` ou `socket` em ambientes de dev

---

## 🧪 Exemplo completo: Kafka → Spark → Delta

```python
from pyspark.sql.functions import from_json

schema = "device STRING, status STRING"

df = spark.readStream     .format("kafka")     .option("kafka.bootstrap.servers", "localhost:9092")     .option("subscribe", "iot")     .load()

parsed = df.selectExpr("CAST(value AS STRING) as json")     .select(from_json("json", schema).alias("data"))     .select("data.*")

parsed.writeStream     .format("delta")     .option("checkpointLocation", "/tmp/checkpoint")     .start("/Tables/delta/iot_stream")
```

---

## 🧠 Recomendações para a DP-700

- Entenda as diferenças entre streaming de arquivos, Kafka e Event Hubs
- Saiba configurar checkpoint, trigger e output modes
- Estude `foreachBatch`, `trigger`, `withWatermark`, `window`
- Pratique agregações e joins com dados em tempo real
- Teste em ambiente com Delta Lake (Fabric, Databricks, etc)

---

## 📎 Recursos adicionais

- [Structured Streaming Guide (Databricks)](https://docs.databricks.com/structured-streaming/index.html)
- [Apache Spark Streaming + Kafka](https://spark.apache.org/docs/latest/structured-streaming-kafka-integration.html)
- [Event Hubs para Apache Kafka](https://learn.microsoft.com/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview)
