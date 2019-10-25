---
title: Beveiligde web-API-app-registratie
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een beveiligde web-API en de informatie die u nodig hebt om de app te registreren.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c905dfd86fd80b9e55aa7bd5a9b9b03f277570c
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802543"
---
# <a name="protected-web-api-app-registration"></a>Beveiligde web-API: app-registratie

In dit artikel worden de details van de app-registratie voor een beveiligde web-API beschreven.

Zie [Quick Start: een toepassing registreren met het micro soft-identiteits platform](quickstart-register-app.md) voor de algemene stappen voor het registreren van een app.

## <a name="accepted-token-version"></a>Geaccepteerde token versie

Het micro soft Identity platform-eind punt kan twee typen tokens uitgeven: v 1.0-tokens en v 2.0-tokens. Zie [toegangs tokens](access-tokens.md)voor meer informatie over deze tokens. De geaccepteerde token versie is afhankelijk van de **ondersteunde account typen** die u hebt gekozen tijdens het maken van uw toepassing:

- Als de waarde van **ondersteunde account typen** **accounts in een organisatorische Directory en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** is, is de geaccepteerde token versie v 2.0.
- Anders is de geaccepteerde token versie v 1.0.

Nadat u de toepassing hebt gemaakt, kunt u de geaccepteerde token versie vaststellen of wijzigen door de volgende stappen uit te voeren:

1. Selecteer in de Azure Portal uw app en selecteer vervolgens het **manifest** voor uw app.
2. Zoek in het manifest naar **' accessTokenAcceptedVersion '** . Houd er rekening mee dat de waarde **2**is. Met deze eigenschap geeft u op Azure Active Directory (Azure AD) dat de Web-API v 2.0-tokens accepteert. Als de waarde **Null**is, is de geaccepteerde token versie v 1.0.
3. Als u de token versie hebt gewijzigd, selecteert u **Opslaan**.

> [!NOTE]
> De Web-API geeft aan welke token versie (v 1.0 of v 2.0) deze accepteert. Wanneer clients een token aanvragen voor uw web-API van het micro soft Identity platform (v 2.0)-eind punt, krijgen ze een token waarmee wordt aangegeven welke versie wordt geaccepteerd door de Web-API.

## <a name="no-redirect-uri"></a>Geen omleidings-URI

Web-Api's hoeven geen omleidings-URI te registreren, omdat er interactief een gebruiker is aangemeld.

## <a name="expose-an-api"></a>Een API beschikbaar maken

Een andere instelling die specifiek is voor web-Api's is de beschik bare API en de weer gegeven bereiken.

### <a name="resource-uri-and-scopes"></a>Bron-URI en bereiken

Bereiken bevinden zich meestal in de vorm `resourceURI/scopeName`. Voor Microsoft Graph hebben de bereiken snelkoppelingen zoals `User.Read`. Deze teken reeks is een snelkoppeling voor `https://graph.microsoft.com/user.read`.

Tijdens de registratie van de app moet u deze para meters definiëren:

- De resource-URI. De portal voor toepassings registratie raadt u standaard aan om `api://{clientId}`te gebruiken. Deze resource-URI is uniek, maar is niet leesbaar. U kunt deze wijzigen, maar u moet er wel voor zorgen dat de nieuwe waarde uniek is.
- Een of meer *scopes*. (Voor client toepassingen worden ze weer gegeven als *gedelegeerde machtigingen* voor uw web-API.)
- Een of meer *app-rollen*. (Voor client toepassingen worden ze weer gegeven als *toepassings machtigingen* voor uw web-API.)

De bereiken worden ook weer gegeven op het venster met de toestemming dat wordt gepresenteerd aan eind gebruikers van uw app. Daarom moet u de overeenkomende teken reeksen opgeven die het bereik beschrijven:

- Zoals gezien door de eind gebruiker.
- Zoals gezien door de Tenant beheerder, wie toestemming van de beheerder kan verlenen.

### <a name="exposing-delegated-permissions-scopes"></a>Gedelegeerde machtigingen (bereiken) weer geven

1. Selecteer de sectie **een API beschikbaar** maken in de registratie van de toepassing.
1. Selecteer **Een bereik toevoegen**.
1. Als u hierom wordt gevraagd, accepteert u de voorgestelde URI voor de toepassings-ID (`api://{clientId}`) door **opslaan en door gaan**te selecteren.
1. Voer de volgende para meters in:
      - Gebruik **access_as_user**voor **Scope naam**.
      - Voor **wie toestemming kan**geven, moet u ervoor zorgen dat **beheerders en gebruikers** zijn geselecteerd.
      - Voer in de **weergave naam van de beheerders toestemming** **de toegangs TodoListService in als een gebruiker**.
      - In de beschrijving van de **beheerders toestemming**voert u **de TodoListService-Web-API in als een gebruiker**.
      - In **weergave naam van gebruikers toestemming**voert u **toegangs TodoListService in als een gebruiker**.
      - In **Beschrijving van de gebruikers toestemming**voert u **de TodoListService-Web-API in als een gebruiker**.
      - Zorg ervoor dat de **status** is ingesteld op **ingeschakeld**.
      - Selecteer **bereik toevoegen**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Als uw web-API wordt aangeroepen door een daemon-app

