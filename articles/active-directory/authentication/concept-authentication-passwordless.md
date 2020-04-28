---
title: Azure Active Directory aanmelding zonder wacht woord (preview-versie)
description: Meer informatie over opties voor aanmelden met een wacht woord voor Azure Active Directory met behulp van FIDO2-beveiligings sleutels of de Microsoft Authenticator-app
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe602972cb16bf24b1c35b2aadfe25c499bce69f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181848"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Verificatie opties met een wacht woord voor Azure Active Directory

Multi-factor Authentication (MFA) is een uitstekende manier om uw organisatie te beveiligen, maar gebruikers worden vaak met de extra beveiligingslaag op de hoogte gehouden om hun wacht woord te onthouden. Verificatie methoden met een wacht woord zijn handiger omdat het wacht woord wordt verwijderd en vervangen door iets wat u hebt, en wat u wel of iets weet.

|   | Iets dat u hebt | Iets dat u weet of weet |
| --- | --- | --- |
| Zonder wachtwoord | Windows 10-apparaat, telefoon of beveiligings sleutel | Biometrische of pincode |

Elke organisatie heeft verschillende behoeften wanneer dit van toepassing is op verificatie. Micro soft biedt de volgende drie verificatie opties met een wacht woord dat kan worden geïntegreerd met Azure Active Directory (Azure AD):

- Windows Hello voor Bedrijven
- Microsoft Authenticator-app
- FIDO2-beveiligings sleutels

