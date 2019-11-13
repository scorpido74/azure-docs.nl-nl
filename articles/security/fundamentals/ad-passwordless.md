---
title: Meer informatie over een wereld zonder wacht woorden met Azure Active Directory | Microsoft Docs
description: Deze hand leiding helpt CEOs, Cio's, CISOs, Chief Identity Architects, Enter prise Architects en Security en IT-besluit vormers die verantwoordelijk zijn voor het kiezen van een verificatie methode met een wacht woord voor hun Azure Active Directory-implementatie.
keywords: geen wacht woord, azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 78f8b7097060f1091bfed30722c9ea456feed384
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007283"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Een wereld zonder wacht woorden met Azure Active Directory

Het is tijd om uw relatie met wacht woorden op te delen. Wacht woorden zijn in het verleden goed van ons, maar in de huidige digitale werk plek zijn ze een relatief eenvoudige aanvals vector geworden voor hackers. Hackers horen graag wacht woorden en het is niet moeilijk om te zien waarom wanneer u denkt dat de meest voorkomende wacht woorden in Azure Active Directory (Azure AD) voor waarden bevatten zoals het jaar, de maand, het seizoen of een lokaal sport team. Bovendien [heeft onderzoek geleerd](https://aka.ms/passwordguidance) dat traditionele aanbevelingen voor wachtwoord beheer, zoals lengte vereisten, complexiteits vereisten en wijzigings frequenties, worden counterproductive om verschillende redenen met betrekking tot menselijke aard.

Drie soorten aanvallen die vaak worden gebruikt om gebruikers accounts te manipuleren, zijn wachtwoord spray, phishing en het opnieuw afspelen van inbreuken. Azure AD-functies als [slimme vergren deling](../../active-directory/authentication/howto-password-smart-lockout.md), [verboden wacht woorden](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)en [wachtwoord beveiliging](../../active-directory/authentication/concept-password-ban-bad-on-premises.md) kunnen helpen beveiligen tegen dergelijke aanvallen. Op dezelfde manier biedt de implementatie van [multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA) of verificatie in twee stappen extra beveiliging door een tweede vorm van verificatie te vereisen. Maar in de lange uitvoering is een oplossing zonder wacht woord de beste oplossing om de veiligste verificatie methode te garanderen.

Dit artikel is het begin van uw reis om inzicht te krijgen in de oplossingen van micro soft met een wacht woord en om u te helpen bij het kiezen van een of meer van de volgende opties:

* **Windows hello voor bedrijven**. In Windows 10 vervangt Windows hello voor bedrijven wacht woorden met sterke twee ledige verificatie op Pc's en mobiele apparaten. Deze verificatie bestaat uit een nieuw type gebruikers referentie dat is gekoppeld aan een apparaat en gebruikmaakt van biometrische herkenning of pincode.

* **Aanmelding zonder wacht woord met Microsoft Authenticator**. De Microsoft Authenticator-app kan worden gebruikt om u aan te melden bij een Azure AD-account zonder een wacht woord te gebruiken. Net als de technologie van Windows hello voor bedrijven gebruikt de Microsoft Authenticator verificatie op basis van een sleutel om een gebruikers referentie in te scha kelen die is gekoppeld aan een apparaat en gebruikmaakt van biometrisch of pincode.

* **Beveiligings sleutels FIDO2**. FIDO2 biedt cryptografische aanmeldings referenties die uniek zijn voor elke website en die worden opgeslagen op een lokaal apparaat, zoals Windows hello of externe beveiligings sleutels. Deze beveiligings sleutels zijn resistent tegen de Risico's van phishing, wachtwoord diefstal en replay-aanvallen. In combi natie met gebruikers verificatie via biometrie of pincode is de oplossing twee ledige verificatie vereisten voor moderne beveiliging.

## <a name="the-future-of-passwordless-authentication"></a>De toekomst van verificatie met een wacht woord

Deze dagen, banken, creditcard maatschappijen en andere organisaties en onlineservices beschermen uw account vaak door u te verplichten uw identiteit twee maal te verifiëren: eenmaal door uw wacht woord te gebruiken, vervolgens opnieuw per telefoon, tekst of een app. Multi-factor Authentication behandelt het beveiligings probleem van wacht woorden die worden gedeeld, gestolen of geraden, maar heeft geen betrekking op de ongemakloze factor van de poging om deze te onthouden. Wat gebruikers en organisaties in de huidige Cloud era willen, zijn de authenticatie methoden met een wacht woord die zeer veilig *en* handig zijn.

![Gemak versus beveiliging](./media/ad-passwordless/azure-ad-pwdless-image1.png)

Windows hello voor bedrijven, aanmelden zonder wacht woord met Microsoft Authenticator en FIDO2-beveiligings sleutels delen allemaal een eenvoudige, gemeen schappelijke architectuur waarmee gebruikers een verificatie methode hebben die zowel zeer veilig als handig is om te gebruiken. Alle drie zijn gebaseerd op open bare en persoonlijke sleutel technologie, hebben een lokale penbeweging nodig, zoals biometrisch of pincode, en persoonlijke sleutels die zijn gebonden aan één apparaat en die veilig worden opgeslagen en nooit worden gedeeld.

## <a name="windows-hello-for-business"></a>Windows Hello voor Bedrijven

In Windows 10 vervangt Windows hello voor bedrijven wacht woorden met sterke twee ledige verificatie op Pc's en apparaten. De verificatie bestaat uit een nieuw type gebruikers referentie dat is gekoppeld aan een apparaat en gebruikmaakt van een biometrische penbeweging of pincode waarmee gebruikers zich kunnen verifiëren bij Azure AD en een on-premises Active Directory. U kunt zich eenvoudig aanmelden bij een apparaat met Windows hello voor bedrijven. U kunt een pincode of biometrische beweging gebruiken, zoals een vinger afdruk of een gezichts herkenning.

### <a name="windows-hello-for-business-scenarios"></a>Windows hello voor bedrijven-scenario's

Windows hello voor bedrijven is ideaal voor IT-mede werkers die hun eigen aangewezen Windows-PC hebben. De biometrische en referenties zijn rechtstreeks gebonden aan de PC van de gebruiker, waardoor de toegang van iemand anders dan de eigenaar wordt voor komen. Met PKI-integratie en ingebouwde ondersteuning voor eenmalige aanmelding (SSO) biedt Windows hello voor bedrijven een eenvoudige en handige methode voor probleemloze toegang tot bedrijfs bronnen on-premises en in de Cloud.

### <a name="windows-hello-for-business-deployment-considerations"></a>Overwegingen voor de implementatie van Windows hello voor bedrijven

Windows hello voor bedrijven is een gedistribueerd systeem dat verschillende onderdelen gebruikt om apparaatregistratie, inrichting en verificatie uit te voeren. Daarom vereist implementatie een juiste planning voor meerdere teams binnen de organisatie. De [plannings handleiding](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) voor Windows hello voor bedrijven kan worden gebruikt om u te helpen bij het nemen van beslissingen over het type implementatie van Windows hello voor bedrijven en de opties die u moet overwegen.

Er zijn veel opties waaruit u kunt kiezen wanneer u Windows hello voor bedrijven implementeert. Door meerdere opties te bieden, zorgt u ervoor dat bijna elke organisatie Windows hello voor bedrijven kan implementeren. Houd rekening met de volgende typen implementaties die worden ondersteund:

* [Implementatie van de Azure AD-sleutel voor de vertrouwens relatie](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Hybride Azure AD-gekoppelde certificaat vertrouwens implementatie](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Implementatie handleidingen voor Azure AD-deelname aan eenmalige aanmelding](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Implementatie van on-premises sleutel vertrouwensrelatie](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Implementatie van on-premises certificaat vertrouwen](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Als u veel opties opgeeft, wordt de implementatie complex. De meeste organisaties zullen waarschijnlijk wel bepalen dat ze de meeste van de infra structuur waarop de Windows hello voor bedrijven-implementatie van toepassing is, al hebben geïmplementeerd. Het is ook belang rijk om te begrijpen dat Windows hello voor bedrijven een gedistribueerd systeem is en dat de juiste planning wordt aanbevolen.

We raden u aan [een implementatie van Windows hello voor bedrijven te plannen](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) om u te helpen beslissen over het implementatie model dat het meest geschikt is voor uw organisatie. Op basis van de planning die u maakt, raadpleegt u de [implementatie handleiding voor Windows hello voor bedrijven](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) om te zorgen voor een succes volle implementatie van Windows hello voor bedrijven in uw bestaande omgeving.

### <a name="how-windows-hello-for-business-works"></a>Hoe Windows hello voor bedrijven werkt

#### <a name="user-sets-up-windows-hello-for-business"></a>Gebruiker stelt Windows hello voor bedrijven in

Na een eerste verificatie in twee stappen van de gebruiker tijdens de registratie, wordt Windows hello ingesteld op het apparaat van de gebruiker en wordt de gebruiker gevraagd een penbeweging in te stellen. Dit kan biometrisch zijn, zoals een vinger afdruk of een gezichts herkenning, of een pincode. Als de gebruiker eenmaal is ingesteld, beschikt u over de penbeweging om hun identiteit te verifiëren. Windows gebruikt Windows hello om gebruikers te verifiëren.

Op basis van de mogelijkheden van uw Windows 10-apparaat hebt u een ingebouwde beveiligde enclave, ook wel bekend als een hardware trusted platform module (TPM) of een software-TPM. De TPM slaat de persoonlijke sleutel op, waarvoor u uw gezicht, vinger afdruk of pincode nodig hebt om deze te ontgrendelen. De biometrische gegevens worden niet geroamd en worden nooit verzonden naar externe apparaten of servers. Er is geen enkele verzamelings punt dat een aanvaller kan misbruiken om biometrische gegevens te stelen, omdat Windows hello alleen biometrische identificatie gegevens op het apparaat opslaat.

> [!TIP]
> Op het Opper vlak lijkt een pincode op een wacht woord, maar het is wel veilig. Een belang rijk verschil tussen een wacht woord en een pincode is dat de pincode is gekoppeld aan het specifieke apparaat waarop het is ingesteld. Iemand die uw wacht woord stelen, kan zich vanaf elke locatie aanmelden bij uw account. Maar als ze uw pincode stelen, moeten ze ook uw fysieke apparaat stelen. Omdat een pincode lokaal is voor het apparaat, wordt deze niet verzonden, zodat deze niet kan worden onderschept in trans missie of dief stal van een server.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Gebruiker die Windows hello voor bedrijven gebruikt voor aanmelding

De biometrische en pincodes van Windows hello voor bedrijven gebruiken asymmetrische versleuteling (open bare/persoonlijke sleutel) voor verificatie. Tijdens de verificatie is de versleuteling gekoppeld aan de TLS-sessie sleutel, die het verificatie proces beveiligt, zodat een man-in-the-middle-aanval (MiTM) het resulterende beveiligings token of artefact niet kan stelen en opnieuw kan afspelen vanaf andere locaties.

Windows hello voor bedrijven biedt een handige aanmeldings ervaring waarmee de gebruiker wordt geverifieerd bij Azure AD en Active Directory-resources. Azure AD-apparaten verifiëren bij Azure tijdens het aanmelden en kunnen eventueel worden geverifieerd bij Active Directory. Hybride Azure Active Directory gekoppelde apparaten verifiëren Active Directory tijdens het aanmelden en verifiëren deze Azure Active Directory op de achtergrond.

![De bron installatie kopie weer geven](./media/ad-passwordless/azure-ad-pwdless-image2.jpeg)

De volgende stappen illustreren de aanmeldings verificatie om te Azure Active Directory.

![Windows 10-vergrendelings scherm](./media/ad-passwordless/azure-ad-pwdless-image3.png)

1. Gebruikers melden zich aan bij Windows met behulp van biometrische of PIN-beweging. Met de penbeweging wordt de persoonlijke sleutel van Windows hello voor bedrijven ontgrendeld en verzonden naar de provider voor Cloud authenticatie van beveiligings ondersteuning, aangeduid als de Cloud-AP-provider.

2. De Cloud-AP-provider vraagt een nonce op uit Azure Active Directory.

3. Azure AD retourneert een nonce die 5 minuten geldig is.

4. De Cloud-AP-provider ondertekent de nonce met de persoonlijke sleutel van de gebruiker en retourneert de ondertekende nonce naar het Azure Active Directory.

5. Azure Active Directory valideert de ondertekende nonce met behulp van de veilig geregistreerde open bare sleutel van de gebruiker op basis van de nonce-hand tekening. Na het valideren van de hand tekening valideert Azure AD vervolgens de geretourneerde ondertekende nonce. Na het valideren van de nonce maakt Azure AD een PRT met een sessie sleutel die is versleuteld met de transport sleutel van het apparaat en retourneert deze naar de Cloud-AP-provider.

6. De Cloud-AP-provider ontvangt de versleutelde PRT met de sessie sleutel. Met de privé transport sleutel van het apparaat wordt de sessie sleutel door de Cloud-provider gedecodeerd en wordt de sessie sleutel beveiligd met de TPM van het apparaat.

7. De Cloud-AP-provider retourneert een geslaagd verificatie antwoord naar Windows, waarna de gebruiker toegang kan krijgen tot Windows, evenals Cloud-en on-premises toepassingen zonder dat ze opnieuw moeten worden geverifieerd (SSO).

Zie [Windows hello voor bedrijven en verificatie](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#azure-ad-join-authentication-to-active-directory-using-a-key)voor een dieper inzicht in het verificatie proces in andere Scenario's met Windows hello voor bedrijven.

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>Gebruikers beheren hun Windows hello voor bedrijven-referenties

De [micro soft PIN reset Services](/windows/security/identity-protection/hello-for-business/hello-feature-pin-reset) is een functie in azure AD waarmee gebruikers hun pincode zo nodig opnieuw kunnen instellen. Met groeps beleid, Microsoft Intune of een compatibel MDM, kan een beheerder Windows 10-apparaten zo configureren dat ze de micro soft PIN reset-service veilig gebruiken waarmee gebruikers hun verg eten pincode opnieuw kunnen instellen via instellingen of boven het vergrendelings scherm, zonder dat ze hoeven te vereisen opnieuw te registreren.

Soms moeten gebruikers terugvallen op het gebruik van wacht woorden. [Self-service voor wachtwoord herstel](../../active-directory/authentication/howto-sspr-deployment.md) (SSPR) is een andere Azure AD-functie waarmee gebruikers hun wacht woord opnieuw kunnen instellen zonder contact op te nemen met IT-personeel. Gebruikers moeten zich registreren voor de selfservice voor het opnieuw instellen van het wacht woord voordat ze de service kunnen gebruiken. Tijdens de registratie kiest de gebruiker een of meer authenticatie methoden die worden ingeschakeld door hun organisatie. SSPR stelt gebruikers in staat om snel een gedeblokkeerd te krijgen en te blijven werken, ongeacht waar ze zijn of op het tijdstip van de dag. Door gebruikers toe te staan zichzelf te deblokkeren, kan uw organisatie de niet-productieve tijd en hoge ondersteunings kosten voor de meest voorkomende problemen met betrekking tot het wacht woord beperken.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Aanmelding zonder wacht woord met Microsoft Authenticator

Aanmelden met een wacht woord met Microsoft Authenticator is een andere oplossing zonder wacht woord dat kan worden gebruikt om u aan te melden bij Azure AD-accounts via aanmelding via de telefoon. U moet uw identiteit nog steeds verifiëren door iets dat u kent en iets wat u hebt, maar met de telefoon aanmelding kunt u uw wacht woord niet meer invoeren en al uw identiteits verificatie op uw mobiele apparaat uitvoeren met behulp van uw vinger afdruk, gezicht of pincode.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator scenario's met een wacht woord

Met de Microsoft Authenticator-app kunnen gebruikers hun identiteit verifiëren en verifiëren met hun werk-of persoonlijke account. Het kan ook worden gebruikt om een wacht woord te verg Roten met een eenmalige wachtwoord code of push melding of de nood zaak voor een wacht woord te vervangen. In plaats van een wacht woord te gebruiken, kunnen gebruikers hun identiteit bevestigen met hun mobiele telefoon via vingerafdruk scan, gezichts-of Iris herkenning of pincode. Dit hulp programma is gebouwd op basis van beveiligde technologie, vergelijkbaar met wat Windows hello gebruikt, en wordt in een eenvoudige app op een mobiel apparaat gebundeld, waardoor het een handige optie is voor gebruikers. De Microsoft Authenticator-app is beschikbaar voor Android en iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Overwegingen bij de implementatie van Microsoft Authenticator

Vereisten voor het gebruik van de Microsoft Authenticator-app voor het uitvoeren van wacht woordloze aanmelding bij Azure AD bevatten het volgende:

* Eind gebruikers zijn ingeschakeld voor Azure Multi-Factor Authentication

* Gebruikers worden aanbevolen hun apparaten te registreren met behulp van Microsoft Intune of Mobile Device Management een MDM-oplossing van derden om de implementatie van de app te vereenvoudigen

Ervan uitgaande dat aan deze vereisten wordt voldaan, kunnen beheerders wacht woordloze aanmelding in de Tenant inschakelen met behulp van [Windows Power shell.](../../active-directory/authentication/howto-authentication-passwordless-phone.md) Als de aanmelding voor telefoon is ingeschakeld in de Tenant, kunnen eind gebruikers zich aanmelden met hun telefoon door hun werk-of school account te selecteren op het scherm **accounts** van de app en vervolgens aanmelden via **telefoon inschakelen**te selecteren.

Aangenomen dat aanmelding met een wacht woord is ingeschakeld door een beheerder, moeten eind gebruikers voldoen aan de volgende vereisten:

* Inge schreven in azure Multi-Factor Authentication

* Nieuwste versie van Microsoft Authenticator geïnstalleerd op apparaten met iOS 8,0 of hoger, of Android 6,0 of hoger

* Werk-of school account met push meldingen toegevoegd aan de app

Om te voor komen dat u uw account kunt vergren delen of dat u geen accounts hoeft te maken op een nieuw apparaat, wordt u aangeraden Microsoft Authenticator te gebruiken om [een back-up van uw account referenties](../../active-directory/user-help/user-help-auth-app-backup-recovery.md) naar de Cloud. Na het maken van een back-up kunt u de app ook gebruiken om uw gegevens op een nieuw apparaat te herstellen, waardoor het voor komen van vergrendelde of het opnieuw maken van accounts.

Aangezien de meeste gebruikers gewend zijn om alleen wacht woorden te gebruiken voor verificatie, is het belang rijk dat uw organisatie gebruikers begeleidt over dit proces. Met bewustzijn kan de kans worden beperkt dat gebruikers uw Help Desk bellen voor [problemen](../../active-directory/authentication/howto-authentication-passwordless-phone.md) met het aanmelden met behulp van de app Microsoft Authenticator.

> [!NOTE]
> Een potentieel storings punt voor deze oplossing is wanneer een zwervende gebruiker zich op een locatie bevindt waar zich geen Internet verbinding bevindt. FIDO2-beveiligings sleutels en Windows hello voor bedrijven zijn niet van toepassing op dezelfde beperking.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Hoe werkt aanmelden zonder wacht woord met Microsoft Authenticator?

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>Gebruiker stelt aanmelden zonder wacht woord in met Microsoft Authenticator

Voordat de Microsoft Authenticator-app kan worden gebruikt als een wachtwoordloze oplossing om zich aan te melden bij een Azure AD-account, moeten de stappen worden uitgevoerd door zowel een beheerder als de eind gebruikers.

Eerst moet een beheerder [het gebruik van de app als referentie](../../active-directory/authentication/howto-authentication-passwordless-phone.md) in de Tenant inschakelen met behulp van Windows Power shell. De beheerder moet ook eind gebruikers inschakelen voor Azure Multi-Factor Authentication (Azure MFA) en de Microsoft Authenticator-app configureren als een van de [verificatie methoden](../../active-directory/authentication/howto-mfa-mfasettings.md#verification-methods).

Eind gebruikers moeten de app Microsoft Authenticator [downloaden en installeren](../../active-directory/user-help/user-help-auth-app-download-install.md) en [hun account instellen](../../active-directory/user-help/security-info-setup-auth-app.md) om de Microsoft Authenticator-app als een van de verificatie methoden te gebruiken.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Gebruiker die gebruikmaakt van Microsoft Authenticator voor aanmelding zonder wacht woord

De Microsoft Authenticator-app kan worden gebruikt om u aan te melden bij een Azure AD-account zonder gebruik te maken van een wacht woord. Terwijl het vergrendelings scherm van Windows 10 de Microsoft Authenticator-app niet als een aanmeldings optie bevat, kunnen gebruikers nog steeds hun gebruikers naam invoeren en vervolgens een push melding ontvangen op hun mobiele apparaat om de aanwezigheid te controleren. Gebruikers bevestigen hun aanwezigheid door te zoeken op een nummer op het aanmeld scherm en vervolgens een gezichts scan, vinger afdruk of pincode op te geven om de persoonlijke sleutel te ontgrendelen en de verificatie te volt ooien. Deze multi-factor-verificatie methode is veiliger dan een wacht woord en handiger dan het invoeren van een wacht woord en een code.

![Aanmelden bij verificator](./media/ad-passwordless/azure-ad-pwdless-image4.png)

Verificatie zonder wacht woord met Microsoft Authenticator volgt hetzelfde basis patroon als Windows hello voor bedrijven, maar het is iets gecompliceerder omdat de gebruiker moet worden geïdentificeerd zodat Azure AD de Microsoft Authenticator App-versie kan vinden gebruik.

![Verificator proces](./media/ad-passwordless/azure-ad-pwdless-image5.png)

1. De gebruiker voert de gebruikers naam in.

2. Azure AD detecteert dat de gebruiker een sterke referentie heeft en de sterke referentie stroom start.

3. Er wordt een melding verzonden naar de app via Apple Push Notification Service (APNS) op iOS-apparaten of via Firebase Cloud Messa ging (FCM) op Android-apparaten.

4. De gebruiker ontvangt de push melding en opent de app.

5. De app roept Azure AD aan en ontvangt een uitdaging en nonce voor de aanwezigheid van een bewijs.

6. De gebruiker voltooit de uitdaging door de biometrische of pincode in te voeren om de persoonlijke sleutel te ontgrendelen.

7. De nonce is ondertekend met de persoonlijke sleutel en teruggestuurd naar Azure AD.

8. Azure AD voert een validatie van open bare/persoonlijke sleutels uit en retourneert een token.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Gebruiker beheert hun aanmelding zonder wacht woord met Microsoft Authenticator referenties

Met [gecombineerde registratie](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)kunnen gebruikers zich registreren en profiteren van de voor delen van zowel Azure multi-factor Authentication als selfservice voor het opnieuw instellen van wacht woorden. Gebruikers kunnen deze instellingen registreren en beheren door te navigeren naar de [pagina Mijn profiel](https://aka.ms/mysecurityinfo). Naast het inschakelen van SSPR ondersteunt gecombineerde registratie meerdere verificatie methoden en-acties.

## <a name="fido2-security-keys"></a>FIDO2-beveiligings sleutels

FIDO2 is de nieuwste versie van FIDO Alliance Standard en heeft twee onderdelen: W3C's Web authentication (webauth) en de bijbehorende FIDO Alliance client-to-Authenticator-Protocol (CTAP2). Met de FIDO2-standaarden kunnen gebruikers gebruikmaken van hardware-, mobiele en biogebaseerde verificators om eenvoudig te kunnen verifiëren met veel apps en websites in mobiele en desktop omgevingen.

Micro soft en partners van de branche werken samen met FIDO2-beveiligings apparaten voor Windows hello om eenvoudige en veilige verificatie mogelijk te maken op gedeelde apparaten. Met FIDO2-beveiligings sleutels kunt u uw referenties met u meenemen en veilig verifiëren [bij een Windows](https://aka.ms/azuread418)10-apparaat dat deel uitmaakt van uw organisatie.

Webauthx definieert een API die de ontwikkeling en implementatie van sterke, wacht woordloze authenticatie door Web-apps en services mogelijk maakt. Het CTAP-protocol maakt het mogelijk dat externe apparaten zoals FIDO-compatibele beveiligings sleutels met webauthn werken en als verificator fungeren. Met webverificatie kunnen gebruikers zich aanmelden bij onlineservices met hun gezicht, vinger afdruk, pincode of draag bare FIDO2 beveiligings sleutels, waarbij gebruik wordt gemaakt van krachtige open bare-sleutel referenties in plaats van wacht woorden. Momenteel wordt webauthn ondersteund in micro soft Edge en ondersteuning voor Chrome en Firefox is in ontwikkeling.

Apparaten en tokens die voldoen aan de FIDO2-, webauth-en CTAP-protocollen, nemen over een platformoverschrijdende oplossing van sterke verificatie zonder wacht woorden gebruiken. Micro soft-partners werken met verschillende factoren voor beveiligings sleutels, zoals USB-beveiligings sleutels en Smart Cards met NFC-functionaliteit.

### <a name="fido2-security-keys-scenarios"></a>FIDO2-scenario's voor beveiligings sleutels

FIDO2-beveiligings sleutels kunnen worden gebruikt om u aan te melden bij Azure AD door de beveiligings sleutel te kiezen als de referentie provider op het vergrendelings scherm van Windows 10. Een gebruikers naam of wacht woord is niet vereist, waardoor het een ideale oplossing is voor de eerste regel van werk nemers die Pc's delen tussen meerdere gebruikers. Ze zijn ook een uitstekende verificatie optie wanneer een bedrijfs beleid bepaalt dat de referenties van een gebruiker fysiek moeten worden gescheiden van hun apparaat. Gebruikers kunnen zich ook aanmelden bij websites met behulp van hun FIDO2-beveiligings sleutel in de micro soft Edge-browser van Windows 10 versie 1809 of hoger.

### <a name="fido2-security-keys-deployment-considerations"></a>Aandachtspunten voor de implementatie van FIDO2-beveiligings sleutels

Beheerders kunnen FIDO2-ondersteuning inschakelen in azure AD en de mogelijkheid toewijzen aan gebruikers of groepen. Policies kunnen ook worden gemaakt voor hoe sleutels worden ingericht en beperkingen configureren, zoals het toestaan of blok keren van een specifieke set beveiligings sleutels voor hardware. Sleutels moeten fysiek worden gedistribueerd naar eind gebruikers.

**De vereisten voor het inschakelen van wacht woordloze aanmelding bij Azure AD en websites met behulp van FIDO2-beveiligings sleutels bevatten het volgende:**

* Azure AD

* Azure Multi-Factor Authentication

* Preview van gecombineerde registratie

* Voor de preview-versie van de FIDO2-beveiligings sleutel is een compatibele FIDO2-beveiligings sleutel vereist

* Voor webverificatie (webauth) is micro soft Edge vereist op Windows 10 versie 1809 of hoger

* Windows-aanmelding op basis van FIDO2 vereist dat Azure AD is toegevoegd aan Windows 10 versie 1809 of hoger (de beste ervaring is op Windows 10 versie 1903 of hoger)

FIDO2-compatibele vorm factoren zijn USB-, NFC-en Bluetooth-apparaten. We raden u aan om de vorm factor te kiezen die voldoet aan uw specifieke behoeften, omdat sommige platforms en browsers nog niet compatibel zijn met FIDO2.

We raden u ook aan om in elke organisatie een protocol te maken voor gebruikers en beheerders die moeten worden uitgevoerd om te controleren of een beveiligings sleutel verloren is gegaan of wordt gestolen. Gebruikers moeten de verloren of gestolen sleutel rapporteren zodat beheerders of de gebruiker hun beveiligings sleutels uit het profiel van de gebruiker kunnen verwijderen en een nieuwe kan inrichten.

### <a name="how-fido2-security-keys-works"></a>Hoe FIDO2-beveiligings sleutels werken

#### <a name="user-sets-up-fido2-security-key"></a>Gebruiker stelt FIDO2-beveiligings sleutel in

Beheerders kunnen [hand matig sleutels inrichten](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless) en deze distribueren naar eind gebruikers, het inrichten en inschakelen van de FIDO2-referentie provider op het vergrendelings scherm van Windows 10 worden ondersteund via [intune](https://docs.microsoft.com/intune/windows-enrollment-methods). Beheerders moeten ook de [Azure Portal](https://portal.azure.com/) gebruiken om hardware-token-apparaten in te scha kelen als een verificatie methode zonder wacht woord.

Voor het implementeren van FIDO2-beveiligings sleutels moeten gebruikers hun sleutels ook registreren via [gecombineerde registratie](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md). Met gecombineerde registratie kunnen gebruikers eenmaal registreren en profiteren van de voor delen van Azure Multi-Factor Authentication en wacht woord opnieuw instellen voor eenmalige aanmelding (SSPR).

Naast het selecteren van het hardware-token als de standaard methode voor multi-factor Authentication, is het raadzaam om ook een extra verificatie optie te selecteren.

* Microsoft Authenticator--melding

* Verificator-app of-hardware-token--code

* Telefoon oproep

* Sms-bericht

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Gebruiker die de FIDO2-beveiligings sleutel gebruikt voor aanmelden

FIDO2 biedt een abstractie laag tussen de vorm factor die wordt gebruikt als authenticatie van de verificator en open bare/persoonlijke sleutel, zodat ingebouwde platform-verificatoren, zoals Windows hello en beveiligings sleutels, kunnen worden omgezet in een persoonlijke sleutel en een open bare sleutel kunnen leveren die kunnen worden gebruikt als een id voor toegang tot externe resources. FIDO2-beveiligings sleutels zijn uitgerust met hun eigen ingebouwde beveiligde enclave die de persoonlijke sleutel opslaat en de biometrische of pincode nodig heeft om deze te ontgrendelen. Referenties kunnen niet opnieuw worden gebruikt, herhaald of gedeeld tussen services en zijn niet onderhevig aan phishing-en MiTM-aanvallen of server inbreuken.

![FIDO2-aanmelding](./media/ad-passwordless/azure-ad-pwdless-image6.png)

FIDO2-beveiligings sleutels bieden beveiligde authenticatie, onafhankelijk van de vorm factor. De beveiligings sleutel bevat de referentie en moet worden beveiligd met een extra tweede factor zoals een vinger afdruk (geïntegreerd in de beveiligings sleutel) of een pincode die moet worden ingevoerd bij de Windows-aanmelding. Micro soft-partners werken aan een aantal verschillende factoren voor beveiligings sleutels. Enkele voor beelden hiervan zijn USB-beveiligings sleutels en NFC-Smart Cards.

> [!NOTE]
> Een beveiligings sleutel moet bepaalde functies en uitbrei dingen implementeren van het FIDO2 CTAP-protocol om compatibel te zijn met [micro soft](https://aka.ms/fido2securitykeys). Micro soft heeft deze oplossingen getest op compatibiliteit met Windows 10 en Azure Active Directory.

![FIDO2-aanmeldings proces](./media/ad-passwordless/azure-ad-pwdless-image9.png)

1. De gebruiker sluit het FIDO2-apparaat aan op de computer.

2. Windows detecteert de FIDO2-beveiligings sleutel.

3. Windows verzendt een verificatie aanvraag.

4. Er wordt een nonce teruggestuurd door Azure AD.

5. De gebruiker heeft de penbeweging voor het ontgrendelen van de persoonlijke sleutel die is opgeslagen in de beveiligde enclave van de FIDO2-beveiligings sleutel.

6. De FIDO2-beveiligings sleutel ondertekent de nonce met de persoonlijke sleutel.

7. De PRT-token aanvraag met een ondertekende nonce wordt verzonden naar Azure AD.

8. Azure AD verifieert de ondertekende nonce met behulp van de open bare sleutel FIDO2.

9. Azure AD retourneert PRT om toegang tot on-premises resources mogelijk te maken.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>Gebruiker beheert de referenties van hun FIDO2-beveiligings sleutel

Net als bij de Microsoft Authenticator-app is referentie beheer voor FIDO2-beveiligings sleutels afhankelijk van de gecombineerde registratie-ervaring voor eind gebruikers.

## <a name="deciding-a-passwordless-method"></a>Een methode voor een wacht woord kiezen

U kunt kiezen uit drie opties met een wacht woord, afhankelijk van de vereisten voor beveiliging, platform en app van uw bedrijf.

Hier volgen enkele factoren waarmee u rekening moet houden bij het kiezen van micro soft-technologie voor wacht woord-minder:

||**Windows Hello voor Bedrijven**|**Aanmelding zonder wacht woord met de Microsoft Authenticator-app**|**FIDO2-beveiligings sleutels**|
|:-|:-|:-|:-|
|**Vereiste**| Windows 10, versie 1809 of hoger<br>Azure Active Directory| Microsoft Authenticator-app<br>Telefoon (iOS-en Android-apparaten met Android 6,0 of hoger.)|Windows 10, versie 1809 of hoger<br>Azure Active Directory|
|**Penmodus**|Platform|Software|Hardware|
|**Systemen en apparaten**|PC met ingebouwde Trusted Platform Module (TPM)<br>Herkenning van PINCODEs en biometrie |Herkenning van PINCODEs en biometrie op telefoon|FIDO2-beveiligings apparaten die compatibel zijn met micro soft|
|**Gebruikers ervaring**|Meld u aan met een pincode of biometrische herkenning (gezicht, Iris of vinger afdruk) met Windows-apparaten.<br>Windows hello-verificatie is gekoppeld aan het apparaat. de gebruiker heeft het apparaat en een aanmeldings onderdeel zoals een pincode of biometrische factor nodig om toegang te krijgen tot bedrijfs bronnen.|Meld u aan met een mobiele telefoon met vingerafdruk scan, gezichts controle of Iris herkenning of pincode.<br>Gebruikers melden zich aan bij een werk-of persoonlijk account vanaf hun PC of mobiele telefoon.|Aanmelden met FIDO2-beveiligings apparaat (biometrie, PIN en NFC)<br>Gebruiker heeft toegang tot het apparaat op basis van organisatie besturings elementen en verificatie op basis van pincode, biometrie met behulp van apparaten zoals USB-beveiligings sleutels en met NFC ingeschakelde Smart Cards, sleutels of Wearables.|
|**Ingeschakelde scenario's**| Wachtwoord minder ervaring met Windows-apparaat.<br>Van toepassing op toegewezen werk computer met mogelijkheid voor eenmalige aanmelding bij apparaten en toepassingen.|Een wacht woord met een mobiele telefoon.<br>Van toepassing voor toegang tot zakelijke of persoonlijke toepassingen op het web vanaf elk apparaat.|Wacht woord minder ervaring voor werk nemers met biometrie, PIN en NFC.<br>Van toepassing op gedeelde Pc's en waar een mobiele telefoon geen geschikte optie is (zoals voor helpdesk personeel, open bare kiosk of ziekenhuis team)|

Gebruik de volgende tabel om te kiezen welke methode uw vereisten en gebruikers ondersteunt.

|Persona|Scenario|Omgeving|Technologie met wacht woord|
|:-|:-|:-|:-|
|**Beheerder**|Beveiligde toegang tot een apparaat voor beheer taken|Toegewezen Windows 10-apparaat|Windows hello voor bedrijven en/of FIDO2-beveiligings sleutel|
|**Beheerder**|Beheer taken op niet-Windows-apparaten| Mobiel of niet-Windows-apparaat|Aanmelding zonder wacht woord met de Microsoft Authenticator-app|
|**Informatie medewerker**|Productiviteits werk|Toegewezen Windows 10-apparaat|Windows hello voor bedrijven en/of FIDO2-beveiligings sleutel|
|**Informatie medewerker**|Productiviteits werk| Mobiel of niet-Windows-apparaat|Aanmelding zonder wacht woord met de Microsoft Authenticator-app|
|**Frontline-werk nemer**|Kiosken in een fabriek, plant, detail handel of gegevens invoer|Gedeelde Windows 10-apparaten|FIDO2-beveiligings sleutels|

## <a name="getting-started"></a>Aan de slag

Verificatie met een wacht woord is de Golf van de toekomst en het pad naar een veiligere omgeving. Het wordt aanbevolen dat organisaties deze wijziging plannen en hun afhankelijkheden voor wacht woorden reduceren. Als u aan de slag wilt gaan, moet u rekening houden met de volgende doelen:

* Gebruikers inschakelen voor MFA + Microsoft Authenticator app + voorwaardelijke toegang.

* Implementatie van Azure AD-wachtwoord beveiliging + update beleid.

* Gebruikers inschakelen voor SSPR met gecombineerde registratie.

* Implementeer Microsoft Authenticator-app voor mobiliteit.

* Windows hello voor bedrijven (1903: blijf actueel) implementeren.

* Implementeer FIDO2-apparaten voor gebruikers die geen telefoons kunnen gebruiken.

* Schakel op wacht woord gebaseerde verificatie, indien mogelijk, uit.

Om deze doel stellingen te bereiken, raden we u aan de volgende aanpak te volgen:

1. Schakel Azure Active Directory in om optimaal gebruik te maken van functies zoals Azure MFA en voorwaardelijke toegang.

2. Schakel multi-factor Authentication in om extra beveiliging te bieden.

3. Schakel self-service voor wacht woord opnieuw instellen in voor de gebeurtenis die gebruikers nodig hebben om terug te vallen op het gebruik van een wacht woord.

4. Microsoft Authenticator aanmelding via de telefoon implementeren voor het toevoegen van mobiliteit.

5. Implementeer Windows hello voor bedrijven op al uw Windows 10-apparaten.

6. Bereid u voor op FIDO2-beveiligings sleutels.

> [!NOTE]
> Raadpleeg de pagina met Azure Active Directory [licenties](https://azure.microsoft.com/pricing/details/active-directory/) voor meer informatie over de licentie vereisten voor methoden zonder wacht woord.

## <a name="conclusion"></a>Conclusie

In de afgelopen jaren heeft micro soft hun toezeg ging voor het inschakelen van een wereld zonder wacht woorden voortgezet. Met Windows 10 heeft micro soft Windows hello voor bedrijven geïntroduceerd, een krachtige, met hardware beschermde twee ledige referentie waarmee eenmalige aanmelding kan worden Azure Active Directory en Active Directory. Net als de technologie van Windows hello voor bedrijven gebruikt de Microsoft Authenticator-app verificatie op basis van een sleutel om een gebruikers referentie in te scha kelen die is gekoppeld aan een mobiel apparaat en gebruikmaakt van een biometrische of pincode. Met FIDO2-beveiligings sleutels kunt u uw referenties met u meenemen en u aanmelden bij Azure AD door de beveiligings sleutel te kiezen als de referentie provider op het vergrendelings scherm van Windows 10. Alle drie deze oplossingen met een wacht woord verminderen het risico op phishing, wacht woord-en replay-aanvallen en bieden gebruikers een zeer veilige en gemakkelijke manier om zich vanaf elke locatie aan te melden en toegang te krijgen tot gegevens.

De invoering van moderne multi-factor Authentication-technologieën, zoals biometrische en open bare-sleutel cryptografie, is een van de meest zinvolle stappen waarmee het risico van de identiteit van het bedrijf kan worden verkleind. Het gebruik van een wacht woord is een langdurige benadering voor beveiligde authenticatie en is nog steeds in ontwikkeling. Aan de hand van de opkomende vereisten kunnen organisaties zichzelf voorbereiden door een plan te maken om te beginnen met het verplaatsen van technologieën zonder wacht woord.

## <a name="next-steps"></a>Volgende stappen

* Een overzicht van [Wat is een wacht woord?](../../active-directory/authentication/concept-authentication-passwordless.md)
* [Wacht woord in azure AD inschakelen](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
