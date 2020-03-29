---
title: Veelgestelde vragen over Azure Multi-Factor Authentication - Azure Active Directory
description: Veelgestelde vragen en antwoorden met betrekking tot Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a622245a7431058582131d9ba224ddfb676d8aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425141"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Veelgestelde vragen over Azure Multi-Factor Authentication

Deze veelgestelde vragen beantwoorden veelgestelde vragen over Azure Multi-Factor Authentication en het gebruik van de multi-factor authentication-service. Het is onderverdeeld in vragen over de service in het algemeen, factureringsmodellen, gebruikerservaringen en probleemoplossing.

## <a name="general"></a>Algemeen

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt Microsoft geen MFA Server meer aan voor nieuwe implementaties. Nieuwe klanten die multi-factor authenticatie van hun gebruikers willen vereisen, moeten azure multi-factor authenticatie in de cloud gebruiken. Bestaande klanten die MFA Server vóór 1 juli hebben geactiveerd, kunnen de nieuwste versie, toekomstige updates downloaden en activeringsreferenties genereren zoals gewoonlijk.
> 
> Licenties op basis van consumptie zijn vanaf 1 september 2018 niet meer beschikbaar voor nieuwe klanten.
> Vanaf 1 september 2018 kunnen nieuwe auth-providers niet meer worden gemaakt. Bestaande auth-providers kunnen blijven worden gebruikt en bijgewerkt. Meervoudige verificatie blijft een beschikbare functie in Azure AD Premium-licenties.

> [!NOTE]
> De onderstaande informatie over de Azure Multi-Factor Authentication Server is alleen van toepassing op gebruikers die de MFA-server al hebben uitgevoerd.

**V: Hoe gaat Azure Multi-Factor Authentication Server om met gebruikersgegevens?**

Met Multi-Factor Authentication Server worden gebruikersgegevens alleen opgeslagen op de on-premises servers. In de cloud worden geen permanente gebruikersgegevens opgeslagen. Wanneer de gebruiker verificatie in twee stappen uitvoert, verzendt Multi-Factor Authentication Server gegevens naar de Azure Multi-Factor Authentication-cloudservice voor verificatie. Communicatie tussen Multi-Factor Authentication Server en de Multi-Factor Authentication cloudservice maakt gebruik van Secure Sockets Layer (SSL) of Transport Layer Security (TLS) via poort 443 outbound.

Wanneer verificatieverzoeken naar de cloudservice worden verzonden, worden gegevens verzameld voor verificatie- en gebruiksrapporten. Gegevensvelden die zijn opgenomen in verificatielogboeken in twee stappen zijn als volgt:

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

**V: Welke sms-shortcodes worden gebruikt voor het verzenden van sms-berichten naar mijn gebruikers?**

In de Verenigde Staten gebruikt Microsoft de volgende sms-shortcodes:

   * 97671
   * 69829
   * 51789
   * 99399

In Canada gebruikt Microsoft de volgende sms-shortcodes:

   * 759731 
   * 673801

Microsoft garandeert geen consistente sms- of spraakgebaseerde multi-factorauthenticatie promptlevering met hetzelfde nummer. In het belang van onze gebruikers kan Microsoft op elk moment korte codes toevoegen of verwijderen als we routeaanpassingen maken om de doorleverbaarheid van sms'en te verbeteren. Microsoft ondersteunt geen korte codes voor landen/regio's naast de Verenigde Staten en Canada.

## <a name="billing"></a>Billing

De meeste factureringsvragen kunnen worden beantwoord door te verwijzen naar de [pagina Multi-Factor Authentication Pricing](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) of de documentatie over hoe u Azure [Multi-Factor Authentication krijgen.](concept-mfa-licensing.md)

**V: Zijn er kosten in rekening gebracht voor het verzenden van de telefoongesprekken en sms-berichten die worden gebruikt voor verificatie?**

