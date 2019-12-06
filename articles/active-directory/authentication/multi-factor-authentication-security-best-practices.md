---
title: Beveiligings richtlijnen voor Azure-Multi-Factor Authentication-Azure Active Directory
description: Dit document bevat richt lijnen over het gebruik van Azure MFA met Azure-accounts
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e42234e9fcdcfe3ee5ce975babbe03b64a750e36
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846824"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Beveiligings richtlijnen voor het gebruik van Azure Multi-Factor Authentication met Azure AD-accounts

Verificatie in twee stappen is de voorkeurs keuze voor de meeste organisaties die hun verificatie proces willen verbeteren. Met Azure Multi-Factor Authentication (MFA) kunnen bedrijven voldoen aan de beveiligings-en nalevings vereisten en tegelijkertijd een eenvoudige aanmeldings ervaring bieden voor hun gebruikers. In dit artikel vindt u enkele tips waarmee u rekening moet houden bij het plannen van de acceptatie van Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Azure MFA in de Cloud implementeren

Er zijn twee manieren om [Azure MFA in te scha kelen voor al uw gebruikers](howto-mfa-getstarted.md).

* Licenties aanschaffen voor elke gebruiker (Azure MFA, Azure AD Premium of Enterprise Mobility + Security)
* Maak een multi-factor Authentication-provider en betaal per gebruiker of per verificatie

### <a name="licenses"></a>Licenties

![Licenties Toep assen op gebruikers, inschakelen, waarschuwen](./media/multi-factor-authentication-security-best-practices/ems.png)

Als u Azure AD Premium-of Enterprise Mobility + Security-licenties hebt, hebt u al Azure MFA. Uw organisatie heeft niets meer nodig om de verificatie mogelijkheid van twee stappen voor alle gebruikers uit te breiden. U hoeft alleen een licentie toe te wijzen aan een gebruiker en vervolgens MFA in te scha kelen.

Houd rekening met de volgende tips bij het instellen van Multi-Factor Authentication:

* Maak geen multi-factor Authentication-provider per verificatie. Als u dat wel doet, kunt u de betaling voor verificatie aanvragen van gebruikers die al licenties hebben, beëindigen.
* Als u niet genoeg licenties hebt voor al uw gebruikers, kunt u een multi-factor Authentication-provider per gebruiker maken voor de rest van uw organisatie. 
* Azure AD Connect is alleen vereist als u uw on-premises Active Directory omgeving synchroniseert met een Azure AD-adres lijst. Als u een Azure AD-adres lijst gebruikt die niet is gesynchroniseerd met een on-premises exemplaar van Active Directory, hebt u niet Azure AD Connect nodig.

### <a name="multi-factor-auth-provider"></a>Multi-factor Authentication-provider

