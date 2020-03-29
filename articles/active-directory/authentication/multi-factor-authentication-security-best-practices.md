---
title: Beveiligingsrichtlijnen voor Azure Multi-Factor Authentication - Azure Active Directory
description: Dit document biedt richtlijnen voor het gebruik van Azure MFA met Azure-accounts
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74846824"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Beveiligingsrichtlijnen voor het gebruik van Azure Multi-Factor Authentication met Azure AD-accounts

Verificatie in twee stappen is de voorkeurskeuze voor de meeste organisaties die hun verificatieproces willen verbeteren. Azure Multi-Factor Authentication (MFA) helpt bedrijven om te voldoen aan hun beveiligings- en nalevingsvereisten en biedt tegelijkertijd een eenvoudige aanmeldingservaring voor hun gebruikers. In dit artikel vindt u enkele tips waarmee u rekening moet houden bij het plannen voor de goedkeuring van Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Azure MFA implementeren in de cloud

Er zijn twee manieren om Azure MFA in te [schakelen voor al uw gebruikers.](howto-mfa-getstarted.md)

* Licenties kopen voor elke gebruiker (Azure MFA, Azure AD Premium of Enterprise Mobility + Security)
* Een Multi-Factor Auth-provider maken en betalen per gebruiker of per verificatie

### <a name="licenses"></a>Licenties

![Licenties toepassen op gebruikers, inschakelen, op de hoogte stellen](./media/multi-factor-authentication-security-best-practices/ems.png)

Als u Azure AD Premium- of Enterprise Mobility + Security-licenties hebt, beschikt u al over Azure MFA. Uw organisatie heeft niets extra's nodig om de verificatiemogelijkheid in twee stappen uit te breiden naar alle gebruikers. U hoeft alleen een licentie toe te wijzen aan een gebruiker en vervolgens u MFA inschakelen.

Houd bij het instellen van multifactorverificatie de volgende tips in overweging:

* Maak geen Multi-Factor Auth-provider per verificatie. Als u dit doet, u uiteindelijk betalen voor verificatieverzoeken van gebruikers die al licenties hebben.
* Als u niet genoeg licenties hebt voor al uw gebruikers, u een Multi-Factor Auth-provider per gebruiker maken om de rest van uw organisatie te dekken. 
* Azure AD Connect is alleen vereist als u uw on-premises Active Directory-omgeving synchroniseert met een Azure AD-map. Als u een Azure AD-map gebruikt die niet is gesynchroniseerd met een on-premises instantie van Active Directory, hebt u geen Azure AD Connect nodig.

### <a name="multi-factor-auth-provider"></a>Multi-Factor Auth Provider

