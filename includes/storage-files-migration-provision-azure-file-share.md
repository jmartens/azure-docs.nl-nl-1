---
title: Overwegingen voor het inrichten van Azure-bestands shares
description: Een Azure-bestands share inrichten voor gebruik met Azure File Sync. Een gemeen schappelijk tekst blok, gedeeld via migratie documenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d48baba5ee60a2bf5a4cb5e4d1ce840fce8eec43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143568"
---
Een Azure-bestands share wordt opgeslagen in de cloud in een Azure-opslag account.
Hier volgt een andere mate van prestatie overwegingen.

Als u over zeer actieve shares beschikt (shares die worden gebruikt door veel gebruikers en/of toepassingen), kunnen twee Azure-bestands shares de prestatie limiet van een opslag account bereiken.

Een best practice is het implementeren van opslag accounts met één bestands share.
U kunt meerdere Azure-bestands shares in hetzelfde opslag account poolen, voor het geval u archiverings shares hebt of u verwacht een goedkope activiteit.

Deze overwegingen zijn van toepassing op directe Cloud toegang (via een Azure VM) dan Azure File Sync. Als u van plan bent om Azure File Sync alleen op deze shares te gebruiken, kunt u de groepering van meerdere accounts in één Azure-opslag account prima.

Als u een lijst met shares hebt gemaakt, moet u elke share toewijzen aan het opslag account waarin deze zich bevindt.

In de vorige fase hebt u het juiste aantal shares bepaald. In deze stap hebt u een toewijzing van opslag accounts gemaakt aan bestands shares. Implementeer nu het juiste aantal Azure-opslag accounts met het juiste aantal Azure-bestands shares.

Zorg ervoor dat de regio van elk van uw opslag accounts hetzelfde is en overeenkomt met de regio van de opslag synchronisatie service resource die u al hebt geïmplementeerd.

> [!CAUTION]
> Als u een Azure-bestands share maakt met een limiet van 100 TiB, kan die share alleen lokaal redundante opslag of zone-redundante opslag redundantie opties gebruiken. Houd rekening met de vereisten voor opslag redundantie voordat u 100-TiB-bestands shares gebruikt.

Azure-bestands shares worden nog steeds met een limiet van 5 TiB standaard gemaakt. Omdat u nieuwe opslag accounts maakt, moet u de [richt lijnen volgen voor het maken van opslag accounts waarmee Azure-bestands shares met 100-TIB-limieten zijn toegestaan](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Een andere overweging bij het implementeren van een opslag account is de redundantie van Azure Storage. Zie [Azure Storage redundantie opties](../articles/storage/common/storage-redundancy.md).

De namen van uw resources zijn ook belang rijk. Als u bijvoorbeeld meerdere shares voor de HR-afdeling in een Azure-opslag account groepeert, moet u het opslag account op de juiste wijze benoemen. Wanneer u uw Azure-bestands shares een naam geeft, moet u ook namen gebruiken die vergelijkbaar zijn met die van hun on-premises equivalenten.