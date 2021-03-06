---
title: Azure Data Lake Storage-uitvoer van 1 tot en met Azure Stream Analytics
description: In dit artikel wordt Azure Data Lake Storage gen 1 beschreven als uitvoer optie voor Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 688cd7c2e54a6b4c6b5a11705e10c33604324f09
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576278"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>Azure Data Lake Storage-uitvoer van 1 tot en met Azure Stream Analytics

Stream Analytics ondersteunt [Azure data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md) -uitvoer. Azure Data Lake Storage is een grootschalige-opslag plaats voor het hele bedrijf voor big data analytische werk belastingen. U kunt Data Lake Storage gebruiken voor het opslaan van gegevens van elke grootte, type en opname snelheid voor operationele en experimentele analyses. Stream Analytics moet worden gemachtigd om toegang te krijgen tot Data Lake Storage.

Azure Data Lake Storage uitvoer van Stream Analytics is niet beschikbaar in de regio's in azure China 21Vianet en Azure Duitsland (T-Systems International).

## <a name="output-configuration"></a>Uitvoer configuratie

De volgende tabel geeft een lijst van eigenschaps namen en de bijbehorende beschrijvingen om uw Data Lake Storage gen 1-uitvoer te configureren.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias | Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query om te leiden naar Data Lake Store. |
| Abonnement | Het abonnement met uw Azure Data Lake Storage-account. |
| Accountnaam | De naam van het Data Lake Store-account waarnaar u uw uitvoer wilt verzenden. Er wordt een vervolg keuzelijst weer gegeven met Data Lake Store accounts die beschikbaar zijn in uw abonnement. |
| Patroon voor voegsel van pad | Het bestandspad dat wordt gebruikt voor het schrijven van uw bestanden binnen het opgegeven Data Lake Store-account. U kunt een of meer exemplaren van de variabelen {date} en {time} opgeven:<br /><ul><li>Voor beeld 1: Map1/logboeken/{date}/{time}</li><li>Voor beeld 2: Map1/logboeken/{date}</li></ul><br />De tijds tempel van de gemaakte mappen structuur volgt UTC en niet lokale tijd.<br /><br />Als het patroon van het bestandspad geen afsluitende slash (/) bevat, wordt het laatste patroon in het bestandspad behandeld als een voor voegsel van de bestands naam. <br /><br />In deze omstandigheden worden nieuwe bestanden gemaakt:<ul><li>Wijziging in uitvoer schema</li><li>Externe of interne herstart van een taak</li></ul> |
| Datum notatie | Optioneel. Als het datum token wordt gebruikt in het voorvoegsel pad, kunt u de datum notatie selecteren waarin uw bestanden zijn ingedeeld. Voor beeld: JJJJ/MM/DD |
|Tijd notatie | Optioneel. Als de time-token wordt gebruikt in het pad van het voor voegsel, geeft u de tijd notatie op waarin uw bestanden zijn geordend. Op dit moment is de enige ondersteunde waarde HH. |
| Serialisatie-indeling voor gebeurtenissen | De serialisatie-indeling voor uitvoer gegevens. JSON, CSV en Avro worden ondersteund.|
| Encoding | Als u de CSV-of JSON-indeling gebruikt, moet u een code ring opgeven. UTF-8 is op dit moment de enige coderings indeling die wordt ondersteund.|
| Scheidingsteken | Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal algemene scheidings tekens voor het serialiseren van CSV-gegevens. Ondersteunde waarden zijn komma, punt komma, spatie, tab en verticale streep.|
| Indeling | Alleen van toepassing op JSON-serialisatie. Met **regel scheiding** wordt opgegeven dat de uitvoer wordt opgemaakt door elk JSON-object gescheiden door een nieuwe regel. Als u **regel gescheiden** selecteert, wordt de JSON één object per keer gelezen. De gehele inhoud zelf zou geen geldige JSON kunnen zijn.  **Matrix** geeft aan dat de uitvoer is ingedeeld als een matrix van JSON-objecten. Deze matrix wordt alleen gesloten wanneer de taak wordt gestopt of Stream Analytics is verplaatst naar het volgende tijd venster. Over het algemeen is het raadzaam om met regel-gescheiden JSON te gebruiken, omdat hiervoor geen speciale verwerking is vereist terwijl het uitvoer bestand nog wordt geschreven.|
| Verificatiemodus | U kunt toegang tot uw Data Lake Storage-account verlenen met behulp van [beheerde identiteit](stream-analytics-managed-identities-adls.md) (preview) of gebruikers token. Nadat u toegang hebt verleend, kunt u de toegang intrekken door het wacht woord van het gebruikers account te wijzigen, de Data Lake Storage uitvoer voor deze taak te verwijderen of de Stream Analytics-taak te verwijderen. |

## <a name="partitioning"></a>Partitionering

Gebruik voor de partitie sleutel {date} en {time} tokens in het pad voorvoegsel patroon. Kies een datum notatie, zoals JJJJ/MM/DD, DD/MM/JJJJ of MM-DD-JJJJ. Gebruik uu voor de tijd notatie. Het aantal schrijvers van de uitvoer volgt de invoer partities voor [volledig kan worden opgestart-query's](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Grootte van uitvoer batch

Zie [Data Lake Storage limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits)voor de maximale bericht grootte. Gebruik Maxi maal 4 MB per schrijf bewerking om de Batch grootte te optimaliseren.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
* [Quickstart: een Azure Stream Analytics-taak maken via de Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Een Azure Stream Analytics-taak maken via een ARM-sjabloon](quick-create-azure-resource-manager.md)
* [Quickstart: Een Stream Analytics-taak maken met behulp van Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Een Azure Stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Snelstartgids: een Azure Stream Analytics-taak maken in Visual Studio code](quick-create-visual-studio-code.md)