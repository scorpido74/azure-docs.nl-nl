---
title: Registratie van beveiligde web-API-apps | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het maken van een beveiligde web-API en de informatie die u nodig hebt om de app te registreren.
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
ms.openlocfilehash: 2cdf3ff45a400d4b8d0b2605bf7ddc364aff1fe6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882383"
---
# <a name="protected-web-api-app-registration"></a>Beveiligde web-API: App-registratie

In dit artikel worden de specifieke kenmerken van app-registratie voor een beveiligde web-API uitgelegd.

Zie [Snelstart: Een toepassing registreren bij het Microsoft-identiteitsplatform](quickstart-register-app.md)voor de algemene stappen om een app te registreren.

## <a name="accepted-token-version"></a>Geaccepteerde tokenversie

Het eindpunt van het Microsoft-identiteitsplatform kan v1.0-tokens en v2.0-tokens uitgeven. Zie [Toegangstokens voor](access-tokens.md)meer informatie over deze tokens.

De geaccepteerde tokenversie is afhankelijk van de waarde **van ondersteunde accounttypen die** u kiest wanneer u uw toepassing maakt.

- Als de waarde van ondersteunde accounttypen **Accounts in een organisatiemap en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** is, is de **geaccepteerde** tokenversie v2.0.
- Anders is de geaccepteerde tokenversie v1.0.

Nadat u de toepassing hebt gemaakt, u de geaccepteerde tokenversie bepalen of wijzigen door de volgende stappen te volgen:

1. Selecteer in de Azure-portal uw app en selecteer **Manifest**.
1. Zoek de toegang tot de **eigenschapTokenAcceptedVersion** in het manifest. De standaardwaarde van de eigenschap is 2.
1. De waarde geeft aan Azure Active Directory (Azure AD) aan welke tokenversie de web-API accepteert.
    - Als de waarde 2 is, accepteert de web-API v2.0-tokens.
    - Als de waarde **null**is, accepteert de web-API v1.0-tokens.
1. Als u de tokenversie hebt gewijzigd, selecteert u **Opslaan**.

> [!NOTE]
> De web-API geeft aan welke tokenversie deze accepteert. Wanneer een client een token voor uw web-API aanvraagt via het Microsoft-identiteitsplatform (v2.0) eindpunt, krijgt de client een token dat aangeeft welke tokenversie de web-API accepteert.

## <a name="no-redirect-uri"></a>Geen omleiding URI

Web API's hoeven geen omleidinguri te registreren omdat geen enkele gebruiker interactief is aangemeld.

## <a name="exposed-api"></a>Blootgestelde API

Andere instellingen die specifiek zijn voor web API's zijn de blootgestelde API en de blootgestelde scopes.

### <a name="application-id-uri-and-scopes"></a>URI van toepassings-id's en scopes

Scopes hebben meestal `resourceURI/scopeName`het formulier . Voor Microsoft Graph hebben de scopes snelkoppelingen. Bijvoorbeeld, `User.Read` is een `https://graph.microsoft.com/user.read`snelkoppeling voor .

Tijdens de registratie van apps moet u de volgende parameters definiëren:

- De bron URI
- Een of meer scopes
- Een of meer app-rollen

Standaard raadt de toepassingsregistratieportal u aan `api://{clientId}`de bron URI te gebruiken. Deze URI is uniek, maar niet menselijk leesbaar. Als u de URI wijzigt, moet u ervoor zorgen dat de nieuwe waarde uniek is.

Voor clienttoepassingen worden scopes weergegeven als *gedelegeerde machtigingen* en worden app-rollen weergegeven als *toepassingsmachtigingen* voor uw web-API.

Scopes worden ook weergegeven in het toestemmingsvenster dat wordt gepresenteerd aan gebruikers van uw app. U moet dus de bijbehorende tekenreeksen bieden die het bereik beschrijven:

- Zoals gezien door een gebruiker.
- Zoals gezien door een tenantbeheerder, die beheerderstoestemming kan verlenen.

### <a name="exposing-delegated-permissions-scopes"></a>Gedelegeerde machtigingen (scopes) blootstellen

1. Selecteer **Een API blootleggen** in de toepassingsregistratie.
1. Selecteer **Een bereik toevoegen**.
1. Accepteer de voorgestelde toepassings-ID`api://{clientId}`URI ( ) door **Opslaan en doorgaan te**selecteren.
1. Geef de volgende waarden op:
    - Selecteer **De naam van het bereik** en voer **access_as_user**in .
    - Selecteer **Wie toestemming kan geven** en zorg ervoor dat beheerders en **gebruikers** zijn geselecteerd.
    - Selecteer **de weergavenaam van beheerderstoestemming** en voer **Access TodoListService in als gebruiker**.
    - Selecteer **de beschrijving van beheerderstoestemming** en voer als gebruiker **Accesss in.**
    - Selecteer **de naam van de weergavenaam voor gebruikerstoestemming** en voer Access **TodoListService in als gebruiker**.
    - Selecteer **De beschrijving van gebruikerstoestemming** en voer als gebruiker **Accesss in.**
    - Houd de **statuswaarde** ingesteld op **Ingeschakeld**.
 1. Selecteer **Bereik toevoegen**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Als uw web-API wordt aangeroepen door een daemon-app

