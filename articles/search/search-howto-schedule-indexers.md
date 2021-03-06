---
title: Uitvoering van de Indexeer functie plannen
titleSuffix: Azure Cognitive Search
description: Plan Azure Cognitive Search Indexeer functies om inhoud periodiek of op een bepaald tijdstip te indexeren.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 80c3f9aa02680097276f966ce6aea02acf1e40fb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358793"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Indexeer functies plannen in azure Cognitive Search

Een Indexeer functie wordt normaal gesp roken eenmaal uitgevoerd, direct nadat deze is gemaakt. U kunt het opnieuw uitvoeren op aanvraag met behulp van de portal, de REST API of de .NET SDK. U kunt ook een Indexeer functie zo configureren dat deze regel matig wordt uitgevoerd volgens een planning.

Een aantal situaties waarin de planning van de Indexeer functie nuttig is:

* De bron gegevens worden na verloop van tijd gewijzigd en u wilt dat de gewijzigde gegevens automatisch worden verwerkt door de Indexeer functies van Azure Cognitive Search.
* De index wordt gevuld met meerdere gegevens bronnen en u wilt er zeker van zijn dat de Indexeer functies op verschillende tijdstippen worden uitgevoerd om conflicten te verminderen.
* De bron gegevens zijn erg groot en u wilt de verwerking van de Indexeer functie gedurende een periode spreiden. Zie voor meer informatie over het indexeren van grote hoeveel heden gegevens [grote gegevens sets indexeren in Azure Cognitive Search](search-howto-large-index.md).

De scheduler is een ingebouwde functie van Azure Cognitive Search. U kunt geen externe planner gebruiken om Zoek Indexeer functies te beheren.

## <a name="define-schedule-properties"></a>Schema-eigenschappen definiëren

Een indexer schema heeft twee eigenschappen:
* **Interval** , waarmee de hoeveelheid tijd tussen de geplande uitvoeringen van de Indexeer functie wordt gedefinieerd. Het kleinste toegestane interval is 5 minuten en de grootste is 24 uur.
* **Begin tijd (UTC)** , waarmee wordt aangegeven wanneer de Indexeer functie voor het eerst moet worden uitgevoerd.

