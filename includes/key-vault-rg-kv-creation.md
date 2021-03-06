---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: ccb69b4f0dc6090cc96849b09f7830c05aa77b73
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027034"
---
In deze quickstart wordt gebruikgemaakt van een vooraf gemaakte Azure-sleutelkluis. U kunt een sleutelkluis maken met behulp van de stappen in de [quickstart voor Azure CLI](../articles/key-vault/general/quick-create-cli.md), de [quickstart voor Azure PowerShell](../articles/key-vault/general/quick-create-powershell.md) of de [quickstart voor de Azure-portal](../articles/key-vault/general/quick-create-portal.md). 

U kunt ook de onderstaande Azure CLI- of Azure PowerShell-opdrachten uitvoeren.

> [!Important]
> Elke sleutelkluis moet een unieke naam hebben. Vervang <your-unique-keyvault-name> door de naam van uw sleutelkluis in de volgende voorbeelden.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```