Nee, er worden geen kosten in rekening gebracht voor afzonderlijke telefoongesprekken of sms-berichten die naar gebruikers worden verzonden via Azure Multi-Factor Authentication. Als u een MFA-provider per verificatie gebruikt, wordt er voor elke verificatie wel rekening geboekt voor elke verificatie, maar niet voor de gebruikte methode.

Uw gebruikers kunnen worden in rekening gebracht voor de telefoongesprekken of sms-berichten die ze ontvangen, volgens hun persoonlijke telefoonservice.

**V: Brengt het factureringsmodel per gebruiker mij kosten in rekening voor alle ingeschakelde gebruikers, of alleen voor gebruikers die tweestapsverificatie hebben uitgevoerd?**

Facturering is gebaseerd op het aantal gebruikers dat is geconfigureerd om multifactorverificatie te gebruiken, ongeacht of ze die maand tweestapsverificatie hebben uitgevoerd.

**V: Hoe werkt multi-factor authenticatie facturering?**

Wanneer u een MFA-provider per gebruiker of per verificatie maakt, wordt het Azure-abonnement van uw organisatie maandelijks gefactureerd op basis van het gebruik. Dit factureringsmodel is vergelijkbaar met de manier waarop Azure rekeningen voor het gebruik van virtuele machines en websites.

Wanneer u een abonnement koopt voor Azure Multi-Factor Authentication, betaalt uw organisatie alleen de jaarlijkse licentiekosten voor elke gebruiker. MFA-licenties en Office 365-, Azure AD Premium- of Enterprise Mobility + Security-bundels worden op deze manier gefactureerd. 

Meer informatie over uw opties in [Hoe u Azure Multi-Factor Authentication krijgen.](concept-mfa-licensing.md)

**V: Is er een gratis versie van Azure Multi-Factor Authentication?**

In sommige gevallen wel, ja.

