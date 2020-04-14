---
title: Veelgestelde vragen over Azure Multi-Factor Authentication - Azure Active Directory
description: Veelgestelde vragen en antwoorden met betrekking tot Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d28f93f316ac2a63be6b3a8eb0b80678bd7607f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271400"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Veelgestelde vragen over Azure Multi-Factor Authentication

Deze veelgestelde vragen beantwoorden veelgestelde vragen over Azure Multi-Factor Authentication en het gebruik van de multi-factor authentication-service. Het is onderverdeeld in vragen over de service in het algemeen, factureringsmodellen, gebruikerservaringen en probleemoplossing.

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt Microsoft geen MFA Server meer aan voor nieuwe implementaties. Nieuwe klanten die multi-factor authenticatie van hun gebruikers willen vereisen, moeten azure multi-factor authenticatie in de cloud gebruiken. Bestaande klanten die MFA Server vóór 1 juli hebben geactiveerd, kunnen de nieuwste versie, toekomstige updates downloaden en activeringsreferenties genereren zoals gewoonlijk.
>
> De onderstaande informatie over de Azure Multi-Factor Authentication Server is alleen van toepassing op gebruikers die de MFA-server al hebben uitgevoerd.
>
> Licenties op basis van consumptie zijn vanaf 1 september 2018 niet meer beschikbaar voor nieuwe klanten.
> Vanaf 1 september 2018 kunnen nieuwe auth-providers niet meer worden gemaakt. Bestaande auth-providers kunnen blijven worden gebruikt en bijgewerkt. Meervoudige verificatie blijft een beschikbare functie in Azure AD Premium-licenties.

## <a name="general"></a>Algemeen

