---
title: 'Zelfstudie: Ga aan de slag met het analyseren van gegevens met een serverloze SQL-pool'
description: In deze zelfstudie leert u hoe u gegevens kunt analyseren met serverloze SQL-pool, met behulp van gegevens die zich in Spark-databases bevinden.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4ca9ababbeb7843f1a014a4bd51a5e24a74acbae
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322935"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Gegevens analyseren met een serverloze SQL-pool in Azure Synapse Analytics

In deze zelfstudie leert u hoe u gegevens kunt analyseren met serverloze SQL-pool, met behulp van gegevens die zich in Spark-databases bevinden. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>NYC Taxi-gegevens analyseren in blob-opslag met een serverloze SQL-pool

1. Klik in de hub **Data** onder **Gekoppeld** met de rechter muisknop op **Azure Blob Storage > Voorbeeldgegevenssets > nyc_tlc_yellow** en selecteer **Top 100-rijen SELECTEREN**
1. Hiermee maakt u een nieuw SQL-script met de volgende code:

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Klik op **Uitvoeren**

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>NYC-taxigegevens analyseren in Spark-databases met behulp van een serverloze SQL-pool

Tabellen in Apache Spark-databases zijn automatisch zichtbaar en er kunnen query's op worden uitgevoerd door een serverloze SQL-pool.

1. Ga in Synapse Studio naar de hub **Ontwikkelen** en maak een nieuw SQL-script.
1. Stel **Verbinding maken met** in op **serverloze SQL-pool**.
1. Plak de volgende tekst in het script en voer het script uit.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > De eerste keer dat u een query uitvoert die een serverloze SQL-pool gebruikt, duurt het ongeveer tien seconden tot een serverloze SQL-pool de SQL-resources heeft verzameld die nodig zijn om uw query's uit te voeren. Daaropvolgende query's worden veel sneller uitgevoerd.
  


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens analyseren in Storage](get-started-analyze-storage.md)
