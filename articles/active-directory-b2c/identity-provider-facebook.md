---
title: Registratie instellen en aanmelden met een Facebook-account
titleSuffix: Azure AD B2C
description: Bied u de mogelijkheid om u aan te melden bij klanten met Facebook-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 37fc33ae8084a2b4e99e7b5dc417eac70060eef5
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516200"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een Facebook-account met Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Een Facebook-toepassing maken

Als u een Facebook-account wilt gebruiken als een [ID-provider](authorization-code-flow.md) in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw Tenant waarmee deze wordt vertegenwoordigd. Als u nog geen Facebook-account hebt, kunt u zich aanmelden bij [https://www.facebook.com/](https://www.facebook.com/) .

1. Meld u aan bij [Facebook for developers](https://developers.facebook.com/) (Facebook voor ontwikkelaars) met de referenties van uw Facebook-account.
1. Als u dit nog niet hebt gedaan, moet u zich registreren als Facebook-ontwikkelaar. Als u dit wilt doen, selecteert u **Get Started** in de rechter bovenhoek van de pagina, accepteert u het beleid van Facebook en voert u de registratiestappen uit.
1. Selecteer **My Apps** en vervolgens **Create App**.
1. Selecteer **Build Connected experiences**.
1. Voer een **weergavenaam** en een geldig **e-mailadres van de contactpersoon** in.
1. Selecteer **App-ID maken**. Hiervoor moet u mogelijk het beleid voor het Facebook-platform accepteren en een onlinebeveiligingscontrole uitvoeren.
1. Selecteer **Settings** > **Basic**.
    1. Kies een **categorie**, bijvoorbeeld `Business and Pages`. Deze waarde is vereist voor Facebook, maar wordt niet gebruikt voor Azure AD B2C.
    1. Voer een URL in voor de voor **waarden van de service-URL**, bijvoorbeeld `http://www.contoso.com/tos` . De beleids-URL is een pagina die u onderhoudt om voor waarden voor uw toepassing te bieden.
    1. Voer een URL in voor **Privacy Policy URL**, bijvoorbeeld `http://www.contoso.com/privacy`. De beleids-URL is een pagina die u kunt onderhouden voor het verstrekken van privacy-informatie voor uw toepassing.
1. Selecteer onder aan de pagina **Add platform** en selecteer vervolgens **Website**.
1. Voer in **site-URL** het adres van uw website in, bijvoorbeeld `https://contoso.com` . 
1. Selecteer **Save changes**.
1. Kopieer de waarde van de **App-ID** aan de bovenkant van de pagina.
1. Selecteer **weer geven** en kopieer de waarde van het **app-geheim**. U kunt beide gebruiken om Facebook te configureren als een id-provider in uw Tenant. **App-geheim** is een belang rijke beveiligings referentie.
1. Selecteer in het menu het **plus** teken naast **producten**. Selecteer in het gedeelte **producten aan uw app toevoegen** de optie **instellen** onder **Facebook-aanmelding**.
1. Selecteer in het menu de optie **Facebook-aanmelding** en selecteer **instellingen**.
1. Voer in **Valid OAuth redirect URIs** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` in. Vervang `your-tenant-name` door de naam van uw tenant. Selecteer **Save changes** onder aan de pagina.
1. Als u uw Facebook-toepassing beschikbaar wilt maken voor Azure AD B2C, selecteert u in de rechter bovenhoek van de pagina de status kiezer en schakelt u deze **in** om de toepassing openbaar te maken en vervolgens **Switch modus** te selecteren.  Op dit moment moet de status worden gewijzigd van **Development** in **Live**.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Een Facebook-account configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Id-providers** en vervolgens **Facebook**.
1. Voer een **naam** in. Bijvoorbeeld *Facebook*.
1. Voer voor **Client-id** de app-id in van de Facebook-toepassing die u eerder hebt gemaakt.
1. Voer voor **Clientgeheim** het app-geheim in dat u hebt genoteerd.
1. Selecteer **Opslaan**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-facebook-as-an-identity-provider"></a>Facebook toevoegen als een id-provider

1. Vervang in het `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** bestand de waarde van `client_id` met de id van de Facebook-toepassing:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>Facebook-ID-provider toevoegen aan een gebruikers stroom 

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom die u wilt van de Facebook-ID-provider.
1. Selecteer in de **providers voor sociale identificatie** de optie **Facebook**.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **gebruikers stroom uitvoeren**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="upload-and-test-the-policy"></a>Het beleid uploaden en testen

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Upload het *TrustFrameworkExtensions.xml* bestand naar uw Tenant.
1. Selecteer **B2C_1A_signup_signin** onder **aangepast beleid**.
1. Selecteer voor **Select-toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer **nu uitvoeren** en selecteer Facebook om u aan te melden met Facebook en het aangepaste beleid te testen.

::: zone-end

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [door geven van Facebook-tokens aan uw toepassing](idp-pass-through-user-flow.md).
