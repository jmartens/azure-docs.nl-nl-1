---
title: Toegangs beheer voor werkmappen Azure Monitor
description: Vereenvoudig complexe rapportage met vooraf ontwikkelde en aangepaste werkmappen met op rollen gebaseerd toegangs beheer
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 7d3bc13dc373cda510153099859cf4cd61b3dd69
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95534531"
---
# <a name="access-control"></a>Toegangsbeheer

Toegangs beheer in werkmappen verwijst naar twee dingen:

* De toegang is vereist om gegevens in een werkmap te lezen. Deze toegang wordt bepaald door de standaard [functies van Azure](../../role-based-access-control/overview.md) voor de resources die worden gebruikt in de werkmap. Werkmappen opgeven of configureren geen toegang tot deze resources. Gebruikers krijgen deze toegang gewoonlijk tot deze resources met behulp van de rol [controle lezer](../../role-based-access-control/built-in-roles.md#monitoring-reader) op deze resources.

* Toegang vereist om werkmappen op te slaan

    - Het opslaan van persoonlijke `("My")` werkmappen vereist geen aanvullende bevoegdheden. Alle gebruikers kunnen persoonlijke werkmappen opslaan en alleen deze werkmappen worden weer geven.
    - Het opslaan van gedeelde werkmappen vereist schrijf bevoegdheden in een resource groep om de werkmap op te slaan. Deze bevoegdheden worden meestal opgegeven door de rol [bewaking Inzender](../../role-based-access-control/built-in-roles.md#monitoring-contributor) , maar kunnen ook worden ingesteld via de rol *werkmappen Inzender* .
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Standaard rollen met machtigingen die betrekking hebben op de werkmap

[Bewakings lezer](../../role-based-access-control/built-in-roles.md#monitoring-reader) bevat standaard/Read bevoegdheden die door controle hulpprogramma's (inclusief werkmappen) worden gebruikt om gegevens van resources te lezen.

[Bewakings bijdrager](../../role-based-access-control/built-in-roles.md#monitoring-contributor) bevat algemene `/write` bevoegdheden die worden gebruikt door verschillende controle hulpprogramma's voor het opslaan van items (inclusief `workbooks/write` bevoegdheden voor het opslaan van gedeelde werkmappen).
"Werkmappen Inzender" voegt "werkmappen/schrijf bevoegdheden" toe aan een object om gedeelde werkmappen op te slaan.
Er zijn geen speciale bevoegdheden vereist voor gebruikers om persoonlijke werkmappen op te slaan die alleen kunnen worden weer geven.

Voor aangepaste rollen:

Toevoegen `microsoft.insights/workbooks/write` om gedeelde werkmappen op te slaan. Zie de rol [werkmap Inzender](../../role-based-access-control/built-in-roles.md#monitoring-contributor) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Ga](./workbooks-overview.md#visualizations) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.