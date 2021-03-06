---
title: Een virtuele Azure-machine labelen met behulp van de CLI
description: Meer informatie over het coderen van een virtuele machine met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 48f906bf0025bda03df226f32db1a0d6afdb9cee
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595023"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>Een virtuele machine labelen met behulp van de CLI

In dit artikel wordt beschreven hoe u een virtuele machine kunt labelen met behulp van de Azure CLI. Tags zijn door de gebruiker gedefinieerde sleutel/waarde-paren die rechtstreeks kunnen worden geplaatst op een resource of resource groep. Azure ondersteunt momenteel Maxi maal 50 Tags per resource en resource groep. Labels kunnen worden geplaatst op een resource op het moment dat ze worden gemaakt of worden toegevoegd aan een bestaande resource. U kunt ook een virtuele machine labelen met behulp van Azure [Power shell](tag-powershell.md).


U kunt alle eigenschappen van een bepaalde virtuele machine weer geven, inclusief de tags, met behulp van `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Als u een nieuwe VM-tag wilt toevoegen via de Azure CLI, kunt u de `azure vm update` opdracht gebruiken en de tag para meter `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Als u tags wilt verwijderen, kunt u de `--remove` para meter in de `azure vm update` opdracht gebruiken.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Nu we tags hebben toegepast op onze resources Azure CLI en de portal, gaan we de gebruiks gegevens bekijken om de tags in de facturerings portal te bekijken.


**Volgende stappen**

- Zie [Azure Resource Manager Overview](../azure-resource-manager/management/overview.md) en [Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)voor meer informatie over het coderen van uw Azure-resources.
- Als u wilt zien hoe Tags u kunnen helpen bij het beheren van uw gebruik van Azure-resources, raadpleegt u [inzicht in uw Azure-factuur](../cost-management-billing/understand/review-individual-bill.md) en [krijgt u inzicht in uw Microsoft Azure Resource verbruik](../cost-management-billing/manage/usage-rate-card-overview.md).
