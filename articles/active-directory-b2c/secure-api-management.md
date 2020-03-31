---
title: Een Azure API Management API beveiligen met Azure Active Directory B2C
description: Meer informatie over het gebruik van toegangstokens die zijn uitgegeven door Azure Active Directory B2C om een Azure API Management API-eindpunt te beveiligen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 00938d831e70289b24acb599b81016aa6e564d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186927"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Een Azure API Management API beveiligen met Azure AD B2C

Meer informatie over hoe u de toegang tot uw Azure API (APIM)-API beperken tot clients die zijn geverifieerd met Azure Active Directory B2C (Azure AD B2C). Volg de stappen in dit artikel om een binnenkomend beleid in APIM te maken en te testen dat de toegang beperkt tot alleen die aanvragen die een geldig Azure AD B2C-uitgegeven toegangstoken bevatten.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende bronnen nodig voordat u verdergaat met de stappen in dit artikel:

* [Azure AD B2C-tenant](tutorial-create-tenant.md)
* [Aanvraag geregistreerd](tutorial-register-applications.md) in uw tenant
* [Gebruikersstromen die](tutorial-create-user-flows.md) in uw tenant zijn gemaakt
* [Gepubliceerde API](../api-management/import-and-publish.md) in Azure API-beheer
* [Postbode](https://www.getpostman.com/) om beveiligde toegang te testen (optioneel)

## <a name="get-azure-ad-b2c-application-id"></a>Azure AD B2C-toepassings-id

Wanneer u een API in Azure API Management beveiligt met Azure AD B2C, hebt u verschillende waarden nodig voor het [inkomende beleid](../api-management/api-management-howto-policies.md) dat u in APIM maakt. Neem eerst de toepassings-id op van een toepassing die u eerder hebt gemaakt in uw Azure AD B2C-tenant. Als u de toepassing gebruikt die u in de vereisten hebt gemaakt, gebruikt u de toepassings-id voor *webbapp1*.

U de huidige **toepassingservaring** of onze nieuwe uniforme **App-registratie (Preview)-ervaring** gebruiken om de toepassings-ID te krijgen. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **Toepassingen** **onder Beheren**.
1. Noteer de waarde in de kolom **TOEPASSINGS-ID** voor *webapp1* of een andere toepassing die u eerder hebt gemaakt.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **App-registraties (Voorbeeld)** en selecteer vervolgens het tabblad **Bezeten toepassingen.**
1. Noteer de waarde in de kolom **Toepassings-id** voor *webapp1* of een andere toepassing die u eerder hebt gemaakt.

* * *

## <a name="get-token-issuer-endpoint"></a>Eindpunt van tokenuitgevende instellingen

Download vervolgens de bekende config-URL voor een van uw Azure AD B2C-gebruikersstromen. U hebt ook het eindpunt URI van de tokenuitgever nodig dat u wilt ondersteunen in Azure API Management.

1. Blader naar uw Azure AD B2C-tenant in de [Azure-portal.](https://portal.azure.com)
1. Selecteer **onder Beleid**de optie **Gebruikersstromen (beleidsregels)**.
1. Selecteer een bestaand beleid, *bijvoorbeeld B2C_1_signupsignin1*, selecteer Vervolgens **Gebruikersstroom uitvoeren**.
1. Neem de URL op in hyperlink die wordt weergegeven onder de kop **Gebruikersstroom uitvoeren** boven aan de pagina. Deze URL is het bekende detectieeindpunt van OpenID Connect voor de gebruikersstroom en u gebruikt deze in de volgende sectie wanneer u het inkomende beleid configureert in Azure API Management.

    ![Bekende URI-hyperlink op de nu-pagina uitvoeren van de Azure-portal](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Selecteer de hyperlink om naar de bekende configuratiepagina van OpenID Connect te bladeren.
1. Neem op de pagina die in `issuer` uw browser wordt geopend de waarde op, bijvoorbeeld:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    U gebruikt deze waarde in de volgende sectie wanneer u uw API configureert in Azure API Management.

U moet nu twee URL's hebben die zijn geregistreerd voor gebruik in de volgende sectie: de openid connect bekende configuratie-eindpunt-URL en de uitgever URI. Bijvoorbeeld:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Binnenkomend beleid configureren in Azure API-beheer

U bent nu klaar om het inkomende beleid toe te voegen aan Azure API Management dat API-aanroepen valideert. Door een [JWT-validatiebeleid](../api-management/api-management-access-restriction-policies.md#ValidateJWT) toe te voegen dat de doelgroep en de uitgever verifieert in een toegangstoken, u ervoor zorgen dat alleen API-aanroepen met een geldig token worden geaccepteerd.

1. Blader naar uw Azure API Management-exemplaar in de [Azure-portal.](https://portal.azure.com)
1. Selecteer **API's**.
1. Selecteer de API die u wilt beveiligen met Azure AD B2C.
1. Selecteer het tabblad **Ontwerpen**.
1. Selecteer **onder Binnenkomende verwerking**om ** \< / ** de beleidscodeeditor te openen.
1. Plaats de `<validate-jwt>` volgende `<inbound>` tag in het beleid.

    1. Werk `url` de waarde `<openid-config>` in het element bij met de bekende configuratie-URL van uw beleid.
    1. Werk `<audience>` het element bij met toepassings-id van de toepassing die u eerder in uw B2C-tenant hebt gemaakt (bijvoorbeeld *webapp1).*
    1. Werk `<issuer>` het element bij met het eindpunt van de tokenuitgever dat u eerder hebt opgenomen.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Beveiligde API-toegang valideren

Om ervoor te zorgen dat alleen geverifieerde bellers toegang hebben tot uw API, u uw Azure API Management-configuratie valideren door de API aan te roepen met [Postman.](https://www.getpostman.com/)

Als u de API wilt aanroepen, hebt u zowel een toegangstoken nodig dat is uitgegeven door Azure AD B2C als een APIM-abonnementssleutel.

### <a name="get-an-access-token"></a>Een toegangstoken opvragen

U hebt eerst een token nodig dat is `Authorization` uitgegeven door Azure AD B2C om te gebruiken in de koptekst in Postman. U er een krijgen door de run **now-functie** van uw aanmeldings-/aanmeldingsgebruikersstroom te gebruiken die u als een van de vereisten had moeten maken.

1. Blader naar uw Azure AD B2C-tenant in de [Azure-portal.](https://portal.azure.com)
1. Selecteer **onder Beleid**de optie **Gebruikersstromen (beleidsregels)**.
1. Selecteer een bestaande gebruikersstroom voor aanmelding/aanmelding, bijvoorbeeld *B2C_1_signupsignin1*.
1. Selecteer voor **toepassing** *webapp1*.
1. Kies `https://jwt.ms` **voor de URL van antwoord**.
1. Selecteer **Gebruikersstroom uitvoeren**.

    ![Gebruikersstroompagina uitvoeren voor aanmeldingsaanmelden in gebruikersstroom in Azure-portal](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Voltooi het aanmeldingsproces. Je moet omgeleid `https://jwt.ms`worden naar.
1. Neem de gecodeerde tokenwaarde op die in uw browser wordt weergegeven. U gebruikt deze tokenwaarde voor de kopautorisatie in Postman.

    ![Gecodeerde tokenwaarde weergegeven op jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>API-abonnementssleutel aanschaffen

Een clienttoepassing (in dit geval Postman) die een gepubliceerde API aanroept, moet een geldige API Management-abonnementssleutel bevatten in de HTTP-aanvragen voor de API. Ga als bedoeld als lidop neem je een abonnementssleutel op in je HTTP-verzoek van de postbode:

1. Blader naar de instantie azure API Management-service in de [Azure-portal.](https://portal.azure.com)
1. Selecteer **Abonnementen**.
1. Selecteer de ellips voor **Product: Onbeperkt**en selecteer **Toetsen weergeven/verbergen**.
1. Neem de **primaire sleutel** voor het product op. U gebruikt deze `Ocp-Apim-Subscription-Key` sleutel voor de koptekst in uw HTTP-aanvraag in Postman.

![Pagina met abonnementssleutel met Toon-/verbergtoetsen geselecteerd in Azure-portal](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Een veilige API-aanroep testen

Nu de toegangstoken en apim-abonnementssleutel zijn geregistreerd, u nu testen of u de beveiligde toegang tot de API correct hebt geconfigureerd.

1. Een nieuw `GET` verzoek maken in [Postman](https://www.getpostman.com/). Geef voor de URL van de aanvraag het eindpunt van de lijst met luidsprekers op van de API die u hebt gepubliceerd als een van de vereisten. Bijvoorbeeld:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Voeg vervolgens de volgende kopteksten toe:

    | Sleutel | Waarde |
    | --- | ----- |
    | `Authorization` | Gecodeerde tokenwaarde waarmee u eerder hebt `Bearer ` geregistreerd, vooraf vastgezet (inclusief de ruimte na 'Drager') |
    | `Ocp-Apim-Subscription-Key` | APIM-abonnementssleutel die u eerder hebt opgenomen |

    Uw URL en **kopteksten** **met GET-aanvragen** moeten vergelijkbaar lijken met:

    ![Gebruikersinterface voor postbodes met de URL en kopteksten van de GET-aanvraag](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Selecteer de knop **Verzenden** in Postman om het verzoek uit te voeren. Als u alles correct hebt geconfigureerd, moet u een JSON-antwoord krijgen met een verzameling conferentiesprekers (hier afgekapt):

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Een onveilige API-aanroep testen

Nu u een succesvol verzoek hebt ingediend, test u de foutaanvraag om ervoor te zorgen dat oproepen naar uw API met een *ongeldig* token worden geweigerd zoals verwacht. Een manier om de test uit te voeren is door een paar `GET` tekens toe te voegen of te wijzigen in de tokenwaarde en vervolgens dezelfde aanvraag uit te voeren als voorheen.

1. Voeg meerdere tekens toe aan de tokenwaarde om een ongeldig token te simuleren. Voeg bijvoorbeeld 'ONGELDIG' toe aan de tokenwaarde:

    ![Headers sectie van Postman UI met ONGELDIG toegevoegd aan token](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Selecteer de knop **Verzenden** om het verzoek uit te voeren. Met een ongeldig token is `401` het verwachte resultaat een ongeautoriseerde statuscode:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Als u `401` de statuscode ziet, hebt u geverifieerd dat alleen bellers met een geldig toegangstoken dat is uitgegeven door Azure AD B2C, met succes uw Azure API API kunnen aanvragen.

## <a name="support-multiple-applications-and-issuers"></a>Meerdere toepassingen en emittenten ondersteunen

Verschillende toepassingen werken meestal samen met één REST-API. Als u uw API wilt inschakelen om tokens te accepteren die `<audiences>` bedoeld zijn voor meerdere toepassingen, voegt u hun toepassings-id's toe aan het element in het Inbound-beleid van APIM.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Als u meerdere token-emittenten wilt ondersteunen, voegt `<issuers>` u ook hun eindpunt-URI's toe aan het element in het Inbound-beleid van APIM.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migreren naar b2clogin.com

Als u een APIM-API hebt die `login.microsoftonline.com` tokens valideert die zijn uitgegeven door het verouderde eindpunt, moet u de API migreren en de toepassingen die deze aanroepen om tokens te gebruiken die zijn uitgegeven door [b2clogin.com](b2clogin.md).

U dit algemene proces volgen om een gefaseerde migratie uit te voeren:

1. Voeg ondersteuning toe aan uw APIM-inkomende beleid voor tokens die zijn uitgegeven door zowel b2clogin.com als login.microsoftonline.com.
1. Werk uw toepassingen één voor één bij om tokens te verkrijgen van het b2clogin.com eindpunt.
1. Zodra al uw toepassingen correct tokens van b2clogin.com verkrijgen, verwijdert u ondersteuning voor login.microsoftonline.com uitgegeven tokens uit de API.

In het volgende voorbeeld apim-inbound-beleid wordt uitgelegd hoe tokens die zijn uitgegeven door zowel b2clogin.com als login.microsoftonline.com kunnen worden geaccepteerd. Daarnaast ondersteunt het API-aanvragen van twee toepassingen.

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Volgende stappen

Zie de [APIM-beleidsreferentie-index](../api-management/api-management-policies.md)voor meer informatie over azure API-beheerbeleid.

U informatie vinden over het migreren van OWIN-gebaseerde webAPI's en hun toepassingen om te b2clogin.com in [Een OWIN-gebaseerde web-API migreren naar b2clogin.com](multiple-token-endpoints.md).
