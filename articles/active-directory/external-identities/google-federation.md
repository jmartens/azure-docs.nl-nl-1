---
title: Google toevoegen als een id-provider voor B2B-Azure AD
description: Met Google communiceren zodat gast gebruikers zich kunnen aanmelden bij uw Azure AD-apps met hun eigen Gmail-accounts.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53d2369e93052ef28191dd1862034c1aaa488add
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355593"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google toevoegen als een id-provider voor B2B-gast gebruikers

Door Federatie met Google in te stellen, kunt u ervoor zorgen dat uitgenodigde gebruikers zich aanmelden bij uw gedeelde apps en resources met hun eigen Gmail-accounts, zonder dat u micro soft-accounts hoeft te maken. 

> [!NOTE]
> Google Federation is speciaal ontworpen voor Gmail-gebruikers. Gebruik [directe Federatie](direct-federation.md)om met G suite-domeinen te communiceren.

> [!IMPORTANT]
> **Vanaf 4 januari 2021** wordt de [ondersteuning voor webweergave van de WEBMODULE voor webmeldingen afgemeld](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Als u gebruikmaakt van Google Federation of Self-Service-aanmelding bij Gmail, moet u [uw line-of-business-toepassingen testen voor compatibiliteit](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="what-is-the-experience-for-the-google-user"></a>Wat is de ervaring van de Google-gebruiker?
Wanneer u een uitnodiging naar Google Gmail-gebruikers verzendt, moeten de gast gebruikers toegang krijgen tot uw gedeelde apps of bronnen met behulp van een koppeling die de context van de Tenant bevat. Hun ervaring varieert afhankelijk van of ze al zijn aangemeld bij Google:
  - Gast gebruikers die zich niet hebben aangemeld bij Google, wordt gevraagd om dit te doen.
  - Gast gebruikers die al zijn aangemeld bij Google, wordt gevraagd het account te kiezen dat ze willen gebruiken. Ze moeten het account kiezen dat u hebt gebruikt om ze te nodigen.

Gast gebruikers die de fout bericht ' koptekst te lang ' zien, kunnen hun cookies wissen of een persoonlijk of incognito-venster openen en zich opnieuw aanmelden.

![Scherm opname van de Google-aanmeldings pagina.](media/google-federation/google-sign-in.png)

## <a name="deprecation-of-webview-sign-in-support"></a>Afschaffing van ondersteuning voor webweergave-aanmelding

Vanaf 4 januari 2021, wordt de [Inge sloten ondersteuning voor webweergave van de Google-service afgemeld](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Als u gebruikmaakt van Google Federation of [Self-Service-aanmelding bij Gmail](identity-providers.md), moet u uw line-of-business-toepassingen testen voor compatibiliteit. Als uw apps webweergave-inhoud bevatten waarvoor authenticatie is vereist, kunnen Google Gmail-gebruikers geen verificatie uitvoeren. Hier volgen enkele bekende scenario's die van invloed zijn op Gmail-gebruikers:

- Windows-apps die gebruikmaken van een Inge sloten webweergave of de WebAccountManager (WAM) in oudere versies van Windows.
- Andere systeem eigen apps die u hebt ontwikkeld en die gebruikmaken van een Inge sloten browser Framework voor authenticatie.

Deze wijziging heeft geen invloed op:

- Windows-apps die gebruikmaken van een Inge sloten webweergave of de WebAccountManager (WAM) op de nieuwste versies van Windows
- Micro soft iOS-apps
- G Suite-identiteiten, bijvoorbeeld wanneer u op SAML gebaseerde [directe Federatie](direct-federation.md) gebruikt met G suite

We gaan de verschillende platformen en scenario's testen en zullen dit artikel dienovereenkomstig bijwerken.
### <a name="to-test-your-apps-for-compatibility"></a>Uw Apps testen op compatibiliteit

1. Volg [de richt lijnen van Google](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) om te bepalen of dit van invloed is op uw apps.
2. Gebruik Fiddler of een ander test programma om een header tijdens het aanmelden in te voeren en een Google externe identiteit te gebruiken om het aanmelden te testen:

   1. Google-accounts-check-OAuth-login: True toevoegen aan uw HTTP-aanvraag headers wanneer de aanvragen worden verzonden naar accounts.google.com.
   1. Probeer u aan te melden bij de app door een Gmail-adres in te voeren op de aanmeldings pagina van accounts.google.com.
   1. Als het aanmelden mislukt en er een fout wordt weer geven, zoals ' deze browser of app is mogelijk niet beveiligd ', wordt het aanmelden door uw Google externe identiteit geblokkeerd.

3. Los het probleem op door een van de volgende handelingen uit te voeren:

   - Als uw Windows-app embedded webweergave of de WebAccountManager (WAM) in een oudere versie van Windows gebruikt, werkt u bij naar de nieuwste versie van Windows.
   - Wijzig uw apps om de systeem browser te gebruiken voor aanmelding. Zie [Inge sloten VS System Web UI](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) in de MSAL.net-documentatie voor meer informatie.  

## <a name="sign-in-endpoints"></a>Aanmeld-eind punten

Teams bieden volledige ondersteuning voor Google gast-gebruikers op alle apparaten. Google-gebruikers kunnen zich aanmelden bij teams vanaf een gemeen schappelijk eind punt, zoals `https://teams.microsoft.com` .

De algemene eind punten van andere toepassingen bieden mogelijk geen ondersteuning voor Google-gebruikers. Google gast gebruikers moeten zich aanmelden met behulp van een koppeling die uw Tenant gegevens bevat. Hier volgen enkele voor beelden:
  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://portal.azure.com/<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Als gebruikers van Google gast een koppeling proberen te gebruiken zoals `https://myapps.microsoft.com` of `https://portal.azure.com` , wordt er een fout melding weer geven.

U kunt Google gast gebruikers ook een rechtstreekse koppeling geven naar een toepassing of resource, mits de koppeling uw Tenant gegevens bevat. Bijvoorbeeld `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 
## <a name="step-1-configure-a-google-developer-project"></a>Stap 1: een Google-ontwikkelaars project configureren
Maak eerst een nieuw project in de Google developers-console om een client-ID en een client geheim op te halen dat u later kunt toevoegen aan Azure Active Directory (Azure AD). 
1. Ga naar de Google-Api's op https://console.developers.google.com en meld u aan met uw Google-account. We raden u aan een Google-account voor een gedeeld team te gebruiken.
2. Ga akkoord met de service voorwaarden als u hierom wordt gevraagd.
3. Een nieuw project maken: in het dash board selecteert u **project maken**, geeft u het project een naam (bijvoorbeeld **Azure AD B2B**) en selecteert u **maken**: 
   
   ![Scherm afbeelding met een nieuwe project pagina.](media/google-federation/google-new-project.png)

4. Selecteer op de pagina **api's & Services** **weer gave** onder het nieuwe project.

5. Selecteer **naar overzicht van api's** op de API-kaart. Selecteer het **venster OAuth-toestemming**.

6. Selecteer **extern** en selecteer vervolgens **maken**. 

7. Voer op het **scherm OAuth-toestemming** een **toepassings naam** in:

   ![Scherm opname van het Google OAuth-dialoog venster voor toestemming.](media/google-federation/google-oauth-consent-screen.png)

8. Ga naar de sectie **geautoriseerde domeinen** en voer **microsoftonline.com** in:

   ![Scherm afbeelding met de sectie geautoriseerde domeinen.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Selecteer **Opslaan**.

10. Selecteer **Referenties**. Selecteer in het menu **referenties maken** de optie **OAuth-client-id**:

    ![Scherm opname van het menu met Google Api's Create credentials.](media/google-federation/google-api-credentials.png)

11. Onder **toepassings type** selecteert u **Web Application**. Geef de toepassing een geschikte naam, zoals **Azure AD B2B**. Voer onder **geautoriseerde omleidings-uri's** de volgende uri's in:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(waar `<tenant ID>` is uw Tenant-id)
   
    > [!NOTE]
    > Als u uw Tenant-ID wilt vinden, gaat u naar de [Azure Portal](https://portal.azure.com). Selecteer onder **Azure Active Directory** **Eigenschappen** en kopieer de **Tenant-id**.

    ![Scherm afbeelding met de sectie geautoriseerde omleidings-Uri's.](media/google-federation/google-create-oauth-client-id.png)

12. Selecteer **Maken**. Kopieer de client-ID en het client geheim. U gebruikt deze wanneer u de ID-provider toevoegt in de Azure Portal.

    ![Scherm opname van de OAuth-client-ID en het client geheim.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Stap 2: Google Federation configureren in azure AD 
U gaat nu de Google-client-ID en het client geheim instellen. U kunt hiervoor de Azure Portal of Power shell gebruiken. Zorg ervoor dat u uw Google Federation-configuratie test door uzelf uit te nodigen. Gebruik een Gmail-adres en probeer de uitnodiging te inwisselen met uw aangenodigene Google-account. 

**Google Federation configureren in de Azure Portal** 
1. Ga naar [Azure Portal](https://portal.azure.com). Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **externe identiteiten**.
3. Selecteer **alle id-providers** en selecteer vervolgens de knop **Google** .
4. Voer de client-ID en het client geheim in die u eerder hebt verkregen. Selecteer **Opslaan**: 

   ![Scherm opname van de pagina Google-ID-provider toevoegen.](media/google-federation/google-identity-provider.png)

**Google Federation configureren met behulp van Power shell**
1. Installeer de nieuwste versie van de Azure AD Power shell for Graph-module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Voer deze opdracht uit: `Connect-AzureAD`
3. Meld u bij de aanmeldings prompt aan met het Managed Global Administrator-account.  
4. Voer de volgende opdracht uit: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Gebruik de client-ID en het client geheim van de app die u hebt gemaakt in ' stap 1: een Google-ontwikkelaars project configureren '. Zie [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview)voor meer informatie. 
 
## <a name="how-do-i-remove-google-federation"></a>Google Federation Hoe kan ik verwijderen?
U kunt de installatie van Google Federation verwijderen. Als u dit doet, kunnen Google gast-gebruikers die hun uitnodiging al hebben ingewisseld, zich niet meer aanmelden. Maar u kunt ze ook weer toegang geven tot uw resources door ze te verwijderen uit de map en ze opnieuw uit te nodigen. 
 
**Google Federation verwijderen in de Azure AD-Portal**
1. Ga naar [Azure Portal](https://portal.azure.com). Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **externe identiteiten**.
3. Selecteer **alle id-providers**.
4. Selecteer op de **Google** -regel de knop met het weglatings teken (**...**) en selecteer vervolgens **verwijderen**. 
   
   ![Scherm afbeelding met de knop verwijderen voor de sociale ID-provider.](media/google-federation/google-social-identity-providers.png)

1. Selecteer **Ja** om de verwijdering te bevestigen. 

**Google Federation verwijderen met behulp van Power shell** 
1. Installeer de nieuwste versie van de Azure AD Power shell for Graph-module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Voer `Connect-AzureAD` uit.  
4. Meld u bij de aanmeldings prompt aan met het Managed Global Administrator-account.  
5. Voer de volgende opdracht in:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Zie [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)voor meer informatie.
