---
title: Zelfstudie voor het voorbereiden van Azure Portal, datacenteromgeving om Azure Stack Edge Pro R te implementeren
description: De eerste zelfstudie over het implementeren van Azure Stack Edge Pro R omvat het voorbereiden van Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: d992748e9829993acc28795778baae3eb92e88f3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464104"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-r"></a>Zelfstudie: Voorbereidingen voor de implementatie van Azure Stack Edge Pro R

Dit is de eerste zelfstudie in de reeks zelfstudies over implementaties die vereist zijn om Azure Stack Edge Pro R volledig te implementeren. In deze zelfstudie wordt beschreven hoe u Azure Portal voorbereidt om een Azure Stack Edge-resource te implementeren. In de zelfstudie wordt gebruikgemaakt van een Azure Stack Edge Pro R-apparaat met één knooppunt dat wordt geleverd met een noodvoeding (UPS).

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. Het voorbereiden van de portal duurt minder dan 10 minuten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een nieuwe resource maken
> * De activeringssleutel ophalen

### <a name="get-started"></a>Aan de slag

Raadpleeg de volgende zelfstudies in de voorgeschreven volgorde voor het implementeren van Azure Stack Edge Pro R.

| Om deze stap uit te voeren | Gebruikt u deze documenten |
| --- | --- |
| **Voorbereiding** |Deze moeten worden voltooid ter voorbereiding van de implementatie. |
| **[Configuratiecontrolelijst voor implementatie](#deployment-configuration-checklist)** |Gebruik deze controlelijst om informatie te verzamelen en te registreren voorafgaand aan en tijdens de implementatie. |
| **[Vereisten voor implementatie](#prerequisites)** |Hiermee wordt gecontroleerd of de omgeving gereed is voor implementatie. |
|  | |
|**Zelfstudies voor implementatie** |Deze zelfstudies zijn vereist als u uw Azure Stack Edge Pro R-apparaat in productie wilt implementeren. |
|**[1. Azure Portal voorbereiden voor apparaat](azure-stack-edge-pro-r-deploy-prep.md)** |Maak en configureer uw Azure Stack Edge-resource voordat u een fysiek Azure Stack Edge-apparaat installeert. |
|**[2. Het apparaat installeren](azure-stack-edge-pro-r-deploy-install.md)**|Uw fysieke apparaat uitpakken, opstellen en bekabelen.  |
|**[3. Verbinding maken met het apparaat](azure-stack-edge-pro-r-deploy-connect.md)** |Zodra het apparaat is geïnstalleerd, maakt u verbinding met de lokale webinterface van het apparaat.  |
|**[4. Netwerkinstellingen configureren](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)** |Configureer een netwerk, inclusief het rekennetwerk en de webproxy-instellingen voor uw apparaat.   |
|**[5. Apparaatinstellingen configureren](azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)** |Wijs een apparaatnaam en een DNS-domein toe, configureer de updateserver en de apparaattijd. |
|**[6. Beveiligingsinstellingen configureren](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)** |Configureer de instellingen voor certificaten, VPN en versleuteling-at-rest voor het apparaat. Gebruik certificaten die door het apparaat zijn gegenereerd of gebruik uw eigen certificaten.   |
|**[7. Het apparaat activeren](azure-stack-edge-pro-r-deploy-activate.md)** |Gebruik de activeringssleutel van de service om het apparaat te activeren. Het apparaat is klaar om er SMB- of NFS-shares op in te stellen of om via REST verbinding te maken. |
|**[8. Rekenproces configureren](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configureer de rekenprocesrol op het apparaat. Er wordt dan ook een Kubernetes-cluster gemaakt. |

U kunt nu Azure Portal gaan instellen.

## <a name="deployment-configuration-checklist"></a>Configuratiecontrolelijst voor implementatie

Voordat u uw apparaat implementeert, moet u informatie verzamelen om de software op uw Azure Stack Edge Pro-apparaat te configureren. De implementatie van het Azure Stack Edge-apparaat in uw omgeving verloopt gestroomlijnder wanneer u deze gegevens zoveel mogelijk op voorhand voorbereidt. Gebruik de [Configuratiecontrolelijst voor de implementatie van Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-checklist.md) om de configuratiegegevens te noteren terwijl u het apparaat implementeert.

## <a name="prerequisites"></a>Vereisten

Hier volgen de configuratievereisten voor uw Azure Stack Edge-resource, uw Azure Stack Edge-apparaat en het datacenternetwerk.

### <a name="for-the-azure-stack-edge-resource"></a>Voor de Azure Stack Edge-resource

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Voor het Azure Stack Edge-apparaat

Voordat u een fysiek apparaat implementeert, controleert u of:

- U hebt de beveiligingsgegevens voor dit apparaat gecontroleerd op: [Veiligheidsrichtlijnen voor uw Azure Stack Edge-apparaat](azure-stack-edge-pro-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 



### <a name="for-the-datacenter-network"></a>Voor datacenternetwerk

Zorg voordat u begint voor het volgende:

- Het netwerk in uw datacenter is geconfigureerd volgens de netwerkvereisten voor uw Azure Stack Edge-apparaat. Zie [Systeemvereisten voor Azure Stack Edge Pro R](azure-stack-edge-pro-r-system-requirements.md) voor meer informatie.

- Voor normale bedrijfsomstandigheden van het apparaat hebt u het volgende nodig:

    - Minimaal 10 Mbps downloadbandbreedte om ervoor te zorgen dat het apparaat bijgewerkt blijft.
    - Minimaal 20 Mbps toegewezen upload- en downloadbandbreedte voor het overdragen van bestanden.

## <a name="create-a-new-resource"></a>Een nieuwe resource maken

Als u al een Azure Stack Edge-resource hebt voor het beheer van uw fysieke apparaat, kunt u deze stap overslaan en verdergaan met [Activeringscode ophalen](#get-the-activation-key).

Voer de volgende stappen uit in de Azure-portal om een Azure Stack Edge-resource te maken.

1. Gebruik uw Microsoft Azure-referenties om u aan te melden bij de Azure-portal op de volgende URL: [https://portal.azure.com](https://portal.azure.com).

2. Selecteer **En een resource maken** in het linkerdeelvenster. Zoek en selecteer **Azure Stack Edge / Data Box Gateway**. Selecteer **Maken**. 

3. Kies het abonnement dat u wilt gebruiken voor het Azure Stack Edge Pro-apparaat. Selecteer het land waar u dit fysieke apparaat naar wilt verzenden. Selecteer **Apparaten weergeven**.

    ![Een resource maken 1](media/azure-stack-edge-pro-r-deploy-prep/create-resource-1.png)

4. Selecteer het apparaattype. Kies onder **Azure Stack Edge** de optie **Azure Stack Edge Pro R** en kies vervolgens **Selecteren**. Als u problemen ziet of als u het apparaattype niet kunt selecteren, gaat u naar [Problemen met de volgorde oplossen](azure-stack-edge-troubleshoot-ordering.md).

    ![Een resource maken 2](media/azure-stack-edge-pro-r-deploy-prep/create-resource-2.png)

5. Op basis van uw bedrijfsbehoeften kunt u **Azure Stack Edge Pro R - één knooppunt** of **Azure Stack Edge Pro R - één knooppunt met UPS** selecteren.  

    ![Een resource maken 3](media/azure-stack-edge-pro-r-deploy-prep/create-resource-3.png)

6. Voer op het tabblad **Basisinstellingen** de volgende **Projectdetails** in of selecteer deze.
    
    |Instelling  |Waarde  |
    |---------|---------|
    |Abonnement    |Dit wordt automatisch ingevuld op basis van de eerdere selectie. Abonnement is gekoppeld aan uw factureringsrekening. |
    |Resourcegroep  |Maak een nieuwe groep of selecteer een bestaande groep.<br>Meer informatie over [Azure-resourcegroepen](../azure-resource-manager/management/overview.md).     |

7. Voer de volgende **exemplaardetails** in of selecteer deze.

    |Instelling  |Waarde  |
    |---------|---------|
    |Naam   | Een beschrijvende naam om de resource aan te duiden.<br>De naam is tussen 2 en 50 tekens lang en kan letters, cijfers en afbreekstreepjes bevatten.<br> De naam begint en eindigt met een letter of cijfer.        |
    |Regio     |Zie [Azure-producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) voor een lijst met alle regio's waar de Azure Stack Edge-resource beschikbaar is. Als Azure Government wordt gebruikt, zijn alle overheidsregio's beschikbaar, zoals wordt weergegeven in de [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).<br> Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren.|

    ![Een resource maken 4](media/azure-stack-edge-pro-r-deploy-prep/create-resource-4.png)


8. Selecteer **Volgende: Verzendadres**.

    - Als u al een apparaat hebt, selecteert u de keuzelijst met invoervak voor **Ik heb een Azure Stack Edge Pro R-apparaat**.

        ![Een resource maken 5](media/azure-stack-edge-pro-r-deploy-prep/create-resource-5.png)

    - Als dit het nieuwe apparaat is dat u bestelt, voert u de naam van de contactpersoon, het bedrijf, het adres voor het verzenden van het apparaat en contactgegevens in.

        ![Een resource maken 6](media/azure-stack-edge-pro-r-deploy-prep/create-resource-6.png)

9. Selecteer **Volgende: Tags**. Geef eventueel tags op voor het categoriseren van resources en het samenvoegen van facturering. Selecteer **Volgende: Beoordelen en maken**.

10. Bekijk op het tabblad **Controleren en maken** de **Prijsdetails**, **Gebruiksvoorwaarden** en de details van uw resource. Selecteer de keuzelijst met invoervak voor **Ik heb de privacyvoorwaarden gecontroleerd**.

    ![Een resource maken 7](media/azure-stack-edge-pro-r-deploy-prep/create-resource-7.png) 

    U wordt ook gewaarschuwd wanneer er een Managed Service Identity (MSI) wordt gemaakt, waarmee u zich kunt verifiëren bij cloudservices. Deze identiteit bestaat zolang de resource bestaat.

11. Selecteer **Maken**.

Het maken van de resource duurt enkele minuten. Er wordt ook een MSI gemaakt waarmee het Azure Stack Edge-apparaat kan communiceren met de resourceprovider in Azure.

Nadat de resource succesvol is gemaakt en geïmplementeerd, wordt u daarvan op de hoogte gebracht. Selecteer **Ga naar resource**.

![Ga naar de Azure Stack Edge Pro-resource](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-1.png)

Nadat de bestelling is geplaatst, controleert Microsoft de bestelling en neemt contact met u op (via e-mail) met verzendgegevens.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

Als u problemen ondervindt tijdens het bestelproces, raadpleegt u [Problemen met de bestelling oplossen](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>De activeringssleutel ophalen

Nadat de Azure Stack Edge-resource is geactiveerd, hebt u de activeringssleutel nodig. Deze sleutel wordt gebruikt om uw Azure Stack Edge Pro-apparaat te activeren en te verbinden met de resource. U kunt deze sleutel nu ophalen, terwijl u Azure Portal geopend hebt.

1. Selecteer de resource die u hebt gemaakt. Selecteer **Overzicht** en selecteer vervolgens **Apparaatinstallatie**.

    ![Apparaatinstallatie selecteren](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-2.png)

2. Geef op de tegel **Activeren** een naam op voor de Azure Key Vault of accepteer de standaardnaam. De naam van de sleutelkluis mag tussen de 3 en 24 tekens lang zijn. 

    Er wordt een sleutelkluis gemaakt voor elke Azure Stack Edge-resource die met uw apparaat wordt geactiveerd. Met de sleutelkluis kunt u geheimen opslaan en openen, de CIK (Channel Integrity Key) voor de service wordt bijvoorbeeld opgeslagen in de sleutelkluis. 

    Wanneer u een naam voor de sleutelkluis hebt opgegeven, selecteert u **Sleutel genereren** om een activeringssleutel te maken. 

    ![Activeringssleutel ophalen](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-3.png)

    Wacht enkele minuten terwijl de sleutelkluis en de activeringssleutel worden gemaakt. Selecteer het kopieerpictogram om de sleutel te kopiëren en op te slaan voor later gebruik.


> [!IMPORTANT]
> - De activeringssleutel verloopt drie dagen nadat deze is gegenereerd.
> - Als de sleutel is verlopen, genereert u een nieuwe sleutel. De oudere toegangssleutel is niet langer geldig.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Stack Edge, zoals:

> [!div class="checklist"]
> * Een nieuwe resource maken
> * De activeringssleutel ophalen

Ga naar de volgende zelfstudie om te lezen hoe u Azure Stack Edge installeert.

> [!div class="nextstepaction"]
> [Azure Stack Edge installeren](./azure-stack-edge-pro-r-deploy-install.md)