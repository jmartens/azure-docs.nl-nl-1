---
title: 'Zelfstudie: een app verwijderen die wordt uitgevoerd in Azure Service Fabric Mesh'
description: In deze zelfstudie leert u hoe u een toepassing kunt verwijderen die wordt uitgevoerd in Service Fabric Mesh en hoe u resources kunt verwijderen.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 30a43bdaf9e0c6c56e4d75597ff3d80b77314677
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744333"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Zelfstudie: Een toepassing en resources verwijderen

Deze zelfstudie is deel vier een serie. U leert hoe u een actieve toepassing verwijdert die [eerder in Service Fabric Mesh was geïmplementeerd](service-fabric-mesh-tutorial-template-deploy-app.md). 

In deel vier van de serie leert u het volgende:

> [!div class="checklist"]
> * Een app verwijderen die wordt uitgevoerd in Service Fabric Mesh
> * De toepassingsresources verwijderen

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een toepassing in Service Fabric Mesh implementeren met behulp van een sjabloon](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Een toepassing schalen die wordt uitgevoerd in Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Een toepassing upgraden die wordt uitgevoerd in Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * Een toepassing verwijderen

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, kunt u een [gratis account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

* Open [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) of [installeer de Azure CLI en Azure Service Fabric Mesh CLI lokaal](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="delete-the-resource-group-and-all-the-resources"></a>De resourcegroep en alle resources verwijderen

Wanneer u de resources die u hebt gemaakt niet langer nodig hebt, kunt u deze verwijderen. Eerder hebt u [een nieuwe resourcegroep gemaakt](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry) voor het hosten van het Azure Container Registry-exemplaar en de Service Fabric Mesh-toepassingsresources.  U kunt deze resourcegroep verwijderen. Hiermee worden alle gekoppelde resources verwijderd.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>De resources afzonderlijk verwijderen
U kunt het ACR-exemplaar, de Service Fabric Mesh-toepassing en de netwerkbronnen ook afzonderlijk verwijderen.

ACR-exemplaar verwijderen:

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Ga als volgt te werk als u de Service Fabric-toepassing wilt verwijderen:

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

Netwerk verwijderen:
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een app verwijderen die wordt uitgevoerd in Service Fabric Mesh
> * De toepassingsresources verwijderen
