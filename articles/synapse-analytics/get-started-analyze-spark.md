---
title: 'Quickstart: Aan de slag met het analyseren met Spark'
description: In deze zelfstudie leert u hoe u gegevens kunt analyseren met Apache Spark
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: ee4dc945f63180fd06f13287b22949d0ac1e3873
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862017"
---
# <a name="analyze-with-apache-spark"></a>Analyseren met behulp van Apache Spark

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>NYC Taxi-gegevens analyseren in blob-opslag met Spark

In deze zelfstudie leert u de basisstappen voor het laden en analyseren van gegevens met Apache Spark voor Azure Synapse.

1. Klik in de hub **Gegevens** op **Een nieuwe resource toevoegen**(plusteken boven **Gekoppeld**) >> **Bladeren in galerie**. 
1. Zoek **NYC Taxi & Limousine Commission - records van Yellow Taxi-ritten** en klik erop. 
1. Druk onderaan de pagina op **Doorgaan** en daarna op **Gegevensset toevoegen**. 
1. Klik nu in de hub **Gegevens** onder **Gekoppeld** met de rechtermuisknop op **Azure Blob Storage >> Voorbeeldgegevenssets >> nyc_tlc_yellow** en selecteer **Nieuw notebook**
1. Hiermee maakt u een nieuw notebook met de volgende code:
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. Kies in het notebook een serverloze Spark-pool in het menu **Koppelen aan**
1. Selecteer **Uitvoeren** op de cel
1. Als u alleen wilt zien hoe een cel met de volgende code wordt uitgevoerd met het schema van het dataframe:
    ```
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>De gegevens van NYC-taxi laden in de Apache Spark-database nyctaxi

Er zijn gegevens beschikbaar in een tabel in **SQLPOOL1**. Laad deze in een Apache Spark-database met de naam **nyctaxi**.

1. Ga in Synapse Studio naar de hub **Ontwikkelen**.
1. Selecteer **+**  > **Notebook**.
1. Stel bovenaan de notebook de waarde voor **Koppelen aan** in op **Spark1**.
1. Selecteer **Code toevoegen** om een notebookcodecel toe te voegen en plak de volgende tekst:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Ga naar de hub **Data**, klik met de rechtermuisknop op **Databases** en selecteer **Vernieuwen**. U zou deze databases moeten zien:

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>De gegevens over de NYC-taxi met behulp van Apache Spark en notebooks analyseren

1. Ga terug naar uw notebook.
1. Maak een nieuwe codecel en voer de volgende tekst in. Voer vervolgens de cel uit om de NYC-taxigegevens weer te geven die zijn geladen in de Apache Spark-database **nyctaxi**.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Voer de volgende code uit om dezelfde analyse uit te voeren die we eerder met de toegewezen SQL-pool **SQLPOOL1** hebben uitgevoerd. Met deze code worden ook de resultaten van de analyse opgeslagen in een tabel met de naam **nyctaxi.passengercountstats** en worden de resultaten gevisualiseerd.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Selecteer **Grafiek** in de celresultaten om de gevisualiseerde gegevens weer te geven.





## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Gegevens uit een Apache Spark-tabel laden in een toegewezen SQL-pooltabel

Eerder hebt u gegevens uit de toegewezen SQL-pooltabel **SQLPOOL1.dbo.Trip** gekopieerd naar de Spark-tabel **nyctaxi.trip**. Vervolgens aggregeerde u de gegevens in de Apache Spark-tabel **nyctaxi.passengercountstats** met behulp van Apache Spark. Nu kopieert u de gegevens uit **nyctaxi.passengercountstats** naar een toegewezen SQL-pooltabel met de naam **SQLPOOL1.dbo.PassengerCountStats**.

Voer de volgende cel uit in uw notebook. Hiermee wordt de geaggregeerde Apache Spark-tabel weer gekopieerd naar de toegewezen SQL-pooltabel.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens analyseren met een serverloze SQL-pool](get-started-analyze-sql-on-demand.md)


