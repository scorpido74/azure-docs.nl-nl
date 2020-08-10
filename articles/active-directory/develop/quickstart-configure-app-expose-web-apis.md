---
title: Een app configureren om web-API's beschikbaar te maken - Microsoft identity platform | Azure
description: Leer hoe u een toepassing kunt configureren voor het beschikbaar maken van een nieuwe machtiging/nieuw bereik en een nieuwe rol, om de toepassing beschikbaar te maken voor clienttoepassingen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/14/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 263eb531466e26ed6069dc889c17e2632aa9ed20
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799409"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis"></a>Quickstart: Een toepassing configureren om web-API's beschikbaar te maken

U kunt een web-API ontwikkelen en deze beschikbaar maken voor clienttoepassingen door [machtigingen/bereiken](developer-glossary.md#scopes) en [rollen](developer-glossary.md#roles) beschikbaar te maken. Een correct geconfigureerde web-API wordt net als de andere Microsoft web-API's beschikbaar gesteld, met inbegrip van de Graph API en de Office 365-API's.

In deze snelstart leert u hoe u een toepassing kunt configureren voor het beschikbaar maken van een nieuw bereik, om de toepassing beschikbaar te maken voor clienttoepassingen.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u, voordat u aan de slag gaat, aan deze vereisten voldoet:

* Lees de informatie over de ondersteunde [machtigingen en toestemming](v2-permissions-and-consent.md). Een goed begrip hiervan is belangrijk bij het bouwen van toepassingen die moeten worden gebruikt door andere gebruikers of met andere toepassingen.
* U moet een tenant hebben waarvoor toepassingen zijn geregistreerd.
  * Als u uw apps niet hebt geregistreerd, [kunt u hier lezen hoe u toepassingen registreert bij het Microsoft Identity Platform](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Aanmelden bij de Azure-portal en de app selecteren

Voordat u de app kunt configureren, volgt u deze stappen:

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als u via uw account toegang hebt tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek en stelt u de portalsessie in op de gewenste Azure Active Directory-tenant.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory**-service en selecteer vervolgens **App-registraties**.
1. Zoek en selecteer de toepassing die u wilt configureren. Wanneer u de app hebt geselecteerd, ziet u de pagina **Overzicht** of de hoofdregistratiepagina van de toepassing.
1. Kies welke methode u wilt gebruiken - een gebruikersinterface of een toepassingsmanifest - om een nieuw bereik beschikbaar te maken:
    * [Een nieuw bereik beschikbaar maken via de gebruikersinterface](#expose-a-new-scope-through-the-ui)
    * [Een nieuw bereik beschikbaar maken via het toepassingsmanifest](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Een nieuw bereik beschikbaar maken via de gebruikersinterface

[![Laat zien hoe u een API beschikbaar maakt met behulp van de gebruikersinterface](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Een nieuw bereik beschikbaar maken via de gebruikersinterface:

1. Selecteer op de pagina **Overzicht** van de app de sectie **Een API beschikbaar maken**.

1. Selecteer **Een bereik toevoegen**.

1. Als u geen **URI voor de toepassings-id** hebt ingesteld, wordt u gevraagd dit te doen. Voer de URI voor de toepassings-id in of gebruik de id die is opgegeven. Selecteer vervolgens **Opslaan en doorgaan**.

1. Wanneer de pagina **Een bereik toevoegen** wordt weergegeven, voert u de gegevens van het bereik in:

    | Veld | Beschrijving |
    |-------|-------------|
    | **Naam van bereik** | Voer een beschrijvende naam voor het bereik in.<br><br>Bijvoorbeeld `Employees.Read.All`. |
    | **Wie kan toestemming verlenen?** | Selecteer of gebruikers toestemming kunnen verlenen voor dit bereik, of dat toestemming van de beheerder is vereist. Selecteer **Alleen beheerders** voor machtigingen met meer bevoegdheden. |
    | **Weergavenaam van beheerderstoestemming** | Voer een duidelijke beschrijving voor het bereik in. Deze wordt zichtbaar voor beheerders.<br><br>Bijvoorbeeld: `Read-only access to Employee records` |
    | **Beschrijving van beheerderstoestemming** | Voer een duidelijke beschrijving voor het bereik in. Deze wordt zichtbaar voor beheerders.<br><br>Bijvoorbeeld: `Allow the application to have read-only access to all Employee data.` |

    Als gebruikers toestemming kunnen verlenen voor het bereik, voegt u ook waarden toe voor de volgende velden:

    | Veld | Beschrijving |
    |-------|-------------|
    | **Weergavenaam van gebruikerstoestemming** | Voer een beschrijvende naam voor het bereik in. Deze wordt zichtbaar voor gebruikers.<br><br>Bijvoorbeeld: `Read-only access to your Employee records` |
    | **Beschrijving van gebruikerstoestemming** | Voer een duidelijke beschrijving voor het bereik in. Deze wordt zichtbaar voor gebruikers.<br><br>Bijvoorbeeld: `Allow the application to have read-only access to your Employee data.` |

1. Stel de **Status** in en selecteer **Bereik toevoegen** wanneer u gereed bent.

1. (Optioneel) Als u vragen om toestemming door gebruikers van uw toepassing wilt onderdrukken voor de bereiken die u hebt gedefinieerd, kunt u de clienttoepassing vooraf autoriseren voor toegang tot uw web-API. U moet *uitsluitend* die clienttoepassingen vooraf autoriseren die u vertrouwt, omdat de gebruikers niet de mogelijkheid hebben om toestemming te weigeren.
    1. Selecteer onder **Geautoriseerde clienttoepassingen** de optie **Een clienttoepassing toevoegen**
    1. Voer de **toepassings-id (client)** in van de client-toepassing die u vooraf wilt autoriseren. Bijvoorbeeld een web-toepassing die u eerder hebt geregistreerd.
    1. Selecteer onder **Geautoriseerde bereiken** de bereiken waarvoor u de vraag om toestemming wilt onderdrukken en selecteer vervolgens **Toepassing toevoegen**.

    De client-app is nu een vooraf geautoriseerde client-app (PCA) en gebruikers worden niet om toestemming gevraagd wanneer ze zich aanmelden.

1. Volg de stappen om te [controleren of de web-API beschikbaar is gemaakt voor andere toepassingen](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Een nieuw bereik of nieuwe rol beschikbaar maken via het toepassingsmanifest

[![Een nieuw bereik beschikbaar maken met behulp van de verzameling oauth2Permissions in het manifest](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Een nieuw bereik beschikbaar maken via het toepassingsmanifest:

1. Selecteer op de pagina **Overzicht** van de app de sectie **Manifest**. Er wordt een webgebaseerde manifesteditor geopend, zodat u het manifest binnen de portal kunt **bewerken**. U kunt optioneel ook op **Downloaden** klikken en het manifest lokaal bewerken. Gebruik vervolgens **Uploaden** om het opnieuw toe te passen op de toepassing.

    In het volgende voorbeeld ziet u hoe u een nieuw bereik met de naam `Employees.Read.All` beschikbaar kunt maken in de resource/API door het volgende JSON-element toe te voegen aan de verzameling `oauth2Permissions`.

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

   > [!NOTE]
   > De waarde `id` moet worden gegenereerd via een programma of met behulp van een hulpprogramma voor het genereren van een GUID zoals [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). De `id` staat voor een unieke id voor het bereik zoals het wordt weergegeven door de web-API. Nadat een client op de juiste wijze is geconfigureerd met machtigingen voor toegang tot de web-API, wordt er een OAuth 2.0-toegangstoken voor uitgegeven via Azure AD. Wanneer de client de web-API aanroept, presenteert deze het toegangstoken waarvan de bereikclaim (scp is) is ingesteld op de machtigingen die zijn aangevraagd in de registratie van de toepassing.
   >
   > U kunt aanvullende bereiken indien nodig later weergeven. Houd er rekening mee dat uw web-API mogelijk meerdere bereiken weergeeft die zijn gekoppeld aan een verscheidenheid van verschillende functies. Via de resource kan toegang tot de web-API tijdens runtime worden beheerd, door het evalueren van de bereikclaim(s) (`scp`) in het ontvangen OAuth 2.0-toegangstoken.

1. Klik op **Opslaan** als u klaar bent. Uw web-API is nu geconfigureerd voor gebruik door andere toepassingen in uw directory.
1. Volg de stappen om te [controleren of de web-API beschikbaar is gemaakt voor andere toepassingen](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Controleren of de web-API beschikbaar is gemaakt voor andere toepassingen

1. Ga terug naar de Azure AD-tenant, selecteer **App-registraties**, en zoek en selecteer vervolgens de clienttoepassing die u wilt configureren.
1. Herhaal de stappen die worden beschreven in [Een clienttoepassing configureren voor toegang tot web-API's](quickstart-configure-app-access-web-apis.md).
1. Wanneer u bij de stap voor het [selecteren van een API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis) bent gekomen, selecteert u uw resource (de app-registratie voor de web-API).
    * Als u de app-registratie voor de web-API via Azure Portal hebt gemaakt, wordt uw API-resource weergegeven op het tabblad **Mijn API's**.
    * Als u Visual Studio de app-registratie voor de web-API hebt laten maken tijdens het maken van het project, wordt uw API-resource weergegeven op het tabblad **API's die mijn organisatie gebruikt**.

Wanneer u de web-API-resource hebt geselecteerd, zou moeten worden weergegeven dat het nieuwe bereik beschikbaar is voor machtigingsaanvragen voor clients.

## <a name="more-on-the-application-manifest"></a>Meer informatie over het toepassingsmanifest

Het toepassingsmanifest fungeert als een mechanisme voor het bijwerken van de toepassingsentiteit, dat alle kenmerken van de configuratie van een Azure Active Directory-toepassings-id definieert. Zie de [documentatie over de toepassingsidentiteit van de Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity) voor meer informatie over de toepassingentiteit en het bijbehorende schema. Het artikel bevat volledige naslaginformatie over de toepassingsentiteitsleden die worden gebruikt voor het opgeven van machtigingen voor uw API, met inbegrip van:

* Het lid appRoles, dat een verzameling van [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type)-entiteiten is, dat wordt gebruikt voor het definiëren van [toepassingsmachtigingen](developer-glossary.md#permissions) voor een web-API.
* Het lid oauth2Permissions, dat een verzameling van [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type)-entiteiten is, dat wordt gebruikt voor het definiëren van [gedelegeerde machtigingen](developer-glossary.md#permissions) voor een web-API.

Zie [Het Azure Active Directory-toepassingsmanifest begrijpen](reference-app-manifest.md) voor meer informatie over toepassingsmanifestconcepten in het algemeen.

## <a name="next-steps"></a>Volgende stappen

Lees meer in deze andere gerelateerde snelstarts voor app-beheer:

* [Een toepassing registreren bij het Microsoft Identity Platform](quickstart-register-app.md)
* [Een clienttoepassing configureren voor toegang tot web-API's](quickstart-configure-app-access-web-apis.md)
* [De accounts wijzigen die worden ondersteund in een toepassing](quickstart-modify-supported-accounts.md)
* [Een geregistreerde toepassing verwijderen uit het Microsoft Identity Platform](quickstart-remove-app.md)

Zie [Toepassingsobjecten en service-principal-objecten](app-objects-and-service-principals.md) voor meer informatie over de twee Azure Active Directory-objecten die een geregistreerde toepassing vertegenwoordigen en de relatie ertussen.

Zie [Huisstijlrichtlijnen voor apps](howto-add-branding-in-azure-ad-apps.md) voor meer informatie over de huisstijlrichtlijnen die u moet gebruiken bij het ontwikkelen van toepassingen met Azure Active Directory.