* [Hoe gaat Azure Multi-Factor Authentication Server om met gebruikersgegevens?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Welke sms-shortcodes worden gebruikt voor het verzenden van sms-berichten naar mijn gebruikers?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Hoe gaat Azure Multi-Factor Authentication Server om met gebruikersgegevens?

Met Multi-Factor Authentication Server worden gebruikersgegevens alleen opgeslagen op de on-premises servers. In de cloud worden geen permanente gebruikersgegevens opgeslagen. Wanneer de gebruiker verificatie in twee stappen uitvoert, verzendt Multi-Factor Authentication Server gegevens naar de Azure Multi-Factor Authentication-cloudservice voor verificatie. Communicatie tussen Multi-Factor Authentication Server en de Multi-Factor Authentication cloudservice maakt gebruik van Secure Sockets Layer (SSL) of Transport Layer Security (TLS) via poort 443 outbound.

Wanneer verificatieverzoeken naar de cloudservice worden verzonden, worden gegevens verzameld voor verificatie- en gebruiksrapporten. De volgende gegevensvelden zijn opgenomen in verificatielogboeken in twee stappen:

* **Unieke id** (gebruikersnaam of on-premises multi-factor authenticatieserver-id)
* **Voor- en achternaam** (optioneel)
* **E-mailadres** (optioneel)
* **Telefoonnummer** (bij gebruik van een spraak- of sms-verificatie)
* **Apparaattoken** (bij verificatie van mobiele apps)
* **Verificatiemodus**
* **Verificatieresultaat**
* **Naam van verificatieserver met meerdere factoren**
* **Multi-Factor Authentication Server IP**
* **Client-IP** (indien beschikbaar)

De optionele velden kunnen worden geconfigureerd in multifactorverificatieserver.

Het verificatieresultaat (succes of weigering) en de reden als deze is geweigerd, wordt opgeslagen met de verificatiegegevens. Deze gegevens zijn beschikbaar in verificatie- en gebruiksrapporten.

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Welke sms-shortcodes worden gebruikt voor het verzenden van sms-berichten naar mijn gebruikers?

In de Verenigde Staten gebruiken we de volgende sms-shortcodes:

* *97671*
* *69829*
* *51789*
* *99399*

In Canada gebruiken we de volgende SMS-shortcodes:

* *759731*
* *673801*

Er is geen garantie voor consistente SMS of voice-based Multi-Factor Authentication prompt levering door hetzelfde nummer. In het belang van onze gebruikers kunnen we op elk moment korte codes toevoegen of verwijderen als we routeaanpassingen maken om de doorleverbaarheid van sms te verbeteren. We ondersteunen geen korte codes voor landen of regio's naast de Verenigde Staten en Canada.

## <a name="billing"></a>Billing

De meeste factureringsvragen kunnen worden beantwoord door te verwijzen naar de [pagina Multi-Factor Authentication Pricing](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) of de documentatie voor Azure [Multi-Factor Authentication-versies en verbruiksplannen.](concept-mfa-licensing.md)

* [Zijn er kosten in rekening gebracht voor het verzenden van de telefoongesprekken en sms-berichten die worden gebruikt voor verificatie?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [Brengt het factureringsmodel per gebruiker mij kosten in rekening voor alle ingeschakelde gebruikers, of alleen voor gebruikers die tweestapsverificatie hebben uitgevoerd?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Hoe werkt multi-factor authenticatie facturering?](#how-does-multi-factor-authentication-billing-work)
* [Is er een gratis versie van Azure Multi-Factor Authentication?](#is-there-a-free-version-of-azure-multi-factor-authentication)
* [Kan mijn organisatie op elk gewenst moment schakelen tussen factureringsmodellen per gebruiker en per verificatie?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Kan mijn organisatie op elk moment schakelen tussen facturering op basis van verbruik en abonnementen (een licentiegebaseerd model) ?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [Moet mijn organisatie identiteiten gebruiken en synchroniseren om Azure Multi-Factor Authentication te gebruiken?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>Zijn er kosten in rekening gebracht voor het verzenden van de telefoongesprekken en sms-berichten die worden gebruikt voor verificatie?

Nee, er worden geen kosten in rekening gebracht voor afzonderlijke telefoongesprekken of sms-berichten die naar gebruikers worden verzonden via Azure Multi-Factor Authentication. Als u een MFA-provider per verificatie gebruikt, wordt er wel een factuur in rekening gebracht voor elke verificatie, maar niet voor de gebruikte methode.

Uw gebruikers kunnen worden in rekening gebracht voor de telefoongesprekken of sms-berichten die ze ontvangen, volgens hun persoonlijke telefoonservice.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>Brengt het factureringsmodel per gebruiker mij kosten in rekening voor alle ingeschakelde gebruikers, of alleen voor gebruikers die tweestapsverificatie hebben uitgevoerd?

Facturering is gebaseerd op het aantal gebruikers dat is geconfigureerd om multifactorverificatie te gebruiken, ongeacht of ze die maand tweestapsverificatie hebben uitgevoerd.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Hoe werkt multi-factor authenticatie facturering?

Wanneer u een MFA-provider per gebruiker of per verificatie maakt, wordt het Azure-abonnement van uw organisatie maandelijks gefactureerd op basis van het gebruik. Dit factureringsmodel is vergelijkbaar met de manier waarop Azure rekeningen voor het gebruik van virtuele machines en Web Apps.

Wanneer u een abonnement koopt voor Azure Multi-Factor Authentication, betaalt uw organisatie alleen de jaarlijkse licentiekosten voor elke gebruiker. MFA-licenties en Office 365-, Azure AD Premium- of Enterprise Mobility + Security-bundels worden op deze manier gefactureerd.

Zie [Azure Multi-Factor Authentication voor](concept-mfa-licensing.md)meer informatie.

### <a name="is-there-a-free-version-of-azure-multi-factor-authentication"></a>Is er een gratis versie van Azure Multi-Factor Authentication?

Beveiligingsstandaardinstellingen kunnen worden ingeschakeld in de laag Gratis Azure AD. Met standaardinstellingen voor beveiliging zijn alle gebruikers ingeschakeld voor meervoudige verificatie met de Microsoft Authenticator-app. Er is geen mogelijkheid om sms-berichten of telefoonverificatie te gebruiken met beveiligingsstandaardinstellingen, alleen de Microsoft Authenticator-app.

Zie [Wat zijn beveiligingsstandaardinstellingen voor](../fundamentals/concept-fundamentals-security-defaults.md) meer informatie?

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>Kan mijn organisatie op elk gewenst moment schakelen tussen factureringsmodellen per gebruiker en per verificatie?

Als uw organisatie MFA als zelfstandige service koopt met facturering op basis van verbruik, kiest u een factureringsmodel wanneer u een MFA-provider maakt. U het factureringsmodel niet wijzigen nadat een MFA-provider is gemaakt. 

Als uw MFA-provider *niet* is gekoppeld aan een Azure AD-tenant of als u de nieuwe MFA-provider koppelt aan een andere Azure AD-tenant, worden gebruikersinstellingen en configuratieopties niet overgedragen. Bestaande Azure MFA-servers moeten ook opnieuw worden geactiveerd met behulp van de activeringsreferenties die worden gegenereerd door de nieuwe MFA-provider. Als u de MFA-servers opnieuw activeert om ze te koppelen aan de nieuwe MFA-provider, is dit niet van invloed op telefonische verificatie en verificatie via een sms-bericht. Mobiele app-meldingen werken echter niet meer voor gebruikers totdat ze de mobiele app opnieuw activeren.

Meer informatie over [MFA-providers in Aan de slag met een Azure Multi-Factor Auth Provider.](concept-mfa-authprovider.md)

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Kan mijn organisatie op elk moment schakelen tussen facturering op basis van verbruik en abonnementen (een licentiegebaseerd model) ?

In sommige gevallen wel, ja.

Als uw directory een Azure Multi-Factor *Authentication-provider per gebruiker* heeft, u MFA-licenties toevoegen. Gebruikers met licenties worden niet meegeteld in de facturering op basis van verbruik per gebruiker. Gebruikers zonder licenties kunnen nog steeds worden ingeschakeld voor MFA via de MFA-provider. Als u licenties koopt en toewijst voor al uw gebruikers die zijn geconfigureerd om multifactorauthenticatie te gebruiken, u de Azure Multi-Factor Authentication-provider verwijderen. U altijd een andere MFA-provider per gebruiker maken als u in de toekomst meer gebruikers dan licenties hebt.

Als uw directory een Azure Multi-Factor *Authentication-provider per verificatie* heeft, wordt er altijd kosten in rekening gebracht voor elke verificatie, zolang de MFA-provider is gekoppeld aan uw abonnement. U MFA-licenties toewijzen aan gebruikers, maar er worden nog steeds kosten in rekening gebracht voor elke verificatieaanvraag in twee stappen, of deze nu afkomstig is van iemand met een MFA-licentie die is toegewezen of niet.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication"></a>Moet mijn organisatie identiteiten gebruiken en synchroniseren om Azure Multi-Factor Authentication te gebruiken?

Als uw organisatie een factureringsmodel op basis van verbruik gebruikt, is Azure Active Directory optioneel, maar niet vereist. Als uw MFA-provider niet is gekoppeld aan een Azure AD-tenant, u alleen azure multi-factor authentication server on-premises implementeren.

Azure Active Directory is vereist voor het licentiemodel omdat licenties worden toegevoegd aan de Azure AD-tenant wanneer u deze aanschaft en toewijst aan gebruikers in de map.

## <a name="manage-and-support-user-accounts"></a>Gebruikersaccounts beheren en ondersteunen

* [Wat moet ik mijn gebruikers vertellen te doen als ze geen antwoord ontvangen op hun telefoon?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Wat moet ik doen als een van mijn gebruikers niet kan meedoen met hun account?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Wat moet ik doen als een van mijn gebruikers een telefoon verliest die app-wachtwoorden gebruikt?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Wat gebeurt er als een gebruiker zich niet kan aanmelden bij apps die niet in de browser zijn aangemeld?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Mijn gebruikers zeggen dat ze soms niet het sms-bericht of de verificatie keer uit te ontvangen.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Kan ik de tijd wijzigen die mijn gebruikers hebben om de verificatiecode uit een sms-bericht in te voeren voordat het systeem een time-out heeft?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Kan ik hardwaretokens gebruiken met Azure Multi-Factor Authentication Server?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Kan ik Azure Multi-Factor Authentication Server gebruiken om Terminal Services te beveiligen?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Ik heb beller-id geconfigureerd in MFA Server, maar mijn gebruikers ontvangen nog steeds Multi-Factor Authentication-oproepen van een anonieme beller.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Waarom wordt mijn gebruikers gevraagd hun beveiligingsgegevens te registreren?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Wat moet ik mijn gebruikers vertellen te doen als ze geen antwoord ontvangen op hun telefoon?

Laat uw gebruikers tot vijf keer proberen in 5 minuten om een telefoontje of sms te krijgen voor verificatie. Microsoft gebruikt meerdere providers voor het leveren van oproepen en sms-berichten. Als deze aanpak niet werkt, opent u een ondersteuningsaanvraag om problemen verder op te lossen.

Beveiligingsapps van derden kunnen ook het sms-bericht of telefoongesprek met de verificatiecode blokkeren. Als u een beveiligingsapp van derden gebruikt, probeert u de beveiliging uit te schakelen en vervolgens een andere MFA-verificatiecode aan te vragen.

Als de bovenstaande stappen niet werken, controleert u of gebruikers zijn geconfigureerd voor meer dan één verificatiemethode. Probeer je opnieuw aan te melden, maar selecteer een andere verificatiemethode op de aanmeldingspagina.

Zie de handleiding [voor het oplossen van problemen met](../user-help/multi-factor-authentication-end-user-troubleshoot.md)eindgebruikers voor meer informatie.

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Wat moet ik doen als een van mijn gebruikers niet kan meedoen met hun account?

U het account van de gebruiker opnieuw instellen door ze opnieuw door het registratieproces te laten doorlopen. Meer informatie over [het beheren van gebruikers- en apparaatinstellingen met Azure Multi-Factor Authentication in de cloud](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Wat moet ik doen als een van mijn gebruikers een telefoon verliest die app-wachtwoorden gebruikt?

Verwijder alle app-wachtwoorden van de gebruiker om ongeautoriseerde toegang te voorkomen. Nadat de gebruiker een vervangend apparaat heeft, kunnen ze de wachtwoorden opnieuw maken. Meer informatie over [het beheren van gebruikers- en apparaatinstellingen met Azure Multi-Factor Authentication in de cloud](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Wat gebeurt er als een gebruiker zich niet kan aanmelden bij apps die niet in de browser zijn aangemeld?

Als uw organisatie nog steeds oudere clients gebruikt en u [het gebruik van app-wachtwoorden hebt toegestaan,](howto-mfa-mfasettings.md#app-passwords)kunnen uw gebruikers zich niet aanmelden bij deze oudere clients met hun gebruikersnaam en wachtwoord. In plaats daarvan moeten ze [app-wachtwoorden instellen.](../user-help/multi-factor-authentication-end-user-app-passwords.md) Uw gebruikers moeten hun aanmeldingsgegevens wissen (verwijderen), de app opnieuw opstarten en zich vervolgens aanmelden met hun gebruikersnaam en *app-wachtwoord* in plaats van hun normale wachtwoord.

Als uw organisatie geen oudere clients heeft, moet u uw gebruikers niet toestaan app-wachtwoorden te maken.

> [!NOTE]
> **Moderne verificatie voor Office 2013-clients**
>
> App-wachtwoorden zijn alleen nodig voor apps die geen moderne verificatie ondersteunen. Office 2013-clients ondersteunen moderne verificatieprotocollen, maar moeten worden geconfigureerd. Moderne verificatie is beschikbaar voor elke klant die de update van maart 2015 of hoger voor Office 2013 uitvoert. Zie de bijgewerkte office [365-verificatie](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)voor meer informatie .

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Mijn gebruikers zeggen dat ze soms niet het sms-bericht of de verificatie keer uit te ontvangen.

Levering van SMS-berichten zijn niet gegarandeerd omdat er oncontroleerbare factoren zijn die de betrouwbaarheid van de service kunnen beïnvloeden. Deze factoren omvatten het land of de regio van bestemming, de mobiele telefoonprovider en de signaalsterkte.

Beveiligingsapps van derden kunnen ook het sms-bericht of telefoongesprek met de verificatiecode blokkeren. Als u een beveiligingsapp van derden gebruikt, probeert u de beveiliging uit te schakelen en vervolgens een andere MFA-verificatiecode aan te vragen.

Als uw gebruikers vaak problemen hebben met het betrouwbaar ontvangen van tekstberichten, moet u hen vertellen dat ze in plaats daarvan de Microsoft Authenticator-app of de telefoonmethode moeten gebruiken. De Microsoft Authenticator kan meldingen ontvangen via mobiele en Wi-Fi-verbindingen. Bovendien kan de mobiele app verificatiecodes genereren, zelfs als het apparaat helemaal geen signaal heeft. De Microsoft Authenticator-app is beschikbaar voor [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) en [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Kan ik de tijd wijzigen die mijn gebruikers hebben om de verificatiecode uit een sms-bericht in te voeren voordat het systeem een time-out heeft?

In sommige gevallen wel, ja.

Voor eenrichtings-sms met Azure MFA Server v7.0 of hoger u de time-outinstelling configureren door een registersleutel in te stellen. Nadat de MFA-cloudservice het sms-bericht heeft verzonden, wordt de verificatiecode (of eenmalige toegangscode) teruggestuurd naar de MFA-server. De MFA-server slaat de code standaard 300 seconden in het geheugen op. Als de gebruiker de code niet invoert voordat de 300 seconden zijn verstreken, wordt de verificatie geweigerd. Gebruik deze stappen om de standaardtime-outinstelling te wijzigen:

1. Ga naar `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Maak een **DWORD-registersleutel** met *de naam pfsvc_pendingSmsTimeoutSeconds* en stel de tijd in seconden in dat u wilt dat de Azure MFA Server eenmalige toegangscodes opslaat.

>[!TIP]
>
> Als u meerdere MFA-servers hebt, kent alleen degene die het oorspronkelijke verificatieverzoek heeft verwerkt, de verificatiecode die naar de gebruiker is verzonden. Wanneer de gebruiker de code invoert, moet het verificatieverzoek om deze te valideren naar dezelfde server worden verzonden. Als de codevalidatie naar een andere server wordt verzonden, wordt de verificatie geweigerd.

Als gebruikers niet binnen de gedefinieerde time-outperiode op de sms reageren, wordt hun verificatie geweigerd.

Voor eenmalige sms'en met Azure MFA in de cloud (inclusief de AD FS-adapter of de netwerkbeleidsserverextensie) u de time-outinstelling niet configureren. Azure AD slaat de verificatiecode gedurende 180 seconden op.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Kan ik hardwaretokens gebruiken met Azure Multi-Factor Authentication Server?

Als u Azure Multi-Factor Authentication Server gebruikt, u EED-tokens (Open Authentication) (oath) importeren op basis van eenmalige wachtwoord (TOTP) en deze vervolgens gebruiken voor verificatie in twee stappen.

U ActiveIdentity-tokens gebruiken die OATH TOTP-tokens zijn als u de geheime sleutel in een CSV-bestand plaatst en importeert naar Azure Multi-Factor Authentication Server. U OATH-tokens gebruiken met Active Directory Federation Services (ADFS), IIS-verificatie op basis van formulieren (Internet Information Server) en RADIUS (Remote Authentication Dial-In User Service) zolang het clientsysteem de gebruikersinvoer kan accepteren.

U OATH TOTP-tokens van derden importeren met de volgende indelingen:  

- Draagbare symmetrische sleutelcontainer (PSKC)
- CSV als het bestand een serienummer, een geheime sleutel in de base 32-indeling en een tijdsinterval bevat

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Kan ik Azure Multi-Factor Authentication Server gebruiken om Terminal Services te beveiligen?

Ja, maar als u Windows Server 2012 R2 of hoger gebruikt, u Terminal Services alleen beveiligen met Extern bureaublad-gateway (Extern bureaublad-gateway).

Beveiligingswijzigingen in Windows Server 2012 R2 hebben de manier gewijzigd waarop Azure Multi-Factor Authentication Server verbinding maakt met het beveiligingspakket (Local Security Authority) in Windows Server 2012 en eerdere versies. Voor versies van Terminal Services in Windows Server 2012 of eerder u [een toepassing beveiligen met Windows Authentication.](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure) Als u Windows Server 2012 R2 gebruikt, hebt u Rd Gateway nodig.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Ik heb beller-id geconfigureerd in MFA Server, maar mijn gebruikers ontvangen nog steeds Multi-Factor Authentication-oproepen van een anonieme beller.

Wanneer multi-factor authenticatie gesprekken worden geplaatst via het openbare telefoonnetwerk, soms worden ze gerouteerd via een provider die geen ondersteuning voor beller-ID. Vanwege dit gedrag van de provider is beller-ID niet gegarandeerd, hoewel het Multi-Factor Authentication-systeem het altijd verzendt.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Waarom wordt mijn gebruikers gevraagd hun beveiligingsgegevens te registreren?

Er zijn verschillende redenen waarom gebruikers kunnen worden gevraagd om hun beveiligingsgegevens te registreren:

- De gebruiker is ingeschakeld voor MFA door de beheerder in Azure AD, maar heeft nog geen beveiligingsgegevens geregistreerd voor zijn account.
- De gebruiker is ingeschakeld voor het opnieuw instellen van selfservicewachtwoorden in Azure AD. De beveiligingsinformatie zal hen helpen hun wachtwoord in de toekomst opnieuw in te stellen als ze het ooit vergeten.
- De gebruiker heeft toegang tot een toepassing die een beleid voor voorwaardelijke toegang heeft om MFA te vereisen en die nog niet eerder is geregistreerd voor MFA.
- De gebruiker registreert een apparaat bij Azure AD (inclusief Azure AD Join) en uw organisatie vereist MFA voor apparaatregistratie, maar de gebruiker heeft zich nog niet eerder geregistreerd voor MFA.
- De gebruiker genereert Windows Hello for Business in Windows 10 (waarvoor MFA nodig is) en heeft zich nog niet eerder geregistreerd voor MFA.
- De organisatie heeft een MFA-registratiebeleid gemaakt en ingeschakeld dat op de gebruiker is toegepast.
- De gebruiker heeft zich eerder geregistreerd voor MFA, maar heeft een verificatiemethode gekozen die een beheerder sindsdien heeft uitgeschakeld. De gebruiker moet daarom opnieuw door mfa-registratie gaan om een nieuwe standaardverificatiemethode te selecteren.

## <a name="errors"></a>Fouten

* [Wat moeten gebruikers doen als ze een foutbericht 'Verificatieverzoek is niet voor een geactiveerd account' zien bij het gebruik van meldingen van mobiele apps?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Wat moeten gebruikers doen als ze een 0x800434D4L-foutbericht zien wanneer ze zich aanmelden bij een niet-browsertoepassing?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Wat moeten gebruikers doen als ze een foutbericht 'Verificatieverzoek is niet voor een geactiveerd account' zien bij het gebruik van meldingen van mobiele apps?

Vraag de gebruiker om de volgende procedure te voltooien om zijn account uit de Microsoft Authenticator te verwijderen en het vervolgens opnieuw toe te voegen:

1. Ga naar [hun Azure-portalprofiel](https://account.activedirectory.windowsazure.com/profile/) en meld u aan met een organisatieaccount.
2. Selecteer **Extra beveiligingsverificatie**.
3. Verwijder het bestaande account uit de Microsoft Authenticator-app.
4. Klik **op Configureren**en volg de instructies om de Microsoft Authenticator opnieuw te configureren.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Wat moeten gebruikers doen als ze een 0x800434D4L-foutbericht zien wanneer ze zich aanmelden bij een niet-browsertoepassing?

De *0x800434D4L-fout* treedt op wanneer u zich probeert aan te melden bij een niet-browsertoepassing die is geïnstalleerd op een lokale computer en die niet werkt met accounts waarvoor verificatie in twee stappen vereist is.

Een tijdelijke oplossing voor deze fout is het hebben van afzonderlijke gebruikersaccounts voor beheergerelateerde en niet-beheerdersbewerkingen. Later u postvakken koppelen tussen uw beheerdersaccount en een niet-beheerdersaccount, zodat u zich aanmelden bij Outlook met uw niet-beheerdersaccount. Voor meer informatie over deze oplossing leest u hoe [u een beheerder de mogelijkheid geeft om de inhoud van het postvak van een gebruiker te openen en te bekijken.](https://help.outlook.com/141/gg709759.aspx?sl=1)

## <a name="next-steps"></a>Volgende stappen

Als uw vraag hier niet wordt beantwoord, zijn de volgende ondersteuningsopties beschikbaar:

* Zoek in de [Microsoft Support Knowledge Base](https://support.microsoft.com) naar oplossingen voor veelvoorkomende technische problemen.
* Zoek en blader door technische vragen en antwoorden van de community of stel uw eigen vraag in de [Azure Active Directory Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html).
* Neem contact op met Microsoft professional via [Azure Multi-Factor Authentication Server support](https://support.microsoft.com/oas/default.aspx?prid=14947). Wanneer u contact met ons opneemt, is het handig als u zoveel mogelijk informatie over uw probleem opnemen. De informatie die u verstrekken, omvat de pagina waarop u de fout hebt gezien, de specifieke foutcode, de specifieke sessie-id en de id van de gebruiker die de fout heeft gezien.
* Als u een oudere PhoneFactor-klant bent en u vragen hebt of [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) hulp nodig hebt bij het opnieuw instellen van een wachtwoord, gebruikt u het e-mailadres om een ondersteuningsaanvraag te openen.
