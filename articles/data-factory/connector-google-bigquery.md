---
title: Gegevens uit Google BigQuery kopiëren met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit Google BigQuery naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een data factory-pijp lijn.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 6751f64706444176f0df8f8fc0c6132e76b39b2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81417326"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Gegevens uit Google BigQuery kopiëren met behulp van Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit Google BigQuery te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Google BigQuery-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit Google BigQuery kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit.

Data Factory biedt een ingebouwd stuur programma om connectiviteit mogelijk te maken. Daarom hoeft u niet hand matig een stuur programma te installeren om deze connector te gebruiken.

>[!NOTE]
>Deze Google BigQuery-connector is gebaseerd op de BigQuery-Api's. Houd er rekening mee dat BigQuery het maximale aantal binnenkomende aanvragen beperkt en de juiste quota per project afdwingt, de [quota's & limieten-API-aanvragen](https://cloud.google.com/bigquery/quotas#api_requests). Zorg ervoor dat u niet te veel gelijktijdige aanvragen naar het account wilt activeren.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Google BigQuery-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de Google BigQuery-gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **GoogleBigQuery**. | Ja |
| project | De project-ID van het standaard BigQuery-project waarmee een query wordt uitgevoerd.  | Ja |
| additionalProjects | Een door komma's gescheiden lijst met project-Id's van open bare BigQuery-projecten voor toegang.  | Nee |
| requestGoogleDriveScope | Of u toegang tot Google Drive wilt aanvragen. Het toestaan van toegang tot Google Drive biedt ondersteuning voor federatieve tabellen die BigQuery gegevens combi neren met gegevens uit Google Drive. De standaardwaarde is **onwaar**.  | Nee |
| authenticationType | Het OAuth 2,0-verificatie mechanisme dat wordt gebruikt voor verificatie. ServiceAuthentication kan alleen worden gebruikt op zelf-hostende Integration Runtime. <br/>Toegestane waarden zijn **UserAuthentication** en **ServiceAuthentication**. Raadpleeg de secties onder deze tabel voor meer eigenschappen en JSON-voor beelden voor deze verificatie typen. | Ja |

### <a name="using-user-authentication"></a>Gebruikers verificatie gebruiken

Stel de eigenschap authenticationType in op **UserAuthentication**en geef de volgende eigenschappen op, samen met algemene eigenschappen die in de vorige sectie zijn beschreven:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| clientId | ID van de toepassing die wordt gebruikt voor het genereren van het vernieuwings token. | Nee |
| clientSecret | Geheim van de toepassing die wordt gebruikt voor het genereren van het vernieuwings token. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Nee |
| refreshToken | Het vernieuwings token dat is verkregen van Google, wordt gebruikt om toegang te verlenen aan BigQuery. Meer informatie over het verkrijgen van [OAuth 2,0-toegangs tokens](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) en [deze Community-Blog](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Nee |

**Voorbeeld:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Service verificatie gebruiken

Stel de eigenschap authenticationType in op **ServiceAuthentication**en geef de volgende eigenschappen op, samen met algemene eigenschappen die in de vorige sectie worden beschreven. Dit verificatie type kan alleen worden gebruikt op zelf-hostende Integration Runtime.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| e-mail | De e-mail-ID van het service account die wordt gebruikt voor ServiceAuthentication. Het kan alleen worden gebruikt op zelf-hostende Integration Runtime.  | Nee |
| keyFilePath | Het volledige pad naar het. p12-sleutel bestand dat wordt gebruikt om het e-mail adres van het service account te verifiëren. | Nee |
| trustedCertPath | Het volledige pad van het. pem-bestand dat vertrouwde CA-certificaten bevat die worden gebruikt om de server te controleren wanneer u verbinding maakt via TLS. Deze eigenschap kan alleen worden ingesteld wanneer u gebruikmaakt van TLS op zelf-hostende Integration Runtime. De standaard waarde is het cacerts. pem-bestand dat is geïnstalleerd met de Integration runtime.  | Nee |
| useSystemTrustStore | Hiermee geeft u op of u een CA-certificaat wilt gebruiken uit de systeem vertrouwens archief of vanuit een opgegeven PEM-bestand. De standaardwaarde is **onwaar**.  | Nee |

**Voorbeeld:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Google BigQuery-gegevensset.

Als u gegevens wilt kopiëren uit Google BigQuery, stelt u de eigenschap type van de gegevensset in op **GoogleBigQueryObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **GoogleBigQueryObject** | Ja |
| sets | De naam van de Google BigQuery-gegevensset. |Nee (als "query" in activiteit bron is opgegeven)  |
| table | De naam van de tabel. |Nee (als "query" in activiteit bron is opgegeven)  |
| tableName | De naam van de tabel. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik en voor nieuwe werk `dataset` belasting `table` . | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door het Google BigQuery-bron type.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource als bron type

Als u gegevens wilt kopiëren uit Google BigQuery, stelt u het bron type in de Kopieer activiteit in op **GoogleBigQuerySource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **GoogleBigQuerySource**. | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `"SELECT * FROM MyTable"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in Data Factory.
