---
title: "Snelstartgids: toegang tot Web-Api's voor app-micro soft Identity platform | Azure"
description: In deze Quick Start configureert u een app die is geregistreerd bij het micro soft Identity-platform voor omleidings-Uri's, referenties of machtigingen voor toegang tot Web-Api's.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 210ed5b8ad53fd59a46e160fe5fc72633d115d44
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82082319"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Snelstartgids: een client toepassing configureren voor toegang tot Web-Api's

In deze Quick Start kunt u omleidings-Uri's, referenties of machtigingen voor toegang tot Web-Api's voor uw toepassing toevoegen. Een web-of vertrouwelijke client toepassing moet beveiligde Referenties instellen om deel te nemen aan een autorisatie subsidie stroom waarvoor verificatie is vereist. De standaardmethode voor verificatie die wordt ondersteund door Azure Portal is de client-ID + geheime sleutel. De app verkrijgt een toegangs token tijdens dit proces.

Voordat een-client toegang kan krijgen tot een web-API die wordt weer gegeven door een resource toepassing, zoals Microsoft Graph-API, zorgt het toestemmings raamwerk ervoor dat de client de machtigings toekenning krijgt die is vereist voor de aangevraagde machtigingen. Standaard kunnen alle toepassingen machtigingen aanvragen via de Microsoft Graph-API.

## <a name="prerequisites"></a>Vereisten

* Volt ooien van [Quick Start: een toepassing registreren bij het micro soft Identity-platform](quickstart-register-app.md).
* Beoordeling van [machtigingen en toestemming in het micro soft Identity platform-eind punt](v2-permissions-and-consent.md).
* Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Aanmelden bij de Azure-portal en de app selecteren

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account u toegang geeft tot meer dan één Tenant, selecteert u uw account in de rechter bovenhoek. Stel uw portal-sessie in op de gewenste Azure AD-Tenant.
1. Zoek naar **Azure Active Directory** en selecteer deze optie. Selecteer **App-registraties** onder **Beheren**.
1. Zoek en selecteer de toepassing die u wilt configureren. Nadat u de app hebt geselecteerd, ziet u het **overzicht** van de toepassing of de hoofd registratie pagina.

Gebruik de volgende procedures om uw toepassing te configureren voor toegang tot Web-Api's.

## <a name="add-redirect-uris-to-your-application"></a>Omleidings-URI's toevoegen aan uw toepassing

U kunt aangepaste omleidings-Uri's en voorgestelde omleidings-Uri's toevoegen aan uw toepassing. Een aangepaste omleidings-URI toevoegen voor web-en open bare client toepassingen:

1. Selecteer op de pagina app- **overzicht** de optie **verificatie**.
1. **Omleidings-uri's**zoeken. Mogelijk moet u **overschakelen naar de oude ervaring**selecteren.
1. Selecteer het type toepassing dat u wilt maken: **Web** of **open bare client/systeem eigen (mobiele & bureau blad)**.
1. Voer de omleidings-URI voor uw toepassing in.

   * Geef voor webtoepassingen de basis-URL van uw toepassing op. `http://localhost:31544` kan bijvoorbeeld de URL zijn van een webtoepassing die op uw lokale machine wordt uitgevoerd. Gebruikers moeten deze URL gebruiken om zich bij een webclienttoepassing aan te melden.
   * Geef voor openbare toepassingen de URI op die door Azure Active Directory wordt gebruikt om tokenantwoorden te retourneren. Voer een waarde in die specifiek is voor uw toepassing, `https://MyFirstApp`bijvoorbeeld:.
1. Selecteer **Opslaan**.

Voer de volgende stappen uit om te kiezen uit voorgestelde omleidings-Uri's voor open bare clients:

1. Selecteer op de pagina app- **overzicht** de optie **verificatie**.
1. **Voorgestelde omleidings-uri's voor open bare clients (mobiel, bureau blad)** zoeken. Mogelijk moet u **overschakelen naar de oude ervaring**selecteren.
1. Selecteer een of meer omleidings-Uri's voor uw toepassing. U kunt ook een aangepaste omleidings-URI invoeren. Raadpleeg de documentatie van de bibliotheek als u niet zeker weet wat u moet gebruiken.
1. Selecteer **Opslaan**.

Bepaalde beperkingen zijn van toepassing op omleidings-Uri's. Zie [omleidings-URI/antwoord-URL-beperkingen en-beperkingen](https://docs.microsoft.com/azure/active-directory/develop/reply-url)voor meer informatie.

