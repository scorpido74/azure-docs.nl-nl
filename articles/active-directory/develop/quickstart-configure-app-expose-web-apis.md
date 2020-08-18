---
title: 'Quickstart: Een app configureren om een web-API beschikbaar te maken | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart leert u hoe u een toepassing kunt configureren voor het beschikbaar maken van een nieuwe machtiging/nieuw bereik en een nieuwe rol, om de toepassing beschikbaar te maken voor clienttoepassingen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830288"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Quickstart: Een toepassing configureren om een web-API beschikbaar te maken

U kunt een web-API ontwikkelen en deze beschikbaar maken voor clienttoepassingen door [machtigingen/bereiken](developer-glossary.md#scopes) en [rollen](developer-glossary.md#roles) beschikbaar te maken. Een correct geconfigureerde web-API wordt net als de andere Microsoft web-API's beschikbaar gesteld, met inbegrip van de Graph API en de Office 365-API's.

In deze quickstart leert u hoe u een toepassing kunt configureren voor het beschikbaar maken van een nieuw bereik, om de toepassing beschikbaar te maken voor clienttoepassingen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Voltooiing van [quickstart: Een toepassing registreren bij het Microsoft-identiteitsplatform](quickstart-register-app.md).

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

Het toepassingsmanifest fungeert als een mechanisme voor het bijwerken van de toepassingsentiteit, dat de kenmerken van een Azure AD-app-registratie definieert.

[![Een nieuw bereik beschikbaar maken met behulp van de verzameling oauth2Permissions in het manifest](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Een nieuw bereik beschikbaar maken door het toepassingsmanifest te bewerken:

1. Selecteer op de pagina **Overzicht** van de app de sectie **Manifest**. Er wordt een webgebaseerde manifesteditor geopend, zodat u het manifest binnen de portal kunt **bewerken**. U kunt optioneel ook op **Downloaden** klikken en het manifest lokaal bewerken. Gebruik vervolgens **Uploaden** om het opnieuw toe te passen op de toepassing.

    In het volgende voorbeeld ziet u hoe u een nieuw bereik met de naam `Employees.Read.All` beschikbaar kunt maken in de resource/API door het volgende JSON-element toe te voegen aan de verzameling `oauth2Permissions`.

    Genereer de waarde `id` programmatisch of door een hulpprogramma voor het genereren van een GUID, zoals [guidgen](https://www.microsoft.com/download/details.aspx?id=55984), te gebruiken.

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

1. Klik op **Opslaan** als u klaar bent. Uw web-API is nu geconfigureerd voor gebruik door andere toepassingen in uw directory.
1. Volg de stappen om te [controleren of de web-API beschikbaar is gemaakt voor andere toepassingen](#verify-the-web-api-is-exposed-to-other-applications).

Zie de documentatie over het resourcetype [Toepassing][ms-graph-application] van Microsoft Graph voor meer informatie over de toepassingsentiteit en het bijbehorende schema.

Zie [Informatie over het Azure AD-app-manifest](reference-app-manifest.md) voor meer informatie over het toepassingsmanifest en het bijbehorende schema.

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Controleren of de web-API beschikbaar is gemaakt voor andere toepassingen

1. Ga terug naar de Azure AD-tenant, selecteer **App-registraties**, en zoek en selecteer vervolgens de clienttoepassing die u wilt configureren.
1. Herhaal de stappen die worden beschreven in [Een clienttoepassing configureren voor toegang tot web-API's](quickstart-configure-app-access-web-apis.md).
1. Wanneer u bij de stap voor het [selecteren van een API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis) bent gekomen, selecteert u uw resource (de app-registratie voor de web-API).
    * Als u de app-registratie voor de web-API via Azure Portal hebt gemaakt, wordt uw API-resource weergegeven op het tabblad **Mijn API's**.
    * Als u Visual Studio de app-registratie voor de web-API hebt laten maken tijdens het maken van het project, wordt uw API-resource weergegeven op het tabblad **API's die mijn organisatie gebruikt**.

Wanneer u de web-API-resource hebt geselecteerd, zou moeten worden weergegeven dat het nieuwe bereik beschikbaar is voor machtigingsaanvragen voor clients.

## <a name="using-the-exposed-scopes"></a>De beschikbaar gemaakte bereiken gebruiken

Nadat een client op de juiste wijze is geconfigureerd met machtigingen voor toegang tot de web-API, kan er een OAuth 2.0-toegangstoken voor worden uitgegeven via Azure AD. Wanneer de client de web-API aanroept, presenteert deze het toegangstoken waarvan de bereikclaim (`scp`) is ingesteld op de machtigingen die zijn aangevraagd in de registratie van de toepassing.

U kunt aanvullende bereiken indien nodig later weergeven. Houd er rekening mee dat uw web-API mogelijk meerdere bereiken weergeeft die zijn gekoppeld aan een verscheidenheid van verschillende functies. Via de resource kan toegang tot de web-API tijdens runtime worden beheerd, door het evalueren van de bereikclaim(s) (`scp`) in het ontvangen OAuth 2.0-toegangstoken.

In uw toepassingen is de volledige bereikwaarde een samenvoeging van de **URI voor de toepassings-id** van uw web-API (de resource) en de **bereiknaam**.

Als de URI voor de toepassings-id van uw web-API bijvoorbeeld `https://contoso.com/api` is en uw bereiknaam `Employees.Read.All` is, is het volledige bereik:

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>Volgende stappen

Nu u uw web-API beschikbaar hebt gemaakt door de bereiken ervan te configureren, configureert u de registratie van uw client-app met machtigingen voor toegang tot die bereiken.

> [!div class="nextstepaction"]
> [Een app-registratie configureren voor toegang tot de web-API](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
