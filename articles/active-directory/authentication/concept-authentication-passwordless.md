---
title: Aanmelden zonder wachtwoord voor Azure Active Directory (voorbeeld)
description: Meer informatie over opties voor wachtwoordloze aanmelding bij Azure Active Directory met FIDO2-beveiligingssleutels of de Microsoft Authenticator-app
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
ms.openlocfilehash: 402eaecbf03fd52fbb5e871fdd196da2bc9a3e1f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743531"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Wachtwoordloze verificatieopties voor Azure Active Directory

Multi-factor authenticatie (MFA) is een geweldige manier om uw organisatie te beveiligen, maar gebruikers raken vaak gefrustreerd met de extra beveiligingslaag bovenop het moeten onthouden van hun wachtwoorden. Wachtwoordloze authenticatie methoden zijn handiger omdat het wachtwoord wordt verwijderd en vervangen door iets wat je hebt, plus iets wat je bent of iets wat je weet.

|   | Iets wat je hebt | Iets wat je bent of weet |
| --- | --- | --- |
| Zonder wachtwoord | Windows 10-apparaat, telefoon of beveiligingssleutel | Biometrische of pincode |

Elke organisatie heeft andere behoeften als het gaat om authenticatie. Microsoft biedt de volgende drie wachtwoordloze verificatieopties die worden geïntegreerd met Azure Active Directory (Azure AD):

- Windows Hello voor Bedrijven
- Microsoft Authenticator-app
- FIDO2-beveiligingssleutels

