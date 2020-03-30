---
title: "Snelstart: toegang tot web-API's voor app - Microsoft-identiteitsplatform | Azure"
description: Configureer in deze quickstart een app die is geregistreerd bij het Microsoft-identiteitsplatform om URI's, referenties of machtigingen om toegang te krijgen tot web-API's op te nemen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/09/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 5e628626f2db49ff67d6d7ab425a3a19870b1ebd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240896"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Snelstart: een clienttoepassing configureren om toegang te krijgen tot web-API's

In deze quickstart voegt u omleidings-URI's, referenties of machtigingen toe om toegang te krijgen tot web-API's voor uw toepassing. Een web- of vertrouwelijke clienttoepassing moet beveiligde referenties vaststellen om deel te nemen aan een autorisatieverleningsstroom waarvoor verificatie vereist is. De standaardmethode voor verificatie die wordt ondersteund door Azure Portal is de client-ID + geheime sleutel. De app verkrijgt tijdens dit proces een toegangstoken.

Voordat een client toegang heeft tot een web-API die wordt blootgesteld door een brontoepassing, zoals Microsoft Graph API, zorgt het toestemmingskader ervoor dat de client de machtiging krijgt die vereist is voor de gevraagde machtigingen. Standaard kunnen alle toepassingen machtigingen aanvragen voor de Microsoft Graph API.

## <a name="prerequisites"></a>Vereisten

* Voltooiing van [Quickstart: Registreer een toepassing bij het Microsoft-identiteitsplatform.](quickstart-register-app.md)
* Controle van machtigingen en toestemming in het eindpunt van [het Microsoft-identiteitsplatform](v2-permissions-and-consent.md).
* Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Aanmelden bij de Azure-portal en de app selecteren

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account u toegang geeft tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek. Stel uw portalsessie in op de gewenste Azure AD-tenant.
1. Zoek naar **Azure Active Directory** en selecteer deze optie. Selecteer **App-registraties** onder **Beheren**.
1. Zoek en selecteer de toepassing die u wilt configureren. Nadat u de app hebt geselecteerd, ziet u het **overzicht** of de hoofdregistratiepagina van de toepassing.

Gebruik de volgende procedures om uw toepassing te configureren om toegang te krijgen tot web-API's.

## <a name="add-redirect-uris-to-your-application"></a>Omleidings-URI's toevoegen aan uw toepassing

U aangepaste omleidings-URI's toevoegen en suggested u URL's omleiden naar uw toepassing. Ga als lid van het nieuwe bedrijf als u een aangepaste omleidingsURI toevoegt voor web- en openbare clienttoepassingen:

1. Selecteer **verificatie**op de pagina **Overzicht van** de app .
1. **Omleidings-URI's zoeken**. Mogelijk moet u **Overschakelen naar de oude ervaring**selecteren.
1. Selecteer het type toepassing dat u bouwt: **web-** of **openbare client/native (mobiel & desktop)**.
1. Voer de omleidings-URI voor uw toepassing in.

   * Geef voor webtoepassingen de basis-URL van uw toepassing op. `http://localhost:31544` kan bijvoorbeeld de URL zijn van een webtoepassing die op uw lokale machine wordt uitgevoerd. Gebruikers moeten deze URL gebruiken om zich bij een webclienttoepassing aan te melden.
   * Geef voor openbare toepassingen de URI op die door Azure Active Directory wordt gebruikt om tokenantwoorden te retourneren. Voer een waarde in die specifiek `https://MyFirstApp`is voor uw toepassing, bijvoorbeeld: .
1. Selecteer **Opslaan**.

Voer de volgende stappen uit voorgestelde omleidings-URI's voor openbare clients uit:

1. Selecteer **verificatie**op de pagina **Overzicht van** de app .
1. **Zoek voorgestelde omleidings-URI's voor openbare clients (mobiel, desktop).** Mogelijk moet u **Overschakelen naar de oude ervaring**selecteren.
1. Selecteer een of meer omleidings-URI's voor uw toepassing. U ook een aangepaste omleiding URI invoeren. Als u niet zeker weet wat u moet gebruiken, raadpleegt u de bibliotheekdocumentatie.
1. Selecteer **Opslaan**.

Bepaalde beperkingen zijn van toepassing op omleiding van URI's. Zie [URL-beperkingen en beperkingen voor](https://docs.microsoft.com/azure/active-directory/develop/reply-url)meer informatie omleiden.

