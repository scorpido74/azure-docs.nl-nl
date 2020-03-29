---
title: Technische beveiligingsmogelijkheden in Azure - Microsoft Azure
description: Inleiding tot beveiligingsservices in Azure waarmee u gegevens, bronnen en toepassingen in de cloud beschermen.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2019
ms.author: TomSh
ms.openlocfilehash: 61afad1d9994fd703bd8df047d1861baddeae997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845340"
---
# <a name="azure-security-technical-capabilities"></a>Technische mogelijkheden van Azure-beveiliging
Dit artikel bevat een inleiding tot beveiligingsservices in Azure waarmee u uw gegevens, resources en toepassingen in de cloud beschermen en voldoen aan de beveiligingsbehoeften van uw bedrijf.

## <a name="azure-platform"></a>Azure-platform

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) is een cloudplatform dat bestaat uit infrastructuur- en toepassingsservices, met geïntegreerde gegevensservices en geavanceerde analyses en ontwikkeltools en -services die worden gehost in de openbare clouddatacenters van Microsoft. Klanten gebruiken Azure voor veel verschillende capaciteiten en scenario's, van basisgegevens, netwerken en opslag tot mobiele en webappservices, tot volledige cloudscenario's zoals Internet of Things, en kunnen worden gebruikt met open source-technologieën en worden geïmplementeerd als hybride cloud of gehost in het datacenter van een klant. Azure biedt cloudtechnologie als bouwstenen om bedrijven te helpen kosten te besparen, snel te innoveren en systemen proactief te beheren. Wanneer u it-assets voortbouwt of migreert naar een cloudprovider, vertrouwt u op de mogelijkheden van die organisatie om uw toepassingen en gegevens te beschermen met de services en de besturingselementen die ze bieden om de beveiliging van uw cloudgebaseerde assets te beheren.

Microsoft Azure is de enige cloud computing-provider die een veilig, consistent applicatieplatform en infrastructure-as-a-service biedt voor teams om te werken binnen hun verschillende cloudvaardigheden en niveaus van projectcomplexiteit, met geïntegreerde gegevensservices en analyses die intelligentie uit gegevens blootleggen waar deze ook bestaat, over zowel Microsoft- als niet-Microsoft-platforms, open frameworks en tools, die de keuze bieden om cloud te integreren met on-premises en Azure-cloudservices te implementeren binnen on-premises datacenters. Als onderdeel van de Microsoft Trusted Cloud vertrouwen klanten op Azure voor toonaangevende beveiliging, betrouwbaarheid, naleving, privacy en het uitgebreide netwerk van mensen, partners en processen om organisaties in de cloud te ondersteunen.

Met Microsoft Azure u het als nog niet doen:

- Versnel innovatie met de cloud.

- Zakelijke beslissingen & apps met inzichten.

- Bouw vrij en implementeer overal.

- Bescherm hun bedrijf.

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>Technische beveiligingsmogelijkheden om uw verantwoordelijkheid te nemen

Microsoft Azure biedt services waarmee u voldoen aan uw beveiligings-, privacy- en nalevingsbehoeften. In de volgende afbeelding u verschillende Azure-services uitleggen die beschikbaar zijn voor u om een veilige en compatibele toepassingsinfrastructuur te bouwen op basis van industriestandaarden.

