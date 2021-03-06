---
title: Een Dynamics 365 maken voor klant betrokkenheid & PowerApps-aanbieding in micro soft Commercial Marketplace
description: Een Dynamics 365 maken voor klant betrokkenheid & PowerApps-aanbieding in Microsoft AppSource. Bied of verkoop uw aanbieding in AppSource of via het programma Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: 0c220daab0d1d9ae7d50d37d9303d6677bd52cc1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360302"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Een Dynamics 365 for Customer Engagement- en PowerApps-aanbieding maken

In dit artikel wordt beschreven hoe u een nieuwe Dynamics 365 maakt voor klant betrokkenheid & PowerApps-aanbieding. Alle apps voor Dynamics 365 voor klant betrokkenheid (PowerApps, Sales, service, project service en Field Service) moeten het certificerings proces door lopen, waarmee wordt gecontroleerd of uw oplossing voldoet aan de standaard vereisten, de compatibiliteit en de juiste prak tijken. Met de proef ervaring kunnen gebruikers uw oplossing implementeren in een live Dynamics 365-omgeving.

Voordat u begint, moet u [een commercieel Marketplace-account maken in Partner Center](create-account.md) als u dit nog niet hebt gedaan. Zorg ervoor dat uw account is inge schreven in het Commercial Marketplace-programma.

