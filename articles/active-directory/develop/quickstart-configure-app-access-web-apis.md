---
title: 'Quickstart: Een app configureren om een web-API te openen | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart configureert u een app die is geregistreerd bij het Microsoft Identity Platform, zodat deze omleidings-URI's, referenties of toegangsmachtigingen voor web-API's bevat.
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
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 800b399e73be032cfd9d2849b004018aa9d9031f
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120861"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Quickstart: Een clienttoepassing configureren voor toegang tot een web-API

In deze quickstart voegt u omleidings-URI’s, referenties of toegangsmachtigingen voor web-API’s toe voor uw toepassing. Een webclienttoepassing of vertrouwelijke clienttoepassing moet beveiligde referenties tot stand brengen om deel te nemen aan een werkstroom voor autorisatieverlening die verificatie vereist. De standaardmethode voor verificatie die wordt ondersteund door Azure Portal is de client-ID + geheime sleutel. De app verkrijgt tijdens dit proces een toegangstoken.

Voordat een client toegang krijgt tot een web-API die beschikbaar is gemaakt via een resourcetoepassing (zoals de Microsoft Graph API), zorgt het toestemmingsframework ervoor dat de client de vereiste toestemming krijgt voor de aangevraagde machtigingen. Standaard kunnen alle toepassingen machtigingen aanvragen bij de Microsoft Graph API.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Voltooiing van [quickstart: Een toepassing configureren om een web-API beschikbaar te maken](quickstart-configure-app-expose-web-apis.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Aanmelden bij de Azure-portal en de app selecteren

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als u via uw account toegang hebt tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek. Stel de portalsessie in op de gewenste Azure AD-tenant.
1. Zoek en selecteer de optie **Azure Active Directory**. Selecteer **App-registraties** onder **Beheren**.
1. Zoek en selecteer de toepassing die u wilt configureren. Nadat u de app hebt geselecteerd, ziet u de pagina **Overzicht** of de hoofdregistratiepagina van de toepassing.

Gebruik de volgende procedures om uw toepassing te configureren voor toegang tot web-API's.

## <a name="add-redirect-uris-to-your-application"></a>Omleidings-URI's toevoegen aan uw toepassing

U kunt aangepaste omleidings-URI's en voorgestelde omleidings-URI's toevoegen aan de toepassing. Een aangepaste omleidings-URI toevoegen voor webclienttoepassingen en openbare clienttoepassingen:

1. Selecteer op de pagina **Overzicht** van de app de optie **Verificatie**.
1. Ga naar **Omleidings-URI's**. Mogelijk moet u **Terugkeren naar de oude ervaring** selecteren.
1. Selecteer het type toepassing dat u bouwt: **Web** of **Openbare client/systeemeigen (mobiel en desktop)** .
1. Voer de omleidings-URI voor uw toepassing in.

   * Geef voor webtoepassingen de basis-URL van uw toepassing op. `http://localhost:31544` kan bijvoorbeeld de URL zijn van een webtoepassing die op uw lokale machine wordt uitgevoerd. Gebruikers moeten deze URL gebruiken om zich bij een webclienttoepassing aan te melden.
   * Geef voor openbare toepassingen de URI op die door Azure Active Directory wordt gebruikt om tokenantwoorden te retourneren. Voer een waarde in die specifiek is voor uw toepassing, bijvoorbeeld `https://MyFirstApp`.
1. Selecteer **Opslaan**.

Volg deze stappen om een keuze te maken uit de voorgestelde omleidings-URI's voor openbare clients:

1. Selecteer op de pagina **Overzicht** van de app de optie **Verificatie**.
1. Ga naar **Voorgestelde omleidings-URI's voor openbare clients (mobiel, desktop)** . Mogelijk moet u **Terugkeren naar de oude ervaring** selecteren.
1. Selecteer een of meer omleidings-URI's voor de toepassing. U kunt ook een aangepaste omleidings-URI opgeven. Raadpleeg de documentatie van de bibliotheek als u niet zeker weet wat u moet gebruiken.
1. Selecteer **Opslaan**.

Op omleidings-URI's zijn bepaalde beperkingen van toepassing. Raadpleeg [Beperkingen voor omleidings-URI's en antwoord-URL's](./reply-url.md) voor meer informatie.

> [!NOTE]
> Probeer de nieuwe **Verificatie**-instellingen waarmee u instellingen voor uw toepassing kunt configureren, op basis van het platform of apparaat dat u op het oog hebt.
>
> Selecteer **De nieuwe ervaring proberen** op de pagina **Verificatie** om deze weergave te zien.
>
> ![Klik op De nieuwe ervaring proberen om de weergave Platformconfiguratie te zien](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Hiermee wordt u naar de [nieuwe pagina **Platformconfiguraties** geleid](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Geavanceerde instellingen configureren voor uw toepassing

Afhankelijk van de toepassing die u wilt registreren, zijn er enkele aanvullende instellingen die u mogelijk moet configureren, zoals:

* **Afmeldings-URL**.
* Voor apps met één pagina kunt u **Impliciete toekenning** inschakelen, en de tokens selecteren die u wilt laten uitgeven via het autorisatie-eindpunt.
* Voor desktop-apps die tokens verkrijgen met behulp van geïntegreerde Windows-verificatie, apparaatcodestroom, of gebruikersnaam/wachtwoord, stelt u in de sectie **Standaardclienttype** de instelling **Toepassing behandelen als openbare client** in op **Ja**.
* Voor oudere apps die gebruikmaken van de Live SDK om te integreren met de Microsoft-accountservice, configureert u **Live SDK-ondersteuning**. Voor nieuwe apps is deze instelling niet nodig.
* **Standaardclienttype**.
* **Ondersteunde accounttypen**.

### <a name="modify-supported-account-types"></a>Ondersteunde accounttypen wijzigen

In **Ondersteunde accounttypen** wordt opgegeven wie de toepassing kan gebruiken of toegang heeft tot de API.

Als u de ondersteunde accounttypen hebt geconfigureerd tijdens het registreren van de toepassing, kunt u deze instelling alleen wijzigen met behulp van de editor van het manifest van de toepassing, indien:

* u accounttypen van **AzureADMyOrg** of **AzureADMultipleOrgs** wijzigt in **AzureADandPersonalMicrosoftAccount**, of andersom, of indien
* u accounttypen van **AzureADMyOrg** wijzigt in **AzureADMultipleOrgs**, of andersom.

Als u de ondersteunde accounttypen voor een bestaande app-registratie wilt wijzigen, moet u de `signInAudience`-sleutel bijwerken. Zie [Het toepassingsmanifest configureren](reference-app-manifest.md#configure-the-app-manifest) voor meer informatie.

## <a name="configure-platform-settings-for-your-application"></a>Platforminstellingen configureren voor uw toepassing

![Instellingen configureren voor uw app op basis van het platform of apparaat](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Als u toepassingsinstellingen wilt configureren op basis van het platform of apparaat, doet u het volgende:

1. Selecteer op de pagina **Platformconfiguraties** de optie **Een platform toevoegen**, en kies uit de beschikbare opties.

   ![Geeft de pagina Platforms configureren weer](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Voer de instellingengegevens in op basis van het platform dat u hebt geselecteerd.

   | Platform                | Configuratie-instellingen            |
   |-------------------------|-----------------------------------|
   | **Web**              | Voer de **Omleidings-URI** voor de toepassing in. |
   | **iOS / macOS**              | Voer de **Bundel-id** van de app in. Deze kunt u vinden in XCode in Info.plist, of in de instellingen voor de build. Door het toevoegen van de bundel-id wordt automatisch een omleidings-URI voor de toepassing gemaakt. |
   | **Android**          | Geef de **Pakketnaam** van de app op. Deze kunt u vinden in het AndroidManifest.xml-bestand.<br/>Genereer de **Hash voor ondertekening** en voer deze in. Door het toevoegen van de hash voor ondertekening wordt automatisch een omleidings-URI voor de toepassing gemaakt.  |
   | **Mobiele toepassingen en desktoptoepassingen**  | Optioneel. Selecteer een van de aanbevolen **Voorgestelde omleidings-URI's** als u apps bouwt voor de desktop en apparaten.<br/>Optioneel. Voer een **Aangepaste omleidings-URI** in. Deze wordt gebruikt als de locatie waarheen gebruikers in Azure AD worden omgeleid als reactie op verificatieaanvragen. Gebruik bijvoorbeeld `http://localhost` voor .NET Core-toepassingen waarbij u interactie wilt. |

   > [!NOTE]
   > Bij AD FS (Active Directory Federation Services) en Azure AD B2C moet u ook een poortnummer opgeven.  Bijvoorbeeld: `http://localhost:1234`.

   > [!IMPORTANT]
   > Voor mobiele toepassingen die niet de nieuwste versie van MSAL (Microsoft Authentication Library) gebruiken of die niet gebruikmaken van een broker, moet u de omleidings-URI’s voor deze toepassingen configureren in **Desktop en apparaten**.

Afhankelijk van het platform dat u hebt gekozen, zijn er mogelijk aanvullende instellingen die u kunt configureren. Voor **web-apps** kunt u het volgende doen:

* Meer omleidings-URI's toevoegen
* **Impliciete toekenning** configureren om de tokens te selecteren die u wilt laten uitgeven via het autorisatie-eindpunt:

  * Voor apps met één pagina selecteert u zowel **Toegangstokens** als **id-tokens**
  * Voor web-apps selecteert u **id-tokens**

## <a name="add-credentials-to-your-web-application"></a>Referenties toevoegen aan uw webtoepassing

Als u een referentie wilt toevoegen aan uw webtoepassing, voegt u een certificaat toe of maakt u een clientgeheim. Ga als volgt te werk om een certificaat toe te voegen:

1. Selecteer op de pagina **Overzicht** van de app de sectie **Certificaten en geheimen**.
1. Selecteer **Certificaat uploaden**.
1. Selecteer het bestand dat u wilt uploaden. Dit moet een van de volgende bestandstypen zijn: .cer, .pem, .crt.
1. Selecteer **Toevoegen**.

Een clientgeheim toevoegen:

1. Selecteer op de pagina **Overzicht** van de app de sectie **Certificaten en geheimen**.
1. Selecteer **Nieuw clientgeheim**.
1. Voeg een beschrijving voor uw clientgeheim toe.
1. Selecteer een duur.
1. Selecteer **Toevoegen**.

> [!NOTE]
> Nadat u de configuratiewijzigingen hebt opgeslagen, bevat de meest rechtse kolom de waarde van het clientgeheim. **Vergeet niet de waarde te kopiëren** voor gebruik in de code van uw clienttoepassing. Deze is namelijk niet meer toegankelijk wanneer u deze pagina verlaat.

## <a name="add-permissions-to-access-web-apis"></a>Machtigingen toevoegen voor toegang tot web-API's

De [Graph API-machtiging: Aanmelden en gebruikersprofiel lezen](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) is standaard geselecteerd. U kunt kiezen uit twee [typen machtigingen](developer-glossary.md#permissions) voor elke web-API:

* **Toepassingsmachtigingen**. De clienttoepassing heeft rechtstreeks als zichzelf (zonder gebruikerscontext) toegang nodig tot de web-API. Voor dit type machtiging is toestemming van de beheerder vereist. Deze machtiging is niet beschikbaar voor clienttoepassingen voor de desktop of voor mobiele clienttoepassingen.
* **Gedelegeerde machtigingen**. de clienttoepassing heeft toegang tot de web-API als de aangemelde gebruiker nodig, maar met toegang die wordt beperkt door de geselecteerde machtiging. Dit type machtiging kan worden verleend door een gebruiker tenzij de machtiging toestemming van de beheerder vereist.

  > [!NOTE]
  > Wanneer een gedelegeerde machtiging aan een toepassing wordt toegevoegd, wordt niet automatisch toestemming verleend aan de gebruikers binnen de tenant. Gebruikers moeten nog steeds handmatig toestemming geven voor de toegevoegde gedelegeerde machtigingen tijdens runtime, tenzij de beheerder toestemming namens alle gebruikers verleent.

Machtigingen toevoegen voor toegang tot resource-API's vanaf de client:

1. Selecteer op de pagina **Overzicht** van de app de optie **API-machtigingen**.
1. Selecteer onder **Geconfigureerde machtigingen** de optie **Een machtiging toevoegen**.
1. Standaard kunt u in de weergave een keuze maken uit **Microsoft-API's**. Selecteer de sectie met API's waarin u geïnteresseerd bent:

    * **Microsoft-API's**. Hier kunt u machtigingen selecteren voor Microsoft-API's, zoals Microsoft Graph.
    * **API's die in mijn organisatie worden gebruikt**. Hier kunt u machtigingen selecteren voor de API's die beschikbaar zijn gemaakt door uw organisatie, of API's waarmee uw organisatie is geïntegreerd.
    * **Mijn API's**. Hier kunt u machtigingen selecteren voor API's die u beschikbaar hebt gemaakt.

1. Wanneer u de API's hebt geselecteerd, ziet u de pagina **API-machtigingen aanvragen**. Als de API zowel gedelegeerde als toepassingsmachtigingen bevat, selecteert u het type machtiging dat uw toepassing nodig heeft.
1. Selecteer **Machtigingen toevoegen** als u klaar bent.

U wordt nu teruggeleid naar de pagina **API-machtigingen**. De machtigingen zijn opgeslagen en toegevoegd aan de tabel.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Uitleg over API-machtigingen en de gebruikersinterface voor beheerderstoestemming

### <a name="configured-permissions"></a>Geconfigureerde machtigingen

In deze sectie ziet u de machtigingen die expliciet zijn geconfigureerd voor het toepassingsobject. Deze machtigingen maken deel uit van de lijst met vereiste resourcetoegang. U kunt machtigingen toevoegen aan of verwijderen uit deze tabel. Als beheerder kunt u ook beheerderstoestemming verlenen of intrekken voor een set API-machtigingen of voor afzonderlijke machtigingen.

### <a name="other-permissions-granted"></a>Overige verleende machtigingen

Als uw toepassing is geregistreerd in een tenant, ziet u mogelijk een extra sectie met de titel **Overige machtigingen die zijn verleend voor de tenant**. In deze sectie ziet u machtigingen die zijn verleend voor de tenant die niet expliciet zijn geconfigureerd voor het toepassingsobject. Deze machtigingen zijn dynamisch aangevraagd en verleend. Deze sectie wordt alleen weergegeven als minstens een van de machtigingen van toepassing is.

U kunt een set API-machtigingen of afzonderlijke machtigingen die u ziet in deze sectie, toevoegen aan de sectie **Geconfigureerde machtigingen**. Als beheerder kunt u beheerderstoestemming ook intrekken voor afzonderlijke API's of machtigingen in deze sectie.

### <a name="admin-consent-button"></a>Knop Beheerderstoestemming

Als uw toepassing is geregistreerd in een tenant, ziet u een knop **Beheerderstoestemming verlenen voor tenant**. Deze is uitgeschakeld als u geen beheerder bent, of als er geen machtigingen zijn geconfigureerd voor de toepassing.
Met deze knop kan een beheerder beheerderstoestemming verlenen voor de machtigingen die zijn geconfigureerd voor de toepassing. Als u op de knop Beheerderstoestemming klikt, wordt een nieuw venster geopend met een instemmingsprompt waarin alle geconfigureerde machtigingen te zien zijn.

> [!NOTE]
> Er is een vertraging tussen de configuratie van machtigingen voor de toepassing en wanneer deze worden weergegeven in de instemmingsprompt. Als u niet alle geconfigureerde machtigingen ziet in de instemmingsprompt, sluit u het venster en opent u het opnieuw.

Als u machtigingen hebt die zijn verleend maar niet zijn geconfigureerd, wordt u via de knop Beheerderstoestemming gevraagd om deze machtigingen af te handelen. U kunt ze toevoegen aan Geconfigureerde machtigingen, of u kunt ze verwijderen.

De instemmingsprompt biedt de optie om te **Accepteren** of te **Annuleren**. Selecteer **Accepteren** om beheerderstoestemming te verlenen. Als u **Annuleren** selecteert, wordt er geen beheerderstoestemming verleend. U ziet dan een foutbericht waarin staat dat toestemming is geweigerd.

> [!NOTE]
> Er is een vertraging tussen het verlenen van beheerderstoestemming door **Accepteren** te selecteren in de instemmingsprompt, en het weergeven van de status van beheerderstoestemming in de portal.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende quickstart in de reeks voor informatie over het configureren van de accounttypen die toegang moeten hebben tot de toepassing. U wilt de toegang bijvoorbeeld mogelijk beperken tot alleen de gebruikers in uw organisatie (één tenant), of ook toegang verlenen aan gebruikers in andere Azure AD-tenants (meerdere tenants) en aan gebruikers met persoonlijke Microsoft-accounts (MSA).

> [!div class="nextstepaction"]
> [De accounts wijzigen die worden ondersteund door een toepassing](quickstart-modify-supported-accounts.md)