![Multi-Factor Authentication provider](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Als u geen licenties hebt die Azure MFA bevatten, kunt u [een MFA-verificatie provider maken](concept-mfa-authprovider.md).

Wanneer u de verificatie provider maakt, moet u een map selecteren en de volgende details overwegen:

* U hebt geen Azure AD-adres lijst nodig om een multi-factor Authentication-provider te maken, maar u krijgt er meer functionaliteit met een. De volgende functies zijn ingeschakeld wanneer u de verificatie provider koppelt aan een Azure AD-adres lijst:
  * Verificatie in twee stappen uitbreiden naar al uw gebruikers
  * Bied uw globale beheerders extra functies, zoals de beheer Portal, aangepaste begroetingen en rapporten.
* Als u uw on-premises Active Directory omgeving synchroniseert met een Azure AD-adres lijst, hebt u DirSync of Azure AD Sync nodig. Als u een Azure AD-adres lijst gebruikt die niet is gesynchroniseerd met een on-premises exemplaar van Active Directory, hebt u geen DirSync of Azure AD Sync nodig.
* Kies het verbruiks model dat het beste bij uw bedrijf past. Wanneer u het gebruiks model selecteert, kunt u dit niet wijzigen. De twee modellen zijn:
  * Per authenticatie: u wordt voor elke verificatie kosten in rekening gebracht. Gebruik dit model als u verificatie in twee stappen wilt gebruiken voor iedereen die toegang heeft tot een bepaalde app, niet voor specifieke gebruikers.
  * Per ingeschakelde gebruiker: kosten voor elke gebruiker die u inschakelt voor Azure MFA. Gebruik dit model als u een aantal gebruikers hebt met een Azure AD Premium-of ENTER prise Mobility Suite-licentie, en sommige zonder.

### <a name="supportability"></a>Ondersteuning

Aangezien de meeste gebruikers gewend zijn om alleen wacht woorden te gebruiken voor verificatie, is het belang rijk dat uw bedrijf zich bewust maakt van alle gebruikers met betrekking tot dit proces. Dit bewustzijn kan de kans verminderen dat gebruikers uw Help Desk bellen voor kleine problemen die betrekking hebben op MFA. Er zijn echter enkele scenario's waarin het tijdelijk uitschakelen van MFA nood zakelijk is. Gebruik de volgende richt lijnen om te begrijpen hoe u deze scenario's kunt afhandelen:

* Train uw mede werkers van technische ondersteuning voor het afhandelen van scenario's waarbij de gebruiker zich niet kan aanmelden, omdat de mobiele app of de telefoon geen melding of telefoon oproep ontvangt. Met technische ondersteuning kunt u een eenmalige [bypass inschakelen](howto-mfa-mfasettings.md#one-time-bypass) zodat een gebruiker één keer kan verifiëren door de verificatie in twee stappen te omzeilen. De bypass is tijdelijk en verloopt na een opgegeven aantal seconden.
* Houd rekening met de [betrouw bare ip's-mogelijkheden](howto-mfa-mfasettings.md#trusted-ips) in azure MFA als een manier om verificatie in twee stappen te minimaliseren. Met deze functie kunnen beheerders van een beheerde of federatieve Tenant verificatie in twee stappen overs laan voor gebruikers die zich aanmelden vanaf het lokale intranet van het bedrijf. De functies zijn beschikbaar voor Azure AD-tenants met Azure AD Premium, Enter prise Mobility Suite of Azure Multi-Factor Authentication-licenties.

## <a name="best-practices-for-an-on-premises-deployment"></a>Aanbevolen procedures voor een on-premises implementatie

Als uw bedrijf heeft besloten om gebruik te maken van een eigen infra structuur om MFA in te scha kelen, moet u [een Azure-multi-factor Authentication-Server op locatie implementeren](howto-mfaserver-deploy.md). De MFA-Server onderdelen worden weer gegeven in het volgende diagram:

![de standaard onderdelen van de MFA-server](./media/multi-factor-authentication-security-best-practices/server.png) \*niet geïnstalleerd door standaard \** geïnstalleerd, maar niet standaard ingeschakeld

Azure Multi-Factor Authentication-server kan cloud resources en on-premises resources beveiligen met behulp van Federatie. U moet AD FS hebben en deze federatief hebben met uw Azure AD-Tenant.
Bij het instellen van Multi-Factor Authentication-server, moet u rekening houden met de volgende details:

* Als u Azure AD-resources beveiligt met behulp van Active Directory Federation Services (AD FS), wordt de eerste verificatie stap on-premises uitgevoerd met behulp van AD FS. De tweede stap wordt on-premises uitgevoerd door de claim toe te kennen.
* U hoeft Azure Multi-Factor Authentication-server uw AD FS federatie server niet te installeren. De Multi-Factor Authentication adapter voor AD FS moet echter worden geïnstalleerd op een Windows Server 2012 R2 met AD FS. U kunt de server op een andere computer installeren, zolang deze een ondersteunde versie is en de AD FS-adapter afzonderlijk installeren op uw AD FS-federatie server. 
* Met de installatie wizard van de Multi-Factor Authentication AD FS adapter maakt u een beveiligings groep met de naam Phone factor admins in uw Active Directory en voegt u vervolgens uw AD FS-service account toe aan deze groep. Controleer of de groep PhoneFactor Admins inderdaad is gemaakt op uw domeincontroller en of het AD FS-serviceaccount lid is van deze groep. Voeg, indien nodig, het AD FS-serviceaccount handmatig toe aan de groep PhoneFactor Admins in uw domeincontroller.

### <a name="user-portal"></a>Gebruikersportal

De gebruikers Portal maakt gebruik van self-service mogelijkheden en biedt een volledige set mogelijkheden voor gebruikers beheer. Het wordt uitgevoerd op een IIS-website (Internet Information Server). Gebruik de volgende richt lijnen voor het configureren van dit onderdeel:

* IIS 6 of hoger gebruiken
* ASP.NET v-2.0.507207 installeren en registreren
* Zorg ervoor dat deze server kan worden geïmplementeerd in een perimeter netwerk

### <a name="app-passwords"></a>App-wacht woorden

Als uw organisatie federatief is voor eenmalige aanmelding met Azure AD en u Azure MFA wilt gebruiken, moet u rekening houden met de volgende details:

* Het app-wacht woord wordt geverifieerd door Azure AD en daarom wordt Federatie omzeild. Federatie wordt alleen gebruikt bij het instellen van app-wacht woorden.
* Voor federatieve gebruikers (SSO) worden wacht woorden opgeslagen in de organisatie-ID. Als de gebruiker het bedrijf verlaat, moet dat informatie naar de organisatie-ID stromen met behulp van DirSync. Het kan tot drie uur duren voordat het account is uitgeschakeld of verwijderd, waardoor het uitschakelen/verwijderen van app-wacht woorden in azure AD wordt vertraagd.
* On-premises instellingen voor toegangsbeheer van client worden niet herkend door het app-wachtwoord.
* Er is geen on-premises verificatie-logboek registratie/controle mogelijkheid beschikbaar voor app-wacht woorden.
* Voor bepaalde geavanceerde architecturale ontwerpen moet u een combi natie van gebruikers naam en wacht woord en app-wacht woorden gebruiken wanneer verificatie in twee stappen wordt gebruikt met clients, afhankelijk van waar ze worden geverifieerd. Voor clients die worden geverifieerd aan de hand van een on-premises infra structuur, gebruikt u een gebruikers naam en wacht woord voor de organisatie. Voor clients die met Azure AD worden geverifieerd, gebruikt u het app-wacht woord.
* Standaard kunnen gebruikers geen app-wacht woorden maken. Als u wilt toestaan dat gebruikers app-wacht woorden maken, selecteert u de optie **gebruikers toestaan app-wacht woorden te maken om zich aan te melden bij niet-browser toepassingen** .

## <a name="additional-considerations"></a>Aanvullende overwegingen

Gebruik deze lijst voor aanvullende overwegingen en richt lijnen voor elk onderdeel dat on-premises wordt geïmplementeerd:

* Azure Multi-Factor Authentication instellen met [Active Directory Federation Services](multi-factor-authentication-get-started-adfs.md).
* De Azure MFA-server instellen en configureren met [RADIUS-verificatie](howto-mfaserver-dir-radius.md).
* De Azure MFA-server instellen en configureren met [IIS-verificatie](howto-mfaserver-iis.md).
* De Azure MFA-server instellen en configureren met [Windows-verificatie](howto-mfaserver-windows.md).
* De Azure MFA-server instellen en configureren met [LDAP-verificatie](howto-mfaserver-dir-ldap.md).
* De Azure MFA-server instellen en configureren met [extern bureaublad-gateway en Azure multi-factor Authentication-server met behulp van RADIUS](howto-mfaserver-nps-rdg.md).
* De synchronisatie tussen de Azure MFA-server en [Windows Server-Active Directory](howto-mfaserver-dir-ad.md)instellen en configureren.
* [Implementatie van de webservice voor mobiele apps van de Azure Multi-Factor Authentication-server](howto-mfaserver-deploy-mobileapp.md).
* [Geavanceerde VPN-configuratie met Azure multi-factor Authentication](howto-mfaserver-nps-vpn.md) voor Cisco ASA, Citrix NetScaler en Juniper/Pulse Secure VPN-apparaten met behulp van LDAP of RADIUS.

## <a name="next-steps"></a>Volgende stappen

Hoewel in dit artikel enkele aanbevolen procedures voor Azure MFA worden besproken, zijn er andere bronnen die u ook kunt gebruiken bij het plannen van uw MFA-implementatie. De onderstaande lijst bevat enkele belang rijke artikelen die u tijdens dit proces kunnen helpen:

* [Rapporten in azure Multi-Factor Authentication](howto-mfa-reporting.md)
* [De verificatie met twee stappen registratie-ervaring](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Veelgestelde vragen over Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)