Multi-Factor Authentication for Azure Administrators biedt een subset van Azure MFA-functies zonder kosten voor toegang tot online services van Microsoft, waaronder de [Azure-portal](https://portal.azure.com) en [het Microsoft 365-beheercentrum.](https://admin.microsoft.com) Deze aanbieding is alleen van toepassing op globale beheerders in Azure Active Directory-exemplaren die niet de volledige versie van Azure MFA hebben via een MFA-licentie, een bundel of een zelfstandige leverancier op basis van eigen verbruik. Als uw beheerders de gratis versie gebruiken en u vervolgens een volledige versie van Azure MFA koopt, worden alle globale beheerders automatisch naar de betaalde versie verheven.

Multi-Factor Authentication voor Office 365-gebruikers biedt een subset van Azure MFA-functies zonder kosten voor toegang tot Office 365-services, waaronder Exchange Online en SharePoint Online. Deze aanbieding is van toepassing op gebruikers die een Office 365-licentie hebben toegewezen, wanneer het bijbehorende exemplaar van Azure Active Directory niet de volledige versie van Azure MFA heeft via een MFA-licentie, een bundel of een zelfstandige leverancier op basis van een eigen verbruik.

**V: Kan mijn organisatie op elk moment schakelen tussen factureringsmodellen per gebruiker en per verificatieverbruik?**

Als uw organisatie MFA als zelfstandige service koopt met facturering op basis van verbruik, kiest u een factureringsmodel wanneer u een MFA-provider maakt. U het factureringsmodel niet wijzigen nadat een MFA-provider is gemaakt. 

Als uw MFA-provider *niet* is gekoppeld aan een Azure AD-tenant of als u de nieuwe MFA-provider koppelt aan een andere Azure AD-tenant, worden gebruikersinstellingen en configuratieopties niet overgedragen. Bestaande Azure MFA-servers moeten ook opnieuw worden geactiveerd met behulp van de activeringsreferenties die worden gegenereerd door de nieuwe MFA-provider. Als u de MFA-servers opnieuw activeert om ze te koppelen aan de nieuwe MFA-provider, is dit niet van invloed op telefonische verificatie en verificatie via een sms-bericht. Mobiele app-meldingen werken echter niet meer voor gebruikers totdat ze de mobiele app opnieuw activeren.

Meer informatie over [MFA-providers in Aan de slag met een Azure Multi-Factor Auth Provider.](concept-mfa-authprovider.md)

**V: Kan mijn organisatie op elk moment schakelen tussen facturering op basis van verbruik en abonnementen (een licentiegebaseerd model) ?**

In sommige gevallen wel, ja.

Als uw directory een Azure Multi-Factor *Authentication-provider per gebruiker* heeft, u MFA-licenties toevoegen. Gebruikers met licenties worden niet meegeteld in de facturering op basis van verbruik per gebruiker. Gebruikers zonder licenties kunnen nog steeds worden ingeschakeld voor MFA via de MFA-provider. Als u licenties koopt en toewijst voor al uw gebruikers die zijn geconfigureerd om multifactorauthenticatie te gebruiken, u de Azure Multi-Factor Authentication-provider verwijderen. U altijd een andere MFA-provider per gebruiker maken als u in de toekomst meer gebruikers dan licenties hebt.

Als uw directory een Azure Multi-Factor *Authentication-provider per verificatie* heeft, wordt er altijd kosten in rekening gebracht voor elke verificatie, zolang de MFA-provider is gekoppeld aan uw abonnement. U MFA-licenties toewijzen aan gebruikers, maar er worden nog steeds kosten in rekening gebracht voor elke verificatieaanvraag in twee stappen, of deze nu afkomstig is van iemand met een MFA-licentie die is toegewezen of niet.

**V: Moet mijn organisatie identiteiten gebruiken en synchroniseren om Azure Multi-Factor Authentication te gebruiken?**

Als uw organisatie een factureringsmodel op basis van verbruik gebruikt, is Azure Active Directory optioneel, maar niet vereist. Als uw MFA-provider niet is gekoppeld aan een Azure AD-tenant, u alleen azure multi-factor authentication server on-premises implementeren.

Azure Active Directory is vereist voor het licentiemodel omdat licenties worden toegevoegd aan de Azure AD-tenant wanneer u deze aanschaft en toewijst aan gebruikers in de map.

## <a name="manage-and-support-user-accounts"></a>Gebruikersaccounts beheren en ondersteunen

**V: Wat moet ik mijn gebruikers vertellen te doen als ze geen antwoord ontvangen op hun telefoon?**

Laat uw gebruikers tot 5 keer proberen in 5 minuten om een telefoontje of sms te krijgen voor verificatie. Microsoft gebruikt meerdere providers voor het leveren van oproepen en sms-berichten. Als dit niet werkt, open dan een ondersteuningsaanvraag bij Microsoft om problemen verder op te lossen.

Als de bovenstaande stappen niet werken hopelijk al uw gebruikers geconfigureerd meer dan een verificatiemethode. Vraag hen om te proberen zich opnieuw aan te melden, maar een andere verificatiemethode op de aanmeldpagina te selecteren.

U uw gebruikers naar de [handleiding voor probleemoplossing van](../user-help/multi-factor-authentication-end-user-troubleshoot.md)eindgebruikers wijzen.

**V: Wat moet ik doen als een van mijn gebruikers niet kan meedoen met hun account?**

U het account van de gebruiker opnieuw instellen door ze opnieuw door het registratieproces te laten doorlopen. Meer informatie over [het beheren van gebruikers- en apparaatinstellingen met Azure Multi-Factor Authentication in de cloud](howto-mfa-userdevicesettings.md).

**V: Wat moet ik doen als een van mijn gebruikers een telefoon verliest die app-wachtwoorden gebruikt?**

Verwijder alle app-wachtwoorden van de gebruiker om ongeautoriseerde toegang te voorkomen. Nadat de gebruiker een vervangend apparaat heeft, kunnen ze de wachtwoorden opnieuw maken. Meer informatie over [het beheren van gebruikers- en apparaatinstellingen met Azure Multi-Factor Authentication in de cloud](howto-mfa-userdevicesettings.md).

**V: Wat als een gebruiker zich niet kan aanmelden bij apps die niet in de browser zijn aangemeld?**

Als uw organisatie nog steeds oudere clients gebruikt en u [het gebruik van app-wachtwoorden hebt toegestaan,](howto-mfa-mfasettings.md#app-passwords)kunnen uw gebruikers zich niet aanmelden bij deze oudere clients met hun gebruikersnaam en wachtwoord. In plaats daarvan moeten ze [app-wachtwoorden instellen.](../user-help/multi-factor-authentication-end-user-app-passwords.md) Uw gebruikers moeten hun aanmeldingsgegevens wissen (verwijderen), de app opnieuw opstarten en zich vervolgens aanmelden met hun gebruikersnaam en *app-wachtwoord* in plaats van hun normale wachtwoord.

Als uw organisatie geen oudere clients heeft, moet u uw gebruikers niet toestaan app-wachtwoorden te maken.

> [!NOTE]
> Moderne verificatie voor Office 2013-clients
>
> App-wachtwoorden zijn alleen nodig voor apps die geen moderne verificatie ondersteunen. Office 2013-clients ondersteunen moderne verificatieprotocollen, maar moeten worden geconfigureerd. Moderne verificatie is nu beschikbaar voor elke klant die de update van maart 2015 of hoger voor Office 2013 uitvoert. Zie de bijgewerkte office [365-verificatie](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)voor meer informatie .

**V: Mijn gebruikers zeggen dat ze soms het sms-bericht of de verificatie-out niet ontvangen.**

Levering van SMS-berichten zijn niet gegarandeerd omdat er oncontroleerbare factoren zijn die de betrouwbaarheid van de service kunnen beïnvloeden. Deze factoren omvatten het land/de regio van bestemming, de mobiele telefoondrager en de signaalsterkte.

Als uw gebruikers vaak problemen hebben met het betrouwbaar ontvangen van sms-berichten, moet u hen in plaats daarvan de mobiele app of telefoongespreksmethode gebruiken. De mobiele app kan meldingen ontvangen via mobiele en Wi-Fi-verbindingen. Bovendien kan de mobiele app verificatiecodes genereren, zelfs als het apparaat helemaal geen signaal heeft. De Microsoft Authenticator-app is beschikbaar voor [Android,](https://go.microsoft.com/fwlink/?Linkid=825072) [IOS](https://go.microsoft.com/fwlink/?Linkid=825073)en [Windows Phone.](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)

**V: Kan ik de tijd wijzigen die mijn gebruikers hebben om de verificatiecode uit een sms-bericht in te voeren voordat het systeem een time-out heeft?**

In sommige gevallen wel, ja. 

Voor eenrichtings-sms met Azure MFA Server v7.0 of hoger u de time-outinstelling configureren door een registersleutel in te stellen. Nadat de MFA-cloudservice het sms-bericht heeft verzonden, wordt de verificatiecode (of eenmalige toegangscode) teruggestuurd naar de MFA-server. De MFA-server slaat de code standaard 300 seconden in het geheugen op. Als de gebruiker de code niet invoert voordat de 300 seconden zijn verstreken, wordt de verificatie geweigerd. Gebruik deze stappen om de standaardtime-outinstelling te wijzigen:

1. Ga naar HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Maak een DWORD-registersleutel met **de naam pfsvc_pendingSmsTimeoutSeconds** en stel de tijd in seconden in dat u wilt dat de Azure MFA Server eenmalige toegangscodes opslaat.

>[!TIP] 
>Als u meerdere MFA-servers hebt, kent alleen degene die het oorspronkelijke verificatieverzoek heeft verwerkt, de verificatiecode die naar de gebruiker is verzonden. Wanneer de gebruiker de code invoert, moet het verificatieverzoek om deze te valideren naar dezelfde server worden verzonden. Als de codevalidatie naar een andere server wordt verzonden, wordt de verificatie geweigerd. 

Als gebruikers niet binnen de gedefinieerde time-outperiode op de sms reageren, wordt hun verificatie geweigerd. 

Voor eenrichtingssms met Azure MFA in de cloud (inclusief de AD FS-adapter of de netwerkbeleidsserverextensie) u de time-outinstelling niet configureren. Azure AD slaat de verificatiecode gedurende 180 seconden op. 

**V: Kan ik hardwaretokens gebruiken met Azure Multi-Factor Authentication Server?**

Als u Azure Multi-Factor Authentication Server gebruikt, u eed-tokens (Open Authentication) (OATH) importeren in time-based, eenmalige wachtwoord (TOTP) van derden en deze vervolgens gebruiken voor verificatie in twee stappen.

U ActiveIdentity-tokens gebruiken die OATH TOTP-tokens zijn als u de geheime sleutel in een CSV-bestand plaatst en importeert naar Azure Multi-Factor Authentication Server. U OATH-tokens gebruiken met Active Directory Federation Services (ADFS), IIS-verificatie op basis van formulieren (Internet Information Server) en RADIUS (Remote Authentication Dial-In User Service) zolang het clientsysteem de gebruikersinvoer kan accepteren.

U OATH TOTP-tokens van derden importeren met de volgende indelingen:  

- Draagbare symmetrische sleutelcontainer (PSKC)  
- CSV als het bestand een serienummer, een geheime sleutel in de base 32-indeling en een tijdsinterval bevat  

**V: Kan ik Azure Multi-Factor Authentication Server gebruiken om Terminal Services te beveiligen?**

Ja, maar als u Windows Server 2012 R2 of hoger gebruikt, u Terminal Services alleen beveiligen met Extern bureaublad-gateway (Extern bureaublad-gateway).

Beveiligingswijzigingen in Windows Server 2012 R2 hebben de manier gewijzigd waarop Azure Multi-Factor Authentication Server verbinding maakt met het beveiligingspakket (Local Security Authority) in Windows Server 2012 en eerdere versies. Voor versies van Terminal Services in Windows Server 2012 of eerder u [een toepassing beveiligen met Windows Authentication.](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure) Als u Windows Server 2012 R2 gebruikt, hebt u Rd Gateway nodig.

**V: Ik heb beller-id geconfigureerd in MFA Server, maar mijn gebruikers ontvangen nog steeds Multi-Factor Authentication-oproepen van een anonieme beller.**

Wanneer multi-factor authenticatie gesprekken worden geplaatst via het openbare telefoonnetwerk, soms worden ze gerouteerd via een provider die geen ondersteuning voor beller-ID. Hierdoor is beller-ID niet gegarandeerd, hoewel het Multi-Factor Authentication-systeem het altijd verzendt.

**V: Waarom worden mijn gebruikers gevraagd hun beveiligingsgegevens te registreren?**
Er zijn verschillende redenen waarom gebruikers kunnen worden gevraagd om hun beveiligingsgegevens te registreren:

- De gebruiker is ingeschakeld voor MFA door de beheerder in Azure AD, maar heeft nog geen beveiligingsgegevens geregistreerd voor zijn account.
- De gebruiker is ingeschakeld voor het opnieuw instellen van selfservicewachtwoorden in Azure AD. De beveiligingsinformatie zal hen helpen hun wachtwoord in de toekomst opnieuw in te stellen als ze het ooit vergeten.
- De gebruiker heeft toegang tot een toepassing die een beleid voor voorwaardelijke toegang heeft om MFA te vereisen en die nog niet eerder is geregistreerd voor MFA.
- De gebruiker registreert een apparaat bij Azure AD (inclusief Azure AD Join) en uw organisatie heeft MFA nodig voor apparaatregistratie, maar de gebruiker heeft zich nog niet eerder geregistreerd voor MFA.
- De gebruiker genereert Windows Hello for Business in Windows 10 (waarvoor MFA nodig is) en heeft zich nog niet eerder geregistreerd voor MFA.
- De organisatie heeft een MFA-registratiebeleid gemaakt en ingeschakeld dat op de gebruiker is toegepast.
- De gebruiker heeft zich eerder geregistreerd voor MFA, maar heeft een verificatiemethode gekozen die een beheerder sindsdien heeft uitgeschakeld. De gebruiker moet daarom opnieuw door mfa-registratie gaan om een nieuwe standaardverificatiemethode te selecteren.

## <a name="errors"></a>Fouten

**V: Wat moeten gebruikers doen als ze een foutbericht 'Verificatieverzoek is niet voor een geactiveerd account' zien bij het gebruik van meldingen van mobiele apps?**

Laat ze deze procedure volgen om hun account uit de mobiele app te verwijderen en het vervolgens opnieuw toe te voegen:

1. Ga naar [uw Azure-portalprofiel](https://account.activedirectory.windowsazure.com/profile/) en meld u aan met uw organisatieaccount.
2. Selecteer **Extra beveiligingsverificatie**.
3. Verwijder het bestaande account uit de mobiele app.
4. Klik **op Configureren**en volg de instructies om de mobiele app opnieuw te configureren.

**V: Wat moeten gebruikers doen als ze een 0x800434D4L-foutbericht zien wanneer ze zich aanmelden bij een niet-browsertoepassing?**

De 0x800434D4L-fout treedt op wanneer u zich probeert aan te melden bij een niet-browsertoepassing die is geïnstalleerd op een lokale computer en die niet werkt met accounts waarvoor verificatie in twee stappen vereist is.

Een tijdelijke oplossing voor deze fout is het hebben van afzonderlijke gebruikersaccounts voor beheergerelateerde en niet-beheerdersbewerkingen. Later u postvakken koppelen tussen uw beheerdersaccount en een niet-beheerdersaccount, zodat u zich aanmelden bij Outlook met uw niet-beheerdersaccount. Voor meer informatie over deze oplossing leest u hoe [u een beheerder de mogelijkheid geeft om de inhoud van het postvak van een gebruiker te openen en te bekijken.](https://help.outlook.com/141/gg709759.aspx?sl=1)

## <a name="next-steps"></a>Volgende stappen

Als uw vraag hier niet wordt beantwoord, laat deze dan achter in de reacties onderaan de pagina. Of, hier zijn enkele extra opties voor het krijgen van hulp:

* Zoek in de [Microsoft Support Knowledge Base](https://support.microsoft.com) naar oplossingen voor veelvoorkomende technische problemen.
* Zoek naar technische vragen en antwoorden van de community of stel uw eigen vraag in de [Azure Active Directory-forums.](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)
* Als u een oudere PhoneFactor-klant bent en u vragen hebt of hulp nodig hebt bij het opnieuw instellen van een wachtwoord, gebruikt u de koppeling [wachtwoordreset](mailto:phonefactorsupport@microsoft.com) om een ondersteuningsaanvraag te openen.
* Neem contact op met een ondersteuningsmedewerker via [azure multi-factor authentication server (PhoneFactor) ondersteuning](https://support.microsoft.com/oas/default.aspx?prid=14947). Wanneer u contact met ons opneemt, is het handig als u zoveel mogelijk informatie over uw probleem opnemen. De informatie die u verstrekken, omvat de pagina waarop u de fout hebt gezien, de specifieke foutcode, de specifieke sessie-id en de id van de gebruiker die de fout heeft gezien.
