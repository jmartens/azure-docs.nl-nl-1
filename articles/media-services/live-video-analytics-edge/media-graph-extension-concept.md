---
title: Wat is een mediagrafiekextensie - Azure
description: Met Live Video Analytics op IoT Edge kunt u de mogelijkheden voor mediagrafiekverwerking uitbreiden via een grafiekextensieknooppunt.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 74929cc51a868d20952f1e25432f5343e4821d08
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89569323"
---
# <a name="media-graph-extension"></a>Media Graph-extensie

Met Live Video Analytics op IoT Edge kunt u de mogelijkheden voor mediagrafiekverwerking uitbreiden via een grafiekextensieknooppunt. Uw Analytics-extensie-invoegtoepassing kan gebruikmaken van traditionele beeldverwerkingstechnieken of AI-modellen van Computer Vision. Grafiekextensies worden ingeschakeld door een knooppunt voor de extensieprocessor op te nemen in een mediagrafiek. Het knooppunt voor de extensieprocessor verzendt videoframes naar het geconfigureerde  eindpunt en fungeert als de interface voor uw extensie. De verbinding kan tot stand worden gebracht met een lokaal of extern eindpunt en kan indien nodig worden beveiligd met verificatie en TLS-versleuteling. Daarnaast biedt het knooppunt voor de grafiekextensieprocessor optioneel schalen en versleutelen van de videoframes voordat ze naar uw aangepaste extensie worden verzonden.

Live Video Analytics ondersteunt twee soorten extensieprocessors voor mediagrafieken:

* [HTTP-extensieprocessor](media-graph-concept.md#http-extension-processor)
* [gRPC-extensieprocessor](media-graph-concept.md#grpc-extension-processor)

## <a name="http-extension-processor"></a>HTTP-extensieprocessor

De HTTP-extensieprocessor maakt uitbreidingsscenario’s met behulp van het HTTP-protocol mogelijk, waarbij prestaties en/of optimaal resourcegebruik niet het belangrijkste zijn. U kunt uw eigen AI beschikbaar maken voor een mediagrafiek via een HTTP REST-eindpunt. 

Gebruik het knooppunt voor de HTTP-extensieprocessor wanneer:

* U betere interoperabiliteit met bestaande HTTP-deductiesystemen wilt hebben.
* Gegevensoverdracht met lagere prestaties acceptabel is.
* U een eenvoudige aanvraag-antwoordinterface voor Live Video Analytics wilt gebruiken.

## <a name="grpc-extension-processor"></a>gRPC-extensieprocessor

De gRPC-extensieprocessor maakt uitbreidingsscenario’s met behulp van het hoogwaardige, gestructureerde gRPC-protocol mogelijk. Het is ideaal voor scenario’s waarin prestaties en/of optimaal resourcegebruik een prioriteit zijn. Met de gRPC-extensieprocessor kunt u volledig profiteren van de gestructureerde gegevensdefinities. gRPC biedt inhoudsoverdracht met hoge prestaties met behulp van:

* [een meegeleverd gedeeld geheugen](https://en.wikipedia.org/wiki/Shared_memory) of 
* rechtstreekse insluiting van de inhoud in de hoofdtekst van gRPC-berichten. 

De gRPC-extensieprocessor kan worden gebruikt voor het verzenden van media-eigenschappen evenals het uitwisselen van deductieberichten.
Gebruik een knooppunt voor de gRPC-extensieprocessor dus wanneer u:

* Een gestructureerd contract wilt gebruiken (bijvoorbeeld gestructureerde berichten voor aanvragen en antwoorden).
* Protocol Buffers ([protobuf](https://developers.google.com/protocol-buffers)) wilt gebruiken als de onderliggende berichtuitwisselingsindeling voor communicatie.
* Met een gRPC-server wilt communiceren in een sessie met één stroom in plaats van het traditionele aanvraag-antwoordmodel waarbij een aangepaste aanvraaghandler nodig is om inkomende aanvragen te parseren en de juiste implementatiefuncties aan te roepen. 
* Communicatie met een lage latentie en hoge doorvoer tussen Live Video Analytics en uw module wilt hebben.

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>Uw deductiemodel gebruiken met Live Video Analytics

Met mediagrafiekextensies kunt u deductiemodellen van uw keuze uitvoeren op elke beschikbare deductieruntime, zoals ONNX, TensorFlow, PyTorch of andere in uw eigen docker-container. De aangepaste deductie-extensie moet samen met de Live Video Analytics Edge-module worden geïmplementeerd voor optimale prestaties en wordt vervolgens aangeroepen via de HTTP-extensieprocessor of de gRPC-extensieprocessor die in uw grafiektopologie is opgenomen. Daarnaast kan de frequentie van de aanroepen bij uw aangepaste extensie worden beperkt door optioneel een [bewegingsdetectorprocessor](media-graph-concept.md#motion-detection-processor) en een [framesnelheidsfilterprocessor](media-graph-concept.md#frame-rate-filter-processor) upstream toe te voegen aan de media-extensieprocessor.

In het onderstaande diagram wordt de gegevensstroom op hoog niveau weergegeven:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph-extension/analyze-live-video-with-AI-inference-service.svg" alt-text="Deductieservice kunstmatige intelligentie":::

## <a name="samples"></a>Voorbeelden

Bekijk enkele van onze [Jupyter Notebook](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/readme.md)-voorbeelden voor Live Video Analytics. Deze notebooks bieden u stapsgewijze instructies voor **de mediagrafiekextensies** over:

* Een Docker-containerinstallatiekopie maken van een extensieservice
* De extensieservice als een container implementeren samen met de Live Video Analytics-container
* Een Live Video Analytics-mediagrafiek met een extensieclient gebruiken en naar het extensie-eindpunt (HTTP/gRPC) laten wijzen