---
title: Verbinding maken met Synapse SQL
description: Maak verbinding met Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 2ff2d9708642c347a829de290d45849d8629807f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462347"
---
# <a name="connect-to-synapse-sql"></a>Verbinding maken met Synapse SQL
Maak verbinding met de Synapse-SQL-optie in Azure Synapse Analytics.

## <a name="supported-tools-for-serverless-sql-pool"></a>Ondersteunde hulpprogramma's voor serverloze SQL-pools

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) wordt volledig ondersteund vanaf versie 1.18.0. SSMS wordt gedeeltelijk ondersteund vanaf versie 18.5, maar u kunt de tool gebruiken om alleen verbinding te maken en query's uit te voeren.

> [!NOTE]
> Als voor een AAD-aanmelding een verbinding is geopend die langer dan één uur duurt tijdens het uitvoeren van de query, mislukken alle query's die afhankelijk zijn van AAD. Dit omvat het uitvoeren van query's in opslag met AAD Pass-through en instructies die communiceren met AAD (zoals CREATE EXTERNAL PROVIDER). Dit is van invloed op elk hulpprogramma dat verbindingen openhoudt, zoals de query-editor in SSMS en ADS. Hulpprogramma's waarmee nieuwe verbindingen worden geopend om een query uit te voeren, zoals Synapse Studio, worden niet beïnvloed.

> U kunt SSMS opnieuw starten of een verbinding maken en weer verbreken in ADS om dit probleem te verhelpen. 

## <a name="find-your-server-name"></a>Uw servernaam vinden

De servernaam voor de toegewezen SQL-pool in het volgende voorbeeld is: showdemoweu.sql.azuresynapse.net.
De servernaam voor de toegewezen SQL-pool in het volgende voorbeeld is: showdemoweu-ondemand.sql.azuresynapse.net.

Ga als volgt te werk om de volledig gekwalificeerde servernaam te vinden:

1. Ga naar de [Azure Portal](https://portal.azure.com).
2. Selecteer **Synapse-werkruimten**.
3. Selecteer de werkruimte waarmee u verbinding wilt maken.
4. Ga naar overzicht.
5. Zoek de volledige servernaam.

## <a name="sql-pool"></a>**SQL-pool**

![Volledige servernaam](./media/connect-overview/server-connect-example.png)

## <a name="serverless-sql-pool"></a>**serverloze SQL-pool**

![Volledige servernaam voor de serverloze SQL-pool](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>Ondersteunde stuurprogramma's en verbindingsreeksen
Synapse SQL biedt ondersteuning voor [ADO.NET](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396) en [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). Selecteer een van de bovenstaande stuurprogramma's om de meest recente versie en documentatie te vinden. Voor het automatisch genereren van de verbindingsreeks voor het stuurprogramma dat u gebruikt vanuit de Azure-portal, selecteert u **Databaseverbindingsreeksen tonen** uit het voorgaande voorbeeld. Hier volgen ook enkele voorbeelden van hoe een verbindingsreeks er voor elk stuurprogramma uitziet.

> [!NOTE]
> Overweeg de verbindingstime-out in te stellen op 300 seconden. De verbinding blijft dan in stand tijdens korte perioden van niet-beschikbaarheid.

### <a name="adonet-connection-string-example"></a>Voorbeeld van ADO.NET-verbindingsreeks

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Voorbeeld van ODBC-verbindingsreeks

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Voorbeeld van PHP-verbindingsreeks

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Voorbeeld van JDBC-verbindingsreeks

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Verbindingsinstellingen
Synapse SQL standaardiseert enkele instellingen tijdens het maken van de verbinding en het maken van objecten. Deze instellingen kunnen niet worden overschreven, en omvatten:

| Database-instelling | Waarde |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |AAN |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |AAN |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |mdy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |7 |

## <a name="recommendations"></a>Aanbevelingen

[Azure Data Studio](get-started-azure-data-studio.md) en Azure Synapse Studio zijn de aanbevolen hulpprogramma's voor het uitvoeren van query's van een **serverloze SQL-pool**.

## <a name="next-steps"></a>Volgende stappen
Zie [Query’s uitvoeren bij Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) als u verbinding wilt maken en een query wilt uitvoeren met Visual Studio. Zie [Verificatie met Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie over verificatieopties.