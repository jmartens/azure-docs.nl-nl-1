---
title: Cluster configuratie in azure Kubernetes Services (AKS)
description: Meer informatie over het configureren van een cluster in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: ab9e2a5483f0699ad7bfca991539025adff34b11
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606909"
---
# <a name="configure-an-aks-cluster"></a>Een AKS-cluster configureren

Als onderdeel van het maken van een AKS-cluster moet u mogelijk uw cluster configuratie aanpassen aan uw behoeften. Dit artikel bevat een aantal opties voor het aanpassen van uw AKS-cluster.

## <a name="os-configuration"></a>Configuratie van besturings systeem

AKS ondersteunt nu Ubuntu 18,04 als het besturings systeem van het knoop punt (OS) in algemene Beschik baarheid voor clusters in kubernetes-versies hoger dan 1.18.8. Voor versies onder 1.18. x is AKS Ubuntu 16,04 nog steeds de standaard basis installatie kopie. Van kubernetes v 1.18. x en later is de standaard basis AKS Ubuntu 18,04.

> [!IMPORTANT]
> Knooppunt groepen die zijn gemaakt op Kubernetes v 1.18 of meer standaard naar `AKS Ubuntu 18.04` knooppunt installatie kopie. Knooppunt Pools op een ondersteunde Kubernetes-versie kleiner dan 1,18 `AKS Ubuntu 16.04` worden als de knooppunt afbeelding ontvangen, maar worden bijgewerkt naar een moment dat `AKS Ubuntu 18.04` de Kubernetes-versie van de knooppunt groep wordt bijgewerkt naar v 1.18 of hoger.
> 
> Het wordt sterk aanbevolen om uw workloads te testen op AKS Ubuntu 18,04-knooppunt Pools voordat u clusters op 1,18 of hoger gebruikt. Meer informatie over het [testen van Ubuntu 18,04-knooppunt groepen](#use-aks-ubuntu-1804-existing-clusters-preview).

In het volgende gedeelte wordt uitgelegd hoe u AKS Ubuntu 18,04 gebruikt en test op clusters die nog geen kubernetes-versie 1.18. x of hoger gebruiken, of die zijn gemaakt voordat deze functie algemeen beschikbaar werd, met behulp van de configuratie voorbeeld van het besturings systeem.

U moet de volgende resources hebben geïnstalleerd:

- [De Azure cli][azure-cli-install], versie 2.2.0 of hoger
- De 0.4.35-uitbrei ding AKS-preview

Gebruik de volgende Azure CLI-opdrachten om de 0.4.35-uitbrei ding AKS-preview of hoger te installeren:

```azurecli
az extension add --name aks-preview
az extension list
```

De `UseCustomizedUbuntuPreview` functie registreren:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status als **geregistreerd** wordt weer gegeven. U kunt de registratiestatus controleren met behulp van de opdracht [az feature list](/cli/azure/feature#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Wanneer de status wordt weer gegeven als geregistreerd, vernieuwt u de registratie van de `Microsoft.ContainerService` resource provider met behulp van de opdracht [AZ provider REGI ster](/cli/azure/provider#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>AKS Ubuntu 18,04 gebruiken voor nieuwe clusters (preview-versie)

Configureer het cluster voor het gebruik van Ubuntu 18,04 wanneer het cluster wordt gemaakt. Gebruik de `--aks-custom-headers` markering om Ubuntu 18,04 in te stellen als het standaard besturingssysteem.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Als u clusters wilt maken met de AKS Ubuntu 16,04-installatie kopie, kunt u dit doen door de aangepaste tag weg te laten `--aks-custom-headers` .

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>AKS Ubuntu 18,04-bestaande clusters gebruiken (preview-versie)

Configureer een nieuwe knooppunt groep om Ubuntu 18,04 te gebruiken. Gebruik de `--aks-custom-headers` markering om Ubuntu 18,04 in te stellen als het standaard besturings systeem voor die knooppunt groep.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Als u knooppunt Pools met de AKS Ubuntu 16,04-installatie kopie wilt maken, kunt u dit doen door de aangepaste tag weg te laten `--aks-custom-headers` .

## <a name="container-runtime-configuration"></a>Container-runtime configuratie

Een container-runtime is software die containers uitvoert en container installatie kopieën beheert op een knoop punt. De runtime helpt bij het samen vatting van de specifieke functionaliteit van het besturings systeem (OS) voor het uitvoeren van containers in Linux of Windows. AKS-clusters met Kubernetes versie 1,19-knooppunt groepen en meer gebruiken `containerd` als container runtime. AKS-clusters die gebruikmaken van Kubernetes vóór v 1.19 voor knooppunt groepen gebruiken [Moby](https://mobyproject.org/) (upstream docker) als container runtime.

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) is een [OCI](https://opencontainers.org/) (open container Initiative) compatibele kern container-runtime die de minimale set vereiste functionaliteit biedt om containers uit te voeren en installatie kopieën op een knoop punt te beheren. Het is [gedoneerd](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) aan de Cloud systeem eigen Compute Foundation (CNCF) in maart 2017. De huidige Moby-versie die AKS gebruikt, is al in gebruik en is gebaseerd op `containerd` , zoals hierboven wordt weer gegeven.

Met een `containerd` knoop punt-en knooppunt groep, in plaats van te praten met de `dockershim` , wordt de kubelet `containerd` via de invoeg toepassing cri (container runtime-Interface) rechtstreeks gecommuniceerd, waarbij extra hops in de stroom worden verwijderd in vergelijking met de docker cri-implementatie. Als zodanig ziet u hoe beter pod opstart latentie en minder bronnen (CPU en geheugen) worden gebruikt.

Door te gebruiken `containerd` voor AKS-knoop punten, verbetert de opstart latentie van Pod en wordt het gebruik van knooppunt bronnen door de container runtime afgenomen. Deze verbeteringen worden door deze nieuwe architectuur ingeschakeld, waarbij kubelet rechtstreeks naar de `containerd` cri-invoeg toepassing praat terwijl de Moby/docker-architectuur kubelet de `dockershim` en de docker-Engine zonder problemen zou kunnen praten voordat `containerd` ze bereiken, waardoor er extra hops in de stroom zijn.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` werkt bij elke GA-versie van Kubernetes in AKS en in elke upstream-versie van Kubernetes hoger dan v 1.19, en biedt ondersteuning voor alle Kubernetes-en AKS-functies.

> [!IMPORTANT]
> Clusters met knooppunt groepen die zijn gemaakt op Kubernetes v 1.19 of meer standaard `containerd` voor de container runtime. Clusters met knooppunt Pools op een ondersteunde Kubernetes-versie lager dan 1,19 `Moby` worden ontvangen voor de container runtime, maar worden bijgewerkt naar een moment dat `ContainerD` de Kubernetes-versie van de knooppunt groep wordt bijgewerkt naar v 1.19 of hoger. U kunt nog steeds gebruikmaken `Moby` van knooppunt Pools en clusters op oudere ondersteunde versies totdat deze ondersteuning bieden.
> 
> Het wordt sterk aanbevolen om uw workloads te testen op AKS-knooppunt Pools met `containerD` voordat u clusters op 1,19 of hoger gebruikt.

In de volgende sectie wordt uitgelegd hoe u AKS kunt gebruiken en testen met `containerD` clusters die nog geen Kubernetes-versie 1,19 of hoger gebruiken, of die zijn gemaakt voordat deze functie algemeen beschikbaar werd, door gebruik te maken van de voor beeld-runtime configuratie van de container.

### <a name="use-containerd-as-your-container-runtime-preview"></a>Gebruiken `containerd` als uw container runtime (preview-versie)

U moet over de volgende vereisten beschikken:

- [De Azure cli][azure-cli-install], versie 2.8.0 of hoger geïnstalleerd
- De AKS-preview-extensie versie 0.4.53 of hoger
- De `UseCustomizedContainerRuntime` functie vlag is geregistreerd
- De `UseCustomizedUbuntuPreview` functie vlag is geregistreerd

Gebruik de volgende Azure CLI-opdrachten om de 0.4.53-uitbrei ding AKS-preview of hoger te installeren:

```azurecli
az extension add --name aks-preview
az extension list
```

De `UseCustomizedContainerRuntime` functies en registreren `UseCustomizedUbuntuPreview` :

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

Het kan enkele minuten duren voordat de status als **geregistreerd** wordt weer gegeven. U kunt de registratiestatus controleren met behulp van de opdracht [az feature list](/cli/azure/feature#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Wanneer de status wordt weer gegeven als geregistreerd, vernieuwt u de registratie van de `Microsoft.ContainerService` resource provider met behulp van de opdracht [AZ provider REGI ster](/cli/azure/provider#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>Gebruiken `containerd` op nieuwe clusters (preview-versie)

Configureer het cluster dat moet worden gebruikt `containerd` wanneer het cluster wordt gemaakt. Gebruik de `--aks-custom-headers` vlag om in te stellen `containerd` als container runtime.

> [!NOTE]
> De `containerd` runtime wordt alleen ondersteund voor knoop punten en knooppunt groepen met behulp van de AKS Ubuntu 18,04-installatie kopie.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Als u clusters met de Moby (docker)-runtime wilt maken, kunt u dit doen door de aangepaste tag weg te laten `--aks-custom-headers` .

### <a name="use-containerd-on-existing-clusters-preview"></a>Gebruiken `containerd` op bestaande clusters (preview-versie)

Configureer een nieuwe knooppunt groep die moet worden gebruikt `containerd` . Gebruik de `--aks-custom-headers` vlag om in te stellen `containerd` als runtime voor die knooppunt groep.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Als u knooppunt Pools met de Moby (docker)-runtime wilt maken, kunt u dit doen door de aangepaste tag weg te laten `--aks-custom-headers` .


### <a name="containerd-limitationsdifferences"></a>`Containerd` beperkingen/verschillen

* Als `containerd` u de container runtime wilt gebruiken, moet u AKS Ubuntu 18,04 gebruiken als basis installatie kopie van het besturings systeem.
* Terwijl de docker-werkset nog steeds aanwezig is op de knoop punten, wordt Kubernetes gebruikt `containerd` als container runtime. Omdat Moby/docker de door Kubernetes gemaakte containers niet op de knoop punten beheert, kunt u uw containers daarom niet weer geven of gebruiken met behulp van docker-opdrachten (zoals `docker ps` ) of de docker-API.
* Voor `containerd` kunt u het beste [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) als vervangings-CLI gebruiken in plaats van de docker-CLI, voor het **oplossen** van een Peul, containers en container installatie kopieën op Kubernetes-knoop punten (bijvoorbeeld `crictl ps` ). 
   * Het biedt geen volledige functionaliteit van de docker-CLI. Het is alleen bedoeld voor het oplossen van problemen.
   * `crictl` biedt een meer kubernetes gebruiks vriendelijke weer gave van containers, met concepten zoals peulen, enzovoort.
* `Containerd` Hiermee stelt u logboek registratie in met de gestandaardiseerde `cri` logboek indeling (die verschilt van wat u momenteel van het JSON-stuur programma van de docker haalt). Uw logboek registratie oplossing moet ondersteuning bieden voor de `cri` indeling voor logboek registratie (zoals [Azure monitor voor containers](../azure-monitor/insights/container-insights-enable-new-cluster.md))
* U hebt geen toegang meer tot de docker-engine, of u kunt `/var/run/docker.sock` docker-in-docker (DinD) niet meer gebruiken.
  * Als u momenteel toepassings Logboeken of bewakings gegevens van de docker-engine uitpakt, kunt u in plaats daarvan iets gebruiken als [Azure monitor voor containers](../azure-monitor/insights/container-insights-enable-new-cluster.md) . Daarnaast biedt AKS geen ondersteuning voor het uitvoeren van out-of-band-opdrachten op de agent knooppunten die instabiliteit kunnen veroorzaken.
  * Zelfs bij gebruik van Moby/docker, het bouwen van installatie kopieën en rechtstreeks gebruikmaken van de docker-engine via de bovenstaande methoden, wordt sterk afgeraden. Kubernetes is niet volledig op de hoogte van deze verbruikte resources en deze benaderingen presen teren hier talloze problemen die [hier](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) [worden beschreven, bijvoorbeeld.](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)
* Installatie kopieën maken: u kunt uw huidige docker-werk stroom gewoon blijven gebruiken, tenzij u imagages in uw AKS-cluster bouwt. In dit geval kunt u het beste overschakelen naar de aanbevolen benadering voor het bouwen van installatie kopieën met [ACR-taken](../container-registry/container-registry-quickstart-task-cli.md)of een veiligere optie in het cluster, zoals [docker buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines-preview"></a>Virtuele machines van de 2e generatie (preview-versie)

Azure biedt ondersteuning voor [generatie 2 (Gen2) virtuele machines (vm's)](../virtual-machines/generation-2.md). Vm's van generatie 2 ondersteunen belang rijke functies die niet worden ondersteund in virtuele machines van de eerste generatie (gen1). Tot deze functies behoren meer geheugen, Intel-software Guard Extensions (Intel SGX) en gevirtualiseerde permanent geheugen (vPMEM).

Vm's van generatie 2 gebruiken de nieuwe op UEFI gebaseerde opstart architectuur in plaats van de op BIOS gebaseerde architectuur die wordt gebruikt door virtuele machines van de eerste generatie.
Alleen specifieke Sku's en grootten bieden ondersteuning voor Gen2-Vm's. Controleer de [lijst met ondersteunde grootten](../virtual-machines/generation-2.md#generation-2-vm-sizes)om te zien of uw SKU Gen2 ondersteunt of vereist.

Niet alle VM-installatie kopieën ondersteunen Gen2, op AKS Gen2-Vm's wordt de nieuwe [AKS Ubuntu 18,04-installatie kopie](#os-configuration)gebruikt. Deze installatie kopie ondersteunt alle Gen2 Sku's en grootten.

Als u Gen2-Vm's wilt gebruiken tijdens de preview, hebt u het volgende nodig:
- De `aks-preview` cli-extensie is geïnstalleerd.
- De `Gen2VMPreview` functie vlag is geregistreerd.

De `Gen2VMPreview` functie registreren:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status als **geregistreerd** wordt weer gegeven. U kunt de registratiestatus controleren met behulp van de opdracht [az feature list](/cli/azure/feature#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Wanneer de status wordt weer gegeven als geregistreerd, vernieuwt u de registratie van de `Microsoft.ContainerService` resource provider met behulp van de opdracht [AZ provider REGI ster](/cli/azure/provider#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Als u de AKS-preview CLI-extensie wilt installeren, gebruikt u de volgende Azure CLI-opdrachten:

```azurecli
az extension add --name aks-preview
```

Gebruik de volgende Azure CLI-opdrachten om de CLI-extensie AKS-preview te installeren:

```azurecli
az extension update --name aks-preview
```

### <a name="use-gen2-vms-on-new-clusters-preview"></a>Gen2 Vm's gebruiken in nieuwe clusters (preview-versie)
Configureer het cluster voor het gebruik van Gen2 Vm's voor de geselecteerde SKU wanneer het cluster wordt gemaakt. Gebruik de `--aks-custom-headers` vlag om Gen2 in te stellen als de VM-generatie op een nieuw cluster.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Als u een normaal cluster wilt maken met virtuele machines van de eerste generatie (gen1), kunt u dit doen door de aangepaste tag weg te laten `--aks-custom-headers` . U kunt er ook voor kiezen om meer gen1-of Gen2-Vm's toe te voegen.

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>Gen2 Vm's gebruiken op bestaande clusters (preview-versie)
Configureer een nieuwe knooppunt groep om Gen2 Vm's te gebruiken. Gebruik de `--aks-custom-headers` vlag om Gen2 in te stellen als de VM-generatie voor die knooppunt groep.

```azurecli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Als u reguliere gen1-knooppunt groepen wilt maken, kunt u dit doen door de aangepaste tag weg te laten `--aks-custom-headers` .


## <a name="ephemeral-os"></a>Kortstondig besturings systeem

Standaard repliceert Azure de besturingssysteem schijf voor een virtuele machine automatisch naar Azure-opslag om gegevens verlies te voor komen, omdat de VM naar een andere host moet worden verplaatst. Omdat containers echter niet zijn ontworpen om de lokale status persistent te maken, biedt dit gedrag een beperkte waarde bij een aantal nadelen, waaronder het langzamer inrichten van knoop punten en een hogere latentie bij lees/schrijf bewerkingen.

Tijdelijke besturingssysteem schijven worden daarentegen alleen op de hostcomputer opgeslagen, net als een tijdelijke schijf. Dit biedt een lagere latentie voor lezen/schrijven, met snellere knooppunt schaling en cluster upgrades.

Net als de tijdelijke schijf wordt een tijdelijke besturingssysteem schijf opgenomen in de prijs van de virtuele machine, zodat u geen extra opslag kosten in rekening brengt.

> [!IMPORTANT]
>Wanneer een gebruiker niet expliciet beheerde schijven voor het besturings systeem aanvraagt, wordt AKS standaard ingesteld op tijdelijk besturings systeem, indien mogelijk voor een bepaalde nodepool-configuratie.

Wanneer u het tijdelijke besturings systeem gebruikt, moet de besturingssysteem schijf passen in de cache van de virtuele machine. De grootte van de VM-cache is tussen haakjes naast i/o-door Voer (' cache grootte in GiB ') beschikbaar in de [Azure-documentatie](../virtual-machines/dv3-dsv3-series.md) .

Met de AKS standaard grootte van virtuele machines Standard_DS2_v2 met de standaard besturingssysteem grootte van 100 GB als voor beeld, ondersteunt deze VM-grootte tijdelijke besturings systemen, maar heeft alleen 86GB van de cache grootte. Deze configuratie wordt standaard ingesteld op het beheer van schijven als de gebruiker niet expliciet opgeeft. Als een gebruiker het tijdelijke besturings systeem expliciet heeft aangevraagd, ontvangt deze een validatie fout.

Als een gebruiker hetzelfde Standard_DS2_v2 aanvraagt met een schijf van 60 GB, wordt deze configuratie standaard ingesteld op kortstondige besturings systemen: de aangevraagde grootte van 60 GB is kleiner dan de maximale cache grootte van 86GB.

Met behulp van Standard_D8s_v3 met een besturingssysteem schijf van 100 GB ondersteunt deze VM-grootte tijdelijke besturings systemen en heeft dit 200 GB aan cache ruimte. Als een gebruiker niet het schijf type van het besturings systeem opgeeft, ontvangt de nodepool standaard tijdelijk besturings systeem. 

Voor het tijdelijke besturings systeem is ten minste versie 2.15.0 van de Azure CLI vereist.

### <a name="use-ephemeral-os-on-new-clusters"></a>Kortstondige besturings systeem op nieuwe clusters gebruiken

Configureer het cluster voor het gebruik van tijdelijke besturingssysteem schijven wanneer het cluster wordt gemaakt. Gebruik de `--node-osdisk-type` markering om kortstondig besturings systeem in te stellen als het schijf type van het besturings systeem voor het nieuwe cluster.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Als u een normaal cluster wilt maken met behulp van besturingssysteem schijven die zijn gekoppeld aan het netwerk, kunt u dit doen door op te geven `--node-osdisk-type=Managed` . U kunt er ook voor kiezen om meer tijdelijke OS-knooppunt groepen toe te voegen aan de hand van hieronder.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Kortstondige besturings systeem op bestaande clusters gebruiken
Configureer een nieuwe knooppunt groep om tijdelijke besturingssysteem schijven te gebruiken. Gebruik de `--node-osdisk-type` vlag om in te stellen als het schijf type van het besturings systeem voor die knooppunt groep.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Met het tijdelijke besturings systeem kunt u installatie kopieën van VM'S en instanties implementeren tot aan de grootte van de VM-cache. In het geval van AKS gebruikt de standaard schijf configuratie van het knoop punt 128 GB, wat betekent dat u een VM-grootte nodig hebt die een cache heeft die groter is dan 128 GB. De standaard Standard_DS2_v2 heeft een cache grootte van 86GB, die niet groot genoeg is. De Standard_DS3_v2 heeft een cache grootte van 172GB, die groot genoeg is. U kunt ook de standaard grootte van de besturingssysteem schijf verminderen met behulp van `--node-osdisk-size` . De minimale grootte voor AKS-installatie kopieën is 30 GB. 

Als u knooppunt groepen met door het netwerk gekoppelde besturingssysteem schijven wilt maken, kunt u dit doen door op te geven `--node-osdisk-type Managed` .

## <a name="custom-resource-group-name"></a>Aangepaste naam van resource groep

Wanneer u een Azure Kubernetes-service cluster in azure implementeert, wordt er een tweede resource groep voor de worker-knoop punten gemaakt. AKS krijgt standaard de naam van de resource groep `MC_resourcegroupname_clustername_location` , maar u kunt ook uw eigen naam opgeven.

Als u de naam van uw eigen resource groep wilt opgeven, installeert u de AKS-preview Azure CLI-extensie versie 0.3.2 of hoger. Gebruik de Azure CLI `--node-resource-group` om de para meter van de `az aks create` opdracht te gebruiken om een aangepaste naam voor de resource groep op te geven. Als u een Azure Resource Manager-sjabloon gebruikt om een AKS-cluster te implementeren, kunt u de naam van de resource groep definiëren met behulp van de `nodeResourceGroup` eigenschap.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

De secundaire resource groep wordt automatisch gemaakt door de Azure-resource provider in uw eigen abonnement. U kunt alleen de naam van de aangepaste resource groep opgeven wanneer het cluster wordt gemaakt. 

Houd er bij het werken met de knooppunt resource groep voor dat u niet:

- Geef een bestaande resource groep op voor de knooppunt resource groep.
- Geef een ander abonnement op voor de knooppunt resource groep.
- Wijzig de naam van de resource groep van het knoop punt nadat het cluster is gemaakt.
- Geef namen voor de beheerde resources op in de resource groep van het knoop punt.
- Door Azure gemaakte Tags van beheerde resources wijzigen of verwijderen in de resource groep van het knoop punt.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het upgraden van de knooppunt installatie kopieën](node-image-upgrade.md) in uw cluster.
- Zie [een Azure Kubernetes service-cluster (AKS) bijwerken](upgrade-cluster.md) voor meer informatie over het upgraden van uw cluster naar de nieuwste versie van Kubernetes.
- Meer informatie over [ `containerd` en Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Raadpleeg de lijst met [Veelgestelde vragen over AKS](faq.md) om antwoorden te vinden op enkele veelgestelde vragen over AKS.
- Meer informatie over [tijdelijke OS-schijven](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