In deze sectie leert u hoe u de beveiligde web-API kunt registreren zodat deze veilig kan worden aangeroepen door daemon-apps.

- U moet *toepassings machtigingen*zichtbaar maken. U declareert alleen toepassings machtigingen omdat daemon-apps geen interactie hebben met gebruikers, dus gedelegeerde machtigingen zouden niet zinvol zijn.
- Tenant beheerders kunnen Azure Active Directory (Azure AD) verplichten om tokens voor uw web-API uit te geven aan toepassingen die zijn geregistreerd voor toegang tot een van de toepassings machtigingen van de Web-API.

#### <a name="exposing-application-permissions-app-roles"></a>Toepassings machtigingen beschikbaar maken (app-rollen)

Als u toepassings machtigingen beschikbaar wilt maken, moet u het manifest bewerken.

1. Selecteer in de registratie van de toepassing voor uw toepassing **manifest**.
1. Bewerk het manifest door de `appRoles`-instelling te zoeken en een of meer toepassings rollen toe te voegen. De roldefinitie is opgenomen in het volgende voor beeld-JSON-blok. De `allowedMemberTypes` alleen op `"Application"` laten instellen. Zorg ervoor dat de `id` een unieke GUID is en dat `displayName` en `value` geen spaties bevatten.
1. Sla het manifest op.

In het volgende voor beeld wordt de inhoud van `appRoles`weer gegeven. (Het `id` kan een wille keurige unieke GUID zijn.)

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Ervoor zorgen dat Azure AD tokens voor uw web-API alleen aan toegestane clients geeft

De Web-API controleert op de functie van de app. (Dat is de ontwikkelaars manier om toepassings machtigingen beschikbaar te maken.) Maar u kunt ook Azure AD zo configureren dat er een token voor uw web-API wordt uitgegeven aan apps die zijn goedgekeurd door de Tenant beheerder voor toegang tot uw API. Deze verhoogde beveiliging toe te voegen:

1. Selecteer op de pagina **overzicht** van apps voor uw app-registratie de koppeling met de naam van uw app onder **beheerde toepassing in lokale map**. De titel voor dit veld kan worden afgekapt. U kunt bijvoorbeeld **beheerde toepassing in..** .

   > [!NOTE]
   >
   > Wanneer u deze koppeling selecteert, gaat u naar de overzichts pagina van de **bedrijfs toepassing** die is gekoppeld aan de service-principal voor uw toepassing in de Tenant waar u deze hebt gemaakt. U kunt teruggaan naar de registratie pagina van de app met behulp van de knop terug van uw browser.

1. Selecteer de pagina **Eigenschappen** in de sectie **beheren** van de pagina's van de Enter prise-toepassing.
1. Als u wilt dat Azure AD toegang tot uw web-API van alleen bepaalde clients toestaat, stelt u de **gebruikers toewijzing vereist** in op **Ja**.

   > [!IMPORTANT]
   >
   > Als u de **gebruikers toewijzing** hebt ingesteld op vereist? **in azure AD worden de**toewijzingen van de app-rollen van clients gecontroleerd wanneer deze een toegangs token aanvragen voor de Web-API. Als de client niet is toegewezen aan de app-rollen, wordt de fout `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`door Azure AD geretourneerd.
   >
   > Als u de **gebruikers toewijzing verplicht stelt?** ingesteld op **Nee**, worden *de toewijzingen van de app-rollen niet door Azure AD gecontroleerd wanneer een client een toegangs token voor uw web-API aanvraagt*. Elke daemon-client (dat wil zeggen elke client die gebruikmaakt van de client referentie stroom) kan een toegangs token voor de API verkrijgen door alleen de doel groep op te geven. Elke toepassing kan toegang krijgen tot de API zonder dat hiervoor machtigingen moeten worden gevraagd. Maar uw web-API kan altijd, zoals wordt beschreven in de vorige sectie, controleren of de toepassing de juiste rol heeft (die is geautoriseerd door de Tenant beheerder). De API voert deze verificatie uit door te valideren dat het toegangs token een rol claim heeft en dat de waarde voor deze claim juist is. (In ons geval is de waarde `access_as_application`.)

1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Configuratie van de app-code](scenario-protected-web-api-app-configuration.md)
