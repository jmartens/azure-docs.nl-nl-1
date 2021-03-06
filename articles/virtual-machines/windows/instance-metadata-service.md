---
title: Azure-Instance Metadata Service voor Windows
description: Meer informatie over de Azure Instance Metadata Service en hoe deze informatie biedt over actieve exemplaren van virtuele machines in Windows.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: cc0d47807101a3cfbb26e7ea69cc7d117d3f9b31
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435226"
---
# <a name="azure-instance-metadata-service"></a>Azire Instance Metadata Service

De Azure Instance Metadata Service (IMDS) bevat informatie over actieve exemplaren van virtuele machines. U kunt deze gebruiken om uw virtuele machines te beheren en te configureren.
Deze informatie omvat de gebeurtenissen SKU, opslag, netwerk configuraties en gepland onderhoud. Zie [meta data api's](#metadata-apis)voor een volledige lijst met beschik bare gegevens.


IMDS is beschikbaar voor het uitvoeren van exemplaren van virtuele machines (Vm's) en instanties van virtuele-machine schaal sets. Alle Api's ondersteunen Vm's die zijn gemaakt en beheerd met behulp van [Azure Resource Manager](/rest/api/resources/). Alleen de attesten en netwerk eindpunten ondersteunen Vm's die zijn gemaakt met behulp van het klassieke implementatie model. Het attested-eind punt heeft daarom slechts een beperkt gebied.

IMDS is een REST-eind punt dat beschikbaar is met een bekend, niet-routeerbaar IP-adres ( `169.254.169.254` ). U hebt alleen toegang tot de app vanuit de virtuele machine. De communicatie tussen de virtuele machine en de IMDS verlaat nooit de host.
Laat uw HTTP-clients Web-proxy's in de virtuele machine omzeilen tijdens het uitvoeren van een query op IMDS, en behandel `169.254.169.254` hetzelfde als [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Beveiliging

Het IMDS-eind punt is alleen toegankelijk vanuit het actieve exemplaar van de virtuele machine op een niet-routeerbaar IP-adres. Daarnaast wordt elke aanvraag met een `X-Forwarded-For` header afgewezen door de service.
Aanvragen moeten ook een `Metadata: true` kop bevatten om ervoor te zorgen dat de daad werkelijke aanvraag rechtstreeks is bedoeld en geen onderdeel van onbedoelde omleiding.

> [!IMPORTANT]
> IMDS is geen kanaal voor gevoelige gegevens. Het eind punt is geopend voor alle processen op de virtuele machine. Denk na over de informatie die door deze service wordt weer gegeven als gedeelde informatie voor alle toepassingen die in de virtuele machine worden uitgevoerd.

## <a name="usage"></a>Gebruik

### <a name="access-azure-instance-metadata-service"></a>Toegang tot Azure Instance Metadata Service

Om toegang te krijgen tot IMDS, maakt u een virtuele machine op basis van [Azure Resource Manager](/rest/api/resources/) of de [Azure Portal](https://portal.azure.com)en gebruikt u de volgende voor beelden.
Zie voor meer voor beelden [Azure instance meta data samples](https://github.com/microsoft/azureimds).

Hier volgt de voorbeeld code voor het ophalen van alle meta gegevens voor een exemplaar. Zie de sectie [meta gegevens-API](#metadata-apis) voor toegang tot een specifieke gegevens bron. 

**Aanvraag**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-09-01 | ConvertTo-Json
```
> [!NOTE]
> De `-NoProxy` vlag is alleen beschikbaar in Power shell 6 of hoger. U kunt de vlag weglaten als u geen proxy-instellingen hebt ingesteld.

**Response**

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Pipet your REST-query door de `ConvertTo-Json` cmdlet voor een mooie afdruk.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
            "imageReference": {
                "id": "",
                "offer": "UbuntuServer",
                "publisher": "Canonical",
                "sku": "16.04.0-LTS",
                "version": "latest"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "createOption": "FromImage",
                "diskSizeGB": "30",
                "diffDiskSettings": {
                    "option": "Local"
                },
                "encryptionSettings": {
                    "enabled": "false"
                },
                "image": {
                    "uri": ""
                },
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampleosdiskname",
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
            "ipv4": {
               "ipAddress": [{
                    "privateIpAddress": "10.144.133.132",
                    "publicIpAddress": ""
                }],
                "subnet": [{
                    "address": "10.144.133.128",
                    "prefix": "26"
                }]
            },
            "ipv6": {
                "ipAddress": [
                 ]
            },
            "macAddress": "0011AAFFBB22"
        }]
    }
}
```

### <a name="data-output"></a>Gegevens uitvoer

IMDS retourneert standaard gegevens in JSON-indeling ( `Content-Type: application/json` ). Sommige Api's kunnen echter gegevens in verschillende indelingen retour neren, indien nodig.
De volgende tabel bevat een lijst met andere gegevens indelingen die door Api's mogelijk worden ondersteund.

API | Standaard gegevens indeling | Andere indelingen
--------|---------------------|--------------
/attested | json | geen
/identity | json | geen
/Instance | json | tekst
/scheduledevents | json | geen

Om toegang te krijgen tot een niet-standaard antwoord indeling, geeft u de aangevraagde indeling op als een query reeks parameter in de aanvraag. Bijvoorbeeld:

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Voor blad knooppunten in `/metadata/instance` werkt het `format=json` niet. Voor deze query's `format=text` moet expliciet worden opgegeven omdat de standaard indeling JSON is.

### <a name="version"></a>Versie

IMDS is Versioning en het opgeven van de API-versie in de HTTP-aanvraag is verplicht.

De ondersteunde API-versies zijn: 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> Versie 2020-10-01 is mogelijk nog niet beschikbaar in elke regio.

Als nieuwere versies worden toegevoegd, hebt u nog steeds toegang tot oudere versies voor compatibiliteit als uw scripts afhankelijk zijn van specifieke gegevens indelingen.

Wanneer u geen versie opgeeft, wordt er een fout bericht weer geven met een lijst met de nieuwste ondersteunde versies.

> [!NOTE]
> Het antwoord is een JSON-teken reeks. In het volgende voor beeld wordt de fout status aangegeven wanneer de versie niet is opgegeven. Het antwoord is tamelijk afgedrukt voor de Lees baarheid.

**Aanvraag**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
```

**Response**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

## <a name="metadata-apis"></a>Api's voor meta gegevens

IMDS bevat meerdere Api's die verschillende gegevens bronnen vertegenwoordigen.

API | Beschrijving | Geïntroduceerde versie
----|-------------|-----------------------
/attested | Zie [attested data](#attested-data) | 2018-10-01
/identity | Zie [een toegangs Token ophalen](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/Instance | Zie [instance API](#instance-api) | 2017-04-02
/scheduledevents | Zie [geplande gebeurtenissen](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>Exemplaar-API

Met instance API worden de belang rijke meta gegevens voor de VM-exemplaren weer gegeven, met inbegrip van de VM, het netwerk en de opslag. U kunt de volgende categorieën openen via `instance/compute` :

Gegevens | Description | Geïntroduceerde versie
-----|-------------|-----------------------
azEnvironment | De Azure-omgeving waarin de virtuele machine wordt uitgevoerd. | 2018-10-01
customData | Deze functie is momenteel uitgeschakeld. | 2019-02-01
isHostCompatibilityLayerVm | Hiermee wordt aangegeven of de virtuele machine wordt uitgevoerd op de compatibiliteit slaag van de host. | 2020-06-01
License type | Het type licentie voor [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Houd er rekening mee dat dit alleen aanwezig is voor virtuele machines met AHB-functionaliteit. | 2020-09-01
location | De Azure-regio waarin de virtuele machine wordt uitgevoerd. | 2017-04-02
naam | De naam van de virtuele machine. | 2017-04-02
offer | Bied informatie aan voor de VM-installatie kopie. Dit is alleen aanwezig voor installatie kopieën die zijn geïmplementeerd in de galerie met installatie kopieën van Azure. | 2017-04-02
osProfile.adminUsername | Hiermee geeft u de naam van het beheerders account. | 2020-07-15
osProfile. ComputerName | Hiermee geeft u de naam van de computer. | 2020-07-15
osProfile. disablePasswordAuthentication | Hiermee wordt aangegeven of wachtwoord verificatie is uitgeschakeld. Houd er rekening mee dat dit alleen aanwezig is voor virtuele Linux-machines. | 2020-10-01
osType | Linux of Windows. | 2017-04-02
placementGroupId | [Plaatsings groep](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) van de schaalset voor de virtuele machine. | 2017-08-01
plannen | [Plan](/rest/api/compute/virtualmachines/createorupdate#plan) met de naam, het product en de uitgever van een virtuele machine als dit een Azure Marketplace-installatie kopie is. | 2018-04-02
platformUpdateDomain |  [Update domein](../manage-availability.md) waarin de virtuele machine wordt uitgevoerd. | 2017-04-02
platformFaultDomain | [Fout domein](../manage-availability.md) waarin de virtuele machine wordt uitgevoerd. | 2017-04-02
providers | De provider van de virtuele machine. | 2018-10-01
publicKeys | [Verzameling van open bare sleutels](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) die zijn toegewezen aan de virtuele machine en paden. | 2018-04-02
publisher | De uitgever van de VM-installatie kopie. | 2017-04-02
resourceGroupName | De [resource groep](../../azure-resource-manager/management/overview.md) voor uw VM. | 2017-08-01
resourceId | De [volledig gekwalificeerde](/rest/api/resources/resources/getbyid) id van de resource. | 2019-03-11
sku | De specifieke SKU voor de VM-installatie kopie. | 2017-04-02
securityProfile. secureBootEnabled | Hiermee wordt aangegeven of UEFI Secure boot is ingeschakeld op de VM. | 2020-06-01
securityProfile.virtualTpmEnabled | Hiermee wordt aangegeven of de virtuele Trusted Platform Module (TPM) is ingeschakeld op de VM. | 2020-06-01
storageProfile | Zie [opslag profiel](#storage-metadata). | 2019-06-01
subscriptionId | Azure-abonnement voor de virtuele machine. | 2017-08-01
tags | [Tags](../../azure-resource-manager/management/tag-resources.md) voor uw VM.  | 2017-08-01
tagsList | Tags die zijn opgemaakt als een JSON-matrix voor eenvoudiger programmatisch parseren.  | 2019-06-04
versie | De versie van de VM-installatie kopie. | 2017-04-02
vmId | De [unieke id](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) voor de virtuele machine. | 2017-04-02
vmScaleSetName | De naam van de [schaalset voor virtuele machines](../../virtual-machine-scale-sets/overview.md) van de schaalset voor virtuele machines. | 2017-12-01
vmSize | Zie [VM-grootte](../sizes.md). | 2017-04-02
zone | [Beschikbaarheids zone](../../availability-zones/az-overview.md) van uw VM. | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>Voor beeld 1: een virtuele machine volgen die wordt uitgevoerd op Azure

Als service provider moet u mogelijk het aantal Vm's waarop uw software wordt uitgevoerd, bijhouden of agents hebben die de uniekheid van de virtuele machine moeten bijhouden. Als u een unieke ID voor een virtuele machine wilt ophalen, gebruikt u het `vmId` veld van IMDS.

**Aanvraag**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Response**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>Voor beeld 2: plaatsing van verschillende gegevens replica's

Voor bepaalde scenario's is het plaatsen van verschillende gegevens replica's van groot belang. Voor de plaatsing van [HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) of containers via een [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) kan het nodig zijn dat u weet dat de `platformFaultDomain` en `platformUpdateDomain` de virtuele machine wordt uitgevoerd.
U kunt [Beschikbaarheidszones](../../availability-zones/az-overview.md) ook gebruiken voor de instanties om deze beslissingen te nemen.
U kunt deze gegevens rechtstreeks opvragen via IMDS.

**Aanvraag**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Response**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>Voor beeld 3: meer informatie over de VM ophalen tijdens de ondersteunings Case

Als service provider kunt u een ondersteunings oproep krijgen waarin u meer informatie over de virtuele machine wilt weten. Als u de klant vraagt om de berekenings-meta gegevens te delen, kan dat in dit geval nuttig zijn.

**Aanvraag**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01
```

**Response**

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Het volgende voor beeld is een mooie afdruk van de Lees baarheid.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>Voor beeld 4: de Azure-omgeving waar de virtuele machine wordt uitgevoerd, ophalen

Azure heeft verschillende soevereine Clouds, zoals [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Soms hebt u de Azure-omgeving nodig om enkele runtime beslissingen te nemen. In het volgende voor beeld ziet u hoe u dit gedrag kunt behaalt.

**Aanvraag**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Response**

```text
AzurePublicCloud
```

De Cloud en de waarden van de Azure-omgeving worden hier weer gegeven.

 Cloud   | Azure-omgeving
---------|-----------------
[Alle algemeen beschik bare wereld wijde Azure-regio's](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Meta gegevens van het netwerk 

De meta gegevens van het netwerk maken deel uit van de exemplaar-API. De volgende netwerk categorieën zijn beschikbaar via het `instance/network` eind punt.

Gegevens | Description | Geïntroduceerde versie
-----|-------------|-----------------------
IPv4-privateIpAddress | Het lokale IPv4-adres van de virtuele machine. | 2017-04-02
IPv4-publicIpAddress | Het open bare IPv4-adres van de virtuele machine. | 2017-04-02
subnet/adres | Het subnetadres van de virtuele machine. | 2017-04-02
subnet/voor voegsel | Het subnetvoorvoegsel. Voor beeld: 24 | 2017-04-02
IPv6/IP-adres | Het lokale IPv6-adres van de virtuele machine. | 2017-04-02
macAddress | Het MAC-adres van de virtuele machine. | 2017-04-02

> [!NOTE]
> Alle API-antwoorden zijn JSON-teken reeksen. Alle volgende voor beelden van antwoorden worden afgedrukt voor de Lees baarheid.

#### <a name="sample-1-retrieve-network-information"></a>Voor beeld 1: netwerk gegevens ophalen

**Aanvraag**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
```

**Response**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="sample-2-retrieve-public-ip-address"></a>Voor beeld 2: een openbaar IP-adres ophalen

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Meta gegevens van opslag

Meta gegevens van de opslag maken deel uit van de exemplaar-API, onder het `instance/compute/storageProfile` eind punt.
Het bevat details over de opslag schijven die aan de virtuele machine zijn gekoppeld. 

Het opslag profiel van een virtuele machine is onderverdeeld in drie categorieën: verwijzing naar installatie kopie, schijf van besturings systeem en gegevens schijven.

Het verwijzings object voor de afbeelding bevat de volgende informatie over de installatie kopie van het besturings systeem:

Gegevens    | Beschrijving
--------|-----------------
id      | Resource-id
offer   | Aanbieding van het platform of de installatie kopie
publisher | Uitgever van installatie kopie
sku     | Afbeeldings-SKU
versie | Versie van het platform of de installatie kopie

Het object schijf van het besturings systeem bevat de volgende informatie over de schijf met het besturings systeem die wordt gebruikt door de virtuele machine:

Gegevens    | Description
--------|-----------------
in | Cache vereisten
createOption | Informatie over de manier waarop de virtuele machine is gemaakt
diffDiskSettings | Tijdelijke schijf instellingen
diskSizeGB | Grootte van de schijf in GB
encryptionSettings | Versleutelings instellingen voor de schijf
image   | Virtuele harde schijf voor installatie kopie van bron gebruiker
managedDisk | Beheerde schijf parameters
naam    | Schijf naam
osType  | Type besturings systeem dat is opgenomen in de schijf
schijven     | Virtuele harde schijf
writeAcceleratorEnabled | Hiermee wordt aangegeven of of niet `writeAccelerator` is ingeschakeld op de schijf

De matrix gegevens schijven bevat een lijst met gegevens schijven die zijn gekoppeld aan de VM. Elk gegevens schijf object bevat de volgende informatie:

Gegevens    | Description
--------|-----------------
in | Cache vereisten
createOption | Informatie over de manier waarop de virtuele machine is gemaakt
diffDiskSettings | Tijdelijke schijf instellingen
diskSizeGB | Grootte van de schijf in GB
image   | Virtuele harde schijf voor installatie kopie van bron gebruiker
LUN     | Nummer van de logische eenheid van de schijf
managedDisk | Beheerde schijf parameters
naam    | Schijf naam
schijven     | Virtuele harde schijf
writeAcceleratorEnabled | Hiermee wordt aangegeven of of niet `writeAccelerator` is ingeschakeld op de schijf

In het volgende voor beeld ziet u hoe u de opslag gegevens van de virtuele machine opvraagt.

**Aanvraag**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01
```

**Response**

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Het volgende voor beeld is een mooie afdruk van de Lees baarheid.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>VM-Tags

VM-Tags zijn onder het eind punt opgenomen als de exemplaar-API `instance/compute/tags` .
Labels zijn mogelijk toegepast op uw virtuele Azure-machine om ze logisch in een taxonomie te organiseren. U kunt de aan een virtuele machine toegewezen Tags ophalen met behulp van de volgende aanvraag.

**Aanvraag**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Response**

```text
Department:IT;Environment:Test;Role:WebRole
```

Het `tags` veld is een teken reeks met de Tags gescheiden door punt komma's. Deze uitvoer kan een probleem zijn als punt komma's worden gebruikt in de labels zelf. Als een parser is geschreven om de Tags programmatisch uit te pakken, moet u vertrouwen op het `tagsList` veld. Het `tagsList` veld is een JSON-matrix met geen scheidings tekens en is daarom gemakkelijker te parseren.

**Aanvraag**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04
```

**Response**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>Attestende gegevens

IMDS helpt garanties te geven dat de verstrekte gegevens afkomstig zijn van Azure. Micro soft tekent een deel van deze informatie, zodat u kunt bevestigen dat een installatie kopie in azure Marketplace de versie is die u op Azure uitvoert.

### <a name="sample-1-get-attested-data"></a>Voor beeld 1: Attestation-gegevens ophalen

> [!NOTE]
> Alle API-antwoorden zijn JSON-teken reeksen. De volgende voorbeeld antwoorden zijn tamelijk afgedrukt voor de Lees baarheid.

**Aanvraag**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> Vanwege het cache mechanisme van IMDS, kan een eerder in de cache opgeslagen `nonce` waarde worden geretourneerd.

`Api-version` is een verplicht veld. Raadpleeg de [sectie gebruik](#usage) voor ondersteunde API-versies.
`Nonce` is een optionele teken reeks van 10 cijfers. Als deze niet is ingevoerd, retourneert IMDS de huidige gecoördineerde universele tijds tempel.

**Response**

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

De hand tekening-blob is een door [pkcs7](https://aka.ms/pkcs7)ondertekende versie van het document. Het bevat het certificaat dat wordt gebruikt voor ondertekening, samen met bepaalde specifieke details over de virtuele machine. 

Voor virtuele machines die zijn gemaakt met behulp van Azure Resource Manager, omvat dit,,, `vmId` `sku` `nonce` `subscriptionId` , `timeStamp` voor het maken en verstrijken van het document en de plannings informatie over de installatie kopie. De plan gegevens worden alleen ingevuld voor installatie kopieën van Azure Marketplace. 

Voor virtuele machines die zijn gemaakt met behulp van het klassieke implementatie model, `vmId` is alleen gegarandeerd dat deze worden gevuld. U kunt het certificaat extra heren uit het antwoord en dit gebruiken om te bevestigen dat het antwoord geldig is en afkomstig is van Azure.

Het document bevat de volgende velden:

Gegevens | Description | Geïntroduceerde versie
-----|-------------|-----------------------
License type | Het type licentie voor de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Houd er rekening mee dat dit alleen aanwezig is voor virtuele machines met AHB-functionaliteit. | 2020-09-01
nonce | Een teken reeks die optioneel kan worden meegeleverd met de aanvraag. Als er geen `nonce` is opgegeven, wordt de huidige UTC (Coordinated Universal Time Time Stamp) gebruikt. | 2018-10-01
plannen | Het [abonnement op de Azure Marketplace-installatie kopie](/rest/api/compute/virtualmachines/createorupdate#plan). Bevat de plan-ID (naam), product afbeelding of aanbieding (product) en uitgever-ID (uitgever). | 2018-10-01
Time Stamp/createdOn | De UTC (Coordinated Universal Time Time Stamp) voor het maken van het ondertekende document. | 2018-20-01
tijds tempel-expiresOn | De UTC (Coordinated Universal Time Time Stamp) voor het verlopen van het ondertekende document. | 2018-10-01
vmId |  De [unieke id](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) voor de virtuele machine. | 2018-10-01
subscriptionId | Het Azure-abonnement voor de virtuele machine. | 2019-04-30
sku | De specifieke SKU voor de VM-installatie kopie. | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>Voor beeld 2: controleren of de virtuele machine wordt uitgevoerd in azure

Leveranciers in azure Marketplace willen ervoor zorgen dat hun software een licentie heeft om alleen in azure uit te voeren. Als iemand de VHD naar een on-premises omgeving kopieert, moet de leverancier dat kunnen detecteren. Via IMDS kunnen deze leveranciers ondertekende gegevens ophalen die alleen reageren op Azure.

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Controleer of de hand tekening van Microsoft Azure is en controleer de certificaat keten op fouten.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

> [!NOTE]
> Vanwege het cache mechanisme van IMDS, kan een eerder in de cache opgeslagen `nonce` waarde worden geretourneerd.

De `nonce` in het ondertekende document kan worden vergeleken als u een `nonce` para meter hebt ingevoerd in de eerste aanvraag.

> [!NOTE]
> Het certificaat voor de open bare Cloud en elke soevereine Cloud wijken af.

Cloud | Certificaat
------|------------
[Alle algemeen beschik bare wereld wijde Azure-regio's](https://azure.microsoft.com/regions/) | *. metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *. metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *. metadata.azure.cn
[Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/)                | *. metadata.microsoftazure.de

> [!NOTE]
> De certificaten hebben mogelijk niet exact overeenkomen met `metadata.azure.com` de open bare Cloud. Daarom moet de certificerings validatie een algemene naam van een wille keurig `.metadata.azure.com` subdomein toestaan.

In gevallen waarin het tussenliggende certificaat niet kan worden gedownload vanwege netwerk beperkingen tijdens de validatie, kunt u het tussenliggende certificaat vastmaken. Houd er rekening mee dat Azure over de certificaten rolt, wat een standaard-PKI-procedure is. U moet de vastgemaakte certificaten bijwerken wanneer de rollover plaatsvindt. Wanneer een wijziging voor het bijwerken van het tussenliggende certificaat is gepland, wordt het Azure-blog bijgewerkt en ontvangen Azure-klanten een melding. 

U kunt de tussenliggende certificaten vinden in de [PKI-opslag plaats](https://www.microsoft.com/pki/mscorp/cps/default.htm). De tussenliggende certificaten voor elk van de regio's kunnen verschillen.

> [!NOTE]
> Het tussenliggende certificaat voor Azure China 21Vianet is van DigiCert Global root CA, in plaats van Baltimore.
Als u de tussenliggende certificaten voor Azure China hebt vastgemaakt als onderdeel van een wijziging in de hoofd keten van de instantie, moeten de tussenliggende certificaten worden bijgewerkt.

## <a name="failover-clustering-in-windows-server"></a>Failover Clustering in Windows Server

Wanneer u een query uitvoert op IMDS met failover clustering, is het soms nodig om een route toe te voegen aan de routerings tabel. U doet dit als volgt:

1. Open een opdrachtprompt met beheerdersbevoegdheden.

1. Voer de volgende opdracht uit en noteer het adres van de interface voor de netwerk bestemming ( `0.0.0.0` ) in de IPv4-route tabel.

```bat
route print
```

> [!NOTE]
> De volgende voorbeeld uitvoer is van een Windows Server-VM met een failovercluster ingeschakeld. Voor de eenvoud bevat de uitvoer alleen de IPv4-route tabel.

```text
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

Voer de volgende opdracht uit en gebruik het adres van de interface voor de netwerk bestemming ( `0.0.0.0` ) () `10.0.1.10` in dit voor beeld.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="managed-identity"></a>Beheerde identiteit

Een beheerde identiteit, toegewezen door het systeem, kan worden ingeschakeld op de VM. U kunt ook een of meer door de gebruiker toegewezen beheerde identiteiten toewijzen aan de virtuele machine.
Vervolgens kunt u tokens aanvragen voor beheerde identiteiten vanuit IMDS. Gebruik deze tokens om te verifiëren bij andere Azure-Services, zoals Azure Key Vault.

Zie [een toegangs token verkrijgen](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)voor gedetailleerde stappen om deze functie in te scha kelen.

## <a name="scheduled-events"></a>Geplande gebeurtenissen
U kunt de status van de geplande gebeurtenissen verkrijgen met behulp van IMDS. Vervolgens kan de gebruiker een reeks acties opgeven die moeten worden uitgevoerd op deze gebeurtenissen. Zie [geplande gebeurtenissen](scheduled-events.md)voor meer informatie. 

## <a name="regional-availability"></a>Regionale beschikbaarheid

De service is algemeen beschikbaar in alle Azure-Clouds.

## <a name="sample-code-in-different-languages"></a>Voorbeeld code in verschillende talen

De volgende tabel bevat voor beelden van het aanroepen van IMDS met behulp van verschillende talen in de virtuele machine:

Taal      | Voorbeeld:
--------------|----------------
C++ (Windows) | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Aan de slag            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Node.js        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Visual Basic  | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb

## <a name="errors-and-debugging"></a>Fouten en fout opsporing

Als er geen gegevens element is gevonden of een ongeldige aanvraag, retourneert IMDS standaard HTTP-fouten. Bijvoorbeeld:

HTTP-statuscode | Reden
-----------------|-------
200 OK |
400 Ongeldige aanvraag | Ontbrekende `Metadata: true` koptekst of ontbrekende para meter `format=json` bij het uitvoeren van een query op een Leaf-knoop punt.
404 Niet gevonden  | Het aangevraagde element bestaat niet.
methode 405 niet toegestaan | Alleen `GET` aanvragen worden ondersteund.
410 verdwenen | Probeer het na enige tijd opnieuw uit met een maximum van 70 seconden.
429 Te veel aanvragen | De API ondersteunt momenteel Maxi maal vijf query's per seconde.
500-service fout     | Probeer het na enige tijd opnieuw.

### <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Ik krijg de fout melding `400 Bad Request, Required metadata header not specified` . Wat betekent dit?**

IMDS vereist dat de header `Metadata: true` in de aanvraag wordt door gegeven. Als deze header door de REST-aanroep wordt door gegeven, is toegang tot IMDS mogelijk.

**Waarom krijg ik geen reken gegevens voor mijn virtuele machine?**

Momenteel ondersteunt IMDS alleen instanties die zijn gemaakt met Azure Resource Manager.

**Ik heb Azure Resource Manager enige tijd geleden mijn virtuele machine gemaakt. Waarom zie ik geen gegevens over de berekening van meta gegevens?**

Als u na september 2016 uw VM hebt gemaakt, voegt u een [tag](../../azure-resource-manager/management/tag-resources.md) toe om de reken-meta gegevens te bekijken. Als u vóór september 2016 uw VM hebt gemaakt, kunt u uitbrei dingen of gegevens schijven toevoegen aan of verwijderen uit het VM-exemplaar om meta gegevens te vernieuwen.

**Waarom worden niet alle gegevens weer gegeven die zijn ingevoerd voor een nieuwe versie?**

Als u na september 2016 uw VM hebt gemaakt, voegt u een [tag](../../azure-resource-manager/management/tag-resources.md) toe om de reken-meta gegevens te bekijken. Als u vóór september 2016 uw VM hebt gemaakt, kunt u uitbrei dingen of gegevens schijven toevoegen aan of verwijderen uit het VM-exemplaar om meta gegevens te vernieuwen.

**Waarom krijg ik de fout `500 Internal Server Error` of `410 Resource Gone` ?**

Probeer de aanvraag opnieuw uit te voeren. Zie [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults)voor meer informatie. Als het probleem zich blijft voordoen, maakt u een ondersteunings probleem in de Azure Portal voor de virtuele machine.

**Zou dit werken voor instanties van de schaalset voor virtuele machines?**

Ja, IMDS is beschikbaar voor instanties van virtuele-machine schaal sets.

**Ik heb mijn tags in virtuele-machine schaal sets bijgewerkt, maar ze worden niet weer gegeven in de instanties (in tegens telling tot Vm's met één exemplaar). Wil ik iets verkeerd doen?**

Momenteel worden labels voor virtuele-machine schaal sets alleen weer gegeven voor de VM op het moment dat de computer opnieuw wordt opgestart, de installatie kopie of de schijf wordt gewijzigd.

**Waarom is er een time-out opgetreden voor mijn aanvraag voor mijn oproep naar de service?**

Meta gegevens moeten worden gemaakt op basis van het primaire IP-adres dat is toegewezen aan de primaire netwerk kaart van de virtuele machine. Als u uw routes hebt gewijzigd, moet er bovendien een route voor het 169.254.169.254/32-adres in de lokale routerings tabel van de VM zijn.
   * <details>
        <summary>De routerings tabel controleren</summary>

        1. Dump uw lokale routerings tabel en zoek naar de IMDS-vermelding. Bijvoorbeeld:
            ```console
            > route print
            IPv4 Route Table
            ===========================================================================
            Active Routes:
            Network Destination        Netmask          Gateway       Interface  Metric
                      0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                    127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                    127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
              127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
                168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
              169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
            ... (continues) ...
            ```
        1. Controleer of er een route bestaat voor `169.254.169.254` en noteer de bijbehorende netwerk interface (bijvoorbeeld `172.16.69.7` ).
        1. Dump de configuratie van de interface en zoek de interface die overeenkomt met het punt waarnaar wordt verwezen in de routerings tabel. Dit is het MAC-adres (fysiek).
            ```console
            > ipconfig /all
            ... (continues) ...
            Ethernet adapter Ethernet:

               Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
               Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
               Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
               DHCP Enabled. . . . . . . . . . . : Yes
               Autoconfiguration Enabled . . . . : Yes
               Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
               IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
               Subnet Mask . . . . . . . . . . . : 255.255.255.0
            ... (continues) ...
            ```
        1. Controleer of de interface overeenkomt met de primaire NIC van de virtuele machine en het primaire IP-adres. U kunt de primaire NIC en het IP-adres vinden door te kijken naar de netwerk configuratie in de Azure Portal of door de Azure CLI te bekijken. Let op de open bare en privé Ip's (en het MAC-adres als u de CLI gebruikt). Hier volgt een Power shell CLI-voor beeld:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. Als ze niet overeenkomen, werkt u de routerings tabel bij zodat de primaire NIC en IP zijn gericht.
    </details>

## <a name="support"></a>Ondersteuning

Als u na meerdere pogingen geen meta gegevens reactie krijgt, kunt u een ondersteunings probleem in het Azure Portal maken.
Selecteer voor **probleem type** **beheer**. Selecteer **instance metadata service** voor **categorie**.

![Scherm opname van Instance Metadata Service ondersteuning](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Volgende stappen

[Een toegangs token voor de virtuele machine ophalen](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Geplande gebeurtenissen](scheduled-events.md)