![Multi-Factor Authentication Provider](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Als u geen licenties hebt die Azure MFA bevatten, u [een MFA Auth-provider maken.](concept-mfa-authprovider.md)

Bij het maken van de Auth Provider moet u een map selecteren en rekening houden met de volgende details:

* U hebt geen Azure AD-map nodig om een Multi-Factor Auth-provider te maken, maar u krijgt meer functionaliteit met één. De volgende functies zijn ingeschakeld wanneer u de Auth-provider koppelt aan een Azure AD-map:
  * Verificatie in twee stappen uitbreiden naar al uw gebruikers
  * Bied uw globale beheerders extra functies, zoals de beheerportal, aangepaste begroetingen en rapporten.
* Als u uw on-premises Active Directory-omgeving synchroniseert met een Azure AD-map, hebt u DirSync of Azure AD Sync nodig. Als u een Azure AD-map gebruikt die niet is gesynchroniseerd met een on-premises instantie van Active Directory, hebt u geen DirSync of Azure AD Sync nodig.
* Kies het verbruiksmodel dat het beste bij uw bedrijf past. Zodra u het gebruiksmodel hebt geselecteerd, u het niet meer wijzigen. De twee modellen zijn:
  * Per verificatie: brengt kosten in rekening voor elke verificatie. Gebruik dit model als u verificatie in twee stappen wilt voor iedereen die toegang heeft tot een bepaalde app, niet voor specifieke gebruikers.
  * Per ingeschakelde gebruiker: brengt kosten in rekening voor elke gebruiker die u inschakelt voor Azure MFA. Gebruik dit model als u bepaalde gebruikers hebt met Azure AD Premium- of Enterprise Mobility Suite-licenties en sommige zonder.

### <a name="supportability"></a>Ondersteuning

Aangezien de meeste gebruikers gewend zijn om alleen wachtwoorden te gebruiken om te verifiëren, is het belangrijk dat uw bedrijf alle gebruikers bewust maakt van dit proces. Dit bewustzijn kan de kans verkleinen dat gebruikers uw helpdesk bellen voor kleine problemen met betrekking tot MFA. Er zijn echter enkele scenario's waarin het tijdelijk uitschakelen van MFA noodzakelijk is. Gebruik de volgende richtlijnen om te begrijpen hoe u met deze scenario's omgaat:

* Train uw technische ondersteuningsmedewerkers om scenario's af te handelen waarin de gebruiker zich niet kan aanmelden omdat de mobiele app of telefoon geen melding of telefoontje ontvangt. Technische ondersteuning kan [een eenmalige bypass inschakelen](howto-mfa-mfasettings.md#one-time-bypass) om een gebruiker in staat te stellen een enkele keer te verifiëren door verificatie in twee stappen te "omzeilen". De bypass is tijdelijk en verloopt na een bepaald aantal seconden.
* Beschouw de [vertrouwde IP-mogelijkheden](howto-mfa-mfasettings.md#trusted-ips) in Azure MFA als een manier om verificatie in twee stappen te minimaliseren. Met deze functie kunnen beheerders van een beheerde of federatieve tenant verificatie in twee stappen omzeilen voor gebruikers die zich aanmelden vanaf het lokale intranet van het bedrijf. De functies zijn beschikbaar voor Azure AD-tenants met Azure AD Premium-, Enterprise Mobility Suite- of Azure Multi-Factor Authentication-licenties.

## <a name="best-practices-for-an-on-premises-deployment"></a>Aanbevolen procedures voor een on-premises implementatie

Als uw bedrijf heeft besloten om zijn eigen infrastructuur te gebruiken om MFA in te schakelen, moet u [een Azure Multi-Factor Authentication Server on-premises implementeren.](howto-mfaserver-deploy.md) De MFA Server-componenten worden weergegeven in het volgende diagram:

![De standaardMFA-servercomponenten](./media/multi-factor-authentication-security-best-practices/server.png) \*niet \*standaard geïnstalleerd *standaard geïnstalleerd, maar niet standaard ingeschakeld

Azure Multi-Factor Authentication Server kan cloudbronnen en on-premises resources beveiligen met behulp van federatie. U moet AD FS hebben en deze laten doen met uw Azure AD-tenant.
Houd bij het instellen van multifactorverificatieserver rekening met de volgende details:

* Als u Azure AD-resources beveiligt met Active Directory Federation Services (AD FS), wordt de eerste verificatiestap on-premises uitgevoerd met AD FS. De tweede stap wordt on-premises uitgevoerd door de claim toe te kennen.
* U hoeft de Azure Multi-Factor Authentication Server niet te installeren op uw AD FS-federatieserver. De multifactorverificatieadapter voor AD FS moet echter worden geïnstalleerd op een Windows Server 2012 R2 met AD FS. U de server op een andere computer installeren, zolang het een ondersteunde versie is, en de AD FS-adapter afzonderlijk installeren op uw AD FS-federatieserver. 
* Met de installatiewizard AD FS-adapter voor meervoudige factorverificatie maakt u een beveiligingsgroep met de naam PhoneFactor-beheerders in uw Active Directory en voegt u vervolgens uw AD FS-serviceaccount toe aan deze groep. Controleer of de groep PhoneFactor Admins inderdaad is gemaakt op uw domeincontroller en of het AD FS-serviceaccount lid is van deze groep. Voeg, indien nodig, het AD FS-serviceaccount handmatig toe aan de groep PhoneFactor Admins in uw domeincontroller.

### <a name="user-portal"></a>Gebruikersportal

De gebruikersportal biedt selfservicemogelijkheden en biedt een volledige set gebruikersbeheermogelijkheden. Het wordt uitgevoerd op een IIS-website (Internet Information Server). Gebruik de volgende richtlijnen om deze component te configureren:

* IIS 6 of hoger gebruiken
* Installeren en registreren ASP.NET v2.0.507207
* Ervoor zorgen dat deze server kan worden geïmplementeerd in een perimeternetwerk

### <a name="app-passwords"></a>App-wachtwoorden

Als uw organisatie is gefedereerd voor SSO met Azure AD en u Azure MFA gaat gebruiken, moet u rekening houden met de volgende details:

* Het app-wachtwoord wordt geverifieerd door Azure AD en omzeilt daarom de federatie. Federatie wordt alleen gebruikt bij het instellen van app-wachtwoorden.
* Voor federatieve (SSO)-gebruikers worden wachtwoorden opgeslagen in de organisatie-ID. Als de gebruiker het bedrijf verlaat, moet die informatie via DirSync naar de organisatie-id worden geleid. Het kan tot drie uur duren voordat het account wordt uitgeschakeld/verwijderd, waardoor app-wachtwoorden in Azure AD worden uitgeschakeld/verwijderd.
* On-premises instellingen voor toegangsbeheer van client worden niet herkend door het app-wachtwoord.
* Er is geen on-premises verificatielogboekregistratie/controlemogelijkheid beschikbaar voor app-wachtwoorden.
* Voor bepaalde geavanceerde architecturale ontwerpen kan het nodig zijn een combinatie van gebruikersnamen en wachtwoorden van organisaties en app-wachtwoorden te gebruiken bij het gebruik van verificatie in twee stappen met clients, afhankelijk van waar ze zich verifiëren. Voor clients die zich verifiëren tegen een on-premises infrastructuur, gebruikt u een gebruikersgebruikersnaam en wachtwoord van de organisatie. Voor clients die zich verifiëren tegen Azure AD, gebruikt u het app-wachtwoord.
* Gebruikers kunnen standaard geen app-wachtwoorden maken. Als u gebruikers wilt toestaan app-wachtwoorden te maken, selecteert u de optie **Gebruikers toestaan app-wachtwoorden te maken om zich aan te melden bij niet-browsertoepassingen.**

## <a name="additional-considerations"></a>Aanvullende overwegingen

Gebruik deze lijst voor aanvullende overwegingen en richtlijnen voor elk onderdeel dat on-premises wordt geïmplementeerd:

* Azure Multi-Factor Authentication instellen met [Active Directory Federation Services](multi-factor-authentication-get-started-adfs.md).
* De Azure MFA-server instellen en configureren met [RADIUS-verificatie](howto-mfaserver-dir-radius.md).
* De Azure MFA Server instellen en configureren met [IIS-verificatie](howto-mfaserver-iis.md).
* De Azure MFA Server instellen en configureren met [Windows-verificatie](howto-mfaserver-windows.md).
* De Azure MFA-server instellen en configureren met [LDAP-verificatie](howto-mfaserver-dir-ldap.md).
* De Azure MFA Server instellen en configureren met [Extern bureaublad-gateway en Azure Multi-Factor Authentication Server met RADIUS](howto-mfaserver-nps-rdg.md).
* Synchronisatie instellen en configureren tussen de Azure MFA Server en [Windows Server Active Directory.](howto-mfaserver-dir-ad.md)
* [De Webservice voor Azure Multi-Factor Authentication Server Mobile App implementeren.](howto-mfaserver-deploy-mobileapp.md)
* [Geavanceerde VPN-configuratie met Azure Multi-Factor Authentication](howto-mfaserver-nps-vpn.md) voor Cisco ASA, Citrix Netscaler en Juniper/Pulse Secure VPN-apparaten met LDAP of RADIUS.

## <a name="next-steps"></a>Volgende stappen

Hoewel in dit artikel enkele aanbevolen procedures voor Azure MFA worden belicht, zijn er andere bronnen die u ook gebruiken tijdens het plannen van uw MFA-implementatie. De onderstaande lijst heeft een aantal belangrijke artikelen die u kunnen helpen tijdens dit proces:

* [Rapporten in Azure Multi-Factor Authentication](howto-mfa-reporting.md)
* [De registratie-ervaring in twee stappen](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Veelgestelde vragen over azure-verificatie met meerdere factoren](multi-factor-authentication-faq.md)
