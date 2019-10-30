---
title: Azure Multi-Factor Authentication-Azure Active Directory configureren
description: In dit artikel wordt beschreven hoe u Azure Multi-Factor Authentication-instellingen configureert in de Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfca7ef1dabb8c6021c9df2e3174d3e208a839b5
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73042099"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Azure Multi-Factor Authentication-instellingen configureren

Dit artikel helpt u bij het beheren van Multi-Factor Authentication instellingen in de Azure Portal. Het omvat diverse onderwerpen waarmee u optimaal gebruik kunt gaan van Azure Multi-Factor Authentication. Niet alle functies zijn beschikbaar in elke versie van Azure Multi-Factor Authentication.

U kunt via de Azure Portal toegang krijgen tot de instellingen met betrekking tot Azure Multi-Factor Authentication door te bladeren **Azure Active Directory** > **MFA**.

![Azure Portal-Azure AD-Multi-Factor Authentication-instellingen](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Instellingen

Sommige van deze instellingen zijn van toepassing op de MFA-server, Azure MFA of beide.

| Functie | Beschrijving |
| ------- | ----------- |
| Account vergrendeling | Accounts in de multi-factor Authentication-Service tijdelijk vergren delen als er te veel pogingen tot authenticatie in een rij zijn geweigerd. Deze functie is alleen van toepassing op gebruikers die een pincode invoeren om te verifiëren. (MFA-server) |
| [Gebruikers blok keren/deblokkeren](#block-and-unblock-users) | Wordt gebruikt om te voor komen dat specifieke gebruikers Multi-Factor Authentication-aanvragen kunnen ontvangen. Verificatiepogingen voor geblokkeerde gebruikers worden automatisch geweigerd. Gebruikers blijven 90 dagen geblokkeerd vanaf het moment dat ze zijn geblokkeerd. |
| [Fraude waarschuwing](#fraud-alert) | Instellingen configureren met betrekking tot gebruikers die frauduleuze verificatie aanvragen rapporteren |
| [Meldingen](#notifications) | Meldingen van gebeurtenissen van MFA-server inschakelen. |
| [OATH-tokens](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Wordt gebruikt in in de cloud gebaseerde Azure MFA-omgevingen om OATH-tokens voor gebruikers te beheren. |
| [Instellingen voor telefoon gesprek](#phone-call-settings) | Instellingen configureren met betrekking tot telefoon gesprekken en begroetingen voor Cloud-en on-premises omgevingen. |
| Providers | Hiermee worden alle bestaande verificatie providers weer gegeven die u mogelijk aan uw account hebt gekoppeld. Nieuwe verificatie providers mogen niet worden gemaakt vanaf 1 september 2018 |

## <a name="manage-mfa-server"></a>MFA-server beheren

De instellingen in deze sectie gelden alleen voor MFA-servers.

| Functie | Beschrijving |
| ------- | ----------- |
| Server instellingen | MFA-server downloaden en activerings referenties genereren om uw omgeving te initialiseren |
| [Eenmalige bypass](#one-time-bypass) | Toestaan dat een gebruiker wordt geverifieerd zonder verificatie in twee stappen gedurende een beperkte periode. |
| [Regels voor opslaan in cache](#caching-rules) |  Caching wordt hoofd zakelijk gebruikt wanneer on-premises systemen, zoals VPN, meerdere verificatie aanvragen verzenden terwijl de eerste aanvraag nog wordt uitgevoerd. Met deze functie kunnen de volgende aanvragen automatisch worden uitgevoerd nadat de gebruiker de eerste verificatie heeft uitgevoerd. |
| Server status | Bekijk de status van uw on-premises MFA-servers, waaronder versie, status, IP en tijd en datum van de laatste communicatie. |

## <a name="activity-report"></a>Activiteiten rapport

De rapportage die hier beschikbaar is, is specifiek voor MFA server (on-premises). Voor Azure MFA-rapporten (Cloud) raadpleegt u het rapport aanmeldingen in azure AD.

## <a name="block-and-unblock-users"></a>Gebruikers blok keren en de blok kering opheffen

Gebruik de functie _blok keren en blok_ keren van gebruikers om te voor komen dat gebruikers verificatie aanvragen ontvangen. Verificatiepogingen voor geblokkeerde gebruikers worden automatisch geweigerd. Gebruikers blijven 90 dagen geblokkeerd vanaf het moment dat ze zijn geblokkeerd.

### <a name="block-a-user"></a>Een gebruiker blok keren

1. Meld u als beheerder aan bij [Azure Portal](https://portal.azure.com).
2. Blader naar **Azure Active Directory** > **MFA** > **blok keren/deblokkeren van gebruikers**.
3. Selecteer **toevoegen** om een gebruiker te blok keren.
4. Selecteer de **replicatie groep**. Voer de gebruikers naam voor de geblokkeerde gebruiker in als **gebruikers naam\@Domain.com**. Voer een opmerking in het veld **reden** in.
5. Selecteer **toevoegen** om het blok keren van de gebruiker te volt ooien.

### <a name="unblock-a-user"></a>Deblokkeren van een gebruiker

1. Meld u als beheerder aan bij [Azure Portal](https://portal.azure.com).
2. Blader naar **Azure Active Directory** > **MFA** > **blok keren/deblokkeren van gebruikers**.
3. Selecteer **blok kering** in de kolom **actie** naast de gebruiker om de blok kering op te heffen.
4. Voer een opmerking in het veld **reden voor blok kering opheffen** in.
5. Selecteer **blok kering opheffen** om het blok keren van de gebruiker te volt ooien.

## <a name="fraud-alert"></a>Melding bij fraude

Configureer de functie _fraude waarschuwing_ zodat uw gebruikers frauduleuze pogingen voor toegang tot hun resources kunnen rapporteren. Gebruikers kunnen fraude pogingen melden via de mobiele app of via hun telefoon.

### <a name="turn-on-fraud-alerts"></a>Fraude waarschuwingen inschakelen

1. Meld u als beheerder aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar **Azure Active Directory** > **MFA** > **fraude waarschuwing**.
3. Stel de instelling **gebruikers toestaan voor het indienen van fraude waarschuwingen** **in op aan**.
4. Selecteer **Opslaan**.

### <a name="configuration-options"></a>Configuratie-opties

* **Gebruiker blok keren wanneer fraude wordt gemeld**: als een gebruiker fraude meldt, wordt hun account geblokkeerd gedurende 90 dagen of totdat een beheerder zijn of haar account heeft gedeblokkeerd. Een beheerder kan aanmeldingen controleren met behulp van het aanmeldings rapport en de juiste actie ondernemen om fraude te voor komen. Een beheerder kan de [blok kering](#unblock-a-user) van het gebruikers account opheffen.
* **Code voor het melden van fraude tijdens de eerste begroeting**: wanneer gebruikers een telefoon oproep ontvangen om verificatie in twee stappen uit te voeren, drukken ze normaal gesp roken op **#** om de aanmelding te bevestigen. Om fraude te rapporteren, voert de gebruiker een code in voordat u op **#** drukt. Deze code is standaard **0** , maar u kunt deze aanpassen.

   >[!NOTE]
   >De standaard voicemail begroetingen van micro soft geven gebruikers de opdracht om op **0 #** te drukken om een fraude waarschuwing te verzenden. Als u een andere code dan **0**wilt gebruiken, moet u uw eigen aangepaste spraak begroetingen opnemen en uploaden met de juiste instructies voor uw gebruikers.
   >

### <a name="view-fraud-reports"></a>Fraude rapporten weer geven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **Azure Active Directory** > **aanmeldingen**. Het fraude rapport maakt nu deel uit van het standaard rapport van Azure AD-aanmeldingen.

## <a name="notifications"></a>Meldingen

Hier kunt u e-mail adressen configureren voor gebruikers die e-mails met fraude waarschuwingen ontvangen.

![Voor beeld e-mail bericht fraude waarschuwing](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Instellingen voor telefoon gesprek

### <a name="caller-id"></a>Beller-ID

**Id-nummer van MFA-beller** : dit is het aantal dat uw gebruikers op hun telefoon zullen zien. Alleen op basis van Amerikaanse nummers zijn toegestaan.

>[!NOTE]
>Wanneer Multi-Factor Authentication aanroepen via het open bare telefoonnet werk worden geplaatst, worden ze soms doorgestuurd via een transporteur die geen beller-ID ondersteunt. Als gevolg hiervan is de beller-ID niet gegarandeerd, zelfs al wordt deze door het Multi-Factor Authentication-systeem altijd verzonden.

In de Verenigde Staten, als u de MFA-beller-ID nog niet hebt geconfigureerd, zijn Voice-aanroepen van micro soft afkomstig van de volgende nummers: + 1 (866) 539 4191, + 1 (855) 330 8653 en + 1 (877) 668 6536. Als u spam filters gebruikt, moet u deze nummers uitsluiten.

### <a name="custom-voice-messages"></a>Aangepaste spraak berichten

U kunt uw eigen opnamen of begroetingen gebruiken voor verificatie in twee stappen met de functie voor _aangepaste spraak berichten_ . Deze berichten kunnen worden gebruikt in aanvulling op of vervangen van de micro soft-opnamen.

Voordat u begint, moet u rekening houden met de volgende beperkingen:

* De ondersteunde bestands indelingen zijn. WAV en. mp3.
* De maximale bestands grootte is 5 MB.
* Verificatie berichten moeten korter dan 20 seconden zijn. Berichten van meer dan 20 seconden kunnen ervoor zorgen dat de verificatie mislukt. De gebruiker reageert mogelijk niet voordat het bericht is voltooid en er wordt een time-out van de verificatie uitgevoerd.

### <a name="custom-message-language-behavior"></a>Taal gedrag aangepaste berichten

Wanneer een aangepast voicemail bericht wordt afgespeeld naar de gebruiker, is de taal van het bericht afhankelijk van de volgende factoren:

* De taal van de huidige gebruiker.
  * De taal die wordt gedetecteerd door de browser van de gebruiker.
  * Andere verificatie scenario's kunnen zich anders gedragen.
* De taal van alle beschik bare aangepaste berichten.
  * Deze taal wordt gekozen door de beheerder wanneer een aangepast bericht wordt toegevoegd.

Als er bijvoorbeeld slechts één aangepast bericht is, met een taal van het Duits:

* Een gebruiker die zich verifieert in de Duitse taal, hoort het aangepaste Duitse bericht.
* Een gebruiker die zich in het Engels verifieert, hoort het standaard Engelse bericht.

### <a name="set-up-a-custom-message"></a>Een aangepast bericht instellen

1. Meld u als beheerder aan bij [Azure Portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory** > **MFA** > **instellingen voor telefoon gesprek**.
1. Selecteer **begroeting toevoegen**.
1. Kies het type begroeting.
1. Kies de taal.
1. Selecteer een. mp3-of. WAV-geluids bestand dat u wilt uploaden.
1. Selecteer **Toevoegen**.

### <a name="custom-voice-message-defaults"></a>Aangepaste standaard waarden voor spraak berichten

Voorbeeld scripts voor het maken van aangepaste berichten.

| Bericht naam | Script |
| --- | --- |
| Verificatie geslaagd | Uw aanmelding is geverifieerd. Zien. |
| Prompt voor verlenging | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt. Druk op de toets hekje om door te gaan. |
| Fraude bevestiging | Er is een fraude waarschuwing verzonden. Als u uw account wilt blok keren, neemt u contact op met de IT-Help Desk van uw bedrijf. |
| Fraude begroeting (standaard) | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt. Druk op het hekje om uw verificatie te volt ooien. Als u deze verificatie niet hebt gestart, probeert iemand mogelijk toegang te krijgen tot uw account. Druk op het hekje nul om een fraude waarschuwing te verzenden. Hiermee wordt het IT-team van uw bedrijf op de hoogte gebracht en worden verdere verificatie pogingen geblokkeerd. |
| Fraude gemeld dat er een fraude waarschuwing is verzonden. | Als u uw account wilt blok keren, neemt u contact op met de IT-Help Desk van uw bedrijf. |
| Activiteitsstatus | Hartelijk dank dat u het micro soft-aanmeld verificatie systeem gebruikt. Druk op het hekje om uw verificatie te volt ooien. |
| Poging tot authenticatie geweigerd | Verificatie geweigerd. |
| Nieuwe poging (standaard) | Hartelijk dank dat u het micro soft-aanmeld verificatie systeem gebruikt. Druk op het hekje om uw verificatie te volt ooien. |
| Begroeting (standaard) | Hartelijk dank dat u het micro soft-aanmeld verificatie systeem gebruikt. Druk op het hekje om uw verificatie te volt ooien. |
| Begroeting (pincode) | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt. Voer uw pincode in, gevolgd door de hekje om uw verificatie te volt ooien. |
| Fraude begroeting (pincode) | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt.  Voer uw pincode in, gevolgd door de hekje om uw verificatie te volt ooien. Als u deze verificatie niet hebt gestart, probeert iemand mogelijk toegang te krijgen tot uw account. Druk op het hekje nul om een fraude waarschuwing te verzenden. Hiermee wordt het IT-team van uw bedrijf op de hoogte gebracht en worden verdere verificatie pogingen geblokkeerd. |
| Nieuwe poging (pincode) | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt. Voer uw pincode in, gevolgd door de hekje om uw verificatie te volt ooien. |
| Vragen om toestel na cijfers | Als u deze uitbrei ding al hebt, drukt u op de toets hekje om door te gaan. |
| Verificatie geweigerd | Je kunt je op dit moment niet aanmelden. Probeer het later opnieuw. |
| Activerings begroeting (standaard) | Hartelijk dank dat u het micro soft-aanmeld verificatie systeem gebruikt. Druk op het hekje om uw verificatie te volt ooien. |
| Nieuwe activerings poging (standaard) | Hartelijk dank dat u het micro soft-aanmeld verificatie systeem gebruikt. Druk op het hekje om uw verificatie te volt ooien. |
| Activerings begroeting (pincode) | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt. Voer uw pincode in, gevolgd door de hekje om uw verificatie te volt ooien. |
| Vragen om toestel voor cijfers | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt. Zet deze oproep over naar de extensie... |

## <a name="one-time-bypass"></a>Eenmalige bypass

Met de functie _eenmalig overs Laan_ kan een gebruiker één keer verifiëren zonder verificatie in twee stappen uit te voeren. De bypass is tijdelijk en verloopt na een opgegeven aantal seconden. In situaties waarin de mobiele app of telefoon geen melding of telefoon oproep ontvangt, kunt u eenmalig overs Laan toestaan, zodat de gebruiker toegang kan krijgen tot de gewenste resource.

### <a name="create-a-one-time-bypass"></a>Eenmalige bypass maken

1. Meld u als beheerder aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar **Azure Active Directory** > **MFA** > **eenmalig overs Laan**.
3. Selecteer **Toevoegen**.
4. Selecteer, indien nodig, de replicatie groep voor de bypass.
5. Voer de gebruikers naam in als **gebruikers naam\@Domain.com**. Voer het aantal seconden in dat de bypass moet duren. Voer de reden voor het overs laan in.
6. Selecteer **Toevoegen**. De tijds limiet gaat onmiddellijk in werking. De gebruiker moet zich aanmelden voordat de eenmalige bypass verlopen.

### <a name="view-the-one-time-bypass-report"></a>Het eenmalige bypass-rapport weer geven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Ga naar **Azure Active Directory** > **MFA** > **eenmalig overs Laan**.

## <a name="caching-rules"></a>Regels voor opslaan in cache

U kunt een tijds periode instellen voor het toestaan van verificatie pogingen nadat een gebruiker is geverifieerd met behulp van de _cache_ functie. Volgende verificatie pogingen voor de gebruiker binnen de opgegeven tijds periode slagen automatisch. Caching wordt hoofd zakelijk gebruikt wanneer on-premises systemen, zoals VPN, meerdere verificatie aanvragen verzenden terwijl de eerste aanvraag nog wordt uitgevoerd. Met deze functie kunnen de volgende aanvragen automatisch worden uitgevoerd nadat de gebruiker de eerste verificatie heeft uitgevoerd.

>[!NOTE]
>De functie caching is niet bedoeld om te worden gebruikt voor aanmeldingen bij het Azure Active Directory van Azure AD.

### <a name="set-up-caching"></a>Caching instellen

1. Meld u als beheerder aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar **Azure Active Directory** > **MFA** > **cache regels**.
3. Selecteer **Toevoegen**.
4. Selecteer het **cache type** in de vervolg keuzelijst. Voer het maximum aantal **cache seconden in**.
5. Selecteer indien nodig een verificatie type en geef een toepassing op.
6. Selecteer **Toevoegen**.

## <a name="mfa-service-settings"></a>Instellingen voor MFA-service

Instellingen voor app-wacht woorden, vertrouwde IP-adressen, verificatie opties en onthoud multi-factor Authentication voor Azure Multi-Factor Authentication zijn te vinden in service-instellingen. U kunt toegang krijgen tot de service-instellingen via de Azure Portal door te bladeren naar **Azure Active Directory** > **MFA** > **aan de slag** > **configuratie** > **Extra op de cloud gebaseerde MFA-instellingen**.

![Service-instellingen van Azure Multi-Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

## <a name="app-passwords"></a>App-wachtwoorden

Sommige toepassingen, zoals Office 2010 of eerder en Apple mail vóór iOS 11, bieden geen ondersteuning voor verificatie in twee stappen. De apps zijn niet geconfigureerd om een tweede verificatie te accepteren. Als u deze toepassingen wilt gebruiken, kunt u profiteren van de functie voor het maken van _app-wacht woorden_ . U kunt een app-wacht woord gebruiken in plaats van uw traditionele wacht woord, zodat een app de verificatie in twee stappen kan overs Laan en verder kan werken.

Moderne verificatie wordt ondersteund voor de Microsoft Office 2013-clients en hoger. Office 2013-clients met inbegrip van Outlook, ondersteunen moderne verificatie protocollen en kunnen worden ingeschakeld om te werken met verificatie in twee stappen. Nadat de client is ingeschakeld, zijn app-wacht woorden niet vereist voor de client.

>[!NOTE]
>App-wacht woorden werken niet met voorwaardelijke toegang op basis van multi-factor Authentication-beleid en moderne verificatie.

### <a name="considerations-about-app-passwords"></a>Overwegingen met betrekking tot app-wacht woorden

Wanneer u app-wacht woorden gebruikt, moet u rekening houden met de volgende belang rijke punten:

* App-wacht woorden worden slechts eenmaal per toepassing ingevoerd. Gebruikers hoeven de wacht woorden niet bij te houden of ze elke keer in te voeren.
* Het daad werkelijke wacht woord wordt automatisch gegenereerd en wordt niet door de gebruiker verstrekt. Het automatisch gegenereerde wacht woord is moeilijker voor een aanvaller om te raden en veiliger.
* Er is een limiet van 40 wacht woorden per gebruiker.
* Toepassingen die wacht woorden in de cache opslaan en gebruiken in on-premises scenario's, kunnen mislukken omdat het app-wacht woord niet bekend is buiten het werk-of school account. Een voor beeld van dit scenario is Exchange-e-mail berichten die on-premises zijn, maar de gearchiveerde e-mail bevindt zich in de Cloud. In dit scenario werkt hetzelfde wacht woord niet.
* Nadat Multi-Factor Authentication is ingeschakeld voor een gebruikers account, kunnen app-wacht woorden worden gebruikt met de meeste niet-browser-clients, zoals Outlook en micro soft Skype voor bedrijven. Beheer acties kunnen niet worden uitgevoerd met behulp van app-wacht woorden via niet-browser toepassingen, zoals Windows Power shell. De acties kunnen niet worden uitgevoerd, zelfs niet wanneer de gebruiker een Administrator-account heeft. Als u Power shell-scripts wilt uitvoeren, maakt u een service account met een sterk wacht woord en schakelt u het account niet in voor verificatie in twee stappen.

>[!WARNING]
>App-wacht woorden werken niet in hybride omgevingen waar clients communiceren met zowel lokale als Cloud-eind punten voor automatische detectie. Domein wachtwoorden zijn vereist voor de verificatie van on-premises. App-wacht woorden zijn vereist voor verificatie met de Cloud.

### <a name="guidance-for-app-password-names"></a>Richt lijnen voor de namen van app-wacht woorden

De namen van app-wacht woorden moeten overeenkomen met het apparaat waarop ze worden gebruikt. Als u een laptop hebt met niet-browser toepassingen zoals Outlook, Word en Excel, maakt u één app-wacht woord met de naam **laptop** voor deze apps. Maak een ander app-wacht woord met de naam **bureau blad** voor dezelfde toepassingen die op uw desktop computer worden uitgevoerd.

>[!NOTE]
>U wordt aangeraden één app-wacht woord per apparaat te maken, in plaats van één app-wacht woord per toepassing.

### <a name="federated-or-single-sign-on-app-passwords"></a>App-wacht woorden voor federatieve of eenmalige aanmelding

Azure AD ondersteunt Federatie of eenmalige aanmelding (SSO) met on-premises Windows Server Active Directory Domain Services (AD DS). Als uw organisatie Federated is met Azure AD en u Azure Multi-Factor Authentication gebruikt, moet u rekening houden met de volgende punten over app-wacht woorden.

>[!NOTE]
>De volgende punten zijn alleen van toepassing op federatieve klanten (SSO).

* App-wacht woorden worden geverifieerd door Azure AD, waardoor de Federatie niet kan worden omzeild. Federatie wordt actief alleen gebruikt bij het instellen van app-wacht woorden.
* Er wordt geen contact gemaakt met de ID-provider (IdP) voor federatieve gebruikers (SSO), in tegens telling tot de passieve stroom. De app-wacht woorden worden opgeslagen in het werk-of school account. Als een gebruiker het bedrijf verlaat, wordt de informatie van de gebruiker naar het werk-of school account stromen met behulp van **DirSync** in realtime. Het uitschakelen of verwijderen van het account kan tot drie uur duren, waardoor het uitschakelen/verwijderen van het app-wacht woord in azure AD kan worden vertraagd.
* On-premises Client Access Control instellingen worden niet gehonoreerd door de functie voor app-wacht woorden.
* Er is geen mogelijkheid voor on-premises verificatie-registratie/-controle beschikbaar voor gebruik met de functie voor app-wacht woorden.
* Voor sommige geavanceerde architecturen is een combi natie van referenties vereist voor verificatie in twee stappen met clients. Deze referenties kunnen een gebruikers naam en wacht woord voor een werk-of school account bevatten en app-wacht woorden. De vereisten zijn afhankelijk van de manier waarop de verificatie wordt uitgevoerd. Voor clients die worden geverifieerd aan de hand van een on-premises infra structuur, wordt de gebruikers naam en het wacht woord van een werk-of school account vereist. Voor clients die met Azure AD worden geverifieerd, is een app-wacht woord vereist.

  Stel bijvoorbeeld dat u de volgende architectuur hebt:

  * Uw on-premises exemplaar van Active Directory is federatief met Azure AD.
  * U gebruikt Exchange Online.
  * U maakt gebruik van Skype voor bedrijven on-premises.
  * U gebruikt Azure Multi-Factor Authentication.

  In dit scenario gebruikt u de volgende referenties:

  * Als u zich wilt aanmelden bij Skype voor bedrijven, gebruikt u de gebruikers naam en het wacht woord van uw werk-of school account.
  * Gebruik een app-wacht woord om toegang te krijgen tot het adres boek van een Outlook-client die verbinding maakt met Exchange Online.

### <a name="allow-users-to-create-app-passwords"></a>Gebruikers toestaan app-wacht woorden te maken

Standaard kunnen gebruikers geen app-wacht woorden maken. De functie voor het maken van app-wacht woorden moet zijn ingeschakeld. Gebruik de volgende procedure om gebruikers de mogelijkheid te geven om app-wacht woorden te maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory** **gebruikers** > .
3. Selecteer **multi-factor Authentication**.
4. Onder Multi-Factor Authentication selecteert u **Service-instellingen**.
5. Selecteer op de pagina **Service-instellingen** de optie **gebruikers toestaan app-wacht woorden te maken om zich aan te melden bij niet-browser-apps** .

### <a name="create-app-passwords"></a>App-wacht woorden maken

Gebruikers kunnen app-wacht woorden maken tijdens de eerste registratie. De gebruiker beschikt over de mogelijkheid om app-wacht woorden te maken aan het einde van het registratie proces.

Gebruikers kunnen ook app-wacht woorden maken na de registratie. Zie [Wat zijn app-wacht woorden in Azure multi-factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md) voor meer informatie en gedetailleerde stappen voor uw gebruikers.

## <a name="trusted-ips"></a>Goedgekeurde IP-adressen

De functie voor _vertrouwde IP-adressen_ van Azure multi-factor Authentication wordt gebruikt door beheerders van een beheerde of federatieve Tenant. De functie omzeilt de verificatie in twee stappen voor gebruikers die zich aanmelden vanaf het bedrijfs intranet. De functie is beschikbaar in de volledige versie van Azure Multi-Factor Authentication en niet de gratis versie voor beheerders. Zie [Azure multi-factor Authentication](multi-factor-authentication.md)voor meer informatie over het verkrijgen van de volledige versie van Azure multi-factor Authentication.

> [!NOTE]
> Vertrouwde Ip's van MFA en benoemde locaties met voorwaardelijke toegang werken alleen met IPV4-adressen.

Als uw organisatie de NPS-extensie implementeert voor het leveren van MFA aan on-premises toepassingen, ziet u dat het bron-IP-adres altijd de NPS-server is die de verificatie poging doorloopt.

| Type Azure AD-Tenant | Opties voor de functie vertrouwde IP-adressen |
|:--- |:--- |
| Managed |**Specifiek bereik van IP-adressen**: beheerders geven een bereik van IP-adressen op waarmee de verificatie in twee stappen kan worden omzeild voor gebruikers die zich aanmelden vanaf het bedrijfs intranet.|
| Federatie |**Alle federatieve gebruikers**: alle federatieve gebruikers die zich aanmelden bij de organisatie, kunnen verificatie in twee stappen overs Laan. De gebruikers Bypass de verificatie met behulp van een claim die is uitgegeven door Active Directory Federation Services (AD FS).<br/>**Specifiek bereik van IP-adressen**: beheerders geven een bereik van IP-adressen op waarmee de verificatie in twee stappen kan worden omzeild voor gebruikers die zich aanmelden vanaf het bedrijfs intranet. |

Het omzeilen van vertrouwde Ip's werkt alleen vanuit het bedrijfs intranet. Als u de optie **alle federatieve gebruikers** selecteert en de gebruiker zich aanmeldt van buiten het bedrijfs intranet, moet de gebruiker verifiëren met behulp van verificatie in twee stappen. Het proces is hetzelfde, zelfs als de gebruiker een AD FS claim presenteert. 

### <a name="end-user-experience-inside-of-corpnet"></a>Ervaring van de eind gebruiker in Corpnet

Wanneer de functie voor vertrouwde IP-adressen is uitgeschakeld, is verificatie in twee stappen vereist voor browser stromen. App-wacht woorden zijn vereist voor oudere uitgebreide client toepassingen.

Wanneer de functie voor vertrouwde IP-adressen is ingeschakeld, is verificatie in twee stappen *niet* vereist voor browser stromen. App-wacht woorden zijn *niet* vereist voor oudere uitgebreide client toepassingen, mits de gebruiker geen app-wacht woord heeft gemaakt. Wanneer een app-wacht woord wordt gebruikt, blijft het wacht woord vereist. 

### <a name="end-user-experience-outside-corpnet"></a>Ervaring voor de eind gebruiker buiten Corpnet

Ongeacht of de functie voor vertrouwde IP-adressen is ingeschakeld, is verificatie in twee stappen vereist voor browser stromen. App-wacht woorden zijn vereist voor oudere uitgebreide client toepassingen.

### <a name="enable-named-locations-by-using-conditional-access"></a>Benoemde locaties inschakelen met behulp van voorwaardelijke toegang

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory** ** >  > ** **voorwaardelijke toegang** > **benoemde locaties**.
3. Selecteer een **nieuwe locatie**.
4. Voer een naam in voor de locatie.
5. Selecteer **markeren als vertrouwde locatie**.
6. Voer het IP-bereik in CIDR-notatie in, zoals **192.168.1.1/24**.
7. Selecteer **Maken**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>De functie voor vertrouwde IP-adressen inschakelen met behulp van voorwaardelijke toegang

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory** ** >  >  ** **voorwaardelijke toegang** > **benoemde locaties**.
3. Selecteer **vertrouwde IP-adressen voor MFA configureren**.
4. Kies op de pagina **Service-instellingen** onder **vertrouwde IP-adressen**een van de volgende twee opties:

   * **Voor aanvragen van federatieve gebruikers die afkomstig zijn van mijn intranet**: als u deze optie kiest, schakelt u het selectie vakje in. Alle federatieve gebruikers die zich aanmelden vanuit het bedrijfs netwerk, kunnen verificatie in twee stappen overs Laan door gebruik te maken van een claim die is uitgegeven door AD FS. Zorg ervoor dat AD FS een regel heeft om de intranet claim toe te voegen aan het juiste verkeer. Als de regel niet bestaat, maakt u de volgende regel in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Voor aanvragen van een specifiek bereik van open bare ip's**: als u deze optie wilt kiezen, voert u de IP-adressen in het tekstvak in met behulp van CIDR-notatie.
      * Voor IP-adressen in het bereik xxx. xxx. xxx. 1 t/m xxx. xxx. xxx. 254 gebruikt u notatie als **xxx. xxx. xxx. 0/24**.
      * Gebruik voor één IP-adres notatie zoals **xxx.xxx.xxx.xxx/32**.
      * Voer Maxi maal 50 IP-adresbereiken in. Gebruikers die zich aanmelden vanaf deze IP-adressen, passeren verificatie in twee stappen.

5. Selecteer **Opslaan**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>De functie voor vertrouwde IP-adressen inschakelen met behulp van service-instellingen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory** **gebruikers** > .
3. Selecteer **multi-factor Authentication**.
4. Onder Multi-Factor Authentication selecteert u **Service-instellingen**.
5. Kies op de pagina **Service-instellingen** onder **betrouw bare ip's**een (of beide) van de volgende twee opties:

   * **Voor aanvragen van federatieve gebruikers op mijn intranet**: als u deze optie wilt selecteren, schakelt u het selectie vakje in. Alle federatieve gebruikers die zich aanmelden vanuit het bedrijfs netwerk, kunnen verificatie in twee stappen overs Laan door gebruik te maken van een claim die is uitgegeven door AD FS. Zorg ervoor dat AD FS een regel heeft om de intranet claim toe te voegen aan het juiste verkeer. Als de regel niet bestaat, maakt u de volgende regel in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Voor aanvragen van een opgegeven bereik van subnetten met IP-adressen**: als u deze optie wilt kiezen, voert u de IP-adressen in het tekstvak in met behulp van CIDR-notatie.
      * Voor IP-adressen in het bereik xxx. xxx. xxx. 1 t/m xxx. xxx. xxx. 254 gebruikt u notatie als **xxx. xxx. xxx. 0/24**.
      * Gebruik voor één IP-adres notatie zoals **xxx.xxx.xxx.xxx/32**.
      * Voer Maxi maal 50 IP-adresbereiken in. Gebruikers die zich aanmelden vanaf deze IP-adressen, passeren verificatie in twee stappen.

6. Selecteer **Opslaan**.

## <a name="verification-methods"></a>Verificatie methoden

U kunt kiezen welke verificatie methoden beschikbaar zijn voor uw gebruikers. De volgende tabel bevat een kort overzicht van de methoden.

Wanneer uw gebruikers hun accounts voor Azure Multi-Factor Authentication inschrijven, kiezen ze hun voorkeurs verificatie methode van de opties die u hebt ingeschakeld. Richt lijnen voor het proces van gebruikers registratie vindt [u in mijn account instellen voor verificatie in twee stappen](../user-help/multi-factor-authentication-end-user-first-time.md).

| Methode | Beschrijving |
|:--- |:--- |
| Bellen naar telefoon |Hiermee wordt een geautomatiseerd telefoon gesprek geplaatst. De gebruiker beantwoordt het gesprek en drukt # in het toetsen blok van de telefoon om te verifiëren. Het telefoon nummer is niet gesynchroniseerd met on-premises Active Directory. |
| SMS-bericht naar telefoon |Hiermee verzendt u een tekst bericht met een verificatie code. De gebruiker wordt gevraagd de verificatie code in te voeren in de aanmeldings interface. Dit proces wordt eenrichtings-SMS genoemd. Twee richtings-SMS betekent dat de gebruiker een bepaalde code moet herstellen. SMS-in twee richtingen is afgeschaft en wordt niet ondersteund na 14 november 2018. Gebruikers die zijn geconfigureerd voor tweerichtings-SMS-verificatie, worden op dat moment automatisch geswitcheerd om _te bellen naar telefoon_ .|
| Melding via mobiele app |Hiermee verzendt u een push melding naar uw telefoon of geregistreerd apparaat. De gebruiker bekijkt de melding en selecteert **verifiëren** om de verificatie te volt ooien. De Microsoft Authenticator-app is beschikbaar voor [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)en [IOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Verificatie code van de mobiele app of het hardware-token |De Microsoft Authenticator-app genereert elke 30 seconden een nieuwe OATH-verificatie code. De gebruiker voert de verificatie code in de aanmeldings interface in. De Microsoft Authenticator-app is beschikbaar voor [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)en [IOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Verificatie methoden in-en uitschakelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory** **gebruikers** > .
3. Selecteer **multi-factor Authentication**.
4. Onder Multi-Factor Authentication selecteert u **Service-instellingen**.
5. Selecteer op de pagina **Service-instellingen** onder **verificatie opties**de methoden die aan uw gebruikers moeten worden verstrekt.
6. Klik op **Opslaan**.

Meer informatie over het gebruik van verificatie methoden vindt u in het artikel [Wat zijn verificatie methoden](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Onthouden Multi-Factor Authentication

De functie _multi-factor Authentication onthouden_ voor apparaten en browsers die worden vertrouwd door de gebruiker is een gratis functie voor alle multi-factor Authentication-gebruikers. Gebruikers kunnen volgende controles over een bepaald aantal dagen overs Laan nadat ze zijn aangemeld bij een apparaat met behulp van Multi-Factor Authentication. De functie verbetert de bruikbaarheid door het aantal keren dat een gebruiker verificatie in twee stappen moet uitvoeren op hetzelfde apparaat te minimaliseren.

>[!IMPORTANT]
>Als een account of apparaat is aangetast, kan de beveiliging worden beïnvloed door Multi-Factor Authentication voor vertrouwde apparaten. Als er een bedrijfs account wordt aangetast of een vertrouwd apparaat verloren is gegaan of wordt gestolen, moet u [MFA-sessies intrekken](howto-mfa-userdevicesettings.md).
>
>Met de herstel actie wordt de vertrouwde status van alle apparaten ingetrokken en de gebruiker moet de verificatie in twee stappen opnieuw uitvoeren. U kunt uw gebruikers ook de opdracht geven om Multi-Factor Authentication op hun eigen apparaten te herstellen met de instructies in [uw instellingen beheren voor verificatie in twee stappen](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Hoe de functie werkt

De functie Multi-Factor Authentication onthouden stelt een permanente cookie in op de browser wanneer een gebruiker de optie **niet opnieuw vragen voor X dagen** selecteert tijdens het aanmelden. De gebruiker wordt niet opnieuw gevraagd om Multi-Factor Authentication van dezelfde browser totdat de cookie verloopt. Als de gebruiker een andere browser op hetzelfde apparaat opent of de cookies wist, wordt deze opnieuw gevraagd om te verifiëren.

De optie **niet opnieuw vragen voor X dagen** wordt niet weer gegeven voor niet-browser toepassingen, ongeacht of de app moderne verificatie ondersteunt. Deze apps maken gebruik van _vernieuwings tokens_ die elk uur nieuwe toegangs tokens bieden. Wanneer een vernieuwings token is gevalideerd, controleert Azure AD of de laatste verificatie in twee stappen binnen het opgegeven aantal dagen is uitgevoerd.

De functie beperkt het aantal authenticaties op Web-apps, die Norma liter elke keer worden gevraagd. De functie verhoogt het aantal authenticaties voor moderne authenticatie clients die Norma liter elke 90 dagen worden gevraagd. Kan ook het aantal authenticaties verhogen in combi natie met beleid voor voorwaardelijke toegang.

>[!IMPORTANT]
>De functie **multi-factor Authentication onthouden** is niet compatibel met de functie **aangemeld blijven** van AD FS, wanneer gebruikers verificatie in twee stappen uitvoeren voor AD FS via Azure multi-factor Authentication-Server of een multi-factor van derden verificatie oplossing.
>
>Als uw gebruikers **ingelogd blijven aangemeld** bij AD FS en hun apparaat ook markeren als vertrouwd voor multi-factor Authentication, wordt de gebruiker niet automatisch gecontroleerd wanneer het aantal dagen van de **Meervoudige multi-factor Authentication** is verlopen. Azure AD vraagt een nieuwe verificatie in twee stappen aan, maar AD FS retourneert een token met de oorspronkelijke Multi-Factor Authentication claim en datum, in plaats van de verificatie in twee stappen opnieuw uit te voeren. **Met deze reactie wordt een verificatie lus tussen Azure AD en AD FS.**
>
>De functie **multi-factor Authentication onthouden** is niet compatibel met B2B-gebruikers en is niet zichtbaar voor B2B-gebruikers wanneer ze zich aanmelden bij de uitgenodigde tenants.
>

### <a name="enable-remember-multi-factor-authentication"></a>Multi-Factor Authentication onthouden inschakelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory** **gebruikers** > .
3. Selecteer **multi-factor Authentication**.
4. Onder Multi-Factor Authentication selecteert u **Service-instellingen**.
5. Op de pagina **service** -instellingen **beheert u multi-factor Authentication onthouden**en selecteert **u de optie gebruikers toestaan om multi-factor Authentication te onthouden op apparaten die ze vertrouwen** .
6. Stel het aantal dagen in zodat vertrouwde apparaten de verificatie in twee stappen kunnen overs Laan. De standaard waarde is 14 dagen.
7. Selecteer **Opslaan**.

### <a name="mark-a-device-as-trusted"></a>Een apparaat als vertrouwd markeren

Nadat u de functie Multi-Factor Authentication onthouden hebt ingeschakeld, kunnen gebruikers een apparaat als vertrouwd markeren wanneer ze zich aanmelden door **niet opnieuw vragen**te selecteren.

## <a name="next-steps"></a>Volgende stappen

[Huis stijl van Azure AD-aanmeldings pagina wijzigen](../fundamentals/customize-branding.md)
