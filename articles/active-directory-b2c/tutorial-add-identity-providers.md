---
title: 'Zelfstudie: identiteitsproviders toevoegen aan uw apps'
titleSuffix: Azure AD B2C
description: Meer informatie over het toevoegen van identiteitsproviders aan uw toepassingen in Azure Active Directory B2C met behulp van de Azure-portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f49061210ca8e3c106b0569f77a67d1f10757a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183513"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Zelfstudie: identiteitsproviders toevoegen aan uw toepassingen in Azure Active Directory B2C

Het is wellicht wenselijk dat uw gebruikers zich in uw toepassingen bij verschillende id-providers kunnen aanmelden. Een *id-provider* maakt, onderhoudt en beheert id-gegevens en biedt tegelijkertijd verificatieservices voor toepassingen. U identiteitsproviders die worden ondersteund door Azure Active Directory B2C (Azure AD B2C) toevoegen aan uw [gebruikersstromen](user-flow-overview.md) met behulp van de Azure-portal.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * De toepassingen van de identiteitsprovider maken
> * De identiteitsproviders toevoegen aan uw tenant
> * De identiteitsproviders toevoegen aan uw gebruikersstroom

U gebruikt doorgaans slechts één identiteitsprovider in uw toepassingen, maar u hebt de optie om meer toe te voegen. In deze zelfstudie ziet u hoe u een Azure AD-identiteitsprovider en een Facebook-identiteitsprovider aan uw toepassing toevoegt. Het toevoegen van beide identiteitsproviders aan uw toepassing is optioneel. U ook andere identiteitsproviders toevoegen, zoals [Amazon,](identity-provider-amazon.md) [GitHub,](identity-provider-github.md) [Google,](identity-provider-google.md) [LinkedIn,](identity-provider-linkedin.md) [Microsoft](identity-provider-microsoft-account.md)of [Twitter.](identity-provider-twitter.md)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

[Maak een gebruikersstroom](tutorial-create-user-flows.md) zodat gebruikers zich kunnen aanmelden en zich kunnen aanmelden bij uw toepassing.

## <a name="create-applications"></a>Toepassingen maken

Gebruikerstoepassingen voor identiteitsservers bieden de id en sleutel om communicatie met uw Azure AD B2C-tenant mogelijk te maken. In dit gedeelte van de zelfstudie maakt u een Azure AD-toepassing en een Facebook-toepassing waaruit u id's en sleutels krijgt om de identiteitsproviders aan uw tenant toe te voegen. Als u slechts één van de identiteitsproviders toevoegt, hoeft u alleen de toepassing voor die provider te maken.

### <a name="create-an-azure-active-directory-application"></a>Een Azure Active Directory-toepassing maken

