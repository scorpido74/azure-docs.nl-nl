---
title: Een API beveiligen met OAuth 2.0 met AAD en API-beheer
titleSuffix: Azure API Management
description: Meer informatie over het beveiligen van een back-end van een web-API met Azure Active Directory en API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 8b396b782c1254b3229aeeb8e51b61cc744d6318
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190370"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Een API beveiligen met behulp van OAuth 2.0 met Azure Active Directory en API Management

In deze handleiding ziet u hoe u uw Azure API Management-exemplaar configureert om een API te beschermen, met behulp van het OAuth 2.0-protocol met Azure Active Directory (Azure AD). 

> [!NOTE]
> Deze functie is beschikbaar in **developer,** **standard** en **premium** lagen API Management.

## <a name="prerequisites"></a>Vereisten
Als u de stappen in dit artikel wilt volgen, moet u het volgende hebben:
* Een API-beheerexemplaar
* Een API die wordt gepubliceerd en die de instantie API-beheer gebruikt
* Een Azure AD-tenant

## <a name="overview"></a>Overzicht

Hier is een snel overzicht van de stappen:

1. Registreer een toepassing (backend-app) in Azure AD om de API weer te geven.
2. Registreer een andere toepassing (client-app) in Azure AD om een clienttoepassing weer te geven die de API moet aanroepen.
3. Geef in Azure AD machtigingen toe om de client-app toe te staan de backend-app te bellen.
4. Configureer de ontwikkelaarsconsole om de API aan te roepen met oauth 2.0-gebruikersautorisatie.
5. Voeg het **validate-jwt-beleid** toe om het OAuth-token voor elke binnenkomende aanvraag te valideren.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Een toepassing registreren in Azure AD om de API weer te geven

Als u een API wilt beveiligen met Azure AD, is de eerste stap het registreren van een toepassing in Azure AD die de API vertegenwoordigt. 

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw toepassing te registreren. **App-registraties**zoeken en selecteren .

1. Selecteer **Nieuwe registratie**. 

1. Wanneer de **pagina Een aanvraag registreren** wordt weergegeven, voert u de registratiegegevens van uw aanvraag in: 
    - Voer in de sectie **Naam** een betekenisvolle toepassingsnaam in die wordt weergegeven aan gebruikers van de app, zoals *backend-app.* 
    - Selecteer in de sectie **Ondersteunde accounttypen** een optie die bij uw scenario past. 

1. Laat de sectie **REDIRECT URI** leeg.

1. Selecteer **Registreren** om de toepassing te maken. 

1. Zoek op de pagina **Overzicht** van de app de waarde van de **id-toepassing (client)** en leg deze vast voor later.

1. Selecteer **Een API blootleggen** en stel de **URI van de toepassings-id** in met de standaardwaarde. Noteer deze waarde voor later.

1. Selecteer de knop **Een bereik toevoegen** om de pagina Een bereik **toevoegen** weer te geven. Maak vervolgens een nieuw bereik dat wordt ondersteund door `Files.Read`de API (bijvoorbeeld). Selecteer ten slotte de knop **Bereik toevoegen** om het bereik te maken. Herhaal deze stap om alle scopes toe te voegen die door uw API worden ondersteund.

1. Wanneer de scopes worden gemaakt, noteer dan een notitie voor gebruik in een volgende stap. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Een andere toepassing registreren in Azure AD om een clienttoepassing weer te geven

Elke clienttoepassing die de API aanroept, moet ook worden geregistreerd als een toepassing in Azure AD. In dit voorbeeld is de clienttoepassing de Ontwikkelaarsconsole in de api-beheerontwikkelaarsportal. U als u een andere toepassing in Azure AD registreren om de ontwikkelaarsconsole weer te geven.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw toepassing te registreren. **App-registraties**zoeken en selecteren .

1. Selecteer **Nieuwe registratie**.

1. Wanneer de **pagina Een aanvraag registreren** wordt weergegeven, voert u de registratiegegevens van uw aanvraag in: 
    - Voer in de sectie **Naam** een betekenisvolle toepassingsnaam in die wordt weergegeven aan gebruikers van de app, zoals *client-app.* 
    - Selecteer **accounts in een organisatiemap (Azure AD-map - Multitenant) in**de sectie Ondersteunde **accounttypen.** 

1. Selecteer en voer in `Web` de sectie `https://contoso5.portal.azure-api.net/signin` **URI omleiden** de URL in en voer deze in.

1. Selecteer **Registreren** om de toepassing te maken. 

1. Zoek op de pagina **Overzicht** van de app de waarde van de **id-toepassing (client)** en leg deze vast voor later.