> [!NOTE]
> Probeer de nieuwe **verificatie-instellingen-ervaring** uit waarbij u instellingen voor uw toepassing configureren op basis van het platform of apparaat dat u wilt targeten.
>
> Als u deze weergave wilt bekijken, selecteert **u De nieuwe ervaring uitproberen** op de pagina **Verificatie.**
>
> ![Klik op 'Probeer de nieuwe ervaring' om de configuratieweergave van het platform te bekijken](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Dit brengt u naar de [nieuwe **pagina Platformconfiguraties.** ](#configure-platform-settings-for-your-application)

### <a name="configure-advanced-settings-for-your-application"></a>Geavanceerde instellingen voor uw toepassing configureren

Afhankelijk van de toepassing die u registreert, zijn er enkele aanvullende instellingen die u mogelijk moet configureren, zoals:

* **Url van afmelden**.
* Voor apps met één pagina u **Impliciete verlening** inschakelen en de tokens selecteren die u wilt dat het autorisatieeindpunt wordt afgegeven.
* Voor bureaublad-apps die tokens aanschaffen met geïntegreerde Windows-verificatie, apparaatcodestroom of gebruikersnaam/wachtwoord in de sectie **Standaardclienttype,** stelt u de **toepassing Behandelen in als openbare clientinstelling** op **Ja.**
* Configureer **Live SDK-ondersteuning**voor oudere apps die de Live SDK gebruikten om te integreren met de Microsoft-accountservice. Nieuwe apps hebben deze instelling niet nodig.
* **Standaardclienttype**.
* **Ondersteunde accounttypen**.

### <a name="modify-supported-account-types"></a>Ondersteunde accounttypen wijzigen

De **ondersteunde accounttypen** geven aan wie de toepassing kan gebruiken of toegang kan krijgen tot de API.

Als u de ondersteunde accounttypen hebt geconfigureerd toen u de toepassing registreerde, u deze instelling alleen wijzigen met behulp van de editor voor het manifest van de toepassing als:

* U wijzigt accounttypen van **AzureADMyOrg** of **AzureADMultipleOrgs** naar **AzureADandPersonalMicrosoftAccount,** of andersom, of
* U wijzigt accounttypen van **AzureADMyOrg** naar **AzureADMultipleOrgs**of andersom.

Als u de ondersteunde accounttypen voor een `signInAudience` bestaande app-registratie wilt wijzigen, werkt u de sleutel bij. Zie [Het toepassingsmanifest configureren](reference-app-manifest.md#configure-the-app-manifest)voor meer informatie .

## <a name="configure-platform-settings-for-your-application"></a>Platforminstellingen configureren voor uw toepassing

![Instellingen voor uw app configureren op basis van het platform of apparaat](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Als u toepassingsinstellingen wilt configureren op basis van het platform of apparaat, target u zich:

1. Selecteer op de pagina **Platformconfiguraties** de optie **Een platform toevoegen** en kies uit de beschikbare opties.

   ![Toont de pagina Platforms configureren](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Voer de instellingengegevens in op basis van het platform dat u hebt geselecteerd.

   | Platform                | Configuratie-instellingen            |
   |-------------------------|-----------------------------------|
   | **Web**              | Voer de **URI omleiden** voor uw toepassing in. |
   | **iOS / macOS**              | Voer de **app Bundle ID**in, die je vinden in XCode in Info.plist of Build Settings. Als u de bundel-id toevoegt, wordt automatisch een omleidings-URI voor de toepassing gemaakt. |
   | **Android**          | Geef de naam van het **app-pakket**op, die u vinden in het bestand AndroidManifest.xml.<br/>De hash **van Handtekening**genereren en invoeren . Als u de handtekeninghash toevoegt, wordt automatisch een omleidings-URI voor de toepassing.  |
   | **Mobiele en desktoptoepassingen**  | Optioneel. Selecteer een van de aanbevolen **aanbevolen voorgestelde omleidings-URI's** als u apps bouwt voor desktops en apparaten.<br/>Optioneel. Voer een **aangepaste omleidingsURI**in, die wordt gebruikt als de locatie waar Azure AD gebruikers omleidt naar verificatieverzoeken. Gebruik bijvoorbeeld voor .NET Core-toepassingen waar `https://localhost`u interactie wilt, gebruik . |

   > [!IMPORTANT]
   > Voor mobiele toepassingen die de nieuwste Microsoft Authentication Library (MSAL) niet gebruiken of geen broker gebruiken, moet u de omleidings-URI's voor deze toepassingen configureren in **Desktop + apparaten.**

Afhankelijk van het platform dat u hebt gekozen, kunnen er extra instellingen zijn die u configureren. Voor **web-apps** u het als:

* Meer omleidings-URI's toevoegen
* **Impliciete subsidie configureren** om de tokens te selecteren die u wilt uitgeven door het autorisatieeindpunt:

  * Selecteer voor apps met één pagina zowel **Access-tokens** als **ID-tokens**
  * Selecteer **ID-tokens** voor web-apps

## <a name="add-credentials-to-your-web-application"></a>Referenties toevoegen aan uw webtoepassing

Als u een referentie wilt toevoegen aan uw webtoepassing, voegt u een certificaat toe of maakt u een clientgeheim. Ga als volgt te werk om een certificaat toe te voegen:

1. Selecteer op de pagina **Overzicht** van de app de sectie **Certificaten & geheimen.**
1. Selecteer **Certificaat uploaden**.
1. Selecteer het bestand dat u wilt uploaden. Dit moet een van de volgende bestandstypen zijn: .cer, .pem, .crt.
1. Selecteer **Toevoegen**.

Ga als lid van de klant naar een clientgeheim:

1. Selecteer op de pagina **Overzicht** van de app de sectie **Certificaten & geheimen.**
1. Selecteer **Nieuw clientgeheim**.
1. Voeg een beschrijving voor uw clientgeheim toe.
1. Selecteer een duur.
1. Selecteer **Toevoegen**.

> [!NOTE]
> Nadat u de configuratiewijzigingen hebt opgeslagen, bevat de meest rechtse kolom de waarde van het clientgeheim. **Vergeet niet de waarde te kopiëren** voor gebruik in de code van uw clienttoepassing. Deze is namelijk niet meer toegankelijk wanneer u deze pagina verlaat.

## <a name="add-permissions-to-access-web-apis"></a>Machtigingen toevoegen voor toegang tot web-API's

De [aanmeldings- en lees-toestemming voor gebruikersprofiel](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) graph is standaard geselecteerd. U kiezen uit [twee soorten machtigingen](developer-glossary.md#permissions) voor elke web-API:

* **Toepassingsmachtigingen**. Uw clienttoepassing moet rechtstreeks toegang krijgen tot de web-API als zichzelf, zonder gebruikerscontext. Voor dit type toestemming is toestemming van de beheerder vereist. Deze toestemming is niet beschikbaar voor desktop- en mobiele clienttoepassingen.
* **Gedelegeerde machtigingen**. de clienttoepassing heeft toegang tot de web-API als de aangemelde gebruiker nodig, maar met toegang die wordt beperkt door de geselecteerde machtiging. Dit type machtiging kan worden verleend door een gebruiker tenzij de machtiging toestemming van de beheerder vereist.

  > [!NOTE]
  > Wanneer een gedelegeerde machtiging aan een toepassing wordt toegevoegd, wordt niet automatisch toestemming verleend aan de gebruikers binnen de tenant. Gebruikers moeten nog steeds handmatig toestemming geven voor de toegevoegde gedelegeerde machtigingen tijdens runtime, tenzij de beheerder toestemming namens alle gebruikers verleent.

Machtigingen toevoegen om toegang te krijgen tot bron-API's van uw client:

1. Selecteer **API-machtigingen**op de pagina **Overzicht van** de app .
1. Selecteer **Onder Geconfigureerde machtigingen**de optie Een machtiging **toevoegen**.
1. Standaard kunt u in de weergave een keuze maken uit **Microsoft-API's**. Selecteer de sectie met API's waarin u geïnteresseerd bent:

    * **Microsoft-API's**. Hiermee u machtigingen selecteren voor Microsoft API's zoals Microsoft Graph.
    * **API's die mijn organisatie gebruikt.** Hiermee u machtigingen selecteren voor API's die uw organisatie blootlegt, of API's waarmee uw organisatie is geïntegreerd.
    * **Mijn API's**. Hiermee u machtigingen selecteren voor API's die u blootstelt.

1. Wanneer u de API's hebt geselecteerd, ziet u de pagina **API-machtigingen aanvragen**. Als de API zowel gedelegeerde als toepassingsmachtigingen bevat, selecteert u het type machtiging dat uw toepassing nodig heeft.
1. Selecteer **Machtigingen toevoegen** als u klaar bent.

U keert terug naar de pagina **MET API-machtigingen.** De machtigingen zijn opgeslagen en toegevoegd aan de tabel.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Api-machtigingen en gebruikersinterface voor beheerderstoestemming begrijpen

### <a name="configured-permissions"></a>Geconfigureerde machtigingen

In deze sectie worden de machtigingen weergegeven die expliciet zijn geconfigureerd voor het toepassingsobject. Deze machtigingen maken deel uit van de lijst met vereiste brontoegang van de app. U machtigingen toevoegen of verwijderen uit deze tabel. Als beheerder u ook beheerderstoestemming verlenen of intrekken voor een set machtigingen of individuele machtigingen van een API.

### <a name="other-permissions-granted"></a>Andere verleende machtigingen

Als uw aanvraag is geregistreerd in een tenant, ziet u mogelijk een extra sectie met de titel **Andere machtigingen verleend voor Tenant.** In deze sectie worden machtigingen weergegeven die zijn verleend voor de tenant die niet expliciet zijn geconfigureerd voor het toepassingsobject. Deze machtigingen werden dynamisch gevraagd en goedgekeurd. Deze sectie wordt alleen weergegeven als er ten minste één machtiging is die van toepassing is.

U een set machtigingen of afzonderlijke machtigingen van een API toevoegen die in deze sectie worden weergegeven aan de sectie **Geconfigureerde machtigingen.** Als beheerder u ook de toestemming van de beheerder voor afzonderlijke API's of machtigingen in deze sectie intrekken.

### <a name="admin-consent-button"></a>Knop Beheerderstoestemming

Als uw aanvraag is geregistreerd in een tenant, ziet u de **knop Toestemming voor beheerders verlenen voor Tenant.** Het is uitgeschakeld als u geen beheerder bent of als er geen machtigingen zijn geconfigureerd voor de toepassing.
Met deze knop kan een beheerder toestemming verlenen voor de machtigingen die voor de toepassing zijn geconfigureerd. Als u op de knop toestemming voor beheerders klikt, wordt een nieuw venster gestart met een toestemmingsprompt met alle geconfigureerde machtigingen.

> [!NOTE]
> Er is een vertraging tussen machtigingen die worden geconfigureerd voor de toepassing en deze worden weergegeven op de toestemmingsprompt. Als u niet alle geconfigureerde machtigingen in de toestemmingsprompt ziet, sluit u deze en start u deze opnieuw.

Als u machtigingen hebt die zijn verleend, maar niet zijn geconfigureerd, wordt u op de knop Beheerderstoestemming gevraagd deze machtigingen te verwerken. U ze toevoegen aan geconfigureerde machtigingen of u ze verwijderen.

De toestemmingsprompt biedt de mogelijkheid om **te accepteren** of **te annuleren.** Selecteer **Accepteren** om beheerderstoestemming te verlenen. Als u **Annuleren**selecteert, wordt er geen toestemming voor beheerders verleend. In een foutbericht staat dat de toestemming is geweigerd.

> [!NOTE]
> Er is een vertraging tussen het verlenen van toestemming van de beheerder door **accepteren** op de toestemmingsprompt te selecteren en de status van beheerderstoestemming die wordt weergegeven in de portal.

## <a name="next-steps"></a>Volgende stappen

Ga door naar het volgende artikel om te leren hoe je web-API's blootleggen.
> [!div class="nextstepaction"]
> [Snelstart: een toepassing configureren om web-API's bloot te leggen](quickstart-configure-app-expose-web-apis.md)

* Zie [Toepassingsobjecten en service-principal-objecten](app-objects-and-service-principals.md) voor meer informatie over de twee Azure Active Directory-objecten die een geregistreerde toepassing vertegenwoordigen en de relatie ertussen.

* Zie [Huisstijlrichtlijnen voor apps](howto-add-branding-in-azure-ad-apps.md) voor meer informatie over de huisstijlrichtlijnen die u moet gebruiken bij het ontwikkelen van toepassingen met Azure Active Directory.

* [Snelstart: een toepassing registreren bij het Microsoft-identiteitsplatform](quickstart-register-app.md)

* [Snelstart: de accounts wijzigen die worden ondersteund door een toepassing](quickstart-modify-supported-accounts.md)

* [Snelstart: een toepassing verwijderen die is geregistreerd bij het Microsoft-identiteitsplatform](quickstart-remove-app.md)