>[!NOTE]
> Zodra een aanbieding is gepubliceerd, worden wijzigingen in de aanbieding alleen bijgewerkt in partner centrum en de online winkel nadat u de aanbieding voor publicatie opnieuw hebt ingediend.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het menu links de optie **commerciële Marketplace**-  >  **overzicht**.
3. Selecteer op de pagina overzicht **+ nieuw aanbod**  >  **Dynamics 365 voor klant betrokkenheid & PowerApps**.

    ![Illustreert het navigatie menu.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

## <a name="new-offer"></a>Nieuwe aanbieding

Voer een **aanbiedings-id** in. Dit is een unieke id voor elke aanbieding in uw account.

- Deze ID is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen, indien van toepassing.
- De aanbiedings-ID gecombineerd met de uitgevers-ID moet langer zijn dan 40 tekens.
- Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar geen spaties. Als uw uitgevers-ID bijvoorbeeld is `testpublisherid` en u **test-aanbieding-1** invoert, is het webadres van de aanbieding `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Deze ID kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

Voer een **alias** voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in Marketplace en wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.
- Deze naam kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="offer-setup"></a>Installatie van aanbieding

### <a name="alias"></a>Alias

Voer een beschrijvende naam in die we gebruiken om alleen in het partner centrum naar deze aanbieding te verwijzen. Deze naam (vooraf ingevuld met de informatie die u hebt ingevoerd tijdens het maken van de aanbieding) wordt niet gebruikt in de Marketplace en is anders dan de naam van de aanbieding die aan klanten wordt weer gegeven. Als u de naam van de aanbieding later wilt bijwerken, gaat u naar de pagina [aanbiedings vermelding](#offer-listing) .

### <a name="setup-details"></a>Details van de installatie

**Hoe wilt u dat potentiële klanten kunnen communiceren met deze aanbieding?**, selecteer dan de optie die u wilt gebruiken voor deze aanbieding.

- **Nu downloaden (gratis)** : bied uw aanbieding aan klanten gratis aan.
- **Gratis proef versie (vermelding)** : bied uw aanbieding aan klanten aan met een koppeling naar een gratis proef versie. Gratis proef versies van aanbieding worden gemaakt, beheerd en geconfigureerd door uw service en er zijn geen abonnementen die door micro soft worden beheerd.

    > [!NOTE]
    > De tokens die uw toepassing via uw proef koppeling ontvangt, kunnen alleen worden gebruikt voor het verkrijgen van gebruikers gegevens via Azure Active Directory (Azure AD) om het maken van accounts in uw app te automatiseren. Micro soft-accounts worden niet ondersteund voor verificatie met behulp van dit token.

- **Contact opnemen** : contact gegevens van de klant verzamelen door verbinding te maken met uw CRM-systeem (Customer Relationship Management). De klant wordt gevraagd om toestemming te krijgen om hun gegevens te delen. Deze klant gegevens, samen met de naam van de aanbieding, ID en Marketplace-bron waar ze uw aanbieding vinden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. Zie [leads van klanten](#customer-leads)voor meer informatie over het configureren van uw CRM.

### <a name="test-drive"></a>Station testen

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen de mogelijkheid te bieden om te kopen voordat u aan de slag gaat, wat resulteert in een verhoogde conversie en de generatie van uiterst gekwalificeerde leads. Begin met [Wat is test drive](../what-is-test-drive.md)voor meer informatie.

Als u een test drive voor een bepaalde periode wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in. Als u test drive uit uw aanbieding wilt verwijderen, schakelt u dit selectie vakje uit.

### <a name="customer-leads"></a>Leads van klanten

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Zie [Lead Management Overview](./commercial-marketplace-get-customer-leads.md)voor meer informatie.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="properties"></a>Eigenschappen

Op deze pagina kunt u de categorieën en industrieën definiëren die worden gebruikt voor het groeperen van uw aanbieding op Marketplace, uw app-versie en de juridische contracten die uw aanbieding ondersteunen.

### <a name="categories"></a>Categorieën

Selecteer categorieën en subcategorieën om uw aanbieding te plaatsen in de juiste Zoek gebieden voor Marketplace. Zorg ervoor dat u beschrijft hoe uw aanbod deze categorieën ondersteunt in de beschrijving van de aanbieding. Selecteer:

- Ten minste één en Maxi maal twee categorieën, met inbegrip van een primaire en secundaire categorie (optioneel).
- Maxi maal twee subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert u **niet van toepassing**.

Bekijk de volledige lijst met categorieën en subcategorieën in [Aanbevolen procedures voor aanbiedingen](../gtm-offer-listing-best-practices.md).

### <a name="industries"></a>Branches

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Toepasselijke Dynamics 365-producten

Selecteer alle Dynamics 365-producten waarop deze aanbieding van toepassing is.

### <a name="app-version"></a>App-versie

Voer het versie nummer van uw aanbieding in. Klanten zien dat deze versie wordt weer gegeven op de detail pagina van de aanbieding.<!-- If you are only updating the version number due to marketing/descriptive changes, check the **Marketing only change** box. This option allows the offer to bypass the certification and provisioning stages.-->

### <a name="terms-and-conditions"></a>Voorwaarden

Geef hier uw eigen juridische voor waarden op. U kunt ook het adres opgeven waar uw voor waarden kunnen worden gevonden. Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="offer-listing"></a>Aanbieding weer geven

<!--This page displays the languages in which your offer will be listed. Currently, **English (United States)** is the only available option.

Define marketplace details for each language/market here, such as offer name, description, and images. Select the language/market name to provide this information.-->This page lets you define offer details such as offer name, description, links, and contacts.

> [!NOTE]
> Geef details van de aanbiedings vermelding in één taal op. Het is niet nodig om in het Engels te zijn, zolang de beschrijving van het aanbod begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. Het is ook acceptabel om een *nuttige koppelings-URL* te bieden om inhoud te bieden in een andere taal dan de versie die wordt gebruikt in de inhoud van de aanbieding.

Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in Microsoft AppSource (alle prijzen zijn bijvoorbeeld alleen bedoeld als voor beeld van de werkelijke kosten):
<!-- update screen? -->
:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Logo
1. Producten
1. Categorieën
1. Ondersteunings adres (koppeling)
1. Gebruiksvoorwaarden
1. Naam van aanbieding
1. Description
1. Scherm afbeeldingen/Video's

### <a name="marketplace-details"></a>Marketplace-gegevens

De **naam** die u hier invoert, wordt aan klanten weer gegeven als de titel van de aanbieding. Dit veld is vooraf ingevuld met de tekst die u hebt ingevoerd voor de **aanbiedings alias** tijdens het maken van de aanbieding, maar u kunt deze waarde wijzigen. Deze naam kan worden aangemerkt (en u kunt symbolen van het handels merk of copyright bevatten). De naam mag niet langer zijn dan 50 tekens en mag geen emojis bevatten.

Geef een korte beschrijving van uw aanbieding, Maxi maal 100 tekens, voor de **samen vatting van de zoek resultaten**. Deze beschrijving kan worden gebruikt in Zoek resultaten voor Marketplace.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

U kunt eventueel Maxi maal drie **Zoek trefwoorden** invoeren om klanten te helpen uw aanbieding op Marketplace te vinden. Voor de beste resultaten gebruikt u deze tref woorden ook in uw beschrijving.

Als u klanten wilt laten weten met welke **producten uw app werkt**, voert u Maxi maal drie product namen in.

### <a name="helpprivacy-urls"></a>Help/Privacy-Url's

Voer de **Help-koppeling in voor uw app** (URL) waar klanten meer informatie kunnen vinden over uw aanbieding. De Help-URL mag niet gelijk zijn aan de ondersteunings-URL.

Voer de **koppeling** van het privacybeleid (URL) naar het privacybeleid van uw organisatie in. U bent verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de wetten en voor schriften van de privacy en om een geldig privacybeleid te bieden.

### <a name="contact-information"></a>Contactgegevens

Geef de naam, het e-mail adres en het telefoon nummer op voor een **ondersteunings contact** en een **technische contact persoon**. Deze informatie wordt niet weer gegeven aan klanten, maar is beschikbaar voor micro soft en kan worden verstrekt aan CSP-partners.

Geef in de sectie **ondersteunings contact op met** de **ondersteunings-URL** waar CSP-partners ondersteuning voor uw aanbieding kunnen vinden. Uw ondersteunings-URL mag niet hetzelfde zijn als uw Help-URL.

### <a name="supporting-documents"></a>Ondersteunende documenten

Geef hier ten minste één (en Maxi maal drie) gerelateerde marketing documenten op, zoals witboeken, brochures, controle lijsten of presentaties, in PDF-indeling.

### <a name="marketplace-media"></a>Media voor Marketplace

Bied logo's en installatie kopieën die worden gebruikt wanneer uw aanbieding aan klanten wordt weer gegeven. Alle installatie kopieën moeten de PNG-indeling hebben.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de door https://upload.xboxlive.com Partner Center gebruikte service niet blokkeert.

#### <a name="logos"></a>Logo's

Geef een PNG-bestand voor het logo van **grote** grootte op. In het partner centrum wordt dit gebruikt om andere vereiste grootten te maken. U kunt dit eventueel later vervangen door een andere installatie kopie.

Deze logo's worden op verschillende plaatsen in de vermelding gebruikt:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Schermopnamen

Scherm afbeeldingen toevoegen die laten zien hoe uw aanbieding werkt. Ten minste één scherm opname is vereist en u kunt Maxi maal vijf toevoegen. Alle scherm opnamen moeten 1280 x 720 pixels en in PNG-indeling zijn.

#### <a name="videos"></a>Video's

U kunt optioneel Maxi maal vier Video's toevoegen die uw aanbieding aantonen. Video's moeten worden gehost op een externe site. Voer voor elke video de naam, het adres en een miniatuur afbeelding van de video in (1280 x 720 pixels).

Zie [Best Practices for Marketplace](../gtm-offer-listing-best-practices.md)List voor aanvullende bronnen voor Marketplace-aanbiedingen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="availability"></a>Beschikbaarheid

Op deze pagina kunt u definiëren waar en hoe uw aanbieding beschikbaar moet worden gesteld.

### <a name="markets"></a>Landen

Als u de markten wilt opgeven waar uw aanbieding beschikbaar moet zijn, selecteert u **markten bewerken** om het pop-upvenster **markt selectie** weer te geven.

Selecteer ten minste één markt. Kies **Alles selecteren** om uw aanbieding beschikbaar te stellen op elke mogelijke markt of selecteer alleen de gewenste specifieke markten. Selecteer **Opslaan** wanneer u klaar bent.

Uw selecties zijn hier alleen van toepassing op nieuwe verwervingen; Als iemand uw app al in een bepaalde markt heeft en u deze markt later verwijdert, kunnen de personen die de aanbieding al op die markt hebben, deze blijven gebruiken, maar kunnen er geen nieuwe klanten op deze markt worden aangeboden.

> [!IMPORTANT]
> Het is uw verantwoordelijkheid om te voldoen aan alle lokale wettelijke vereisten, zelfs als deze vereisten hier niet worden vermeld of in het partner centrum. Zelfs als u alle markten, lokale wetten, beperkingen of andere factoren selecteert, kan het voor komen dat bepaalde aanbiedingen in sommige landen en regio's worden weer gegeven.

### <a name="preview-audience"></a>Voor beeld van doel groep

Voordat u uw aanbieding naar de bredere Marketplace-aanbieding publiceert, moet u deze eerst beschikbaar maken voor een beperkte preview- **doel groep**. Voer hier een **Hide-toets** (wille keurige teken reeks met alleen kleine letters en/of cijfers) in. Leden van uw preview-doel groep kunnen deze verbergen sleutel als een token gebruiken om een voor beeld van uw aanbieding op Marketplace te bekijken.

Wanneer u klaar bent om uw aanbieding beschikbaar te maken en de preview-beperking te verwijderen, moet u de **sleutel verbergen** verwijderen en opnieuw publiceren.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="technical-configuration"></a>Technische configuratie

Op deze pagina worden de technische details gedefinieerd die worden gebruikt om verbinding te maken met uw aanbieding. Met deze verbinding kan ons uw aanbieding voor de eind klant inrichten als deze ervoor kiezen deze te verkrijgen.

### <a name="offer-information"></a>Aanbiedings gegevens

**Basis licentie model** bepaalt hoe klanten uw toepassing toewijzen in het CRM-beheer centrum. Selecteer **resource** voor op exemplaren gebaseerde licentie verlening of **gebruiker** als er één per Tenant aan licenties wordt toegewezen.

Met het selectie vakje ' **S2S outbound en CRM Secure Store Access ' is** de configuratie van de beveiligde Store van CRM of van S2S-toegang (server-naar-server) mogelijk. Deze functie vereist een specialistische overweging van het Dynamics 365-team tijdens de certificerings periode. Micro soft neemt contact met u op om extra stappen uit te voeren om deze functie te ondersteunen.

De **URL van de toepassings configuratie** leeg laten. het is voor toekomstig gebruik.

### <a name="crm-package"></a>CRM-pakket

Voor de **URL van uw pakket locatie voert u** de URL in van een Azure Blob Storage-account dat het geüploade bestand CRM package. zip bevat. Neem een alleen-lezen SAS-sleutel op in de URL, zodat micro soft uw pakket voor verificatie kan ophalen.

> [!IMPORTANT]
> Als u een publicatie blok wilt voor komen, moet u ervoor zorgen dat de verval datum in de URL van de Blob-opslag nog niet is verlopen. U kunt de datum wijzigen door toegang tot uw beleid te krijgen. We raden u aan de **verloop tijd** ten minste één maand in de toekomst te hebben.

Selecteer de optie **Er is meer dan één CRM-pakket in het vak pakket bestand,** indien van toepassing. Als dat het geval is, moet u alle pakketten in uw zip-bestand toevoegen.

Zie [stap 3: een AppSource-pakket maken voor uw app](/powerapps/developer/common-data-service/create-package-app-appsource)voor meer informatie over het bouwen van uw pakket en het bijwerken van de structuur.

### <a name="crm-package-availability"></a>Beschik baarheid van CRM-pakket

Selecteer **+ regio toevoegen** om de geografische regio's op te geven waarin uw CRM-pakket beschikbaar is voor klanten. Implementatie in de volgende soevereine regio's vereisen speciale machtigingen en validatie tijdens het certificerings proces: [Duitsland](../../germany/index.yml), [Amerikaanse OVERHEIDS Cloud](../../azure-government/documentation-government-welcome.md)en tip.

De **configuratie-URL** voor de toepassing die u hierboven hebt opgegeven, wordt standaard gebruikt voor elke regio. Als u wilt, kunt u een afzonderlijke configuratie-URL voor de toepassing invoeren voor een of meer specifieke regio's.

Selecteer **concept opslaan** voordat u doorgaat.

<!-- ## Test drive technical configuration

This page lets you set up a demonstration ("test drive") that allows customers to try your offer before purchasing it. Learn more in [What is test drive](../what-is-test-drive.md).

To enable a test drive, select the **Enable a test drive** check box on the [Offer setup](#test-drive) tab. To remove test drive from your offer, clear this check box.

When you've finished setting up your test drive, select **Save draft** before continuing. -->

## <a name="supplemental-content"></a>Aanvullende inhoud

Op deze pagina kunt u aanvullende informatie opgeven om ons te helpen uw aanbieding te valideren. Deze informatie wordt niet weer gegeven aan klanten of gepubliceerd op Marketplace.

### <a name="key-usage-scenario"></a>Scenario voor sleutel gebruik

Upload een PDF-bestand met een lijst met belang rijke scenario's voor het gebruik van uw aanbieding. Alle scenario's kunnen door ons validatie team worden geverifieerd voordat we uw aanbieding voor Marketplace goed keuren.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="publish"></a>Publiceren

### <a name="submit-offer-to-preview"></a>Aanbieding verzenden naar Preview

Nadat u alle vereiste secties van de aanbieding hebt voltooid, selecteert u in de rechter bovenhoek van de portal **controleren en publiceren** .

Als dit de eerste keer is dat u deze aanbieding publiceert, kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding.
    - **Niet gestart** : de sectie is niet gerakend en moet worden voltooid.
    - **Onvolledig** : de sectie bevat fouten die moeten worden hersteld of waarvoor meer informatie nodig is. Ga terug naar de sectie (s) en werk deze bij.
    - **Volt ooien** : de sectie is voltooid, alle vereiste gegevens zijn opgegeven en er zijn geen fouten. Alle secties van de aanbieding moeten een volledige status hebben voordat u de aanbieding kunt indienen.
- Geef in de sectie **opmerkingen voor certificering** test instructies op voor het certificerings team om ervoor te zorgen dat uw app correct wordt getest, naast eventuele aanvullende notities die nuttig zijn voor de uitleg van uw app.
- Verzend de aanbieding voor publicatie door **verzenden** te selecteren. U ontvangt een e-mail wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Ga terug naar het partner centrum en selecteer **Go-Live** om uw aanbieding naar het publiek te publiceren.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