Maak nu een clientgeheim voor deze toepassing die in een volgende stap kan worden gebruikt.

1. Selecteer **certificaten & geheimen**in de lijst met pagina's voor uw client-app en selecteer Nieuw **clientgeheim**.

1. Geef **onder Een clientgeheim toevoegen**een beschrijving **op**. Kies wanneer de sleutel moet verlopen en selecteer **Toevoegen**.

Wanneer het geheim wordt gemaakt, noteert u de sleutelwaarde voor gebruik in een volgende stap. 

## <a name="grant-permissions-in-azure-ad"></a>Machtigingen verlenen in Azure AD

Nu u twee toepassingen hebt geregistreerd om de API en de Ontwikkelaarsconsole weer te geven, moet u machtigingen verlenen om de client-app toe te staan de backend-app te bellen.  

1. Ga naar de [Azure-portal](https://portal.azure.com) om machtigingen toe te kennen aan uw clienttoepassing. **App-registraties**zoeken en selecteren .

1. Kies uw client-app. Selecteer vervolgens in de lijst met pagina's voor de app **API-machtigingen**.

1. Selecteer **Een machtiging toevoegen**.

1. Selecteer **onder Een API selecteren**de optie Mijn **API's**en zoek en selecteer vervolgens uw backend-app.

1. Selecteer **onder Gedelegeerde machtigingen**de juiste machtigingen voor uw backend-app en selecteer Machtigingen **toevoegen**.

1. Selecteer optioneel op de pagina **API-machtigingen** de **optie Toestemming voor beheerders verlenen \<voor uw tenantnaam>** om namens alle gebruikers in deze directory toestemming te verlenen. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>OAuth 2.0-gebruikersautorisatie inschakelen in de ontwikkelaarsconsole

Op dit moment hebt u uw toepassingen in Azure AD gemaakt en hebt u de juiste machtigingen verleend om de client-app toe te staan de backend-app te bellen. 

In dit voorbeeld is de Developer Console de client-app. In de volgende stappen wordt beschreven hoe u de gebruikersautorisatie van OAuth 2.0 inschakelt in de ontwikkelaarsconsole. 

1. Blader in Azure-portal naar uw API-beheerexemplaar.

1. Selecteer **OAuth 2.0** > **Toevoegen**.

1. Geef een **weergavenaam** en **-beschrijving op.**

1. Voer voor de URL van de **clientregistratiepagina**een tijdelijke aanduidingswaarde in, zoals `http://localhost`. De **URL van de clientregistratiepagina** verwijst naar een pagina die gebruikers kunnen gebruiken om hun eigen accounts te maken en te configureren voor OAuth 2.0-providers die dit ondersteunen. In dit voorbeeld maken en configureren gebruikers hun eigen accounts niet, dus gebruikt u in plaats daarvan een tijdelijke aanduiding.

1. Selecteer **Autorisatiecode**voor **subsidietypen**voor autorisatie .

1. Geef de **URL van het eindpunt autorisatie** en de URL voor **eindpunten van token op**. Haal deze waarden op van de pagina **Eindpunten** in uw Azure AD-tenant. Blader opnieuw naar de pagina **App-registraties** en selecteer **Eindpunten**.


1. Kopieer het eindpunt van de **OAuth 2.0-autorisatie**en plak het in het tekstvak **Voor eindpunt-URL-autorisatie.** Selecteer **POST** onder de methode Autorisatieaanvraag.

1. Kopieer het **OAuth 2.0-tokeneindpunt**en plak het in het tekstvak **Token-eindpunt-URL.** 

    >[!IMPORTANT]
    > U **v1-** of **v2-eindpunten** gebruiken. Afhankelijk van welke versie u echter kiest, is de onderstaande stap anders. We raden u aan v2-eindpunten te gebruiken. 

1. Als u **v1-eindpunten** gebruikt, voegt u een hoofdparameter met de naam **resource**toe . Gebruik **toepassings-id** van de back-end-app voor de waarde van deze parameter. 

1. Als u **v2-eindpunten** gebruikt, gebruikt u het bereik dat u hebt gemaakt voor de backend-app in het veld **Standaardbereik.**

1. Geef vervolgens de clientreferenties op. Dit zijn de referenties voor de client-app.

1. Voor **client-id**gebruikt u de **toepassings-id** van de client-app.

1. Gebruik **voor Clientsecret**de sleutel die u eerder voor de client-app hebt gemaakt. 

1. Direct na het klantgeheim is de **redirect_url** voor het type autorisatiecodeverlening. Noteer deze URL.

1. Selecteer **Maken**.

1. Ga terug naar uw client-app en selecteer **Verificatie**.

1. Selecteer **onder URI's omleiden**het type als **web,** plak de **redirect_url** onder **Uri omleiden**en sla op.

Nu u een OAuth 2.0-autorisatieserver hebt geconfigureerd, kan de Ontwikkelaarsconsole toegangstokens verkrijgen van Azure AD. 

De volgende stap is het inschakelen van OAuth 2.0-gebruikersautorisatie voor uw API. Hierdoor weet de Developer Console dat het een toegangstoken moet verkrijgen namens de gebruiker, voordat hij naar uw API belt.

1. Blader naar uw API-beheerexemplaar en ga naar **API's**.

2. Selecteer de API die u wilt beveiligen. U bijvoorbeeld de `Echo API`.

3. Ga naar **Settings**.

4. Kies **onder Beveiliging**de optie **OAuth 2.0**en selecteer de OAuth 2.0-server die u eerder hebt geconfigureerd. 

5. Selecteer **Opslaan**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>De API met succes aanroepen vanuit de ontwikkelaarsportal

> [!NOTE]
> Deze sectie is niet van toepassing op de **sectie Verbruik,** die geen ondersteuning biedt voor de ontwikkelaarsportal.

Nu de OAuth 2.0-gebruikersautorisatie is ingeschakeld op uw API, krijgt de Developer Console namens de gebruiker een toegangstoken voordat de API wordt aangeroepen.

1. Blader naar een bewerking onder de API in de ontwikkelaarsportal en selecteer **Probeer deze uit.** Dit brengt u naar de Developer Console.

2. Let op een nieuw item in de sectie **Autorisatie,** overeenkomend met de autorisatieserver die u zojuist hebt toegevoegd.

3. Selecteer **Autorisatiecode** in de vervolgkeuzelijst autorisatie en u wordt gevraagd zich aan te melden bij de Azure AD-tenant. Als je al bent aangemeld met het account, wordt je mogelijk niet gevraagd.

4. Na een succesvolle aanmelding wordt een `Authorization` koptekst aan de aanvraag toegevoegd, met een toegangstoken van Azure AD. Het volgende is een voorbeeldtoken (Base64 gecodeerd):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Selecteer **Verzenden**en u de API met succes aanroepen.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Een JWT-validatiebeleid configureren om aanvragen vooraf te autoriseren

Op dit moment wordt de gebruiker, wanneer een gebruiker probeert te bellen vanaf de Ontwikkelaarsconsole, gevraagd zich aan te melden. De Developer Console verkrijgt een toegangstoken namens de gebruiker en bevat het token in het verzoek aan de API.

Maar wat als iemand uw API aanroept zonder token of met een ongeldig token? Probeer bijvoorbeeld de API aan `Authorization` te roepen zonder de header, de aanroep gaat nog steeds door. De reden hiervoor is dat API Management het toegangstoken op dit moment niet valideert. Het geeft `Authorization` gewoon de header aan de back-end API.

U het [JWT-beleid valideren](api-management-access-restriction-policies.md#ValidateJWT) gebruiken om aanvragen vooraf te autoriseren in API-beheer, door de toegangstokens van elke binnenkomende aanvraag te valideren. Als een aanvraag geen geldig token heeft, blokkeert API-beheer deze. Voeg bijvoorbeeld het volgende beleid `<inbound>` toe aan `Echo API`het beleidsgedeelte van de . Het controleert de doelgroepclaim in een toegangstoken en retourneert een foutbericht als het token niet geldig is. Zie Beleid instellen of bewerken voor informatie over het configureren van [beleid.](set-edit-policies.md)

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```
> [!NOTE]
> Deze `openid-config` URL komt overeen met het v1-eindpunt. Gebruik voor `openid-config`het v2-eindpunt `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`.

## <a name="build-an-application-to-call-the-api"></a>Een toepassing bouwen om de API aan te roepen

In deze handleiding gebruikte u de Developer Console in API `Echo API` Management als voorbeeldclienttoepassing om de beveiliging van OAuth 2.0 aan te roepen. Zie [Azure Active Directory-codesamples](../active-directory/develop/sample-v2-code.md)voor meer informatie over het bouwen van een toepassing en het implementeren van OAuth 2.0.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [Azure Active Directory en OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Bekijk meer [video's](https://azure.microsoft.com/documentation/videos/index/?services=api-management) over API Management.
* Zie [Verificatie van wederzijds certificaat](api-management-howto-mutual-certificates.md)voor andere manieren om uw back-endservice te beveiligen.

* [Een API-beheerservice-instantie maken](get-started-create-service-instance.md).

* [Beheer uw eerste API.](import-and-publish.md)