Als u aanmelding wilt inschakelen voor gebruikers van Azure AD, moet u een toepassing registreren binnen de Azure AD-tenant. De Azure AD-tenant is niet hetzelfde als uw Azure AD B2C-tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw Azure AD-tenant bevat.
1. Kies **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer **app-registraties**.
1. Selecteer **Nieuwe registratie**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld `Azure AD B2C App`.
1. Accepteer de selectie van **accounts in deze organisatiemap alleen** voor deze toepassing.
1. Accepteer voor de **URI-omleiding**de waarde van **web** en voer de `your-B2C-tenant-name` volgende URL in alle kleine letters in, vervangen door de naam van uw Azure AD B2C-tenant.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Bijvoorbeeld `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Alle URL's moeten nu [b2clogin.com](b2clogin.md)gebruiken.

1. Selecteer **Registreren**en noteer vervolgens de **id van toepassing (client)** die u in een latere stap gebruikt.
1. Selecteer **onder Beheren** in het toepassingsmenu Certificaten & **geheimen**en selecteer Vervolgens **Nieuw clientgeheim**.
1. Voer een **beschrijving** in voor het klantgeheim. Bijvoorbeeld `Azure AD B2C App Secret`.
1. Selecteer de vervaldatum. Voor deze aanvraag, accepteer de selectie van **In 1 jaar**.
1. Selecteer **Toevoegen**en noteer vervolgens de waarde van het nieuwe clientgeheim dat u in een latere stap gebruikt.

### <a name="create-a-facebook-application"></a>Een Facebook-toepassing maken

Als u een Facebook-account wilt gebruiken als identiteitsprovider in Azure AD B2C, moet u een toepassing maken op Facebook. Als je nog geen Facebook-account hebt, kun [https://www.facebook.com/](https://www.facebook.com/)je het krijgen op .

1. Meld je aan bij [Facebook voor ontwikkelaars](https://developers.facebook.com/) met je Facebook-accountreferenties.
1. Als je dit nog niet hebt gedaan, moet je je registreren als Facebook-ontwikkelaar. Selecteer hiervoor **aan** de slag in de rechterbovenhoek van de pagina, accepteer het beleid van Facebook en voltooi t.a.v. De registratiestappen.
1. Selecteer **Mijn apps** en maak vervolgens **App**.
1. Voer een **weergavenaam** en een geldige **e-mail met contactpersonen in**.
1. Klik **op App-id maken**. Dit kan vereisen dat je het beleid van het Facebook-platform accepteert en een online beveiligingscontrole voltooit.
1. Selecteer **Basisinstellingen** > **Basic**.
1. Kies bijvoorbeeld `Business and Pages`een **rubriek.** Deze waarde is vereist door Facebook, maar wordt niet gebruikt door Azure AD B2C.
1. Selecteer onder aan de pagina **Platform toevoegen**en selecteer **Vervolgens Website**.
1. Voer in **site-URL**de `https://your-tenant-name.b2clogin.com/` vervanging `your-tenant-name` in met de naam van uw tenant.
1. Voer bijvoorbeeld `http://www.contoso.com/`een URL in voor de **URL van het privacybeleid**. De URL van het privacybeleid is een pagina die u bijhoudt om privacygegevens voor uw toepassing te verstrekken.
1. Selecteer **Save changes**.
1. Noteer boven aan de pagina de waarde van **app-id**.
1. Selecteer naast **App Secret**de optie De waarde **weergeven** en registreren. Je gebruikt zowel de app-id als het app-geheim om Facebook te configureren als identiteitsprovider in je tenant. **App Secret** is een belangrijke beveiligingsreferentie die u veilig moet opslaan.
1. Selecteer het plusteken naast **PRODUCTEN**en selecteer vervolgens onder **Facebook-aanmelding**de optie **Instellen**.
1. Selecteer **Instellingen**in het linkermenu onder **Facebook-aanmelding** in het linkermenu.
1. Voer in **Valid OAuth redirect URI's**in `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Vervang `your-tenant-name` door de naam van uw huurder. Selecteer Wijzigingen onder aan de pagina **opslaan.**
1. Als u uw Facebook-toepassing beschikbaar wilt maken voor Azure AD B2C, klikt u op de **statuskiezer** rechtsboven op de pagina en schakelt u deze **in** om de toepassing openbaar te maken en klikt u vervolgens op **Bevestigen**. Op dit punt moet de status veranderen van **ontwikkeling** naar **Live.**

## <a name="add-the-identity-providers"></a>De identiteitsproviders toevoegen

Nadat u de toepassing hebt gemaakt voor de identiteitsprovider die u wilt toevoegen, voegt u de identiteitsprovider toe aan uw tenant.

### <a name="add-the-azure-active-directory-identity-provider"></a>De Azure Active Directory-identiteitsprovider toevoegen