![Beschikbare technische beveiligingsmogelijkheden- Groot beeld](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Identiteit en gebruikerstoegang beheren en beheren

Azure helpt u zakelijke en persoonlijke gegevens te beschermen door u in staat te stellen gebruikersidentiteiten en referenties te beheren en de toegang te beheren.

### <a name="azure-active-directory"></a>Azure Active Directory

Microsoft-oplossingen voor identiteits- en toegangsbeheer helpen IT de toegang tot toepassingen en resources in het bedrijfsdatacenter en in de cloud te beschermen, waardoor extra validatieniveaus mogelijk zijn, zoals meervoudige verificatie en voorwaardelijke toegang Beleid. Het controleren van verdachte activiteiten via geavanceerde beveiligingsrapportage, controle en waarschuwingen, helpt om potentiële beveiligingsproblemen te verminderen. [Azure Active Directory Premium](../../active-directory/active-directory-whatis.md) biedt één malige aanmelding voor duizenden cloud-apps en toegang tot web-apps die u on-premises uitvoert.

Beveiligingsvoordelen van Azure Active Directory (Azure AD) zijn onder meer de mogelijkheid om:

- Maak en beheer één enkele identiteit voor elke gebruiker in uw hybride onderneming, zodat gebruikers, groepen en apparaten gesynchroniseerd blijven.

- Geef toegang tot uw toepassingen met één aanmelding, waaronder duizenden vooraf geïntegreerde SaaS-apps.

- Toegangsbeveiliging tot toepassingen inschakelen door Multi-Factor Authentication op basis van regels af te dwingen voor zowel on-premises toepassingen als cloudtoepassingen.

- Informeer veilige externe toegang tot on-premises webtoepassingen via Azure AD Application Proxy.

De [Azure Active Directory-portal](https://aad.portal.azure.com/) is beschikbaar als onderdeel van de Azure-portal. Vanuit dit dashboard u een overzicht krijgen van de status van uw organisatie en eenvoudig de directory, gebruikers of toepassingstoegang beheren.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

Hieronder volgen de belangrijkste azure-mogelijkheden voor identiteitsbeheer:

- Eenmalige aanmelding

- Multi-Factor Authentication

- Beveiligingsbewaking, waarschuwingen en op machine learning gebaseerde rapporten

- Beheer van de identiteit en toegang van de consument

- Apparaatregistratie

- Privileged Identity Management

- Identiteitsbeveiliging

#### <a name="single-sign-on"></a>Eenmalige aanmelding

[Eenmalige aanmelding (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) betekent toegang hebben tot alle toepassingen en bronnen die u nodig hebt om zaken te doen, door u slechts één keer aan te melden met één gebruikersaccount. Eenmaal aangemeld, hebt u toegang tot alle toepassingen die u nodig hebt zonder dat u een tweede keer een wachtwoord hoeft te verifiëren (bijvoorbeeld een wachtwoord moet typen).

Veel organisaties vertrouwen op software as a service (SaaS)-toepassingen zoals Office 365, Box en Salesforce voor de productiviteit van eindgebruikers. In het verleden moesten IT-medewerkers gebruikersaccounts individueel maken en bijwerken in elke SaaS-toepassing en gebruikers moesten een wachtwoord onthouden voor elke SaaS-toepassing.

[Azure AD breidt on-premises Active Directory uit naar de cloud,](../../active-directory/manage-apps/what-is-single-sign-on.md)zodat gebruikers hun primaire organisatieaccount kunnen gebruiken om zich niet alleen aan te melden bij hun met een domein verbonden apparaten en bedrijfsbronnen, maar ook alle web- en SaaS-toepassingen die nodig zijn voor hun taak.

Niet alleen hoeven gebruikers niet meerdere sets gebruikersnamen en wachtwoorden te beheren, toegang tot toepassingen kan automatisch worden ingericht of gede-ingericht op basis van organisatiegroepen en hun status als werknemer. [Azure AD introduceert besturingselementen voor beveiligings- en toegangsbeheer](../../active-directory/active-directory-enterprise-apps-manage-sso.md) waarmee u de toegang van gebruikers in SaaS-toepassingen centraal beheren.

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[Azure Multi-Factor Authentication (MFA)](../../active-directory/authentication/multi-factor-authentication.md) is een verificatiemethode die het gebruik van meer dan één verificatiemethode vereist en een kritieke tweede beveiligingslaag toevoegt aan aanmeldingen en transacties van gebruikers. [MFA helpt de](../../active-directory/authentication/concept-mfa-howitworks.md) toegang tot gegevens en toepassingen te waarborgen en tegelijkertijd te voldoen aan de vraag van gebruikers naar een eenvoudig aanmeldingsproces. Het levert sterke verificatie via een reeks verificatieopties: telefoongesprek, sms of melding of verificatiecode voor mobiele apps en OAuth-tokens van derden.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Beveiligingsbewaking, waarschuwingen en op machine learning gebaseerde rapporten

Beveiligingsbewaking en waarschuwingen en op machine learning gebaseerde rapporten die inconsistente toegangspatronen identificeren, kunnen u helpen uw bedrijf te beschermen. U de toegangs- en gebruiksrapporten van Azure Active Directory gebruiken om inzicht te krijgen in de integriteit en beveiliging van de directory van uw organisatie. Met deze informatie kan een directorybeheerder beter bepalen waar mogelijke beveiligingsrisico's kunnen liggen, zodat ze adequaat kunnen plannen om die risico's te beperken.

In de Azure-portal of via de [Azure Active Directory-portal](https://aad.portal.azure.com/)worden [rapporten](../../active-directory/active-directory-reporting-azure-portal.md) op de volgende manieren gecategoriseerd:

- Anomalierapporten – bevatten aanmeldingsgebeurtenissen die we afwijkend vonden. Ons doel is om u bewust te maken van dergelijke activiteiten en u in staat stellen om te beslissen of een gebeurtenis verdacht is.

- Geïntegreerde toepassingsrapporten – geef inzicht in hoe cloudtoepassingen worden gebruikt in uw organisatie. Azure Active Directory biedt integratie met duizenden cloudtoepassingen.

- Foutrapporten : geef fouten aan die kunnen optreden bij het inrichten van accounts op externe toepassingen.

- Gebruikersspecifieke rapporten – geef apparaat- en aanmeldingsactiviteitsgegevens voor een specifieke gebruiker weer.

- Activiteitslogboeken : bevat een record van alle gecontroleerde gebeurtenissen in de afgelopen 24 uur, de afgelopen 7 dagen of de laatste 30 dagen, en wijzigingen in groepsactiviteiten en wachtwoordreset en registratieactiviteit.

#### <a name="consumer-identity-and-access-management"></a>Beheer van de identiteit en toegang van de consument

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) is een zeer beschikbare, wereldwijde, identiteitsbeheerservice voor toepassingen die gericht zijn op consumenten die worden geschaald naar honderden miljoenen identiteiten. De service kan worden geïntegreerd in zowel mobiele als webplatforms. Uw klanten kunnen zich bij al uw toepassingen aanmelden via verschillende aanpasbare methoden met hun bestaande sociale accounts of door nieuwe aanmeldingsgegevens te maken.

In het verleden, applicatie-ontwikkelaars die wilden [aanmelden en aanmelden consumenten](../../active-directory-b2c/overview.md) in hun toepassingen zou hebben geschreven hun eigen code. Bovendien gebruikten ze on-premises databases of systemen om gebruikersnamen en wachtwoorden op te slaan. Azure Active Directory B2C biedt uw organisatie een betere manier om identiteitsbeheer van consumenten te integreren in toepassingen met behulp van een veilig, op standaarden gebaseerd platform en een grote reeks uitbreidbaar beleid.

Wanneer u Azure Active Directory B2C gebruikt, kunnen uw consumenten zich aanmelden voor uw toepassingen met behulp van hun bestaande sociale accounts (Facebook, Google, Amazon, LinkedIn) of door nieuwe referenties (e-mailadres en wachtwoord, of gebruikersnaam en wachtwoord) te maken.

#### <a name="device-registration"></a>Apparaatregistratie

[Azure AD-apparaatregistratie](../../active-directory/devices/overview.md) is de basis voor scenario's [voor voorwaardelijke toegang op](../../active-directory/devices/overview.md) basis van apparaten. Wanneer een apparaat is geregistreerd, biedt Azure AD-apparaatregistratie het apparaat een identiteit die wordt gebruikt om het apparaat te verifiëren wanneer de gebruiker zich aanmeldt. Het geverifieerde apparaat en de kenmerken van het apparaat kunnen vervolgens worden gebruikt om beleid voor voorwaardelijke toegang af te dwingen voor toepassingen die worden gehost in de cloud en on-premises.

In combinatie met een [MDM-oplossing (Mobile Device Management),](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) zoals Intune, worden de apparaatkenmerken in Azure Active Directory bijgewerkt met aanvullende informatie over het apparaat. Hiermee u regels voor voorwaardelijke toegang maken die de toegang vanaf apparaten afdwingen om te voldoen aan uw normen voor beveiliging en naleving.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Met Privileged Identity Management (Azure Active Directory)](../../active-directory/privileged-identity-management/pim-configure.md) kun je met Azure Active Directory (AD) Privileged Identity Management je bevoorrechte identiteiten en toegang tot bronnen in Azure AD en andere Online Services van Microsoft, zoals Office 365 of Microsoft Intune, beheren, beheren en bewaken.

Soms moeten gebruikers bevoorrechte bewerkingen uitvoeren in Azure- of Office 365-bronnen of andere SaaS-apps. Dit betekent vaak dat organisaties hen permanent bevoorrechte toegang moeten geven in Azure AD. Dit is een groeiend beveiligingsrisico voor door de cloud gehoste resources omdat organisaties niet voldoende kunnen controleren wat die gebruikers doen met hun beheerdersrechten. Bovendien, als een gebruikersaccount met bevoorrechte toegang is aangetast, kan die ene inbreuk van invloed zijn op hun algehele cloudbeveiliging. Azure AD Privileged Identity Management helpt om dit risico op te lossen.

Met Azure AD Privileged Identity Management u:

- Zien welke gebruikers Azure AD-beheerders zijn

- On-demand, 'just-in-time' administratieve toegang tot Microsoft Online Services zoals Office 365 en Intune inschakelen

- Rapporten over beheerderstoegangsgeschiedenis en wijzigingen in beheerderstoewijzingen opsturen

- Meldingen ontvangen over toegang tot een bevoorrechte rol

#### <a name="identity-protection"></a>Identiteitsbeveiliging

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) is een beveiligingsservice die een geconsolideerd overzicht biedt van risicodetecties en potentiële kwetsbaarheden die van invloed zijn op de identiteit van uw organisatie. Identiteitsbeveiliging maakt gebruik van de mogelijkheden voor anomaliedetectie van bestaande Azure Active Directory (beschikbaar via de anomalous Activity Reports van Azure AD) en introduceert nieuwe typen risicodetectie die afwijkingen in realtime kunnen detecteren.

## <a name="secure-resource-access"></a>Beveiligde toegang tot bronnen

Toegangsbeheer in Azure begint vanuit een factureringsperspectief. De eigenaar van een Azure-account, toegankelijk door een bezoek aan het [Azure-accountcentrum](https://account.windowsazure.com/subscriptions), is de Accountadministrator (AA). Abonnementen zijn een container voor facturering, maar ze fungeren ook als een beveiligingsgrens: elk abonnement heeft een Service Administrator (SA) die Azure-resources in dat abonnement kan toevoegen, verwijderen en wijzigen met behulp van de Azure-portal. De standaard SA van een nieuw abonnement is de AA, maar de AA kan de SA wijzigen in het Azure-accountcentrum.

![Beveiligde brontoegang in Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

Abonnementen hebben ook een associatie met een directory. De map definieert een set gebruikers. Dit kunnen gebruikers zijn van het werk of de school die de directory heeft gemaakt, of externe gebruikers (dat wil zeggen Microsoft-accounts). Abonnementen zijn toegankelijk door een subset van gebruikers van directorys die zijn toegewezen als Service Administrator (SA) of Co-Administrator (CA); de enige uitzondering is dat Microsoft-accounts (voorheen Windows Live ID) om oudere redenen als SA of CA kunnen worden toegewezen zonder in de map aanwezig te zijn.

Beveiligingsgerichte bedrijven moeten zich richten op het geven van de exacte machtigingen die werknemers nodig hebben. Te veel machtigingen kunnen een account blootstellen aan aanvallers. Te weinig machtigingen betekenen dat werknemers hun werk niet efficiënt kunnen doen. [RBAC (Azure Role-Based Access Control)](../../role-based-access-control/overview.md) helpt dit probleem op te lossen door fijnkorrelig toegangsbeheer voor Azure aan te bieden.

![Beveiligde toegang tot bronnen](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Met op rollen gebaseerd toegangsbeheer kunt u taken scheiden binnen uw team en alleen de mate van toegang verlenen aan gebruikers die nodig is om de taken uit te voeren. In plaats van iedereen onbeperkte machtigingen te geven in uw Azure-abonnement of -bronnen, u alleen bepaalde acties toestaan. Gebruik bijvoorbeeld RBAC om de ene werknemer virtuele machines in een abonnement te laten beheren, terwijl een andere werknemer SQL-databases binnen hetzelfde abonnement kan beheren.

![Beveiligde brontoegang in Azure (RBAC)](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Gegevensbeveiliging en -versleuteling

Een van de sleutels tot gegevensbescherming in de cloud is het verantwoorden van de mogelijke status waarin uw gegevens kunnen optreden en welke besturingselementen beschikbaar zijn voor die status. Voor aanbevolen procedures voor gegevensbeveiliging en versleuteling van Azure bevinden de aanbevelingen zich rond de volgende gegevensstatussen.

- At-rest: Dit omvat alle informatie-opslag objecten, containers en typen die statisch bestaan op fysieke media, zij het magnetische of optische schijf.

- In-transit: Wanneer gegevens worden overgedragen tussen componenten, locaties of programma's, zoals via het netwerk, over een servicebus (van on-premises naar cloud en vice versa, inclusief hybride verbindingen zoals ExpressRoute), of tijdens een input/output proces, wordt het gezien als in-motion.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Ga in op elk van de volgende handelingen om versleuteling in rust te bereiken:

Ondersteuning voor ten minste één van de aanbevolen versleutelingsmodellen die in de volgende tabel worden beschreven om gegevens te versleutelen.

| Versleutelingsmodellen |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Serverversleuteling | Serverversleuteling | Serverversleuteling | Clientversleuteling
| Versleuteling aan de serverzijde met servicebeheersleutels | Versleuteling aan de serverzijde met door de klant beheerde sleutels in Azure Key Vault | Versleuteling aan de serverzijde met on-premises door de klant beheerde sleutels |
| • Azure Resource Providers voeren de versleutelings- en decryptiebewerkingen uit <br> • Microsoft beheert de sleutels <br>• Volledige cloudfunctionaliteit | • Azure Resource Providers voeren de versleutelings- en decryptiebewerkingen uit<br>• Sleutels voor klantbediening via Azure Key Vault<br>• Volledige cloudfunctionaliteit | • Azure Resource Providers voeren de versleutelings- en decryptiebewerkingen uit <br>• Sleutels van klantcontroles on-premises <br> • Volledige cloudfunctionaliteit| • Azure-services kunnen gedecodeerde gegevens niet zien <br>• Klanten bewaren sleutels on-premises (of in andere beveiligde winkels). Sleutels zijn niet beschikbaar voor Azure-services <br>• Minder cloudfunctionaliteit|

### <a name="enabling-encryption-at-rest"></a>Versleuteling in rust inschakelen

**Alle locaties identificeren Uw winkelgegevens**

Het doel van encryptie in rust is om alle gegevens te versleutelen. Hierdoor wordt de mogelijkheid om belangrijke gegevens of alle blijvende locaties te missen geëlimineerd. Opsommen van alle gegevens die zijn opgeslagen door uw toepassing.

> [!Note]
> Niet alleen "applicatiegegevens" of "PII" maar alle gegevens met betrekking tot toepassing inclusief accountmetadata (abonnementstoewijzingen, contractinfo, PII).

Bedenk welke winkels u gebruikt om gegevens op te slaan. Bijvoorbeeld:

- Externe opslag (bijvoorbeeld SQL Azure, Document DB, HDInsights, Data Lake, enz.)

- Tijdelijke opslag (alle lokale cache met tenantgegevens)

- In-memory cache (kan in het paginabestand worden geplaatst.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Gebruik maken van de bestaande ondersteuning voor versleuteling in rust in Azure

Voor elke winkel die u gebruikt, maakt u gebruik van de bestaande ondersteuning voor versleuteling in rust.

- Azure Storage: Zie [Azure Storage Service Encryption for Data at Rest](../../storage/common/storage-service-encryption.md),

- SQL Azure: Zie [Transparent Data Encryption (TDE), SQL Always Encrypted SQL](https://msdn.microsoft.com/library/mt163865.aspx)

- VM & lokale schijfopslag[(Azure Disk Encryption)](../azure-security-disk-encryption-overview.md)

Voor VM- en lokale schijfopslag u Azure Disk Encryption gebruiken waar ondersteuning wordt ondersteund:

#### <a name="iaas"></a>IaaS

Services met IaaS VM's (Windows of Linux) moeten [Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) gebruiken om volumes met klantgegevens te versleutelen.

#### <a name="paas-v2"></a>PaaS v2

Services die worden uitgevoerd op PaaS v2 met Service Fabric kunnen Azure-schijfversleuteling gebruiken voor Virtual Machine Scale Set [VMSS] om hun PaaS v2 VM's te versleutelen.

#### <a name="paas-v1"></a>PaaS v1

Azure Disk Encryption wordt momenteel niet ondersteund op PaaS v1. Daarom moet u versleuteling op toepassingsniveau gebruiken om aanhoudende gegevens in rust te versleutelen.  Dit omvat, maar is niet beperkt tot, toepassingsgegevens, tijdelijke bestanden, logboeken en crashdumps.

De meeste services moeten proberen gebruik te maken van de versleuteling van een opslagresourceprovider. Sommige services moeten expliciete versleuteling doen, bijvoorbeeld, alle persistent key material (Certificaten, root / master keys) moet worden opgeslagen in Key Vault.

Als u service-side encryptie met door de klant beheerde sleutels ondersteunt, moet er een manier zijn voor de klant om de sleutel bij ons te krijgen. De ondersteunde en aanbevolen manier om dat te doen door te integreren met Azure Key Vault (AKV). In dit geval kunnen klanten hun sleutels toevoegen en beheren in Azure Key Vault. Een klant kan leren hoe u AKV gebruiken via [Aan de slag met Key Vault.](https://go.microsoft.com/fwlink/?linkid=521402)

Als u wilt integreren met Azure Key Vault, voegt u code toe om een sleutel van AKV aan te vragen wanneer dat nodig is voor decryptie.

- Zie [Azure Key Vault – Stap voor stap](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) voor informatie over hoe u integreren met AKV.

Als u door klanten beheerde sleutels ondersteunt, moet u een UX voor de klant opgeven om op te geven welke Key Vault (of Key Vault URI) moet worden gebruikt.

Aangezien Encryption at Rest de versleuteling van host-, infrastructuur- en tenantgegevens omvat, kan het verlies van de sleutels als gevolg van systeemfouten of schadelijke activiteiten betekenen dat alle versleutelde gegevens verloren gaan. Het is daarom van cruciaal belang dat uw Encryptie at Rest-oplossing een uitgebreid verhaal voor noodherstel heeft dat bestand is tegen systeemfouten en schadelijke activiteiten.

Services die Encryptie at Rest implementeren, zijn meestal nog steeds gevoelig voor de versleutelingssleutels of gegevens die onversleuteld op het hoststation worden achtergelaten (bijvoorbeeld in het paginabestand van het hostbesturingssysteem.) Daarom moeten services ervoor zorgen dat het hostvolume voor hun services is versleuteld. Om dit te vergemakkelijken heeft compute-team de implementatie van Host Encryption ingeschakeld, die [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) NKP en extensies aan de DCM-service en -agent gebruikt om het hostvolume te versleutelen.

De meeste services worden geïmplementeerd op standaard Azure VM's. Dergelijke services moeten [hostversleuteling](../azure-security-disk-encryption-overview.md) automatisch ontvangen wanneer Compute dit inschakelt. Voor services die worden uitgevoerd in Compute managed clusters wordt hostversleuteling automatisch ingeschakeld wanneer Windows Server 2016 wordt uitgerold.

### <a name="encryption-in-transit"></a>Versleuteling tijdens het transport

Het beschermen van gegevens tijdens het transport moet essentieel onderdeel zijn van uw strategie voor gegevensbescherming. Aangezien gegevens vanaf veel locaties heen en weer bewegen, is de algemene aanbeveling dat u altijd SSL/TLS-protocollen gebruikt om gegevens uit te wisselen op verschillende locaties. In sommige omstandigheden u het hele communicatiekanaal isoleren tussen uw on-premises en cloudinfrastructuur met behulp van een VPN (Virtual Private Network).

Voor gegevens die worden verplaatst tussen uw on-premises infrastructuur en Azure, moet u rekening houden met passende beveiligingen zoals HTTPS of VPN.

Voor organisaties die toegang moeten beveiligen van meerdere werkstations die on-premises tot Azure zijn gevestigd, gebruikt u [Azure site-to-site VPN.](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)

Voor organisaties die toegang moeten beveiligen vanaf één werkstation dat on-premises tot Azure is gevestigd, gebruikt u [Point-to-Site VPN.](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Grotere gegevenssets kunnen worden verplaatst via een speciale wanverbinding met hoge snelheid, zoals [ExpressRoute.](https://azure.microsoft.com/services/expressroute/) Als u ervoor kiest expressroute te gebruiken, u de gegevens ook op toepassingsniveau versleutelen met [SSL/TLS](https://support.microsoft.com/kb/257591) of andere protocollen voor extra bescherming.

Als u interactie hebt met Azure Storage via de Azure Portal, vinden alle transacties plaats via HTTPS. [Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) via HTTPS kan ook worden gebruikt om te communiceren met [Azure Storage](https://azure.microsoft.com/services/storage/) en Azure [SQL Database.](https://azure.microsoft.com/services/sql-database/)

Organisaties die niet om gegevens te beschermen tijdens het transport zijn gevoeliger voor [man-in-the-middle-aanvallen](https://technet.microsoft.com/library/gg195821.aspx), [afluisteren](https://technet.microsoft.com/library/gg195641.aspx), en sessie kaping. Deze aanvallen kunnen de eerste stap zijn om toegang te krijgen tot vertrouwelijke gegevens.

U meer informatie vinden over de Azure VPN-optie door het artikel [Planning and design for VPN Gateway te](../../vpn-gateway/vpn-gateway-about-vpngateways.md)lezen.

### <a name="enforce-file-level-data-encryption"></a>Gegevensversleuteling op bestandsniveau afdwingen

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) maakt gebruik van versleutelings-, identiteits- en autorisatiebeleid om uw bestanden en e-mail te beveiligen. Azure RMS werkt op meerdere apparaten: telefoons, tablets en pc's door bescherming te bieden aan zowel binnen uw organisatie als buiten uw organisatie. Deze mogelijkheid is mogelijk omdat Azure RMS een beschermingsniveau toevoegt dat bij de gegevens blijft, zelfs wanneer deze de grenzen van uw organisatie overschrijdt.

Wanneer u Azure RMS gebruikt om uw bestanden te beschermen, gebruikt u industriestandaard cryptografie met volledige ondersteuning van [FIPS 140-2.](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) Wanneer u Azure RMS gebruikt voor gegevensbescherming, hebt u de zekerheid dat de beveiliging bij het bestand blijft, zelfs als het wordt gekopieerd naar opslag die niet onder de controle van IT valt, zoals een cloudopslagservice. Hetzelfde geldt voor bestanden die via e-mail worden gedeeld, het bestand wordt beschermd als bijlage bij een e-mailbericht, met instructies voor het openen van de beveiligde bijlage.
Bij het plannen voor Azure RMS-adoptie raden we het volgende aan:

- Installeer de [RMS-app voor delen](https://technet.microsoft.com/library/dn339006.aspx). Deze app integreert met Office-toepassingen door een Office-invoegtoepassing te installeren, zodat gebruikers bestanden eenvoudig rechtstreeks kunnen beveiligen.

- Toepassingen en services configureren om Azure RMS te ondersteunen

- Maak [aangepaste sjablonen](https://technet.microsoft.com/library/dn642472.aspx) die aansluiten bij uw bedrijfsvereisten. Bijvoorbeeld: een sjabloon voor top geheime gegevens die moeten worden toegepast in alle top geheime gerelateerde e-mails.

Organisaties die zwak zijn op het niveau [van gegevensclassificatie](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) en bestandsbeveiliging kunnen gevoeliger zijn voor gegevenslekken. Zonder de juiste bestandsbescherming kunnen organisaties geen bedrijfsinzichten verkrijgen, controleren op misbruik en kwaadaardige toegang tot bestanden voorkomen.

> [!Note]
> U meer informatie over Azure RMS lezen door het artikel Aan de [slag te gaan met Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application"></a>Uw toepassing beveiligen
Hoewel Azure verantwoordelijk is voor het beveiligen van de infrastructuur en het platform waarop uw toepassing wordt uitgevoerd, is het uw verantwoordelijkheid om uw toepassing zelf te beveiligen. Met andere woorden, u moet uw toepassingscode en -inhoud op een veilige manier ontwikkelen, implementeren en beheren. Zonder dit kan uw toepassingscode of -inhoud nog steeds kwetsbaar zijn voor bedreigingen.

### <a name="web-application-firewall"></a>Web Application Firewall
[Web application firewall (WAF)](../../application-gateway/waf-overview.md) is een functie van [Application Gateway](../../application-gateway/overview.md) die gecentraliseerde bescherming van uw webapplicaties biedt tegen veelvoorkomende exploits en kwetsbaarheden.

Web Application Firewall is gebaseerd op regels uit de [Core Rule Set 3.0 of 2.2.9 van OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van veelvoorkomende bekende beveiligingsproblemen. Veelvoorkomende aanvallen zijn hierbij onder andere aanvallen met SQL-injecties en aanvallen via scripting op meerdere sites. Het kan een hele uitdaging zijn om dergelijke aanvallen in toepassingscode te voorkomen en dit kan tevens veel onderhoud, patching en controle vereisen op meerdere lagen van de toepassingstopologie. Een gecentraliseerde firewall voor webtoepassingen maakt het beveiligingsbeheer veel eenvoudiger en biedt toepassingsbeheerders meer veiligheid tegen bedreigingen of aanvallen. Een WAF-oplossing kan ook sneller reageren op een beveiligingsrisico door een patch voor een bekend beveiligingsprobleem toe te passen op een centrale locatie in plaats van elke afzonderlijke webtoepassing te beveiligen. Bestaande toepassingsgateways kunnen eenvoudig worden geconverteerd naar een toepassingsgateway met Web Application Firewall.

Hieronder ziet u enkele voorbeelden van veelvoorkomende beveiligingsproblemen waartegen Web Application Firewall bescherming biedt:

- Beveiliging tegen SQL-injecties

- Beveiliging tegen scripting op meerdere sites

- Beveiliging tegen veelvoorkomende aanvallen via internet, zoals opdrachtinjectie, het smokkelen van HTTP-aanvragen, het uitsplitsen van HTTP-antwoorden en aanvallen waarbij een extern bestand wordt ingesloten

- Beveiliging tegen schendingen van het HTTP-protocol

- Beveiliging tegen afwijkingen van het HTTP-protocol, zoals een gebruikersagent voor de host en Accept-headers die ontbreken

- Beveiliging tegen bots, crawlers en scanners

- Detectie van veelvoorkomende toepassingsverkeerde configuraties (dat wil zeggen Apache, IIS, enz.)

> [!Note]
> Zie voor een meer gedetailleerde lijst van regels en hun bescherming de volgende [kernregelsets:](../../application-gateway/waf-overview.md)

Azure biedt ook verschillende gebruiksvriendelijke functies om zowel binnenkomend als uitgaand verkeer voor uw app te beveiligen. Azure helpt klanten ook hun toepassingscode te beveiligen door extern geleverde functionaliteit te bieden om uw webtoepassing te scannen op kwetsbaarheden.

- [Azure Active Directory-verificatie instellen voor uw app](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Verkeer naar uw app beveiligen door Tls/SSL (Transport Layer Security) in te schakelen - HTTPS](../../app-service/configure-ssl-bindings.md)

  - [Al het binnenkomende verkeer via HTTPS-verbinding forceren](http://microsoftazurewebsitescheatsheet.info/)

  - [Strikte transportbeveiliging (HSTS) inschakelen](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Toegang tot uw app beperken via het IP-adres van de klant](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Toegang tot uw app beperken op basis van het gedrag van de klant - aanvraagfrequentie en gelijktijdigheid](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Scan uw web-app code op kwetsbaarheden met behulp van Tinfoil Security Scanning](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Tls-wederzijdse verificatie configureren om clientcertificaten te vereisen om verbinding te maken met uw web-app](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Een clientcertificaat configureren voor gebruik vanuit uw app om veilig verbinding te maken met externe bronnen](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Standaardserverkoppen verwijderen om te voorkomen dat hulpprogramma's uw app fingerprinting](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Verbind uw app veilig met bronnen in een privénetwerk via Point-To-Site VPN](../../app-service/web-sites-integrate-with-vnet.md)

- [Uw app veilig verbinden met bronnen in een privénetwerk met hybride verbindingen](../../app-service/app-service-hybrid-connections.md)

Azure App Service maakt gebruik van dezelfde Antimalware-oplossing die wordt gebruikt door Azure Cloud Services en Virtuele Machines. Voor meer informatie over dit verwijzen naar onze [Antimalware documentatie](antimalware.md).

## <a name="secure-your-network"></a>Uw netwerk beveiligen
Microsoft Azure bevat een robuuste netwerkinfrastructuur ter ondersteuning van uw vereisten voor toepassings- en serviceconnectiviteit. Netwerkconnectiviteit is mogelijk tussen resources in Azure, tussen on-premises en azure gehoste resources en van en naar internet en Azure.

Met de [Azure-netwerkinfrastructuur](../../virtual-machines/windows/infrastructure-example.md) u Azure-resources veilig met elkaar verbinden met [virtuele netwerken (VNets).](../../virtual-network/virtual-networks-overview.md) Een VNet is een weergave van uw eigen netwerk in de cloud. Een VNet is een logische isolatie van het Azure-cloudnetwerk dat is toegewezen aan uw abonnement. U VNets verbinden met uw on-premises netwerken.

![Uw netwerk beveiligen (beveiligen)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Als u toegangsbeheer op basis van netwerkniveau nodig hebt (op basis van IP-adres en de TCP- of UDP-protocollen), u [netwerkbeveiligingsgroepen gebruiken.](../../virtual-network/virtual-network-vnet-plan-design-arm.md) Een Network Security Group (NSG) is een eenvoudige stateful packet filtering firewall en het stelt u in staat om de toegang te beheren op basis van een [5-tuple](https://www.techopedia.com/definition/28190/5-tuple).

Azure networking ondersteunt de mogelijkheid om het routeringsgedrag voor netwerkverkeer op uw Azure Virtual Networks aan te passen. U dit doen door [door door gebruikers gedefinieerde routes](../../virtual-network/virtual-networks-udr-overview.md) in Azure te configureren.

[Gedwongen tunneling](https://www.petri.com/azure-forced-tunneling) is een mechanisme dat u gebruiken om ervoor te zorgen dat uw services geen verbinding met apparaten op het internet mogen starten.

Azure ondersteunt speciale WAN-koppelingsconnectiviteit met uw on-premises netwerk en een Azure Virtual Network met [ExpressRoute.](../../expressroute/expressroute-introduction.md) De koppeling tussen Azure en uw site maakt gebruik van een speciale verbinding die niet via het openbare internet verloopt. Als uw Azure-toepassing in meerdere datacenters wordt uitgevoerd, u [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) gebruiken om aanvragen van gebruikers intelligent over instanties van de toepassing te routeren. U ook verkeer doorsturen naar services die niet in Azure worden uitgevoerd als ze toegankelijk zijn vanaf internet.

## <a name="virtual-machine-security"></a>Beveiliging van virtuele machines

[Met Azure Virtual Machines](../../virtual-machines/index.yml) u een breed scala aan computeroplossingen op een flexibele manier implementeren. Met ondersteuning voor Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP en Azure BizTalk Services u elke workload en elke taal op bijna elk besturingssysteem implementeren.

Met Azure u [antimalwaresoftware](antimalware.md) van beveiligingsleveranciers zoals Microsoft, Symantec, Trend Micro en Kaspersky gebruiken om uw virtuele machines te beschermen tegen schadelijke bestanden, adware en andere bedreigingen.

Microsoft Antimalware voor Azure Cloud Services en Virtuele Machines is een realtime beveiligingsmogelijkheid die helpt bij het identificeren en verwijderen van virussen, spyware en andere schadelijke software. Microsoft Antimalware biedt configureerbare waarschuwingen wanneer bekende schadelijke of ongewenste software zichzelf probeert te installeren of op uw Azure-systemen uitvoert.

[Azure Backup](../../backup/backup-overview.md) is een schaalbare oplossing die uw toepassingsgegevens beschermt met nul kapitaalinvesteringen en minimale bedrijfskosten. Toepassingsfouten kunnen uw gegevens beschadigen en menselijke fouten kunnen bugs in uw toepassingen introduceren. Met Azure Backup zijn uw virtuele machines met Windows en Linux beveiligd.

[Azure Site Recovery](../../site-recovery/site-recovery-overview.md) helpt bij het orkestreren van replicatie, failover en herstel van workloads en apps, zodat ze beschikbaar zijn vanaf een secundaire locatie als uw primaire locatie uitvalt.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Naleving garanderen: checklist voor due diligence voor cloudservices

Microsoft heeft [de Cloud Services Due Diligence Checklist](https://aka.ms/cloudchecklist.download) ontwikkeld om organisaties te helpen due diligence uit te oefenen wanneer ze een overstap naar de cloud overwegen. Het biedt een structuur voor een organisatie van elke omvang en elk type - particuliere bedrijven en organisaties in de publieke sector, inclusief de overheid op alle niveaus en non-profitorganisaties - om hun eigen doelstellingen op het gebied van prestaties, service, gegevensbeheer en governance te identificeren en Eisen. Dit stelt hen in staat om het aanbod van verschillende cloudserviceproviders te vergelijken en vormt uiteindelijk de basis voor een cloudserviceovereenkomst.

De checklist biedt een kader dat clausule per clausule afstemt op een nieuwe internationale standaard voor cloudserviceovereenkomsten, ISO/IEC 19086. Deze standaard biedt een uniforme reeks overwegingen voor organisaties om hen te helpen beslissingen te nemen over cloudadoptie en een gemeenschappelijke basis te creëren voor het vergelijken van cloudserviceaanbiedingen.

De checklist bevordert een grondig doorgelicht verhuizing naar de cloud, met gestructureerde begeleiding en een consistente, herhaalbare aanpak voor het kiezen van een cloudserviceprovider.

Cloudadoptie is niet langer alleen een technologische beslissing. Omdat checklistvereisten elk aspect van een organisatie raken, dienen ze om alle belangrijke interne besluitvormers bijeen te roepen, de CIO en CISO, evenals juridische, risicobeheer-, inkoop- en complianceprofessionals. Dit verhoogt de efficiëntie van het besluitvormingsproces en de grondbeslissingen in een goede redenering, waardoor de kans op onvoorziene wegversperringen tot goedkeuring wordt verminderd.

Daarnaast is de checklist:

- Onthult belangrijke discussieonderwerpen voor besluitvormers aan het begin van het cloudadoptieproces.

- Ondersteunt grondige zakelijke discussies over regelgeving en de eigen doelstellingen van de organisatie op het gebied van privacy, persoonlijk identificeerbare informatie (PII) en gegevensbeveiliging.

- Helpt organisaties bij het identificeren van mogelijke problemen die van invloed kunnen zijn op een cloudproject.

- Biedt een consistente set vragen, met dezelfde termen, definities, statistieken en deliverables voor elke provider, om het proces van het vergelijken van aanbiedingen van verschillende cloudserviceproviders te vereenvoudigen.

## <a name="azure-infrastructure-and-application-security-validation"></a>Azure-infrastructuur en toepassingsbeveiligingsvalidatie

[Azure Operational Security](operational-security.md) verwijst naar de services, besturingselementen en functies die beschikbaar zijn voor gebruikers voor het beschermen van hun gegevens, toepassingen en andere elementen in Microsoft Azure.

![beveiligingsvalidatie (detecteren)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azure Operational Security is gebouwd op een framework dat de opgedane kennis bevat via verschillende mogelijkheden die uniek zijn voor Microsoft, waaronder de Microsoft Security Development Lifecycle (SDL), het Microsoft Security Response Centre-programma , en diep bewustzijn van de cyberveiligheid bedreiging landschap.

### <a name="microsoft-azure-monitor"></a>Microsoft Azure-monitor

[Azure Monitor](../../azure-monitor/index.yml) is de IT-beheeroplossing voor de hybride cloud. Azure Monitor-logboeken worden alleen gebruikt of om uw bestaande System Center-implementatie uit te breiden, bieden u de maximale flexibiliteit en controle voor cloudgebaseerd beheer van uw infrastructuur.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Met Azure Monitor u elk exemplaar in elke cloud beheren, inclusief on-premises, Azure, AWS, Windows Server, Linux, VMware en OpenStack, tegen lagere kosten dan concurrerende oplossingen. Azure Monitor is gebouwd voor de cloud-first-wereld en biedt een nieuwe aanpak voor het beheren van uw onderneming die de snelste, meest kosteneffectieve manier is om nieuwe zakelijke uitdagingen het hoofd te bieden en nieuwe workloads, toepassingen en cloudomgevingen aan te kunnen.

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

[Azure Monitor-logboeken](https://azure.microsoft.com/documentation/services/log-analytics) bieden bewakingsservices door gegevens uit beheerde resources te verzamelen in een centrale opslagplaats. Deze gegevens kunnen gebeurtenissen, prestatiegegevens en aangepaste gegevens omvatten die via de API worden geleverd. Na verzameling zijn de gegevens beschikbaar voor waarschuwingen, analyse en export.

![Azure Monitor-logboeken](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Met deze methode u gegevens uit verschillende bronnen consolideren, zodat u gegevens uit uw Azure-services combineren met uw bestaande on-premises omgeving. De methode maakt ook een duidelijk onderscheid tussen het verzamelen van gegevens en het bewerken hiervan. Zo zijn alle bewerkingen beschikbaar voor alle soorten gegevens.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-intro.md) helpt u bij het detecteren, voorkomen van en reageren op bedreigingen dankzij een verhoogde zichtbaarheid van en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden blijven, en werkt met een uitgebreid ecosysteem van beveiligingsoplossingen.

Security Center analyseert de beveiligingsstatus van uw Azure-resources om mogelijke beveiligingsproblemen op te sporen. Een lijst met aanbevelingen begeleidt u bij het configureren van benodigde besturingselementen.

Voorbeelden zijn:

- Inrichting van antimalware om schadelijke software te identificeren en te verwijderen

- Netwerkbeveiligingsgroepen en -regels configureren om verkeer naar VM's te beheren

- Inrichten van Web Application Firewalls om te beschermen tegen aanvallen die zijn gericht op uw webtoepassingen

- Implementatie van ontbrekende systeemupdates

- Aanpassing van besturingssysteemconfiguraties die niet overeenkomen met de aanbevolen basislijnen

Security Center verzamelt, analyseert en integreert automatisch logboekgegevens van uw Azure-resources, het netwerk en partneroplossingen zoals antimalwareprogramma's en firewalls. Wanneer er dreigingen worden gedetecteerd, wordt een beveiligingswaarschuwing gemaakt. Voorbeelden zijn detectie van:

- Gecompromitteerde VM's die communiceren met bekende kwaadaardige IP-adressen

- Geavanceerde malware die is gedetecteerd met Windows Foutrapportage

- Brute kracht aanvallen tegen VM's

- Beveiligingswaarschuwingen van geïntegreerde antimalwareprogramma's en firewalls

### <a name="azure-monitor"></a>Azure-monitor

[Azure Monitor](../../azure-monitor/overview.md) biedt verwijzingen naar informatie over specifieke typen resources. Het biedt visualisatie, query, routering, waarschuwingen, automatische schaal en automatisering op gegevens, zowel vanuit de Azure-infrastructuur (Activity Log) als elke afzonderlijke Azure-bron (Diagnostische logboeken).

Cloudtoepassingen zijn complex met veel bewegende delen. Monitoring biedt gegevens om ervoor te zorgen dat uw toepassing in een gezonde toestand actief blijft. Het helpt je ook om potentiële problemen af te wenden of problemen op te lossen in het verleden.

![Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png) Monitor Daarnaast u bewakingsgegevens gebruiken om diepgaande inzichten over uw toepassing te verkrijgen. Die kennis kan u helpen om de prestaties of onderhoudbaarheid van toepassingen te verbeteren, of acties te automatiseren waarvoor anders handmatige interventie nodig zou zijn.

Het controleren van uw netwerkbeveiliging is essentieel voor het opsporen van netwerkkwetsbaarheden en het waarborgen van naleving van uw IT-beveiligings- en regelgevingsmodel. Met de weergave Beveiligingsgroep u de geconfigureerde netwerkbeveiligingsgroep en beveiligingsregels en de effectieve beveiligingsregels ophalen. Met de lijst met toegepaste regels u bepalen welke poorten open zijn en ss-netwerkkwetsbaarheid.

### <a name="network-watcher"></a>Netwerkwatcher

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) is een regionale service waarmee u de omstandigheden op netwerkniveau in, naar en vanuit Azure controleren en diagnosticeren. Netwerkdiagnose- en visualisatietools die beschikbaar zijn met Network Watcher helpen u inzicht te krijgen in uw netwerk in Azure, diagnoses en inzichten te krijgen. Deze service omvat packet capture, next hop, IP flow verify, security group view, NSG flow logs. Monitoring op scenarioniveau biedt een end-to-endweergave van netwerkbronnen in tegenstelling tot afzonderlijke netwerkbronbewaking.

### <a name="storage-analytics"></a>Opslaganalyse

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) kan statistieken opslaan die geaggregeerde transactiestatistieken en capaciteitsgegevens over aanvragen voor een opslagservice bevatten. Transacties worden gerapporteerd op zowel api-bewerkingsniveau als op het niveau van de opslagservice en de capaciteit wordt gerapporteerd op het niveau van de opslagservice. Statistiekengegevens kunnen worden gebruikt om het gebruik van opslagservices te analyseren, problemen met aanvragen tegen de opslagservice te diagnosticeren en de prestaties van toepassingen die een service gebruiken te verbeteren.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) is een uitbreidbare Application Performance Management (APM) service voor webontwikkelaars op meerdere platforms. Hiermee kunt u uw livewebtoepassing controleren. Afwijkende prestaties worden automatisch gedetecteerd. Het bevat krachtige analysetools om u te helpen problemen te diagnosticeren en te begrijpen wat gebruikers met uw app doen. Het is bedoeld om u te helpen de prestaties en bruikbaarheid continu te verbeteren. Het werkt voor apps op een breed scala aan platforms, waaronder .NET, Node.js en Java EE, gehost on-premises of in de cloud. Het integreert met uw devOps proces, en heeft verbindingspunten aan een diverse ontwikkelingshulpmiddelen.

Met deze service kunt u het volgende controleren:

- **Aantal aanvragen, reactietijden en foutpercentages** - ga na welke pagina's het populairst zijn op welke tijdstippen van de dag en waar uw gebruikers zich bevinden. Ontdek welke pagina's het beste presteren. Als uw reactietijden en foutpercentages omhoog gaan wanneer er meer aanvragen binnenkomen, hebt u mogelijk te weinig resources.

- **Aantal afhankelijkheidsrelaties, reactietijden en foutpercentages** - controleer of externe services zorgen voor vertraging.

- **Uitzonderingen** - analyseer de cumulatieve statistische gegevens of kies specifieke gegevens en zoom in op de stack-trace en verwante aanvragen. Zowel server- als browseruitzonderingen worden gerapporteerd.

- **Paginaweergaven en de prestaties bij het laden van pagina’s** - deze gegevens worden gerapporteerd door de browsers van uw gebruikers.

- **AJAX roept op vanaf webpagina's** - tarieven, responstijden en uitvalpercentages.

- **Gebruikers- en sessietellingen.**

- **Prestatiemeteritems** van uw Windows- of Linux-servers, zoals die voor CPU-, geheugen- en netwerkgebruik.

- **Diagnostische gegevens van hosts** van Docker of Azure.

- **Diagnostische traceerlogboeken** van uw app - met behulp hiervan kunt u de samenhang vaststellen tussen traceergebeurtenissen en aanvragen.

- **Aangepaste gebeurtenissen en statistieken** die u zelf schrijft in de client- of servercode, om zakelijke gebeurtenissen bij te houden, zoals verkochte artikelen of gewonnen games.

De infrastructuur voor uw toepassing bestaat meestal uit veel onderdelen, zoals een virtuele machine, een opslagaccount en een virtueel netwerk, of een webtoepassing, database, databaseserver en services van derden. Deze onderdelen moet u niet zien als afzonderlijke entiteiten, maar als onderdelen die één entiteit vormen en aan elkaar zijn gerelateerd en afhankelijk zijn van elkaar. U implementeert, beheert en bewaakt deze onderdelen als groep. [Met Azure Resource Manager](../../azure-resource-manager/management/overview.md) u als groep werken met de resources in uw oplossing.

U kunt alle resources voor uw oplossing implementeren, bijwerken of verwijderen in een enkele, gecoördineerde bewerking. Voor implementatie gebruikt u een sjabloon. Deze sjabloon kan voor verschillende omgevingen worden gebruikt, zoals testen, faseren en productie. Resource Manager biedt beveiliging, controle en tagfuncties die u na de implementatie helpen bij het beheren van uw resources.

**De voordelen van Resource Manager**

Resource Manager biedt diverse voordelen:

- U kunt alle resources voor uw oplossing implementeren, beheren en bewaken als groep, in plaats van deze resources afzonderlijk te verwerken.

- U kunt gedurende de ontwikkelingscyclus uw oplossing herhaaldelijk implementeren en erop vertrouwen dat uw resources consistent worden geïmplementeerd.

- U kunt uw infrastructuur beheren via declaratieve sjablonen in plaats van scripts.

- U de afhankelijkheden tussen resources definiëren, zodat ze in de juiste volgorde worden geïmplementeerd.

- U kunt toegangsbeheer toepassen op alle services in de resourcegroep omdat op rollen gebaseerd toegangsbeheer (RBAC) is geïntegreerd in het beheerplatform.

- U kunt tags toepassen op de resources om alle resources in uw abonnement op een logische manier te organiseren.

- U kunt de facturering van uw organisatie transparanter maken door te kijken naar de kosten voor een groep resources met dezelfde tag.

> [!Note]
> Resource Manager biedt een nieuwe manier om uw oplossingen te implementeren en te beheren. Zie Inzicht in [resourcemanager-implementatie en klassieke implementatie](../../azure-resource-manager/management/deployment-models.md)als u het eerdere implementatiemodel hebt gebruikt en meer wilt weten over de wijzigingen.

## <a name="next-steps"></a>Volgende stappen

Lees meer over beveiliging door enkele van onze diepgaande beveiligingsonderwerpen te lezen:

- [Controle en logboekregistratie](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Cybercriminaliteit](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Ontwerp en operationele beveiliging](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Codering](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Identiteits- en toegangsbeheer](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Netwerkbeveiliging](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Beveiligingsbeheer](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
