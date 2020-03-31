---
title: Azure AD Cloud Beheerd beheer voor on-premises workloads - Azure
description: In dit onderwerp wordt beschreven dat cloudbeheer voor on-premises workloads wordt beheerd.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109523"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Hoe Azure AD cloudbeheer levert voor on-premises workloads

Azure Active Directory (Azure AD) is een uitgebreide iDaaS-oplossing (Identity As a service) die door miljoenen organisaties wordt gebruikt en die alle aspecten van identiteit, toegangsbeheer en beveiliging omvatten. Azure AD heeft meer dan een miljard gebruikersidentiteiten en helpt gebruikers zich aan te melden en veilig toegang te krijgen tot beide:

* Externe bronnen, zoals Microsoft Office 365, de Azure-portal en duizenden andere SaaS-toepassingen (Software-as-a-Service).
* Interne resources, zoals toepassingen op het bedrijfsnetwerk en intranet van een organisatie, samen met alle cloudtoepassingen die door die organisatie zijn ontwikkeld.

Organisaties kunnen Azure AD gebruiken als ze 'pure cloud' zijn, of als een 'hybride' implementatie als ze on-premises workloads hebben. Een hybride implementatie van Azure AD kan deel uitmaken van een strategie voor een organisatie om haar IT-assets naar de cloud te migreren of bestaande on-premises infrastructuur te blijven integreren naast nieuwe cloudservices.

Historisch gezien hebben 'hybride' organisaties Azure AD gezien als een uitbreiding van hun bestaande on-premises infrastructuur. In deze implementaties zijn het on-premises beheer van identiteitsbeheer, Windows Server Active Directory of andere interne directorysystemen de controlepunten en worden gebruikers en groepen vanuit die systemen gesynchroniseerd met een cloudmap zoals Azure AD. Zodra deze identiteiten zich in de cloud bevinden, kunnen ze beschikbaar worden gesteld voor Office 365, Azure en andere toepassingen.