![Verificatie: Beveiliging versus gemak](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello voor Bedrijven

Windows Hello for Business is ideaal voor informatiewerkers die hun eigen aangewezen Windows-pc hebben. De biometrische en pincode is rechtstreeks gekoppeld aan de pc van de gebruiker, waardoor toegang van iemand anders dan de eigenaar wordt voorkomen. Met pki-integratie (public key infrastructure) en ingebouwde ondersteuning voor single sign-on (SSO) biedt Windows Hello for Business een handige methode om naadloos toegang te krijgen tot bedrijfsbronnen on-premises en in de cloud.

![Voorbeeld van een gebruikersaanmelding met Windows Hello voor Bedrijven](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

In de volgende stappen wordt weergegeven hoe het aanmeldingsproces werkt met Azure Active Directory.

![Diagram waarin de stappen worden beschreven die betrokken zijn bij de aanmelding van gebruikers met Windows Hello voor Bedrijven](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Een gebruiker meldt zich bij Windows met biometrische of pincodegebaar. Het gebaar ontgrendelt de privésleutel van Windows Hello for Business en wordt verzonden naar de cloudverificatiebeveiligingsprovider, de *Cloud AP-provider*genoemd.
1. De Cloud AP-provider vraagt een nonce van Azure AD.
1. Azure AD retourneert een nonce die 5 minuten geldig is.
1. De Cloud AP-provider ondertekent de nonce met behulp van de privésleutel van de gebruiker en retourneert de ondertekende nonce naar het Azure AD.
1. Azure AD valideert de ondertekende nonce met behulp van de veilig geregistreerde openbare sleutel van de gebruiker tegen de nonce-handtekening. Na het valideren van de handtekening valideert Azure AD vervolgens de geretourneerde ondertekende nonce. Wanneer de nonce is gevalideerd, maakt Azure AD een primair vernieuwingstoken (PRT) met sessiesleutel die is versleuteld naar de transportsleutel van het apparaat en deze retourneert naar de Cloud AP-provider.
1. De Cloud AP-provider ontvangt de versleutelde PRT met sessiesleutel. Met behulp van de privétransportsleutel van het apparaat decodeert de Cloud AP-provider de sessiesleutel en beschermt hij de sessiesleutel met behulp van de Trusted Platform Module (TPM) van het apparaat.
1. De Cloud AP-provider retourneert een succesvolle verificatiereactie op Windows. De gebruiker heeft dan toegang tot Windows en cloud- en on-premises applicaties zonder dat hij zich opnieuw hoeft te authenticeren (SSO).

De [planningshandleiding](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) voor Windows Hello voor Bedrijven kan worden gebruikt om u te helpen beslissingen te nemen over het type Windows Hello voor Business-implementatie en de opties waarmee u moet overwegen.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-app

Laat de telefoon van uw werknemer een wachtwoordloze verificatiemethode worden. Mogelijk gebruikt u de Microsoft Authenticator-app al als een handige optie voor meervoudige verificatie naast een wachtwoord. U de Authenticator-app ook als wachtwoordloze optie gebruiken.

![Aanmelden bij Microsoft Edge met de Microsoft Authenticator-app](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

De Authenticator-app verandert elke iOS- of Android-telefoon in een sterke, wachtwoordloze referentie. Gebruikers kunnen zich aanmelden bij elk platform of browser door een melding op hun telefoon te krijgen, een nummer op het scherm te matchen met het nummer op hun telefoon en vervolgens hun biometrische (aanraking of gezicht) of pincode te gebruiken om te bevestigen. Raadpleeg [De Microsoft Authenticator-app downloaden en installeren](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) voor installatiegegevens.

Wachtwoordloze verificatie met de Authenticator-app volgt hetzelfde basispatroon als Windows Hello for Business. Het is een beetje ingewikkelder als de gebruiker moet worden geïdentificeerd, zodat Azure AD kan de Microsoft Authenticator App versie wordt gebruikt vinden:

![Diagram waarin de stappen worden beschreven die betrokken zijn bij de aanmelding van gebruikers met de Microsoft Authenticator-app](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. De gebruiker voert zijn gebruikersnaam in.
1. Azure AD detecteert dat de gebruiker een sterke referentie heeft en start de strong credential flow.
1. Er wordt een melding naar de app verzonden via De Push Notification Service (APNS) van Apple op iOS-apparaten of via Firebase Cloud Messaging (FCM) op Android-apparaten.
1. De gebruiker ontvangt de pushmelding en opent de app.
1. De app roept Azure AD en ontvangt een proof-of-presence uitdaging en nonce.
1. De gebruiker voltooit de uitdaging door het invoeren van hun biometrische of PINCODE om prive-sleutel te ontgrendelen.
1. De nonce is ondertekend met de privésleutel en teruggestuurd naar Azure AD.
1. Azure AD voert validatie van openbare/private sleutels uit en retourneert een token.

## <a name="fido2-security-keys"></a>FIDO2-beveiligingssleutels

FIDO2-beveiligingssleutels zijn een onphishable standaardengebaseerde wachtwoordloze verificatiemethode die in elke vormfactor kan worden geleverd. Fast Identity Online (FIDO) is een open standaard voor wachtwoordloze authenticatie. Fido stelt gebruikers en organisaties in staat om gebruik te maken van de standaard om in te loggen op hun bronnen zonder gebruikersnaam of wachtwoord met behulp van een externe beveiligingssleutel of een platformsleutel die in een apparaat is ingebouwd.

Voor openbare preview kunnen werknemers beveiligingssleutels gebruiken om zich aan te melden bij hun Azure AD of hybride Azure AD-apparaten die lid zijn geworden van Windows 10-apparaten en zich eenmalig aanmelden bij hun cloud- en on-premises resources. Gebruikers kunnen zich ook aanmelden bij ondersteunde browsers. FIDO2-beveiligingssleutels zijn een geweldige optie voor bedrijven die zeer beveiligingsgevoelig zijn of scenario's of werknemers hebben die niet bereid of in staat zijn om hun telefoon als tweede factor te gebruiken.

![Aanmelden bij Microsoft Edge met een beveiligingssleutel](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Het volgende proces wordt gebruikt wanneer een gebruiker zich aanmeldt met een FIDO2-beveiligingssleutel:

![Diagram waarin de stappen worden beschreven die betrokken zijn bij het aanmelden van gebruikers met een FIDO2-beveiligingssleutel](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. De gebruiker sluit de FIDO2-beveiligingssleutel aan op zijn computer.
2. Windows detecteert de FIDO2-beveiligingssleutel.
3. Windows stuurt een verificatieverzoek.
4. Azure AD stuurt een nonce terug.
5. De gebruiker voltooit zijn gebaar om de privésleutel te ontgrendelen die is opgeslagen in de beveiligde enclave van de FIDO2-beveiligingssleutel.
6. De FIDO2-beveiligingssleutel tekent de nonce met de privésleutel.
7. De primaire vernieuwingstoken (PRT)-tokenaanvraag met ondertekend nonce wordt verzonden naar Azure AD.
8. Azure AD verifieert de ondertekende nonce met behulp van de fido2-openbare sleutel.
9. Azure AD retourneert PRT om toegang tot on-premises resources mogelijk te maken.

Hoewel er veel sleutels zijn die FIDO2-gecertificeerd zijn door de FIDO Alliance, vereist Microsoft een aantal optionele uitbreidingen van de FIDO2 Client-to-Authenticator Protocol (CTAP)-specificatie die door de leverancier moet worden geïmplementeerd om maximale beveiliging en de beste ervaring te garanderen.

Een beveiligingssleutel **MOET** de volgende functies en extensies uit het FIDO2 CTAP-protocol implementeren om Microsoft-compatibel te zijn:

| # | Vertrouwensrelatie functie/ extensie | Waarom is deze functie of extensie vereist? |
| --- | --- | --- |
| 1 | De sleutel van de ingezetene | Met deze functie kan de beveiligingssleutel draagbaar zijn, waarbij uw referentie is opgeslagen op de beveiligingssleutel. |
| 2 | Clientpin | Met deze functie u uw referenties met een tweede factor beveiligen en is van toepassing op beveiligingssleutels die geen gebruikersinterface hebben. |
| 3 | hmac-geheim | Deze extensie zorgt ervoor dat u zich aanmelden bij uw apparaat wanneer het off-line of in vliegtuigmodus is. |
| 4 | Meerdere accounts per RP | Deze functie zorgt ervoor dat u dezelfde beveiligingssleutel gebruiken voor meerdere services zoals Microsoft Account en Azure Active Directory. |

De volgende providers bieden FIDO2-beveiligingssleutels van verschillende vormfactoren waarvan bekend is dat ze compatibel zijn met de wachtwoordloze ervaring. We raden u aan om de beveiligingseigenschappen van deze sleutels te evalueren door contact op te nemen met de leverancier en de FIDO Alliance.

| Provider | Contactpersoon |
| --- | --- |
| Yubico Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian (Feitian) | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| Hid | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Thales Groep) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf](https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> Als u NFC-gebaseerde beveiligingssleutels koopt en van plan bent te gebruiken, hebt u een ondersteunde NFC-lezer nodig voor de beveiligingssleutel. De NFC-lezer is geen Azure-vereiste of -beperking. Neem contact op met de leverancier voor uw NFC-gebaseerde beveiligingssleutel voor een lijst met ondersteunde NFC-lezers.

Als u een leverancier bent en uw apparaat in deze lijst [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)met ondersteunde apparaten wilt plaatsen, neemt u contact op met .

## <a name="what-scenarios-work-with-the-preview"></a>Welke scenario's werken met de preview?

- Beheerders kunnen verificatiemethoden zonder wachtwoord inschakelen voor hun tenant
- Beheerders kunnen alle gebruikers of gebruikers/groepen binnen hun tenant voor elke methode targeten
- Eindgebruikers kunnen deze wachtwoordloze verificatiemethoden registreren en beheren in hun accountportal
- Eindgebruikers kunnen zich aanmelden met deze wachtwoordloze verificatiemethoden
   - Microsoft Authenticator-app: werkt in scenario's waarin Azure AD-verificatie wordt gebruikt, ook in alle browsers, tijdens de installatie van Windows 10 Out Of Box (OOBE) en met geïntegreerde mobiele apps op elk besturingssysteem.
   - Beveiligingssleutels: Werk op het vergrendelingsscherm voor Windows 10 en het web in ondersteunde browsers zoals Microsoft Edge (zowel legacy als nieuwe Edge).

## <a name="choose-a-passwordless-method"></a>Een methode zonder wachtwoord kiezen

De keuze tussen deze drie wachtwoordloze opties is afhankelijk van de beveiligings-, platform- en app-vereisten van uw bedrijf.

Hier volgen enkele factoren waarmee u rekening moet houden bij het kiezen van microsoft-wachtwoordloze technologie:

||**Windows Hello voor Bedrijven**|**Aanmelden zonder wachtwoord met de Microsoft Authenticator-app**|**FIDO2-beveiligingssleutels**|
|:-|:-|:-|:-|
|**Vereiste**| Windows 10, versie 1809 of hoger<br>Azure Active Directory| Microsoft Authenticator-app<br>Telefoon (iOS- en Android-apparaten met Android 6.0 of hoger.)|Windows 10, versie 1809 of hoger<br>Azure Active Directory|
|**Modus**|Platform|Software|Hardware|
|**Systemen en apparaten**|PC met een ingebouwde Trusted Platform Module (TPM)<br>Identificatie van pincodes en biometrie |PIN- en biometrieherkenning op telefoon|FIDO2-beveiligingsapparaten die compatibel zijn met Microsoft|
|**Gebruikerservaring**|Meld u aan met een pincode of biometrische herkenning (gezichts-, iris- of vingerafdruk) met Windows-apparaten.<br>Windows Hello-verificatie is gekoppeld aan het apparaat; de gebruiker heeft zowel het apparaat als een aanmeldingscomponent nodig, zoals een pincode of biometrische factor, om toegang te krijgen tot bedrijfsbronnen.|Meld u aan met een mobiele telefoon met vingerafdrukscan, gezichts- of irisherkenning of pincode.<br>Gebruikers melden zich aan voor een werk- of privéaccount vanaf hun pc of mobiele telefoon.|Inloggen met fido2-beveiligingsapparaat (biometrie, pincode en NFC)<br>De gebruiker heeft toegang tot het apparaat op basis van organisatiebesturingselementen en kan worden geverifieerd op basis van pincode, biometrie met behulp van apparaten zoals USB-beveiligingssleutels en NFC-enabled smartcards, sleutels of wearables.|
|**Ingeschakelde scenario's**| Ervaring zonder wachtwoord met Windows-apparaat.<br>Van toepassing op speciale werk-pc's met mogelijkheid voor eenmalige aanmelding voor apparaten en toepassingen.|Overal een oplossing zonder wachtwoord met behulp van een mobiele telefoon.<br>Van toepassing voor toegang tot werk of persoonlijke toepassingen op het web vanaf elk apparaat.|Ervaring zonder wachtwoord voor werknemers die biometrie, pincode en NFC gebruiken.<br>Van toepassing op gedeelde pc's en wanneer een mobiele telefoon geen haalbare optie is (zoals voor helpdeskpersoneel, openbare kiosk of ziekenhuisteam)|

Gebruik de volgende tabel om te kiezen welke methode uw vereisten en gebruikers ondersteunt.

|Persona|Scenario|Omgeving|Wachtwoordloze technologie|
|:-|:-|:-|:-|
|**Beheerder**|Beveiligde toegang tot een apparaat voor beheertaken|Toegewezen Windows 10-apparaat|Windows Hello for Business- en/of FIDO2-beveiligingssleutel|
|**Beheerder**|Beheertaken op niet-Windows-apparaten| Mobiel of niet-Windows-apparaat|Aanmelden zonder wachtwoord met de Microsoft Authenticator-app|
|**Informatiemedewerker**|Productiviteitswerk|Toegewezen Windows 10-apparaat|Windows Hello for Business- en/of FIDO2-beveiligingssleutel|
|**Informatiemedewerker**|Productiviteitswerk| Mobiel of niet-Windows-apparaat|Aanmelden zonder wachtwoord met de Microsoft Authenticator-app|
|**Eerstelijnswerknemer**|Kiosken in een fabriek, fabriek, detailhandel of gegevensinvoer|Gedeelde Windows 10-apparaten|FIDO2-beveiligingssleutels|

## <a name="next-steps"></a>Volgende stappen

[Fido2-beveiligingssleutelwachtwoordloze opties in uw organisatie inschakelen](howto-authentication-passwordless-security-key.md)

[Opties zonder telefoon inschakelen in uw organisatie](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Externe koppelingen

[FIDO Alliantie](https://fidoalliance.org/)

[FIDO2 CTAP-specificatie](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