In deze sectie leert u hoe u uw beveiligde web-API registreert, zodat daemon-apps deze veilig kunnen noemen.

- U declareert en stelt alleen *toepassingsmachtigingen* bloot omdat daemon-apps geen interactie hebben met gebruikers. Gedelegeerde machtigingen zouden niet logisch zijn.
- Tenantbeheerders kunnen azure AD verplichten om alleen web-API-tokens uit te geven aan toepassingen die zijn geregistreerd om toegang te krijgen tot een van de toepassingsmachtigingen van de API.

#### <a name="exposing-application-permissions-app-roles"></a>Toepassingsmachtigingen blootstellen (app-rollen)

Als u toepassingsmachtigingen wilt blootstellen, moet u het manifest bewerken.

1. Selecteer **Manifest**in de aanvraagregistratie voor uw aanvraag.
1. Als u het manifest `appRoles` wilt bewerken, zoekt u de instelling en voegt u toepassingsrollen toe. De roldefinities worden vermeld in het volgende voorbeeld JSON-blok.
1. Laat `allowedMemberTypes` alleen `"Application"` ingesteld op.
1. Zorg `id` ervoor dat het een unieke GUID is.
1. Zorg `displayName` ervoor `value` dat en bevatten geen spaties.
1. Bewaar het manifest.

In het volgende voorbeeld `appRoles`ziet u `id` de inhoud van , waar de waarde van elke unieke GUID kan zijn.

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Ervoor zorgen dat Azure AD tokens voor uw web-API uitgeeft aan alleen toegestane clients

De web-API controleert op de app-rol. Deze rol is de manier van een softwareontwikkelaar om toepassingsmachtigingen bloot te leggen. U Azure AD ook configureren om API-tokens alleen uit te geven aan apps die de tenantbeheerder goedkeurt voor API-toegang.

Ga als volgt te werk om deze verhoogde beveiliging toe te voegen:

1. Ga naar de pagina **Overzicht van** de app voor uw app-registratie.
1. Selecteer **onder Beheerde toepassing in de lokale map**de koppeling met de naam van uw app. Het label voor deze selectie kan worden afgekapt. U ziet bijvoorbeeld **beheerde toepassing in ...**

   > [!NOTE]
   >
   > Wanneer u deze koppeling selecteert, gaat u naar de pagina **Overzicht van de Ondernemingstoepassing.** Deze pagina is gekoppeld aan de serviceprincipal voor uw toepassing in de tenant waar u deze hebt gemaakt. U naar de pagina voor app-registratie gaan met behulp van de terugknop van uw browser.

1. Selecteer de pagina **Eigenschappen** in de sectie **Beheren** van de enterprise-toepassingspagina's.
1. Als u wilt dat Azure AD alleen toegang tot uw web-API toestaat van alleen bepaalde clients, stelt u **gebruikerstoewijzing in op** **Ja.**

   > [!IMPORTANT]
   >
   > Als u **Gebruikerstoewijzing vereist hebt?** op **Ja,** controleert Azure AD de toewijzingen van de app-rol van een client wanneer deze een web-API-toegangstoken aanvraagt. Als de client niet is toegewezen aan app-rollen, retourneert Azure AD het foutbericht 'invalid_client: AADSTS501051: \<Application application\> name is niet toegewezen aan een rol voor de \<web-API'.\>
   >
   > Als u **gebruikerstoewijzing vereist houdt?** ingesteld op **Nee,** controleert Azure AD de toewijzingen van de app-rol niet wanneer een client een toegangstoken voor uw web-API aanvraagt. Elke daemon-client, wat betekent dat elke client de clientreferentiesstroom gebruikt, kan een toegangstoken voor de API krijgen door alleen zijn doelgroep op te geven. Elke toepassing heeft toegang tot de API zonder dat er machtigingen voor hoeven te worden aangevraagd.
   >
   > Maar zoals uitgelegd in de vorige sectie, kan uw web-API altijd controleren of de toepassing de juiste rol heeft, die is geautoriseerd door de tenantbeheerder. De API voert deze verificatie uit door te valideren dat het access token een rollenclaim heeft en dat de waarde voor deze claim juist is. In het vorige JSON-voorbeeld `access_as_application`is de waarde .

1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-codeconfiguratie](scenario-protected-web-api-app-configuration.md)
