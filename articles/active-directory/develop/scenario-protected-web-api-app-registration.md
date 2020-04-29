---
title: Beveiligde web API-app registreren | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een beveiligde web-API en de informatie die u nodig hebt om de app te registreren.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 214d379525f2ee534415d713aa298ec858a84c92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868833"
---
# <a name="protected-web-api-app-registration"></a>Beveiligde web-API: app-registratie

In dit artikel worden de details van de app-registratie voor een beveiligde web-API beschreven.

Voor de algemene stappen voor het registreren van een app raadpleegt u [Quick Start: een toepassing registreren bij het micro soft Identity-platform](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Geaccepteerde token versie

Met het micro soft Identity platform-eind punt kunnen v 1.0-tokens en v 2.0-tokens worden uitgegeven. Zie [toegangs tokens](access-tokens.md)voor meer informatie over deze tokens.

De geaccepteerde token versie is afhankelijk van de waarde van de **ondersteunde account typen** die u kiest bij het maken van uw toepassing.

- Als de waarde van **ondersteunde account typen** **accounts in een organisatorische Directory en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** is, is de geaccepteerde token versie v 2.0.
- Anders is de geaccepteerde token versie v 1.0.

Nadat u de toepassing hebt gemaakt, kunt u de geaccepteerde token versie vaststellen of wijzigen door de volgende stappen uit te voeren:

1. Selecteer in de Azure Portal uw app en selecteer vervolgens **manifest**.
1. Zoek de eigenschap **accessTokenAcceptedVersion** in het manifest. De standaard waarde van de eigenschap is 2.
1. De waarde geeft aan Azure Active Directory (Azure AD) welke token versie de Web-API accepteert.
    - Als de waarde 2 is, accepteert de Web-API v 2.0-tokens.
    - Als de waarde **Null**is, accepteert de Web-API v 1.0-tokens.
1. Als u de token versie hebt gewijzigd, selecteert u **Opslaan**.

> [!NOTE]
> De Web-API geeft aan welke token versie wordt geaccepteerd. Wanneer een client een token voor uw web-API opvraagt vanuit het micro soft Identity platform (v 2.0)-eind punt, wordt door de client een token opgehaald waarmee wordt aangegeven welke token versie wordt geaccepteerd door de Web-API.

## <a name="no-redirect-uri"></a>Geen omleidings-URI

Web-Api's hoeven geen omleidings-URI te registreren, omdat er niet interactief een gebruiker is aangemeld.

## <a name="exposed-api"></a>Weer gegeven API

Andere specifieke instellingen voor web-Api's zijn de beschik bare API en de weer gegeven bereiken.

### <a name="application-id-uri-and-scopes"></a>URI en bereiken van de toepassings-ID

Bereiken hebben meestal het formulier `resourceURI/scopeName`. Voor Microsoft Graph hebben de bereiken snelkoppelingen. `User.Read` Is bijvoorbeeld een snelkoppeling voor `https://graph.microsoft.com/user.read`.

Tijdens de registratie van de app moet u deze para meters definiëren:

- De resource-URI
- Een of meer scopes
- Een of meer app-rollen

De portal voor toepassings registratie raadt u standaard aan de resource-URI `api://{clientId}`te gebruiken. Deze URI is uniek, maar niet leesbaar. Als u de URI wijzigt, moet u ervoor zorgen dat de nieuwe waarde uniek is.

Voor client toepassingen worden scopes weer gegeven als *gedelegeerde machtigingen* en app-rollen weer gegeven als *toepassings machtigingen* voor uw web-API.

Bereiken worden ook weer gegeven in het venster met toestemming dat wordt gepresenteerd aan gebruikers van uw app. U moet dus de overeenkomende teken reeksen opgeven die het bereik beschrijven:

- Zoals gezien door een gebruiker.
- Die door een Tenant beheerder worden gezien en die beheerders toestemming kan verlenen.

### <a name="exposing-delegated-permissions-scopes"></a>Gedelegeerde machtigingen (bereiken) weer geven

1. Selecteer **een API beschikbaar** maken in de registratie van de toepassing.
1. Selecteer **Een bereik toevoegen**.
1. Als u hierom wordt gevraagd, accepteert u de voorgestelde`api://{clientId}`URI voor de toepassings-id () door **opslaan en door gaan**te selecteren.
1. Geef deze waarden op:
    - Selecteer **Scope naam** en voer **access_as_user**in.
    - Selecteer **wie toestemming kan** geven en zorg ervoor dat **beheerders en gebruikers** zijn geselecteerd.
    - Selecteer **weergave naam beheerder toestemming** en voer **toegangs TodoListService in als gebruiker**.
    - Selecteer **Beschrijving beheerder toestemming** en voer **de TodoListService-Web-API in als een gebruiker**.
    - Selecteer **weergave naam gebruikers toestemming** en voer **toegangs TodoListService in als gebruiker**.
    - De **Beschrijving van de gebruikers bevestiging** selecteren en **de TodoListService-Web-API openen als een gebruiker**.
    - Zorg ervoor dat de **status** waarde is ingesteld op **ingeschakeld**.
 1. Selecteer **bereik toevoegen**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Als uw web-API wordt aangeroepen door een daemon-app

In deze sectie leert u hoe u de beveiligde web-API kunt registreren, zodat de daemon-apps deze veilig kunnen aanroepen.

- U declareert en bewaart alleen *toepassings machtigingen* omdat daemon-apps niet met gebruikers werken. Gedelegeerde machtigingen zouden onlogisch kunnen zijn.
- Tenant beheerders kunnen Azure AD verplichten om Web API-tokens te verlenen aan toepassingen die zijn geregistreerd voor toegang tot een van de toepassings machtigingen van de API.

#### <a name="exposing-application-permissions-app-roles"></a>Toepassings machtigingen beschikbaar maken (app-rollen)

Als u toepassings machtigingen beschikbaar wilt maken, moet u het manifest bewerken.

1. Selecteer in de registratie van de toepassing voor uw toepassing **manifest**.
1. Als u het manifest wilt bewerken, `appRoles` gaat u naar de instelling en voegt u toepassings rollen toe. De roldefinities zijn opgenomen in het volgende voor beeld-JSON-blok.
1. Stel `allowedMemberTypes` niets in `"Application"` op.
1. Zorg ervoor `id` dat u een unieke GUID hebt.
1. Zorg ervoor `displayName` dat `value` ze geen spaties bevatten.
1. Sla het manifest op.

In het volgende voor beeld wordt de `appRoles`inhoud weer gegeven van, `id` waarbij de waarde van een unieke GUID kan zijn.

```json
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

De Web-API controleert op de functie van de app. Deze rol is de manier waarop software ontwikkelaars toepassings machtigingen kunnen weer geven. U kunt Azure AD ook zo configureren dat API-tokens worden uitgegeven aan apps die de Tenant beheerder heeft goedgekeurd voor API-toegang.

Deze verhoogde beveiliging toe te voegen:

1. Ga naar de **overzichts** pagina van de app voor de registratie van uw app.
1. Onder **beheerde toepassing in lokale map**selecteert u de koppeling met de naam van uw app. Het label voor deze selectie kan worden afgekapt. Zo ziet u mogelijk een **beheerde toepassing in..** .

   > [!NOTE]
   >
   > Wanneer u deze koppeling selecteert, gaat u naar de **overzichts** pagina van de bedrijfs toepassing. Deze pagina is gekoppeld aan de service-principal voor uw toepassing in de Tenant waar u deze hebt gemaakt. U kunt naar de pagina app-registratie gaan met behulp van de knop terug van uw browser.

1. Selecteer de pagina **Eigenschappen** in de sectie **beheren** van de pagina's van de Enter prise-toepassing.
1. Als u wilt dat Azure AD toegang tot uw web-API van alleen bepaalde clients toestaat, stelt u de **gebruikers toewijzing vereist** in op **Ja**.

   > [!IMPORTANT]
   >
   > Als u de **gebruikers toewijzing** hebt ingesteld op vereist? in azure **AD worden de**toewijzingen van de app-rollen van een client gecontroleerd wanneer deze een web API-toegangs token aanvraagt. Als de client niet is toegewezen aan de app-rollen, retourneert Azure AD de fout melding "invalid_client: AADSTS501051: \<de naam\> van de toepassings toepassing is niet toegewezen aan \<een rol\>voor de Web-API.
   >
   > Als u de **gebruikers toewijzing verplicht stelt?** ingesteld op **Nee**, worden de toewijzingen van de app-rollen niet door Azure AD gecontroleerd wanneer een client een toegangs token voor uw web-API aanvraagt. Elke daemon-client, wat betekent dat elke client die gebruikmaakt van de client referentie stroom, een toegangs token voor de API kan krijgen door de doel groep op te geven. Elke toepassing kan toegang krijgen tot de API zonder dat hiervoor machtigingen moeten worden gevraagd.
   >
   > Maar zoals beschreven in de vorige sectie, kan uw web-API altijd controleren of de toepassing de juiste rol heeft, die door de Tenant beheerder wordt geautoriseerd. De API voert deze verificatie uit door te valideren dat het toegangs token een rol claim heeft en dat de waarde voor deze claim juist is. In het vorige JSON-voor beeld is `access_as_application`de waarde.

1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-code configuratie](scenario-protected-web-api-app-configuration.md)
