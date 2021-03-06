---
title: IT Service Management-connector in Log Analytics
description: Dit artikel bevat een overzicht van IT Service Management-connector (ITSMC) en informatie over het gebruik ervan om ITSM-werk items in Log Analytics te controleren en te beheren en om snel problemen op te lossen.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 568243c6fecf26510f6e9988907d1ccad103cdc2
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618798"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Verbinding maken tussen Azure en ITSM-hulpprogram ma's met behulp van IT Service Management-connector

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

In dit artikel vindt u informatie over het configureren van de IT Service Management-connector (ITSMC) in Log Analytics om uw werk items centraal te beheren.

## <a name="add-it-service-management-connector"></a>IT Service Management-connector toevoegen

Voordat u een verbinding kunt maken, moet u ITSMC toevoegen.

1. Selecteer in het Azure Portal **een resource maken**:

   ![Scherm opname van het menu-item een resource maken.](media/itsmc-overview/azure-add-new-resource.png)

2. Zoek naar **IT Service Management-connector** in azure Marketplace. Selecteer **maken**:

   ![Scherm opname waarin de knop maken wordt weer gegeven in azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. Selecteer in de sectie **OMS-werk ruimte** de Azure log Analytics-werk ruimte waar u ITSMC wilt installeren.
   >[!NOTE]
   >
   > * Als onderdeel van de doorlopende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor worden OMS-werk ruimten nu aangeduid als *log Analytics*.
   > * ITSMC kan alleen in Log Analytics-werk ruimten worden geïnstalleerd in de volgende regio's: VS-Oost, VS-West 2, Zuid-Centraal VS, West-Centraal VS, US Gov-Arizona, US Gov-Virginia, Canada-centraal, Europa-west, Zuid-Brittannië, Zuidoost-Azië, Japan-Oost, Centraal-India en Australië-zuidoost.

4. Selecteer in de sectie **log Analytics werk ruimte** de resource groep waar u de ITSMC-resource wilt maken:

   ![Scherm afbeelding van de sectie Log Analytics werk ruimte.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Als onderdeel van de doorlopende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor worden OMS-werk ruimten nu aangeduid als *log Analytics*.

5. Selecteer **OK**.

Wanneer de ITSMC-resource wordt geïmplementeerd, wordt er een melding weer gegeven in de rechter bovenhoek van het venster.

## <a name="create-an-itsm-connection"></a>Een ITSM-verbinding maken

Nadat u ITSMC hebt geïnstalleerd, kunt u een verbinding maken.

Als u een verbinding wilt maken, moet u uw ITSM-hulp programma voorbereiden om de verbinding van ITSMC toe te staan.  

Op basis van het ITSM-product waarmee u verbinding maakt, selecteert u een van de volgende koppelingen voor instructies:

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Nadat u uw ITSM-hulpprogram ma's hebt bereid, voert u de volgende stappen uit om een verbinding te maken:

1. Zoek in **alle resources** naar **Service Desk (*de naam van uw werk ruimte*)**:

   ![Scherm opname van recente resources in het Azure Portal.](media/itsmc-overview/itsm-connections.png)

1. Selecteer onder **gegevens bronnen voor werk ruimte** in het linkerdeel venster **ITSM-verbindingen**:

   ![Scherm opname van het menu-item ITSM-verbindingen.](media/itsmc-overview/add-new-itsm-connection.png)
   Op deze pagina wordt de lijst met verbindingen weer gegeven.
1. Selecteer **verbinding toevoegen**.

4. Geef de verbindings instellingen op, zoals beschreven in [de ITSMC-verbinding configureren met uw ITSM-producten of-services](./itsmc-connections.md).

   > [!NOTE]
   >
   > De configuratie gegevens van de verbinding worden standaard elke 24 uur vernieuwd door ITSMC. Als u de gegevens van uw verbinding onmiddellijk wilt vernieuwen om te zien welke wijzigingen of sjabloon updates u aanbrengt, selecteert u de knop **synchroniseren** op de Blade van de verbinding:
   >
   > ![Scherm afbeelding met de knop synchroniseren op de Blade verbinding.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>ITSMC gebruiken

   U kunt ITSMC gebruiken om werk items te maken op basis van Azure-waarschuwingen, Log Analytics waarschuwingen en Log Analytics logboek records.

## <a name="template-definitions"></a>Sjabloon definities

   Er zijn typen werk items die sjablonen kunnen gebruiken die zijn gedefinieerd door het ITSM-hulp programma.
Met behulp van sjablonen kunt u velden definiëren die automatisch worden ingevuld op basis van vaste waarden die zijn gedefinieerd als onderdeel van de actie groep. U definieert sjablonen in het hulp programma ITSM.
U kunt definiëren in welke sjabloon u wilt gebruiken als onderdeel van de definitie van de actie groep.

## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-werk items maken op basis van Azure-waarschuwingen

Nadat u uw ITSM-verbinding hebt gemaakt, kunt u in uw ITSM-hulp programma werk items maken op basis van Azure-waarschuwingen. Als u de werk items wilt maken, gebruikt u de actie ITSM in actie groepen.

Actie groepen bieden een modulaire en herbruikbare manier om acties voor uw Azure-waarschuwingen te activeren. U kunt actie groepen met metrische waarschuwingen, waarschuwingen voor activiteiten logboeken en waarschuwingen voor Azure Log Analytics gebruiken in de Azure Portal.

> [!NOTE]
> Nadat u de ITSM-verbinding hebt gemaakt, moet u 30 minuten wachten totdat het synchronisatie proces is voltooid.

Gebruik de volgende procedure om werk items te maken:

1. Selecteer in de Azure Portal  **waarschuwingen**.
2. Selecteer in het menu boven aan het scherm **acties beheren**:

    ![Scherm afbeelding met de menu opdracht acties beheren.](media/itsmc-overview/action-groups-selection-big.png)

   Het venster **actie groep maken** wordt weer gegeven.

3. Selecteer het **abonnement** en de **resource groep** waar u uw actie groep wilt maken. Geef een naam op voor de **actie groep** en de **weergave naam** voor uw actie groep. Selecteer **volgende: meldingen**.

    ![Scherm opname van het venster actie groep maken.](media/itsmc-overview/action-groups-details.png)

4. Selecteer in de lijst met meldingen **volgende: acties**.
5. Selecteer in de lijst acties de optie **ITSM** in de lijst **actie type** . Geef een **naam** op voor de actie. Selecteer de knop pen die de **Details** van de bewerking weergeeft.
6. Selecteer in de lijst **abonnement** het abonnement waarin uw log Analytics-werk ruimte zich bevindt. Selecteer in de lijst **verbinding** de naam van uw ITSM-connector. Deze wordt gevolgd door de naam van uw werk ruimte. Bijvoorbeeld MyITSMConnector (MyWorkspace).

7. Selecteer een type **werk item** .

8. Als u velden met vaste waarden wilt invullen, selecteert u **aangepaste sjabloon gebruiken**. Als dat niet het geval is, kiest u een bestaande [sjabloon](#template-definitions) in de lijst **sjabloon** en voert u de vaste waarden in de sjabloon velden in.

9. Als u **afzonderlijke werk items maken voor elk configuratie-item** selecteert, heeft elk configuratie-item een eigen werk item. Er wordt één werk item per configuratie-item weer. Het wordt bijgewerkt op basis van de waarschuwingen die worden gemaakt.

    * In het geval dat u in de vervolg keuzelijst werk item of waarschuwing klikt: als u het selectie vakje **afzonderlijke werk items voor elk configuratie-item maken** uitschakelt, wordt er door elke waarschuwing een nieuw werk item gemaakt. Er kan meer dan één waarschuwing per configuratie-item zijn.

       ![Scherm opname van het ITSM-incident venster.](media/itsmc-overview/itsm-action-configuration.png)

    * In het geval dat u in de vervolg keuzelijst voor werk items selecteert: als u **afzonderlijke werk items maken voor elke logboek vermelding** in de keuze rondjes selecteert, wordt er met elke waarschuwing een nieuw werk item gemaakt. Als u **afzonderlijke werk items maken selecteert voor elk configuratie-item** in de keuze rondjes selectie, heeft elk configuratie-item een eigen werk item.
   ![Scherm opname van het ITSM-gebeurtenis venster.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Selecteer **OK**.

Wanneer u een Azure-waarschuwings regel maakt of bewerkt, gebruikt u een actie groep met een ITSM-actie. Wanneer de waarschuwing wordt geactiveerd, wordt het werk item gemaakt of bijgewerkt in het ITSM-hulp programma.

> [!NOTE]
>
>- Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor actie groepen voor meer informatie over de prijzen van de actie ITSM.
>
>
>- Het veld korte beschrijving in de definitie van de waarschuwings regel is beperkt tot 40 tekens wanneer u het verzendt met behulp van de actie ITSM.

## <a name="additional-information"></a>Aanvullende informatie

### <a name="data-synced-from-your-itsm-product"></a>Gegevens die zijn gesynchroniseerd met uw ITSM-product

Incidenten en wijzigings aanvragen worden gesynchroniseerd vanuit uw ITSM-product naar uw Log Analytics-werk ruimte, op basis van de configuratie van de verbinding.

In deze sectie vindt u enkele voor beelden van gegevens die zijn verzameld door ITSMC.

De velden in **ServiceDesk_CL** variëren, afhankelijk van het type werk item dat u in log Analytics importeert. Hier volgt een lijst met velden voor twee typen werk items:

**Werk item:** **incidenten**  
ServiceDeskWorkItemType_s = "incident"

**Fields**

- ServiceDeskConnectionName
- Service Desk-ID
- Staat
- Urgentie
- Impact
- Prioriteit
- Escalatie
- Created By
- Opgelost door
- Gesloten door
- Bron
- Toegewezen aan
- Categorie
- Titel
- Beschrijving
- Gemaakt op
- Datum gesloten
- Datum opgelost
- Datum laatst gewijzigd
- Computer

**Werk item:** **wijzigings aanvragen**

ServiceDeskWorkItemType_s = "ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- Service Desk-ID
- Created By
- Gesloten door
- Bron
- Toegewezen aan
- Titel
- Type
- Categorie
- Staat
- Escalatie
- Conflict status
- Urgentie
- Prioriteit
- Risico
- Impact
- Toegewezen aan
- Gemaakt op
- Datum gesloten
- Datum laatst gewijzigd
- Aangevraagde datum
- Geplande begin datum
- Geplande eind datum
- Begin datum van werk
- Eind datum van werk
- Beschrijving
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>Uitvoer gegevens voor een ServiceNow-incident

| Log Analytics veld | Het veld ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Getal |
| IncidentState_s | Staat |
| Urgency_s |Urgentie |
| Impact_s |Impact|
| Priority_s | Prioriteit |
| CreatedBy_s | Geopend door |
| ResolvedBy_s | Opgelost door|
| ClosedBy_s  | Gesloten door |
| Source_s| Type contact |
| AssignedTo_s | Toegewezen aan  |
| Category_s | Categorie |
| Title_s|  Korte beschrijving |
| Description_s|  Opmerkingen |
| CreatedDate_t|  Had |
| ClosedDate_t| gesloten|
| ResolvedDate_t|Opgelost|
| Computer  | Configuratie-item |

## <a name="output-data-for-a-servicenow-change-request"></a>Uitvoer gegevens voor een wijzigings aanvraag voor een ServiceNow

| Log Analytics | Het veld ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Getal |
| CreatedBy_s | Aangevraagd door |
| ClosedBy_s | Gesloten door |
| AssignedTo_s | Toegewezen aan  |
| Title_s|  Korte beschrijving |
| Type_s|  Type |
| Category_s|  Categorie |
| CRState_s|  Staat|
| Urgency_s|  Urgentie |
| Priority_s| Prioriteit|
| Risk_s| Risico|
| Impact_s| Impact|
| RequestedDate_t  | Aangevraagd door datum |
| ClosedDate_t | Gesloten datum |
| PlannedStartDate_t  | Geplande begin datum |
| PlannedEndDate_t  | Geplande eind datum |
| WorkStartDate_t  | Werkelijke begin datum |
| WorkEndDate_t | Werkelijke eind datum|
| Description_s | Beschrijving |
| Computer  | Configuratie-item |

## <a name="next-steps"></a>Volgende stappen

[ITSM-producten/-services toevoegen aan IT Service Management-connector](./itsmc-connections.md)
