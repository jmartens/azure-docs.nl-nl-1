---
title: Para meters gekoppelde services in Azure Data Factory
description: Meer informatie over het para meters van gekoppelde services in Azure Data Factory en het door geven van dynamische waarden tijdens runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/09/2020
author: dcstwh
ms.author: weetok
manager: anandsub
ms.openlocfilehash: da38dd99d0f27d83d5810a664d0c05f979f47080
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920075"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Para meters gekoppelde services in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

U kunt nu een gekoppelde service para meters en dynamische waarden door geven tijdens runtime. Als u bijvoorbeeld verbinding wilt maken met verschillende data bases op dezelfde logische SQL-Server, kunt u de naam van de data base nu para meters in de definitie van de gekoppelde service. Zo voor komt u dat u een gekoppelde service moet maken voor elke Data Base op de logische SQL-Server. U kunt ook andere eigenschappen in de definitie van de gekoppelde service para meters, bijvoorbeeld *gebruikers naam.*

U kunt de Data Factory-gebruikers interface in de Azure Portal of een programmeer interface gebruiken om gekoppelde services te para meters.

> [!TIP]
> We raden u aan om wacht woorden of geheimen niet para meters. Sla alle verbindings reeksen op in Azure Key Vault in plaats daarvan en para meters de naam van het *geheim*.

Bekijk de volgende video voor een inleiding en demonstratie van zeven minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>Ondersteunde gekoppelde service typen

U kunt elk type gekoppelde service para meters.
Bij het ontwerpen van een gekoppelde service op de gebruikers interface biedt Data Factory ingebouwde parameterisering-ervaring voor de volgende typen gekoppelde services. In de Blade gekoppelde service maken/bewerken kunt u opties voor nieuwe para meters vinden en dynamische inhoud toevoegen.

- Amazon Redshift
- Amazon S3
- Azure Cosmos DB (SQL API)
- Azure Database for MySQL
- Azure Databricks
- Azure SQL Database
- Azure SQL Managed Instance
- Azure Synapse Analytics 
- MySQL
- Oracle
- SQL Server
- Algemene HTTP
- Algemene REST

Voor andere gekoppelde service typen die zich niet in de bovenstaande lijst bevinden, kunt u de gekoppelde service para meters door de JSON te bewerken in de gebruikers interface:

- In de Blade gekoppelde service maken/bewerken-> vouw ' Geavanceerd ' aan de onderkant uit > Schakel het selectie vakje dynamische inhoud opgeven in JSON-indeling in, > Geef de JSON-nettolading van de gekoppelde service op. 
- Het is ook mogelijk dat u, nadat u een gekoppelde service zonder parameterisering hebt gemaakt, in [Management hub](author-visually.md#management-hub) -> gekoppelde services-> de specifieke gekoppelde service te vinden-> op ' code ' (knop ' {} ') klikken om de JSON te bewerken. 

Raadpleeg de sectie [JSON](#json) -voor beeld om ` parameters` para meters te definiëren en te verwijzen naar de para meter met ` @{linkedService().paraName} ` .

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory

![Dynamische inhoud toevoegen aan de definitie van de gekoppelde service](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Een nieuwe parameter maken](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