U kunt een planning opgeven wanneer u eerst de Indexeer functie maakt of door de eigenschappen van de Indexeer functie later bij te werken. De planningen voor de Indexeer functie kunnen worden ingesteld met behulp van de [Portal](#portal), de [rest API](#restApi)of de [.NET SDK](#dotNetSdk).

Er kan slechts één uitvoering van een Indexeer functie tegelijk worden uitgevoerd. Als er al een Indexeer functie wordt uitgevoerd wanneer de volgende uitvoering wordt gepland, wordt die uitvoering uitgesteld tot het volgende geplande tijdstip.

Laten we eens kijken naar een voor beeld om dit meer concreet te maken. Stel dat we een indexer schema configureren met een **interval** van elk uur en een **begin tijd** van 1 juni 2019 om 8:00:00 uur UTC. Dit kan gebeuren wanneer het uitvoeren van een Indexeer functie langer dan een uur duurt:

* De eerste uitvoering van de Indexeer functie begint op of rond 1 juni 2019 om 8:00 uur UTC. Stel dat deze uitvoering 20 minuten duurt (of een periode van minder dan 1 uur).
* De tweede uitvoering begint op of rond 1 juni 2019 9:00 uur UTC. Stel dat deze uitvoering 70 minuten-meer dan een uur duurt en niet wordt voltooid tot 10:10 uur UTC.
* De derde uitvoering is gepland om te beginnen om 10:00 uur UTC, maar op dat moment wordt de vorige uitvoering nog steeds uitgevoerd. Deze geplande uitvoering wordt vervolgens overgeslagen. De volgende uitvoering van de Indexeer functie wordt pas gestart als 11:00 uur UTC.

> [!NOTE]
> Als een Indexeer functie is ingesteld op een bepaald schema, maar herhaaldelijk meerdere keren een fout optreedt in hetzelfde document, wordt de Indexeer functie gestart op een minder frequent interval (Maxi maal ten minste één keer per 24 uur) totdat de voortgang weer wordt uitgevoerd.  Als u van mening bent dat het probleem dat de Indexeer functie niet op een bepaald moment vastloopt, kunt u een on-demand uitvoering van de Indexeer functie uitvoeren. als dat wel het geval is, keert de Indexeer functie weer terug naar het ingestelde plannings interval.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Plannen in de portal

Met de wizard gegevens importeren in de portal kunt u het schema definiëren voor een Indexeer functie tijdens het maken. De standaard instelling voor het schema is elk **uur** , wat betekent dat de Indexeer functie eenmaal wordt uitgevoerd nadat deze is gemaakt en daarna opnieuw wordt uitgevoerd.

U kunt de instelling van het schema wijzigen in **één keer** wanneer u niet wilt dat de Indexeer functie automatisch opnieuw wordt uitgevoerd of **dagelijks** één keer per dag wordt uitgevoerd. Stel deze in op **aangepast** als u een ander interval of een specifieke toekomstige begin tijd wilt opgeven.

Wanneer u de planning instelt op **aangepast** , worden er velden weer gegeven waarin u het **interval** en de **begin tijd (UTC)** kunt opgeven. Het kortste tijds interval dat is toegestaan, is 5 minuten en de langste is 1440 minuten (24 uur).

   ![Indexerings schema instellen in de wizard gegevens importeren](media/search-howto-schedule-indexers/schedule-import-data.png "Indexerings schema instellen in de wizard gegevens importeren")

Nadat u een Indexeer functie hebt gemaakt, kunt u de schema-instellingen wijzigen met behulp van het bewerkings paneel van de Indexeer functie. De plannings velden zijn hetzelfde als in de wizard gegevens importeren.

   ![Het schema instellen in het paneel voor het bewerken van indexeren](media/search-howto-schedule-indexers/schedule-edit.png "Het schema instellen in het paneel voor het bewerken van indexeren")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Plannen met behulp van REST-Api's

U kunt het schema voor een Indexeer functie definiëren met behulp van de REST API. Hiertoe neemt u de eigenschap **schema** op bij het maken of bijwerken van de Indexeer functie. In het onderstaande voor beeld ziet u een PUT-aanvraag voor het bijwerken van een bestaande Indexeer functie:

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

De **interval** parameter is vereist. Het interval verwijst naar de tijd tussen het begin van twee opeenvolgende indexerings uitvoeringen. Het kleinste toegestane interval is 5 minuten. de langste is één dag. Deze moet worden ingedeeld als een XSD ' dayTimeDuration-waarde (een beperkte subset van een [ISO 8601 duration](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) -waarde). Het patroon hiervoor is: `P(nD)(T(nH)(nM))` . Voor beelden: elke `PT15M` 2 uur voor elke 15 minuten `PT2H` .

De optionele **StartTime** geeft aan wanneer geplande uitvoeringen moeten beginnen. Als u deze para meter weglaat, wordt de huidige UTC-tijd gebruikt. Deze tijd kan in het verleden liggen. in dat geval wordt de eerste uitvoering gepland alsof de Indexeer functie continu actief is sinds de oorspronkelijke **StartTime**.

U kunt op elk gewenst moment een Indexeer functie op aanvraag uitvoeren met de aanroep run indexer. Zie voor meer informatie over het uitvoeren van Indexeer functies en het instellen van index planningen indexering [uitvoeren](/rest/api/searchservice/run-indexer), [Indexeer functie ophalen](/rest/api/searchservice/get-indexer)en [Indexeer functie bijwerken](/rest/api/searchservice/update-indexer) in de referentie rest API.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Plannen met behulp van de .NET SDK

U kunt het schema voor een Indexeer functie definiëren met behulp van de Azure Cognitive Search .NET SDK. Hiertoe neemt u de eigenschap **schema** op wanneer u een Indexeer functie maakt of bijwerkt.

In het volgende C#-voor beeld wordt een Azure SQL database Indexeer functie gemaakt met behulp van een vooraf gedefinieerde gegevens bron en index, en wordt ingesteld dat het schema elke dag wordt uitgevoerd:

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```


Als de eigenschap **Schedule** wordt wegge laten, wordt de Indexeer functie slechts eenmaal uitgevoerd nadat deze is gemaakt.

De para meter **StartTime** kan worden ingesteld op een tijd in het verleden. In dat geval wordt de eerste uitvoering gepland alsof de Indexeer functie continu actief is sinds de opgegeven **StartTime**.

De planning is gedefinieerd met behulp van de [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) -klasse. De **IndexingSchedule** -constructor vereist een **interval** parameter die is opgegeven met behulp van een **time span** -object. De kleinste toegestane interval waarde is 5 minuten en de grootste is 24 uur. De tweede para meter **StartTime** , opgegeven als **Date Time offset** -object, is optioneel.

Met de .NET SDK kunt u Indexeer bewerkingen beheren via de [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). 

U kunt op elk gewenst moment een indexer op aanvraag uitvoeren met een van de methoden [RunIndexer](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) of [RunIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexerasync) .

Zie [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)voor meer informatie over het maken, bijwerken en uitvoeren van Indexeer functies.