---
title: Naslaginformatieover de verwijzingen naar beheerbeheervan Azure Active Directory
description: Deze handleiding voor bewerkingen beschrijft de controles en acties die u moet ondernemen om het beheer van de governance te beveiligen
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535455"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Naslaginformatieover de verwijzingen naar beheerbeheervan Azure Active Directory

In dit gedeelte van de [naslaggids voor Azure AD-bewerkingen](active-directory-ops-guide-intro.md) worden de controles en acties beschreven die u moet uitvoeren om de toegekende niet-bevoorrechte en bevoorrechte identiteiten, audit en controlewijzigingen in de omgeving te beoordelen en te bevestigen.

> [!NOTE]
> Deze aanbevelingen zijn actueel vanaf de publicatiedatum, maar kunnen in de loop van de tijd veranderen. Organisaties moeten hun governancepraktijken voortdurend evalueren naarmate Microsoft-producten en -services in de loop van de tijd evolueren.

## <a name="key-operational-processes"></a>Belangrijkste operationele processen

### <a name="assign-owners-to-key-tasks"></a>Eigenaren toewijzen aan belangrijke taken

Voor het beheren van Azure Active Directory is de continue uitvoering van belangrijke operationele taken en processen vereist, die mogelijk geen deel uitmaken van een implementatieproject. Het is nog steeds belangrijk dat u deze taken instelt om uw omgeving te optimaliseren. De belangrijkste taken en de aanbevolen eigenaren zijn:

| Taak | Eigenaar |
| :- | :- |
| Azure AD-controlelogboeken archiveren in SIEM-systeem | InfoSec Operations Team |
| Ontdek toepassingen die buiten de naleving worden beheerd | IAM Operations Team |
| Controleer regelmatig de toegang tot aanvragen | InfoSec-architectuurteam |
| Controleer regelmatig de toegang tot externe identiteiten | InfoSec-architectuurteam |
| Controleer regelmatig wie bevoorrechte rollen heeft | InfoSec-architectuurteam |
| Beveiligingspoorten definiëren om bevoorrechte rollen te activeren | InfoSec-architectuurteam |
| Regelmatig toestemmingssubsidies herzien | InfoSec-architectuurteam |
| Ontwerpcatalogi en toegangspakketten voor toepassingen en resources op basis van werknemers in de organisatie | App-eigenaren |
| Beveiligingsbeleid definiëren om gebruikers toegang te geven tot pakketten | InfoSec-team + app-eigenaren |
| Als beleidsregels goedkeuringswerkstromen bevatten, controleert u regelmatig werkstroomgoedkeuringen | App-eigenaren |
| Uitzonderingen in beveiligingsbeleid controleren, zoals beleid voor voorwaardelijke toegang, met behulp van toegangsbeoordelingen | InfoSec Operations Team |

Als u uw lijst bekijkt, moet u mogelijk een eigenaar toewijzen voor taken die een eigenaar missen of het eigendom aanpassen voor taken met eigenaren die niet zijn afgestemd op de bovenstaande aanbevelingen.

#### <a name="owner-recommended-reading"></a>Eigenaar aanbevolen lezen