> [!NOTE]
> Probeer de nieuwe ervaring voor **verificatie** -instellingen uit, waar u instellingen voor uw toepassing kunt configureren op basis van het platform of apparaat dat u wilt instellen.
>
> Als u deze weer gave wilt weer geven, selecteert u **de nieuwe ervaring** op de pagina **verificatie** uitproberen.
>
> ![Klik op ' Probeer de nieuwe ervaring ' om de weer gave platform configuratie te bekijken](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Hiermee gaat u naar de [pagina nieuwe **platform configuraties** ](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Geavanceerde instellingen configureren voor uw toepassing

Afhankelijk van de toepassing die u wilt registreren, zijn er enkele extra instellingen die u mogelijk moet configureren, zoals:

* **Afmeldings-URL**.
* Voor apps met één pagina kunt u **impliciete toekenning** inschakelen en de tokens selecteren die u wilt verlenen aan het autorisatie-eind punt.
* Voor desktop-apps die tokens verkrijgen door gebruik te maken van geïntegreerde Windows-verificatie, programma code stroom of gebruikers naam/wacht woord in het gedeelte **standaard client type** , stelt u de instelling **toepassing behandelen als open bare client** in op **Ja**.
* Voor oudere apps die gebruikmaken van de Live SDK om te integreren met de Microsoft-account-service, moet u de **ondersteuning voor Live SDK**configureren. Deze instelling is niet vereist voor nieuwe apps.
* **Standaard client type**.
* **Ondersteunde account typen**.

### <a name="modify-supported-account-types"></a>Ondersteunde account typen wijzigen

De **ondersteunde account typen** bepalen wie de toepassing kan gebruiken of toegang kan krijgen tot de API.

Als u de ondersteunde account typen hebt geconfigureerd tijdens het registreren van de toepassing, kunt u deze instelling alleen wijzigen met behulp van de manifest editor van de toepassing in de volgende gevallen:

* U kunt de account typen wijzigen van **AzureADMyOrg** of **AzureADMultipleOrgs** in **AzureADandPersonalMicrosoftAccount**, of op een andere manier rond, of
* U kunt de account typen wijzigen van **AzureADMyOrg** in **AzureADMultipleOrgs**, of op een andere manier.

Als u de ondersteunde account typen voor een bestaande app-registratie wilt wijzigen `signInAudience` , moet u de sleutel bijwerken. Zie [het toepassings manifest configureren](reference-app-manifest.md#configure-the-app-manifest)voor meer informatie.

## <a name="configure-platform-settings-for-your-application"></a>Platform instellingen voor uw toepassing configureren

![Instellingen configureren voor uw app op basis van het platform of apparaat](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Als u toepassings instellingen wilt configureren op basis van het platform of apparaat, bent u gericht op het volgende:

1. Selecteer op de pagina **platform configuraties** de optie **een platform toevoegen** en kies uit de beschik bare opties.

   ![Toont de pagina platforms configureren](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Voer de instellingen gegevens in op basis van het platform dat u hebt geselecteerd.

   | Platform                | Configuratie-instellingen            |
   |-------------------------|-----------------------------------|
   | **Web**              | Voer de **omleidings-URI** voor uw toepassing in. |
   | **iOS/macOS**              | Voer de app **-bundel-id**in, die u kunt vinden in Xcode in info. plist of build-instellingen. Als u de bundel-ID toevoegt, wordt er automatisch een omleidings-URI voor de toepassing gemaakt. |
   | **Android**          | Geef de naam op van het app- **pakket**, dat u kunt vinden in het bestand AndroidManifest. XML.<br/>Genereer en voer de **hand tekening-hash**in. Als de hand tekening-hash wordt toegevoegd, wordt er automatisch een omleidings-URI voor de toepassing gemaakt.  |
   | **Mobiele en desktop toepassingen**  | Optioneel. Selecteer een van de aanbevolen **omleidings-uri's** als u apps voor desktop-en apparaten bouwt.<br/>Optioneel. Voer een **aangepaste omleidings-URI**in, die wordt gebruikt als de locatie waar Azure AD gebruikers omleidt als reactie op verificatie aanvragen. Voor .NET core-toepassingen waarbij u bijvoorbeeld interactie wilt, gebruikt `http://localhost`u. |

   > [!NOTE]
   > Op Active Directory Federation Services (AD FS) en Azure AD B2C moet u ook een poort nummer opgeven.  Bijvoorbeeld: `http://localhost:1234`. 

   > [!IMPORTANT]
   > Voor mobiele toepassingen die niet gebruikmaken van de nieuwste micro soft Authentication Library (MSAL) of die geen Broker gebruikt, moet u de omleidings-Uri's voor deze toepassingen configureren in **Desktop +-apparaten**.

Afhankelijk van het platform dat u hebt gekozen, zijn er mogelijk extra instellingen die u kunt configureren. Voor **Web** apps kunt u het volgende doen:

* Meer omleidings-Uri's toevoegen
* Configureer **impliciete toekenning** om de tokens te selecteren die u wilt uitgeven door het autorisatie-eind punt:

  * Selecteer voor apps met één pagina zowel **toegangs tokens** als **id-tokens**
  * Voor web apps selecteert u **id-tokens**

## <a name="add-credentials-to-your-web-application"></a>Referenties toevoegen aan uw webtoepassing

Als u een referentie wilt toevoegen aan uw webtoepassing, voegt u een certificaat toe of maakt u een client geheim. Ga als volgt te werk om een certificaat toe te voegen:

1. Selecteer op de pagina **overzicht** van apps de sectie **certificaten & geheimen** .
1. Selecteer **Certificaat uploaden**.
1. Selecteer het bestand dat u wilt uploaden. Dit moet een van de volgende bestandstypen zijn: .cer, .pem, .crt.
1. Selecteer **Toevoegen**.

Een client geheim toevoegen:

1. Selecteer op de pagina **overzicht** van apps de sectie **certificaten & geheimen** .
1. Selecteer **Nieuw clientgeheim**.
1. Voeg een beschrijving voor uw clientgeheim toe.
1. Selecteer een duur.
1. Selecteer **Toevoegen**.

> [!NOTE]
> Nadat u de configuratiewijzigingen hebt opgeslagen, bevat de meest rechtse kolom de waarde van het clientgeheim. **Vergeet niet de waarde te kopiëren** voor gebruik in de code van uw clienttoepassing. Deze is namelijk niet meer toegankelijk wanneer u deze pagina verlaat.

## <a name="add-permissions-to-access-web-apis"></a>Machtigingen toevoegen voor toegang tot web-API's

De [machtiging Graph API aanmelden en gebruikers profiel lezen](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) is standaard geselecteerd. U kunt kiezen uit [twee typen machtigingen](developer-glossary.md#permissions) voor elke web-API:

* **Toepassings machtigingen**. Uw client toepassing moet rechtstreeks toegang hebben tot de Web-API, zonder gebruikers context. Voor dit type machtiging is toestemming van de beheerder vereist. Deze machtiging is niet beschikbaar voor desktop-en mobiele-client toepassingen.
* **Gedelegeerde machtigingen**. de clienttoepassing heeft toegang tot de web-API als de aangemelde gebruiker nodig, maar met toegang die wordt beperkt door de geselecteerde machtiging. Dit type machtiging kan worden verleend door een gebruiker tenzij de machtiging toestemming van de beheerder vereist.

  > [!NOTE]
  > Wanneer een gedelegeerde machtiging aan een toepassing wordt toegevoegd, wordt niet automatisch toestemming verleend aan de gebruikers binnen de tenant. Gebruikers moeten nog steeds handmatig toestemming geven voor de toegevoegde gedelegeerde machtigingen tijdens runtime, tenzij de beheerder toestemming namens alle gebruikers verleent.

Om machtigingen toe te voegen voor toegang tot resource-Api's vanaf uw client:

1. Selecteer op de pagina app- **overzicht** de optie **API-machtigingen**.
1. Selecteer onder **geconfigureerde machtigingen** **de optie een machtiging toevoegen**.
1. Standaard kunt u in de weergave een keuze maken uit **Microsoft-API's**. Selecteer de sectie met API's waarin u geïnteresseerd bent:

    * **Api's van micro soft**. Hiermee kunt u machtigingen voor micro soft-Api's, zoals Microsoft Graph, selecteren.
    * **Api's die mijn organisatie gebruikt**. Hiermee kunt u machtigingen selecteren voor Api's die uw organisatie beschikbaar stelt, of Api's die uw organisatie heeft geïntegreerd met.
    * **Mijn api's**. Hiermee kunt u machtigingen selecteren voor Api's die u beschikbaar maakt.

1. Wanneer u de API's hebt geselecteerd, ziet u de pagina **API-machtigingen aanvragen**. Als de API zowel gedelegeerde als toepassingsmachtigingen bevat, selecteert u het type machtiging dat uw toepassing nodig heeft.
1. Selecteer **Machtigingen toevoegen** als u klaar bent.

U keert terug naar de pagina **API permissions** . De machtigingen zijn opgeslagen en toegevoegd aan de tabel.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Uitleg over API-machtigingen en gebruikers interface voor beheerders toestemming

### <a name="configured-permissions"></a>Geconfigureerde machtigingen

In deze sectie vindt u de machtigingen die expliciet zijn geconfigureerd voor het toepassings object. Deze machtigingen maken deel uit van de vereiste resource toegangs lijst van de app. U kunt machtigingen toevoegen aan of verwijderen uit deze tabel. Als beheerder kunt u ook toestemming van de beheerder verlenen of intrekken voor een set van de machtigingen of afzonderlijke machtigingen van een API.

### <a name="other-permissions-granted"></a>Andere verleende machtigingen

Als uw toepassing is geregistreerd in een Tenant, ziet u mogelijk een extra sectie met de titel **andere machtigingen die zijn verleend voor de Tenant**. In deze sectie worden de machtigingen weer gegeven die zijn verleend voor de Tenant en die niet expliciet zijn geconfigureerd voor het toepassings object. Deze machtigingen werden dynamisch aangevraagd en verzonden. Deze sectie wordt alleen weer gegeven als er ten minste één machtiging is die van toepassing is.

U kunt een set van de machtigingen van een API of afzonderlijke machtigingen die in deze sectie worden weer gegeven, toevoegen aan de sectie **geconfigureerde machtigingen** . Als beheerder kunt u de toestemming van de beheerder ook intrekken voor afzonderlijke Api's of machtigingen in deze sectie.

### <a name="admin-consent-button"></a>Knop toestemming beheerder

Als uw toepassing is geregistreerd in een Tenant, ziet u een **machtiging verlenen beheerder voor Tenant** . Het is uitgeschakeld als u geen beheerder bent of als er geen machtigingen zijn geconfigureerd voor de toepassing.
Met deze knop kan een beheerder toestemming geven voor de beheerder om de machtigingen te verlenen die voor de toepassing zijn geconfigureerd. Als u op de knop toestemming beheerder klikt, wordt er een nieuw venster geopend met de toestemming om alle geconfigureerde machtigingen weer te geven.

> [!NOTE]
> Er is een vertraging tussen de machtigingen die zijn geconfigureerd voor de toepassing en deze worden weer gegeven op de toestemming prompt. Als u niet alle geconfigureerde machtigingen in de toestemming prompt ziet, sluit u deze en start u deze opnieuw.

Als u machtigingen hebt die zijn verleend, maar niet zijn geconfigureerd, wordt u door de beheerder toestemming gevraagd deze machtigingen af te handelen. U kunt ze toevoegen aan geconfigureerde machtigingen of u kunt ze verwijderen.

De prompt voor toestemming biedt de mogelijkheid om te **accepteren** of te **Annuleren**. Selecteer **accepteren** om beheerders toestemming te verlenen. Als u **Annuleren**selecteert, wordt de beheerder geen toestemming verleend. Er wordt een fout bericht weer gegeven waarin staat dat toestemming is afgewezen.

> [!NOTE]
> Er is een vertraging tussen het verlenen van beheerders toestemming door **accepteren** te selecteren in de toestemming prompt en de status van de beheerder toestemming wordt weer gegeven in de portal.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het beschikbaar maken van web-Api's.
> [!div class="nextstepaction"]
> [Snelstartgids: een toepassing configureren voor het beschikbaar maken van web-Api's](quickstart-configure-app-expose-web-apis.md)

* Zie [Toepassingsobjecten en service-principal-objecten](app-objects-and-service-principals.md) voor meer informatie over de twee Azure Active Directory-objecten die een geregistreerde toepassing vertegenwoordigen en de relatie ertussen.

* Zie [Huisstijlrichtlijnen voor apps](howto-add-branding-in-azure-ad-apps.md) voor meer informatie over de huisstijlrichtlijnen die u moet gebruiken bij het ontwikkelen van toepassingen met Azure Active Directory.

* [Snelstartgids: een toepassing registreren bij het micro soft Identity-platform](quickstart-register-app.md)

* [Snelstartgids: de accounts wijzigen die worden ondersteund door een toepassing](quickstart-modify-supported-accounts.md)

* [Snelstartgids: een toepassing verwijderen die is geregistreerd bij het micro soft Identity-platform](quickstart-remove-app.md)
