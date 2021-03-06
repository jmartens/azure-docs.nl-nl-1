---
title: Systeem vereisten voor Microsoft Azure Data Box | Microsoft Docs
description: Meer informatie over belang rijke systeem vereisten voor uw Azure Data Box en voor de clients die verbinding maken met de Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/02/2020
ms.author: alkohli
ms.openlocfilehash: 5f1623ef4dde59e816e3afe5a5f5894c49469580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767862"
---
# <a name="azure-data-box-system-requirements"></a>Systeem vereisten voor Azure Data Box

In dit artikel worden de belangrijkste systeem vereisten beschreven voor uw Microsoft Azure Data Box en voor clients die verbinding maken met de Data Box. We raden u aan de informatie zorgvuldig te bekijken voordat u uw Data Box implementeert en raadpleegt u deze indien nodig tijdens de implementatie en de werking.

De systeem vereisten zijn onder andere:

* **Software vereisten:** Voor hosts die verbinding maken met de Data Box, worden ondersteunde besturings systemen, protocollen voor bestands overdracht, opslag accounts, opslag typen en browsers voor de lokale webgebruikersinterface beschreven.
* **Netwerk vereisten:** Voor de Data Box worden de netwerk verbindings-en poort vereisten beschreven voor een optimale werking van de Data Box.


## <a name="software-requirements"></a>Softwarevereisten

De software vereisten zijn onder andere ondersteunde besturings systemen, protocollen voor bestands overdracht, opslag accounts, opslag typen en browsers voor de lokale webgebruikersinterface.

### <a name="supported-operating-systems-for-clients"></a>Ondersteunde besturingssystemen voor clients

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Ondersteunde protocollen voor bestands overdracht voor clients

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> Verbinding met Data Box shares wordt niet ondersteund via REST voor export orders. 

### <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Ondersteunde opslagtypen

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Ondersteunde webbrowsers

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Netwerk vereisten

Uw datacenter moet een netwerk met hoge snelheid hebben. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10 GbE-verbinding beschikbaar is, kan een 1 GbE-gegevens koppeling worden gebruikt voor het kopiëren van gegevens, maar de Kopieer snelheden worden beïnvloed.

### <a name="port-requirements"></a>Poortvereisten

De volgende tabel geeft een lijst van de poorten die in uw firewall moeten worden geopend om SMB-of NFS-verkeer toe te staan. In deze tabel verwijst *(* *Inkomend*) naar de richting waarin de inkomende client toegang tot uw apparaat vraagt. *Uit* (of *uitgaand*) verwijst naar de richting waarin uw data Box apparaat gegevens extern verzendt, naast de implementatie: bijvoorbeeld uitgaand naar Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Volgende stappen

* [Uw Azure Data Box implementeren](data-box-deploy-ordered.md)