![Verificatie: beveiliging versus gebruiks gemak](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello voor Bedrijven

Windows hello voor bedrijven is ideaal voor IT-mede werkers die hun eigen aangewezen Windows-PC hebben. De biometrische en pincode zijn rechtstreeks gebonden aan de PC van de gebruiker, waardoor de toegang van iemand anders dan de eigenaar wordt voor komen. Met de integratie van open bare-sleutel infrastructuur (PKI) en ingebouwde ondersteuning voor eenmalige aanmelding (SSO) biedt Windows hello voor bedrijven een handige methode voor probleemloze toegang tot bedrijfs bronnen on-premises en in de Cloud.

![Voor beeld van een gebruiker die zich aanmeldt met Windows hello voor bedrijven](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

De volgende stappen laten zien hoe het aanmeldings proces werkt met Azure Active Directory.

![Diagram met een overzicht van de stappen die nodig zijn voor het aanmelden van gebruikers met Windows hello voor bedrijven](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Een gebruiker meldt zich aan bij Windows met biometrische of PIN-beweging. Met de penbeweging wordt de persoonlijke sleutel van Windows hello voor bedrijven ontgrendeld en verzonden naar de provider voor Cloud authenticatie van beveiligings ondersteuning, aangeduid als de *Cloud-AP-provider*.
1. De Cloud-AP-provider vraagt een nonce aan bij Azure AD.
1. Azure AD retourneert een nonce die 5 minuten geldig is.
1. De Cloud-AP-provider ondertekent de nonce met de persoonlijke sleutel van de gebruiker en retourneert de ondertekende nonce naar Azure AD.
1. Azure AD valideert de ondertekende nonce met de veilig geregistreerde open bare sleutel van de gebruiker op basis van de nonce-hand tekening. Na het valideren van de hand tekening valideert Azure AD vervolgens de geretourneerde ondertekende nonce. Wanneer de nonce is gevalideerd, maakt Azure AD een primair vernieuwings token (PRT) met een sessie sleutel die is versleuteld met de transport sleutel van het apparaat en retourneert deze naar de Cloud-AP-provider.
1. De Cloud-AP-provider ontvangt de versleutelde PRT met de sessie sleutel. Met de privé transport sleutel van het apparaat wordt de sessie sleutel door de Cloud-provider gedecodeerd en wordt de sessie sleutel beschermd met behulp van de Trusted Platform Module (TPM) van het apparaat.
1. De Cloud-AP-provider retourneert een geslaagd verificatie antwoord naar Windows. De gebruiker kan vervolgens toegang krijgen tot zowel Windows als Cloud-en on-premises toepassingen zonder dat ze opnieuw moeten worden geverifieerd (SSO).

De [plannings handleiding](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) voor Windows hello voor bedrijven kan worden gebruikt om u te helpen bij het nemen van beslissingen over het type implementatie van Windows hello voor bedrijven en de opties die u moet overwegen.

## <a name="microsoft-authenticator-app"></a>App Microsoft Authenticator

Laat de telefoon van uw werk nemer een verificatie methode met een wacht woord maken. U kunt ook de Microsoft Authenticator-app gebruiken als een handige multi-factor Authentication-optie naast een wacht woord. U kunt de verificator-app ook gebruiken als een optie met een wacht woord.

![Aanmelden bij micro soft Edge met de app Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Met de verificator-app wordt een wille keurige iOS-of Android-telefoon omgezet in een sterke, wacht woordloze referentie. Gebruikers kunnen zich aanmelden bij elk platform of browser door een melding te ontvangen bij een wille keurige telefoon, een nummer dat op het scherm wordt weer gegeven op de telefoon en vervolgens met behulp van de biometrische (Touch of face) of de pincode te bevestigen. Raadpleeg [de Microsoft Authenticator-app downloaden en installeren](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) voor meer informatie over de installatie.

Verificatie met een wacht woord met behulp van de verificator-app volgt hetzelfde basis patroon als Windows hello voor bedrijven. Het is iets gecompliceerder omdat de gebruiker moet worden geïdentificeerd zodat Azure AD de Microsoft Authenticator App-versie die wordt gebruikt kan vinden:

![Diagram met een overzicht van de stappen die nodig zijn voor het aanmelden van gebruikers met de app Microsoft Authenticator](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. De gebruiker voert de gebruikers naam in.
1. Azure AD detecteert dat de gebruiker een sterke referentie heeft en de sterke referentie stroom start.
1. Er wordt een melding verzonden naar de app via Apple Push Notification Service (APNS) op iOS-apparaten of via Firebase Cloud Messa ging (FCM) op Android-apparaten.
1. De gebruiker ontvangt de push melding en opent de app.
1. De app roept Azure AD aan en ontvangt een uitdaging en nonce voor de aanwezigheid van een bewijs.
1. De gebruiker voltooit de uitdaging door de biometrische of pincode in te voeren om de persoonlijke sleutel te ontgrendelen.
1. De nonce is ondertekend met de persoonlijke sleutel en teruggestuurd naar Azure AD.
1. Azure AD voert een validatie van open bare/persoonlijke sleutels uit en retourneert een token.

## <a name="fido2-security-keys"></a>FIDO2-beveiligings sleutels

FIDO2-beveiligings sleutels zijn een niet-verwerkte op standaarden gebaseerde verificatie methode op basis van een wacht woord, die in elke vorm factor kan worden gebruikt. Fast Identity online (FIDO) is een open standaard voor verificatie met een wacht woord. Met FIDO kunnen gebruikers en organisaties de standaard gebruiken om zich aan te melden bij hun resources zonder gebruikers naam of wacht woord met behulp van een externe beveiligings sleutel of een platform sleutel die is ingebouwd in een apparaat.

Voor een open bare Preview kunnen werk nemers beveiligings sleutels gebruiken om zich aan te melden bij hun Azure AD-of hybride Azure AD-lid van Windows 10-apparaten en eenmalige aanmelding op hun Cloud-en on-premises resources op te halen. Gebruikers kunnen zich ook aanmelden bij ondersteunde browsers. FIDO2-beveiligings sleutels zijn een uitstekende optie voor ondernemingen die zeer gevoelig zijn voor bedrijven of die scenario's of werk nemers hebben die hun telefoon niet kunnen gebruiken als een tweede factor.

![Aanmelden bij micro soft Edge met een beveiligings sleutel](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Het volgende proces wordt gebruikt wanneer een gebruiker zich aanmeldt met een FIDO2-beveiligings sleutel:

![Diagram met een overzicht van de stappen voor het aanmelden van gebruikers met een FIDO2-beveiligings sleutel](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. De gebruiker koppelt de FIDO2-beveiligings sleutel aan hun computer.
2. Windows detecteert de FIDO2-beveiligings sleutel.
3. Windows verzendt een verificatie aanvraag.
4. Er wordt een nonce teruggestuurd door Azure AD.
5. De gebruiker heeft de penbeweging voor het ontgrendelen van de persoonlijke sleutel die is opgeslagen in de beveiligde enclave van de FIDO2-beveiligings sleutel.
6. De FIDO2-beveiligings sleutel ondertekent de nonce met de persoonlijke sleutel.
7. De token aanvraag voor het primaire vernieuwings token (PRT) met een ondertekende nonce wordt verzonden naar Azure AD.
8. Azure AD verifieert de ondertekende nonce met behulp van de open bare sleutel FIDO2.
9. Azure AD retourneert PRT om toegang tot on-premises resources mogelijk te maken.

Hoewel er veel sleutels zijn die FIDO2 gecertificeerd door de FIDO Alliance, vereist micro soft een aantal optionele uitbrei dingen van de FIDO2-client-to-Authenticator-Protocol (CTAP) die door de leverancier moet worden geïmplementeerd om de maximale beveiliging en de beste ervaring te garanderen.

Een beveiligings sleutel **moet** de volgende functies en uitbrei dingen van het FIDO2 CTAP-protocol implementeren als compatibel met micro soft:

| # | Functie/uitbrei ding vertrouwen | Waarom is deze functie of extensie vereist? |
| --- | --- | --- |
| 1 | Residente sleutel | Met deze functie kan de beveiligings sleutel worden overdraagbaar, waar uw referentie wordt opgeslagen op de beveiligings sleutel. |
| 2 | Client pincode | Met deze functie kunt u uw referenties beveiligen met een tweede factor en van toepassing op beveiligings sleutels die geen gebruikers interface hebben. |
| 3 | HMAC-geheim | Deze uitbrei ding zorgt ervoor dat u zich kunt aanmelden bij uw apparaat wanneer het offline is of in de vliegtuig stand. |
| 4 | Meerdere accounts per RP | Deze functie zorgt ervoor dat u dezelfde beveiligings sleutel kunt gebruiken voor meerdere services, zoals micro soft-account en Azure Active Directory. |

De volgende providers bieden FIDO2-beveiligings sleutels van verschillende vorm factoren waarvan bekend is dat ze compatibel zijn met de ervaring zonder wacht woord. We raden u aan de beveiligings eigenschappen van deze sleutels te evalueren door contact op te nemen met de leverancier en FIDO Alliance.

| Provider | Contactpersoon |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| SPECIALE | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Thales-Groep) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/sites/default/files/2019-01/OneSpan-FIDO-Authentication.pdf](https://www.onespan.com/sites/default/files/2019-01/OneSpan-FIDO-Authentication.pdf) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> Als u van plan bent om op NFC gebaseerde beveiligings sleutels te kopen en te gebruiken, moet u een ondersteunde NFC-lezer voor de beveiligings sleutel hebben. De NFC-lezer is geen vereisten of beperking van Azure. Neem contact op met de leverancier van uw op NFC gebaseerde beveiligings sleutel voor een lijst met ondersteunde NFC-lezers.

Als u een leverancier bent en uw apparaat op deze lijst met ondersteunde apparaten wilt ontvangen, neemt u [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)contact op met.

## <a name="what-scenarios-work-with-the-preview"></a>Welke scenario's werken met de preview-versie?

- Beheerders kunnen verificatie methoden met een wacht woord toestaan voor hun Tenant
- Beheerders kunnen alle gebruikers bereiken of gebruikers/groepen selecteren binnen hun Tenant voor elke methode
- Eind gebruikers kunnen deze verificatie methoden zonder wacht woord registreren en beheren in hun account Portal
- Eind gebruikers kunnen zich aanmelden met deze verificatie methoden met een wacht woord
   - Microsoft Authenticator-app: werkt in scenario's waarbij Azure AD-verificatie wordt gebruikt, inclusief alle browsers, tijdens de installatie van Windows 10 out of Box (OOBE), en met geïntegreerde mobiele apps op elk besturings systeem.
   - Beveiligings sleutels: werken op het vergrendelings scherm voor Windows 10 en het web in ondersteunde browsers zoals micro soft Edge (zowel oudere als nieuwe rand).

## <a name="choose-a-passwordless-method"></a>Een methode voor een wacht woord kiezen

De keuze tussen deze drie opties met een wacht woord is afhankelijk van de vereisten voor beveiliging, platform en app van uw bedrijf.

Hier volgen enkele factoren waarmee u rekening moet houden bij het kiezen van micro soft-technologie voor een wacht woord:

||**Windows Hello voor Bedrijven**|**Aanmelding zonder wacht woord met de Microsoft Authenticator-app**|**FIDO2-beveiligings sleutels**|
|:-|:-|:-|:-|
|**Vereiste**| Windows 10, versie 1809 of hoger<br>Azure Active Directory| Microsoft Authenticator-app<br>Telefoon (iOS-en Android-apparaten met Android 6,0 of hoger.)|Windows 10, versie 1809 of hoger<br>Azure Active Directory|
|**Modus**|Platform|Software|Hardware|
|**Systemen en apparaten**|PC met ingebouwde Trusted Platform Module (TPM)<br>Herkenning van PINCODEs en biometrie |Herkenning van PINCODEs en biometrie op telefoon|FIDO2-beveiligings apparaten die compatibel zijn met micro soft|
|**Gebruikerservaring**|Meld u aan met een pincode of biometrische herkenning (gezicht, Iris of vinger afdruk) met Windows-apparaten.<br>Windows hello-verificatie is gekoppeld aan het apparaat. de gebruiker heeft het apparaat en een aanmeldings onderdeel zoals een pincode of biometrische factor nodig om toegang te krijgen tot bedrijfs bronnen.|Meld u aan met een mobiele telefoon met vingerafdruk scan, gezichts controle of Iris herkenning of pincode.<br>Gebruikers melden zich aan bij een werk-of persoonlijk account vanaf hun PC of mobiele telefoon.|Aanmelden met FIDO2-beveiligings apparaat (biometrie, PIN en NFC)<br>Gebruiker heeft toegang tot het apparaat op basis van organisatie besturings elementen en verificatie op basis van pincode, biometrie met behulp van apparaten zoals USB-beveiligings sleutels en met NFC ingeschakelde Smart Cards, sleutels of Wearables.|
|**Ingeschakelde scenario's**| Wachtwoord minder ervaring met Windows-apparaat.<br>Van toepassing op toegewezen werk computer met mogelijkheid voor eenmalige aanmelding bij apparaten en toepassingen.|Een wacht woord met een mobiele telefoon.<br>Van toepassing voor toegang tot zakelijke of persoonlijke toepassingen op het web vanaf elk apparaat.|Wacht woord minder ervaring voor werk nemers met biometrie, PIN en NFC.<br>Van toepassing op gedeelde Pc's en waar een mobiele telefoon geen geschikte optie is (zoals voor helpdesk personeel, open bare kiosk of ziekenhuis team)|

Gebruik de volgende tabel om te kiezen welke methode uw vereisten en gebruikers ondersteunt.

|Persona|Scenario|Omgeving|Technologie met wacht woord|
|:-|:-|:-|:-|
|**Beheerder**|Beveiligde toegang tot een apparaat voor beheer taken|Toegewezen Windows 10-apparaat|Windows hello voor bedrijven en/of FIDO2-beveiligings sleutel|
|**Beheerder**|Beheer taken op niet-Windows-apparaten| Mobiel of niet-Windows-apparaat|Aanmelding zonder wacht woord met de Microsoft Authenticator-app|
|**Informatiemedewerker**|Productiviteits werk|Toegewezen Windows 10-apparaat|Windows hello voor bedrijven en/of FIDO2-beveiligings sleutel|
|**Informatiemedewerker**|Productiviteits werk| Mobiel of niet-Windows-apparaat|Aanmelding zonder wacht woord met de Microsoft Authenticator-app|
|**Frontline-werk nemer**|Kiosken in een fabriek, plant, detail handel of gegevens invoer|Gedeelde Windows 10-apparaten|FIDO2-beveiligings sleutels|

## <a name="next-steps"></a>Volgende stappen

[Opties voor wacht woordloze FIDO2 inschakelen in uw organisatie](howto-authentication-passwordless-security-key.md)

[Op telefoon gebaseerde opties voor wacht woord in uw organisatie inschakelen](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Externe koppelingen

[FIDO Alliance](https://fidoalliance.org/)

[FIDO2 CTAP-specificatie](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