![Levenscyclus van identiteit](media/cloud-governed-management-for-on-premises//image1.png)

Naarmate organisaties meer van hun IT-infrastructuur samen met hun applicaties naar de cloud verplaatsen, zijn velen op zoek naar de verbeterde beveiliging en vereenvoudigde beheermogelijkheden van identiteitsbeheer als service. De door de cloud geleverde IDaaS-functies in Azure AD versnellen de overgang naar cloudbeheer door de oplossingen en mogelijkheden te bieden waarmee organisaties snel meer van hun identiteitsbeheer kunnen overnemen en verplaatsen van traditioneel on-premises systemen naar Azure AD, terwijl bestaande en nieuwe toepassingen blijven worden ondersteund.

In dit document wordt de strategie van Microsoft voor hybride IDaaS beschreven en wordt beschreven hoe organisaties Azure AD kunnen gebruiken voor hun bestaande toepassingen.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>De Azure AD-benadering van cloudbeheer

Als organisaties overstappen naar de cloud, hebben ze garanties nodig dat ze controle hebben over hun volledige omgeving - meer beveiliging en meer inzicht in activiteiten, ondersteund door automatisering en proactieve inzichten. "**Cloud bestuurd beheer**" beschrijft hoe organisaties hun gebruikers, applicaties, groepen en apparaten vanuit de cloud beheren en beheren.

In deze moderne wereld moeten organisaties effectief kunnen beheren op schaal, vanwege de wildgroei van SaaS-toepassingen en de toenemende rol van samenwerking en externe identiteiten. Het nieuwe risicolandschap van de cloud betekent dat een organisatie responsiever moet zijn - een kwaadwillende actor die een cloudgebruiker compromitteert, kan van invloed zijn op cloud- en on-premises toepassingen.

Met name hybride organisaties moeten taken kunnen delegeren en automatiseren, wat voorheen handmatig is gedaan. Om taken te automatiseren, hebben ze API's en processen nodig die de levenscyclus van de verschillende identiteitsgerelateerde bronnen (gebruikers, groepen, toepassingen, apparaten) orkestreren, zodat ze het dagelijkse beheer van deze resources kunnen delegeren aan meer personen buiten core IT-personeel. Azure AD verhelpt deze vereisten via gebruikersaccountbeheer en native verificatie voor gebruikers zonder on-premises identiteitsinfrastructuur. Het niet bouwen van on-premises infrastructuur kan profiteren van organisaties die nieuwe community's van gebruikers hebben, zoals zakelijke partners, die niet afkomstig zijn uit hun on-premises directory, maar waarvan het toegangsbeheer van cruciaal belang is voor het bereiken van bedrijfsresultaten.

Bovendien is het beheer niet compleet zonder governance --- en governance in deze nieuwe wereld is een geïntegreerd onderdeel van het identiteitssysteem in plaats van een add-on. Identiteitsbeheer biedt organisaties de mogelijkheid om de levenscyclus van identiteit en toegang te beheren tussen werknemers, zakelijke partners en leveranciers en services en toepassingen.

Het integreren van identiteitsbeheer maakt het eenvoudiger om de organisatie in staat te stellen over te stappen op cloudbeheer, stelt IT in staat om te schalen, pakt nieuwe uitdagingen aan met gasten en biedt diepere inzichten en automatisering dan wat klanten hadden met on-premises infrastructuur. Governance in deze nieuwe wereld betekent de mogelijkheid voor een organisatie om transparantie, zichtbaarheid en goede controles op de toegang tot bronnen binnen de organisatie te hebben. Met Azure AD hebben beveiligingsbewerkingen en auditteams inzicht in wie --- heeft en wie toegang moet hebben tot welke resources in de organisatie (op welke apparaten), wat die gebruikers met die toegang doen en of de organisatie de juiste toegang heeft en gebruikt controles om de toegang te verwijderen of te beperken in overeenstemming met het bedrijfs- of regelgevingsbeleid.

Het nieuwe managementmodel komt organisaties ten goede met zowel SaaS- als line-of-business (LOB)-toepassingen, omdat ze gemakkelijker toegang tot die toepassingen kunnen beheren en beveiligen. Door toepassingen te integreren met Azure AD, kunnen organisaties toegang in zowel cloud- als on-premises identiteiten consistent gebruiken en beheren. Het beheer van de levenscyclus van toepassingen wordt geautomatiseerder en Azure AD biedt uitgebreide inzichten in het gebruik van toepassingen die niet gemakkelijk haalbaar waren in on-premises identiteitsbeheer. Via de Azure AD-, Office 365-groepen en teams-selfservicefuncties kunnen organisaties eenvoudig groepen maken voor toegangsbeheer en -samenwerking en gebruikers in de cloud toevoegen of verwijderen om vereisten voor samenwerking en toegangsbeheer mogelijk te maken.

Het selecteren van de juiste Azure AD-mogelijkheden voor cloudbeheer is afhankelijk van de toepassingen die moeten worden gebruikt en hoe deze toepassingen worden geïntegreerd met Azure AD. In de volgende secties worden de te nemen benaderingen beschreven voor AD-geïntegreerde toepassingen en toepassingen die federatieprotocollen gebruiken (bijvoorbeeld SAML, OAuth of OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Cloud beheer voor AD-geïntegreerde applicaties

Azure AD verbetert het beheer voor de on-premises Active Directory-geïntegreerde toepassingen van een organisatie door middel van beveiligde externe toegang en voorwaardelijke toegang tot die toepassingen. Daarnaast biedt Azure AD ook accountlifecyclebeheer en referentiebeheer voor de bestaande AD-accounts van de gebruiker, waaronder:

* **Beveiligde toegang op afstand en voorwaardelijke toegang voor on-premises toepassingen**

Voor veel organisaties is de eerste stap in het beheren van toegang vanuit de cloud voor on-premises ad-geïntegreerde web- en externe desktoptoepassingen het implementeren van de [toepassingsproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) voor die toepassingen om veilige toegang op afstand te bieden.

Na één aanmelding bij Azure AD hebben gebruikers toegang tot zowel cloud- als on-premises toepassingen via een externe URL of een interne toepassingsportal. Application Proxy biedt bijvoorbeeld externe toegang en single sign-on voor Extern bureaublad, SharePoint, evenals apps zoals Tableau en Qlik en LOB-toepassingen (Line Of Business). Bovendien kan het beleid voor voorwaardelijke toegang bestaan uit het weergeven van de [gebruiksvoorwaarden](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) en [ervoor zorgen dat de gebruiker ermee heeft ingestemd](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) voordat hij toegang heeft tot een toepassing.

![App Proxy-architectuur](media/cloud-governed-management-for-on-premises/image2.png)

* **Automatisch levenscyclusbeheer voor Active Directory-accounts**

Identiteitsbeheer helpt organisaties een evenwicht te vinden tussen *productiviteit* --- hoe snel kan een persoon toegang hebben tot de resources die hij nodig heeft, bijvoorbeeld wanneer ze lid worden van de organisatie? --- en *beveiliging* --- hoe hun toegang in de loop van de tijd moet veranderen, bijvoorbeeld wanneer de arbeidsstatus van die persoon verandert? Identity lifecycle management is de basis voor identity governance, en effectief beheer op schaal vereist modernisering van de infrastructuur voor identiteitslevenscyclusbeheer voor toepassingen.

Voor veel organisaties is de levenscyclus van identiteiten voor werknemers gekoppeld aan de vertegenwoordiging van die gebruiker in een HCM-systeem (Human Capital Management). Voor organisaties die Workday als HCM-systeem gebruiken, kan Azure AD ervoor zorgen dat gebruikersaccounts in AD automatisch worden [ingericht en ingericht voor werknemers in Workday.](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) Dit leidt tot een verbeterde productiviteit van de gebruiker door automatisering van geboorterechtaccounts en beheert risico's door ervoor te zorgen dat de toegang tot toepassingen automatisch wordt bijgewerkt wanneer een gebruiker van rol verandert of de organisatie verlaat. Het workday-gestuurde [implementatieplan voor het](https://aka.ms/WorkdayDeploymentPlan) inrichten van gebruikers is een stapsgewijze handleiding waarmee organisaties de implementatie van Workday-implementatie van Workday naar Active Directory User Provisioning-oplossing in een proces in vijf stappen doorlopen.

Azure AD Premium bevat ook Microsoft Identity Manager, dat records kan importeren uit andere on-premises HCM-systemen, waaronder SAP, Oracle eBusiness en Oracle PeopleSoft.

Business-to-business samenwerking vereist steeds meer toegang tot mensen buiten uw organisatie. [Azure AD B2B-samenwerking](https://docs.microsoft.com/azure/active-directory/b2b/) stelt organisaties in staat om hun applicaties en services veilig te delen met gastgebruikers en externe partners, terwijl ze de controle over hun eigen bedrijfsgegevens behouden.

Azure AD kan [automatisch accounts maken in AD voor gastgebruikers](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) als dat nodig is, zodat zakelijke gasten toegang hebben tot on-premises ad-geïntegreerde toepassingen zonder dat ze een ander wachtwoord nodig hebben. Organisaties kunnen [MFA-beleid (multi-factor authentication)](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)instellen voor de s van gastgebruikers, zodat MFA-controles worden uitgevoerd tijdens verificatie van de toepassingsproxy. Ook [alle toegangsbeoordelingen](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) die worden uitgevoerd op Cloud B2B-gebruikers zijn van toepassing op on-premises gebruikers. Als de cloudgebruiker bijvoorbeeld wordt verwijderd via het beleid voor levenscyclusbeheer, wordt de on-premises gebruiker ook verwijderd.

**Referentiebeheer voor Active Directory-accounts** Met de selfservice wachtwoordreset van Azure AD kunnen gebruikers die hun wachtwoorden zijn vergeten opnieuw worden geverifieerd en hun wachtwoorden opnieuw instellen, met de gewijzigde wachtwoorden [die zijn geschreven naar on-premises Active Directory.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) Het wachtwoordresetproces kan ook gebruik maken van het on-premises Active Directory-wachtwoordbeleid: wanneer een gebruiker zijn wachtwoord opnieuw instelt, wordt gecontroleerd of het voldoet aan het on-premises Active Directory-beleid voordat het wordt verbonden aan die directory. Het [selfservice-implementatieplan voor](https://aka.ms/deploymentplans/sspr) wachtwoorden bevat aanbevolen procedures voor het opnieuw instellen van selfservicewachtwoorden voor gebruikers via web- en Windows-geïntegreerde ervaringen.

![Azure AD SSPR-architectuur](media/cloud-governed-management-for-on-premises/image3.png)

Ten slotte kan AD voor organisaties waarmee gebruikers hun wachtwoorden in AD kunnen wijzigen, worden geconfigureerd om hetzelfde wachtwoordbeleid te gebruiken als de organisatie in Azure AD gebruikt via de [azure AD-wachtwoordbeveiligingsfunctie](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), momenteel in openbare preview.

Wanneer een organisatie klaar is om een ad-geïntegreerde toepassing naar de cloud te verplaatsen door het besturingssysteem dat de toepassing host naar Azure te verplaatsen, biedt [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) DOMEINservices met AD-compatibele domeinservices (zoals domeinjoin, groepsbeleid, LDAP en Kerberos/NTLM-verificatie). Azure AD Domain Services integreert met de bestaande Azure AD-tenant van de organisatie, waardoor gebruikers zich kunnen aanmelden met hun bedrijfsreferenties. Bovendien kunnen bestaande groepen en gebruikersaccounts worden gebruikt om de toegang tot bronnen te beveiligen, zodat on-premises resources soepeler worden 'optillen' naar Azure-infrastructuurservices.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Cloud beheerd beheer voor on-premises federatietoepassingen

Voor een organisatie die al een on-premises identiteitsprovider gebruikt, maakt het verplaatsen van toepassingen naar Azure AD een veiligere toegang en een eenvoudigere beheerervaring voor federatiebeheer mogelijk. Azure AD maakt het configureren van gedetailleerde toegangsbesturingselementen per toepassing, waaronder Azure Multi-Factor Authentication, mogelijk met behulp van Voorwaardelijke toegang tot Azure AD. Azure AD ondersteunt meer mogelijkheden, waaronder toepassingsspecifieke tokenondertekeningscertificaten en configureerbare vervaldatums van certificaten. Deze mogelijkheden, tools en richtlijnen stellen organisaties in staat om hun on-premises identiteitsproviders met pensioen te laten gaan. Microsoft's eigen IT heeft bijvoorbeeld 17.987 toepassingen verplaatst van microsoft's interne Active Directory Federation Services (AD FS) naar Azure AD.

![Azure AD-evolutie](media/cloud-governed-management-for-on-premises/image5.png)

Als u wilt beginnen met het migreren van federatieve https://aka.ms/migrateapps toepassingen naar Azure AD als identiteitsprovider, verwijst u naar koppelingen naar:

* De whitepaper [Migreren van uw toepassingen naar Azure Active Directory](https://aka.ms/migrateapps/whitepaper), waarin de voordelen van migratie worden gepresenteerd en wordt beschreven hoe u migratie plannen in vier duidelijk omschreven fasen: detectie, classificatie, migratie en doorlopend beheer. U wordt begeleid door hoe u over het proces nadenken en uw project opsplitsen in gemakkelijk te consumeren stukken. In het hele document zijn links naar belangrijke bronnen die u onderweg zullen helpen.

* In de oplossingshandleiding [Wordt toepassingsverificatie van Active Directory Federation Services naar Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) in meer detail onderzocht in dezelfde vier fasen van het plannen en uitvoeren van een toepassingsmigratieproject. In deze handleiding leert u hoe u deze fasen toepassen op het specifieke doel om een toepassing te verplaatsen van Active Directory Federation Services (AD FS) naar Azure AD.

* Het [Active Directory Federation Services Migration Readiness Script](https://aka.ms/migrateapps/adfstools) kan worden uitgevoerd op bestaande on-premises AD FS-servers (Active Directory Federation Services) om de gereedheid van toepassingen voor migratie naar Azure AD te bepalen.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Doorlopend toegangsbeheer voor cloud- en on-premises toepassingen

Organisaties hebben een proces nodig om de schaalbare toegang te beheren. Gebruikers blijven toegangsrechten verzamelen en eindigen met verder dan wat in eerste instantie voor hen was ingericht. Bovendien moeten bedrijfsorganisaties efficiënt kunnen schalen om het toegangsbeleid en -controles voortdurend te ontwikkelen en af te dwingen.

Doorgaans hebben IT-afgevaardigden toegang tot goedkeuringsbeslissingen voor zakelijke besluitvormers. Bovendien kan IT de gebruikers zelf betrekken. Gebruikers die toegang hebben tot vertrouwelijke klantgegevens in de marketingtoepassing van een bedrijf in Europa, moeten bijvoorbeeld het beleid van het bedrijf kennen. Gastgebruikers kunnen ook niet op de hoogte zijn van de verwerkingsvereisten voor gegevens in een organisatie waarvoor ze zijn uitgenodigd.

Organisaties kunnen het proces van toegangslevenscyclus automatiseren via technologieën zoals [dynamische groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership), in combinatie met gebruikersvoorzieningen voor [SaaS-toepassingen](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)of [toepassingen die zijn geïntegreerd met behulp van de SCIM-standaard (System for Cross-Domain Identity Management).](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) Organisaties kunnen ook bepalen welke [gastgebruikers toegang hebben tot on-premises toepassingen.](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) Deze toegangsrechten kunnen vervolgens regelmatig worden beoordeeld met terugkerende [Azure AD-toegangsbeoordelingen.](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

## <a name="future-directions"></a>Toekomstige aanwijzingen

In hybride omgevingen is de strategie van Microsoft om implementaties mogelijk te maken waarbij de **cloud het controlevlak is voor identiteit**en on-premises mappen en andere identiteitssystemen, zoals Active Directory en andere on-premises toepassingen, het doel zijn voor het inrichten van gebruikers met toegang. Deze strategie zal de rechten, identiteiten en toegang blijven waarborgen in die toepassingen en workloads die op hen vertrouwen. Op dit moment kunnen organisaties de productiviteit van eindgebruikers volledig vanuit de cloud verhogen.

![Azure AD-architectuur](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Volgende stappen

Zie de Azure AD-implementatieplannen op <https://aka.ms/deploymentplans> . Ze bieden end-to-end richtlijnen voor het implementeren van Azure Active Directory-mogelijkheden (Azure AD). In elk plan worden de bedrijfswaarde, planningsoverwegingen, het ontwerp en de operationele procedures uitgelegd die nodig zijn om gemeenschappelijke Azure AD-mogelijkheden met succes uit te rollen. Microsoft werkt de implementatieplannen voortdurend bij met aanbevolen procedures die zijn geleerd van klantimplementaties en andere feedback wanneer we nieuwe mogelijkheden toevoegen aan het beheer vanuit de cloud met Azure AD.