- [Beheerdersrollen toewijzen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governance in Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>Configuratiewijzigingen testen

Er zijn wijzigingen die speciale overwegingen vereisen bij het testen, van eenvoudige technieken zoals het uitrollen van een doelsubset van gebruikers tot het implementeren van een wijziging in een parallelle testtenant. Als u geen teststrategie hebt geïmplementeerd, moet u een testbenadering definiëren op basis van de richtlijnen in de onderstaande tabel:

| Scenario| Aanbeveling |
|-|-|
|Het verificatietype wijzigen van federatief naar PHS/PTA of vice versa| Gebruik [gefaseerde implementatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) om de impact van het wijzigen van het verificatietype te testen.|
|Een nieuw CA-beleid (voorwaardelijke toegang) of beleid voor identiteitsbescherming uitrollen|Maak een nieuw CA-beleid en wijs gebruikers toe.|
|Onboarding een testomgeving van een toepassing|Voeg de toepassing toe aan een productieomgeving, verberg deze voor het deelvenster MyApps en wijs deze toe aan testgebruikers tijdens de QA-fase.|
|Synchronisatieregels wijzigen|Voer de wijzigingen uit in een test Azure AD Connect met dezelfde configuratie die momenteel in productie is, ook wel staging-modus genoemd, en analyseer CSExport-resultaten. Indien tevreden, swap naar productie wanneer klaar.|
|Verandering van branding|Test in een aparte testtenant.|
|Een nieuwe functie uitrollen|Als de functie ondersteuning biedt voor de uitrol naar een doelset gebruikers, identificeert u pilootgebruikers en bouwt u uit. Self-service wachtwoordreset en multi-factor authenticatie kunnen zich bijvoorbeeld richten op specifieke gebruikers of groepen.|
|Een toepassing overknippen van een on-premises identiteitsprovider (IdP), bijvoorbeeld Active Directory, naar Azure AD|Als de toepassing meerdere IdP-configuraties ondersteunt, bijvoorbeeld Salesforce, configureert u beide en test azure AD tijdens een wijzigingsvenster (in het geval de toepassing HRD-pagina introduceert). Als de toepassing geen ondersteuning biedt voor meerdere IdP's, plant u de tests tijdens een wijzigingscontrolevenster en programmadowntime.|
|Dynamische groepsregels bijwerken|Maak een parallelle dynamische groep met de nieuwe regel. Vergelijk met het berekende resultaat, bijvoorbeeld, voer PowerShell uit met dezelfde voorwaarde.<br>Als de test geslaagd is, verwisselt u de plaatsen waar de oude groep is gebruikt (indien mogelijk).|
|Productlicenties migreren|Raadpleeg [De licentie wijzigen voor één gebruiker in een gelicentieerde groep in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses)|
|AD FS-regels wijzigen, zoals Autorisatie, Uitgifte, MFA|Groepsclaim gebruiken om subset van gebruikers te targeten.|
|De ervaring met AD FS-verificatie of vergelijkbare wijzigingen in de hele boerderij wijzigen|Maak een parallelle farm met dezelfde hostnaam, implementeer config-wijzigingen, test van clients met hosts-bestand, NLB-routeringsregels of vergelijkbare routering.<br>Als het doelplatform geen HOSTS-bestanden (bijvoorbeeld mobiele apparaten) ondersteunt, bedien je de wijziging.|

## <a name="access-reviews"></a>Toegangsbeoordelingen

### <a name="access-reviews-to-applications"></a>Toegang tot beoordelingen tot toepassingen

Na verloop van tijd kunnen gebruikers toegang tot bronnen verzamelen terwijl ze zich door verschillende teams en posities verplaatsen. Het is belangrijk dat resource-eigenaren de toegang tot toepassingen regelmatig controleren en bevoegdheden verwijderen die niet langer nodig zijn gedurende de levenscyclus van gebruikers. Met Azure [AD-toegangsbeoordelingen](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) kunnen organisaties groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt beheren. Eigenaren van resources moeten de toegang van gebruikers regelmatig controleren om ervoor te zorgen dat alleen de juiste mensen toegang hebben. Idealiter u overwegen Azure AD-toegangsbeoordelingen voor deze taak te gebruiken.

![Startpagina voor beoordelingen openen](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Elke gebruiker die interactie heeft met toegangsbeoordelingen, moet een betaalde Azure AD Premium P2-licentie hebben.

### <a name="access-reviews-to-external-identities"></a>Toegang tot beoordelingen naar externe identiteiten

Het is van cruciaal belang om de toegang tot externe identiteiten beperkt te houden tot resources die nodig zijn, gedurende de tijd die nodig is. Stel een regelmatig geautomatiseerd toegangscontroleproces op voor alle externe identiteiten en toepassingstoegang met Azure [AD-toegangsbeoordelingen.](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) Als er al een proces on-premises bestaat, u Azure AD-toegangsbeoordelingen gebruiken. Zodra een toepassing is uitgeschakeld of niet meer wordt gebruikt, verwijdert u alle externe identiteiten die toegang hadden tot de toepassing.

> [!NOTE]
> Elke gebruiker die interactie heeft met toegangsbeoordelingen, moet een betaalde Azure AD Premium P2-licentie hebben.

## <a name="privileged-account-management"></a>Geprivilegieerd accountbeheer

### <a name="privileged-account-usage"></a>Gebruik van geprivilegieerde accounts

Hackers richten zich vaak op beheerdersaccounts en andere elementen van bevoorrechte toegang om snel toegang te krijgen tot gevoelige gegevens en systemen.Aangezien gebruikers met bevoorrechte rollen in de loop van de tijd vaak accumuleren, is het belangrijk om beheerderstoegang regelmatig te controleren en te beheren en just-in-time bevoorrechte toegang te bieden tot Azure AD- en Azure-bronnen.

Als er geen proces bestaat in uw organisatie om bevoegde accounts te beheren, of als u momenteel beheerders hebt die hun reguliere gebruikersaccounts gebruiken om services en resources te beheren, moet u onmiddellijk beginnen met het gebruik van afzonderlijke accounts, bijvoorbeeld een voor gewone dagelijkse activiteiten; de andere voor bevoorrechte toegang en geconfigureerd met MFA. Beter nog, als uw organisatie een Azure AD Premium P2-abonnement heeft, moet u Azure [AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) (PIM) onmiddellijk implementeren. In dezelfde token moet u ook deze bevoorrechte accounts controleren en [minder bevoorrechte rollen toewijzen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) indien van toepassing.

Een ander aspect van geprivilegieerd accountbeheer dat moet worden geïmplementeerd, is het definiëren van [toegangsbeoordelingen](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) voor die accounts, handmatig of [geautomatiseerd via PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review).

#### <a name="privileged-account-management-recommended-reading"></a>Aanbevolen accountbeheer voor bevoegde accounts

- [Rollen in Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>Accounts voor noodtoegang

Organisaties moeten [noodaccounts](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) maken om Azure AD te beheren voor gevallen zoals verificatieonderbrekingen zoals:

- Onderdelen van verificatie-infrastructuren uitval (AD FS, On-premises AD, MFA-service)
- Verloop van administratief personeel

Om te voorkomen dat u per ongeluk wordt uitgesloten van uw tenant omdat u zich niet aanmelden of het account van een bestaande individuele gebruiker als beheerder activeren, moet u twee of meer noodaccounts maken en ervoor zorgen dat deze zijn geïmplementeerd en afgestemd op [de aanbevolen procedures van Microsoft](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) en glazen procedures [breken.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)

### <a name="privileged-access-to-azure-ea-portal"></a>Bevoorrechte toegang tot Azure EA-portal

Met de [Azure Enterprise Agreement-portal (Azure Enterprise Agreement)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) u Azure-abonnementen maken tegen een hoofdovereenkomst voor onderneming, wat een krachtige rol binnen de onderneming is. Het is gebruikelijk om de creatie van deze portal op te start's voordat u Azure AD op zijn plaats krijgt, dus het is noodzakelijk om Azure AD-identiteiten te gebruiken om het te vergrendelen, persoonlijke accounts van de portal te verwijderen, ervoor te zorgen dat de juiste delegatie aanwezig is en het risico op uitsluiting te beperken .

Als het autorisatieniveau voor EA-portalmomenteel is ingesteld op 'gemengde modus', moet u [microsoft-accounts](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) verwijderen uit alle bevoorrechte toegangen in de EA-portal en de EA-portal configureren om alleen Azure AD-accounts te gebruiken. Als de gedelegeerde rollen van de EA-portal niet zijn geconfigureerd, moet u ook gedelegeerde rollen voor afdelingen en accounts zoeken en implementeren.

#### <a name="privileged-access-recommended-reading"></a>Aanbevolen lezen voor bevoegde toegang

- [Machtigingen voor beheerrol in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>Rechtenbeheer

[Met Entitlement management (EM)](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) kunnen app-eigenaren resources bundelen en toewijzen aan specifieke persona's in de organisatie (zowel intern als extern). EM maakt selfservice-aanmelding en overdracht mogelijk naar bedrijfseigenaren, terwijl het governancebeleid wordt behouden om toegang te verlenen, toegangsduur in te stellen en goedkeuringswerkstromen toe te staan. 

> [!NOTE]
> Azure AD Entitlement Management vereist Azure AD Premium P2-licenties.

## <a name="summary"></a>Samenvatting

Er zijn acht aspecten aan een veilig identiteitsbestuur. Met deze lijst u bepalen welke acties u moet ondernemen om de toegang tot niet-bevoorrechte en bevoorrechte identiteiten te beoordelen en te bevestigen, te controleren en wijzigingen in de omgeving te beheren.

- Eigenaren toewijzen aan belangrijke taken.
- Implementeer een teststrategie.
- Gebruik Azure AD Access-recensies om groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren.
- Stel een regelmatig, geautomatiseerd toegangscontroleproces op voor alle soorten externe identiteiten en toepassingstoegang.
- Stel een toegangsbeoordelingsproces op om de beheerderstoegang regelmatig te controleren en te beheren en just-in-time bevoorrechte toegang te bieden tot Azure AD- en Azure-bronnen.
- Noodaccounts inrichten om Azure AD te beheren voor onverwachte storingen.
- De toegang tot de Azure EA-portal vergrendelen.
- Implementeren Van het rechtbeheer om geregelde toegang tot een verzameling resources te bieden.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de operationele controles en acties van [Azure AD.](active-directory-ops-guide-ops.md)