1. Zorg ervoor dat u de map gebruikt die Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw Azure AD B2C-tenant.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Identiteitsproviders**en selecteer **vervolgens Nieuwe OpenID Connect-provider**.
1. Voer een **naam**in . Typ bijvoorbeeld *Contoso Azure AD*.
1. Voer voor de url met `your-AD-tenant-domain` **aagegevens**de volgende URL in die wordt vervangen door de domeinnaam van uw Azure AD-tenant:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Bijvoorbeeld `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Voer **voor client-id**de toepassings-id in die u eerder hebt opgenomen.
1. Voer **voor clientgeheim**het clientgeheim in dat u eerder hebt opgenomen.
1. Laat de standaardwaarden voor **bereik,** **antwoordtype**en **reactiemodus .**
1. (Optioneel) Voer een waarde in voor **Domain_hint**. Bijvoorbeeld *ContosoAD*. [Domeinhints](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) zijn richtlijnen die zijn opgenomen in het verificatieverzoek van een toepassing. Ze kunnen worden gebruikt om de gebruiker te versnellen naar hun federatieve IdP aanmeldingspagina. Of ze kunnen worden gebruikt door een multi-tenant toepassing om de gebruiker te versnellen rechtstreeks naar de branded Azure AD aanmeldingspagina voor hun tenant.
1. Voer **onder Het toewijzen van claims voor identiteitsprovider**de volgende waarden voor het toewijzen van claims in:

    * **Gebruikersnaam**: *oid*
    * **Weergavenaam**: *naam*
    * **Voornaam**: *given_name*
    * **Achternaam**: *family_name*
    * **E-mail**: *unique_name*

1. Selecteer **Opslaan**.

### <a name="add-the-facebook-identity-provider"></a>De Facebook-identiteitsprovider toevoegen

1. Selecteer **identiteitsproviders**en selecteer **Vervolgens Facebook**.
1. Voer een **naam**in . Bijvoorbeeld, *Facebook*.
1. Voer voor de **client-id**de app-id in van de Facebook-toepassing die u eerder hebt gemaakt.
1. Voer **voor**het geheim van de client het app-geheim in dat u hebt opgenomen.
1. Selecteer **Opslaan**.

## <a name="update-the-user-flow"></a>De gebruikersstroom bijwerken

In de zelfstudie die u hebt voltooid als onderdeel van de vereisten, hebt u een gebruikersstroom gemaakt voor aanmelding en aanmelding met de naam *B2C_1_signupsignin1*. In deze sectie voegt u de identiteitsproviders toe aan de *B2C_1_signupsignin1* gebruikersstroom.

1. Selecteer **Gebruikersstromen (beleidsregels)** en selecteer vervolgens de *B2C_1_signupsignin1* gebruikersstroom.
2. Selecteer **Identiteitsproviders**, selecteer de **Facebook-** en **Contoso Azure AD-identiteitsproviders** die u hebt toegevoegd.
3. Selecteer **Opslaan**.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer op de pagina Overzicht van de gebruikersstroom die u hebt gemaakt de optie **Gebruikersstroom uitvoeren**.
1. Selecteer **voor Toepassing**de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De URL van `https://jwt.ms`het **antwoord** moet worden weergegeven .
1. Selecteer **Gebruikersstroom uitvoeren**en meld u aan bij een identiteitsprovider die u eerder hebt toegevoegd.
1. Herhaal stap 1 tot en met 3 voor de andere identiteitsproviders die u hebt toegevoegd.

Als de aanmeldingsbewerking is geslaagd, `https://jwt.ms` wordt u omgeleid naar welke degecodeerde token wordt weergegeven, vergelijkbaar met:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * De toepassingen van de identiteitsprovider maken
> * De identiteitsproviders toevoegen aan uw tenant
> * De identiteitsproviders toevoegen aan uw gebruikersstroom

Lees vervolgens hoe u de gebruikersinterface van de pagina's die aan gebruikers worden weergegeven als onderdeel van hun identiteitservaring in uw toepassingen, aanpassen:

> [!div class="nextstepaction"]
> [De gebruikersinterface van uw toepassingen aanpassen in Azure Active Directory B2C](tutorial-customize-ui.md)
