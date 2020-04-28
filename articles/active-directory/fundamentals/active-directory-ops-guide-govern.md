---
title: Naslag Gids voor Azure Active Directory governance-bewerkingen
description: In deze Naslag Gids voor bewerkingen worden de controles en acties beschreven die u moet uitvoeren om beheer te beveiligen
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74535455"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Naslag Gids voor Azure Active Directory governance-bewerkingen

In deze sectie van de [Naslag Gids voor Azure AD-bewerkingen](active-directory-ops-guide-intro.md) worden de controles en acties beschreven die u moet uitvoeren om de toegang tot niet-privilegede en geprivilegieerde identiteiten, controles en controle wijzigingen in de omgeving te beoordelen en te controleren.

> [!NOTE]
> Deze aanbevelingen zijn actueel vanaf de datum van publicatie, maar kunnen worden gewijzigd in de loop van de tijd. Organisaties moeten voortdurend hun beheer procedures evalueren naarmate micro soft-producten en-services zich in de loop van de tijd ontwikkelen.

## <a name="key-operational-processes"></a>Belangrijkste operationele processen

### <a name="assign-owners-to-key-tasks"></a>Eigen aren toewijzen aan belang rijke taken

Voor het beheren van Azure Active Directory is de continue uitvoering van belang rijke operationele taken en-processen vereist, die mogelijk geen deel uitmaken van een implementatie project. Het is nog belang rijk dat u deze taken instelt om uw omgeving te optimaliseren. De belangrijkste taken en de aanbevolen eigen aren zijn onder andere:

| Taak | Eigenaar |
| :- | :- |
| Azure AD-audit logboeken archiveren in het SIEM-systeem | InfoSec Operations-team |
| Toepassingen detecteren die niet meer compatibel zijn | IAM Operations-team |
| De toegang tot toepassingen regel matig controleren | Team van InfoSec-architectuur |
| De toegang tot externe identiteiten regel matig controleren | Team van InfoSec-architectuur |
| Regel matig controleren wie geprivilegieerde rollen heeft | Team van InfoSec-architectuur |
| Beveiligings poorten definiëren om geprivilegieerde rollen te activeren | Team van InfoSec-architectuur |
| Regel matige subsidies voor toestemming controleren | Team van InfoSec-architectuur |
| Ontwerpen van catalogi en toegangs pakketten voor toepassingen en resources op basis van werk nemers in de organisatie | App-eigen aren |
| Beveiligings beleid definiëren om gebruikers toe te wijzen voor toegangs pakketten | InfoSec team + app-eigen aren |
| Als beleids regels goedkeurings werk stromen bevatten, lees dan regel matig workflowgoedkeuring | App-eigen aren |
| Uitzonde ringen in beveiligings beleid bekijken, zoals beleids regels voor voorwaardelijke toegang, met behulp van toegangs beoordelingen | InfoSec Operations-team |

Wanneer u uw lijst bekijkt, moet u mogelijk een eigenaar toewijzen voor taken waarvoor een eigenaar ontbreekt of het eigendom van taken met eigen aars aanpassen die niet zijn afgestemd op de bovenstaande aanbevelingen.

#### <a name="owner-recommended-reading"></a>Door eigenaar aanbevolen lezen

- [Beheerdersrollen toewijzen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governance in Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>Testen van configuratie wijzigingen

Er zijn wijzigingen die speciale overwegingen vereisen tijdens het testen, van eenvoudige technieken, zoals het uitrollen van een doel subset van gebruikers, voor het implementeren van een wijziging in een parallelle test Tenant. Als u geen test strategie hebt geïmplementeerd, moet u een test benadering definiëren op basis van de richt lijnen in de volgende tabel:

| Scenario| Aanbeveling |
|-|-|
|Wijzig het verificatie type van federatieve naar PHS/PTA of vice versa| Gebruik [gefaseerde implementatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) om de impact van het wijzigen van het verificatie type te testen.|
|Een nieuw beleid voor voorwaardelijke toegang (CA) of beleid voor identiteits beveiliging implementeren|Maak een nieuw CA-beleid en wijs dit toe aan gebruikers testen.|
|Een test omgeving van een toepassing onboarden|Voeg de toepassing toe aan een productie omgeving, verberg deze in het deel venster MyApps en wijs deze toe aan test gebruikers tijdens de Quality Assurance (QA)-fase.|
|Wijzigen van synchronisatie regels|Voer de wijzigingen in een test Azure AD Connect uit met dezelfde configuratie die momenteel in productie is, ook wel de faserings modus genoemd, en analyseer CSExport-resultaten. Als u tevreden bent, kunt u overgaan naar productie wanneer u klaar bent.|
|Huis stijl wijzigen|Test in een afzonderlijke test Tenant.|
|Een nieuwe functie implementeren|Als de functie ondersteuning biedt voor het implementeren van een doel groep gebruikers, identificeert u de test gebruikers en bouwt u deze op. De selfservice voor wachtwoord herstel en multi-factor Authentication kunnen bijvoorbeeld worden gericht op specifieke gebruikers of groepen.|
|Cutover een toepassing van een on-premises ID-provider (IdP), bijvoorbeeld Active Directory, naar Azure AD|Als de toepassing meerdere IdP-configuraties ondersteunt, bijvoorbeeld Sales Force, configureren en Azure AD testen tijdens een wijzigings venster (in het geval dat de toepassing de HRD-pagina introduceert). Als de toepassing niet meerdere id ondersteunt, plant u de test tijdens een wijzigings controle venster en wordt de uitval tijd van het programma gepland.|
|Dynamische groeps regels bijwerken|Maak een parallelle dynamische groep met de nieuwe regel. Vergelijkt met het berekende resultaat, bijvoorbeeld Power shell uitvoeren met dezelfde voor waarde.<br>Als de test is geslaagd, kunt u de plaatsen waar de oude groep is gebruikt, wisselen (indien mogelijk).|
|Product licenties migreren|Raadpleeg [de licentie voor één gebruiker in een gelicentieerde groep in azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses).|
|AD FS regels wijzigen zoals autorisatie, uitgifte, MFA|Groeps claim gebruiken om de subset van gebruikers te bereiken.|
|AD FS-verificatie-ervaring of soort gelijke wijzigingen in de hele farm wijzigen|Maak een parallelle farm met dezelfde hostnaam, implementeer configuratie wijzigingen, test van clients met een HOSTS-bestand, NLB-routerings regels of een vergelijk bare route ring.<br>Als het doel platform geen HOSTS-bestanden (bijvoorbeeld mobiele apparaten) ondersteunt, wijzigt u de wijziging van het besturings element.|

## <a name="access-reviews"></a>Toegangsbeoordelingen

### <a name="access-reviews-to-applications"></a>Toegangs Beoordelingen voor toepassingen

Na verloop van tijd kunnen gebruikers de toegang tot resources verzamelen wanneer ze over verschillende teams en posities heen gaan. Het is belang rijk dat resource-eigen aren de toegang tot toepassingen regel matig bekijken en bevoegdheden verwijderen die niet langer nodig zijn voor de levens cyclus van gebruikers. Met Azure AD- [toegangs beoordelingen](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) kunnen organisaties efficiënt groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen beheren. Resource-eigen aren moeten regel matig de toegang van gebruikers controleren om ervoor te zorgen dat alleen de juiste personen toegang hebben. In het ideale geval kunt u het gebruik van Azure AD-toegangs Beoordelingen voor deze taak overwegen.

![Start pagina toegangs beoordelingen](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Elke gebruiker die communiceert met toegangs beoordelingen moet beschikken over een betaalde Azure AD Premium P2-licentie.

### <a name="access-reviews-to-external-identities"></a>Toegangs beoordelingen naar externe identiteiten

Het is van cruciaal belang om de toegang tot de externe identiteiten te beperken die alleen gelden voor bronnen die nodig zijn tijdens de tijd die nodig is. Stel een regel matig geautomatiseerd toegangs beoordelings proces in voor alle externe identiteiten en toegang tot toepassingen met behulp van Azure AD- [toegangs beoordelingen](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview). Als er al een proces on-premises bestaat, kunt u de Azure AD-toegangs beoordelingen gebruiken. Wanneer een toepassing buiten gebruik is gesteld of niet meer wordt gebruikt, verwijdert u alle externe identiteiten die toegang tot de toepassing hebben.

> [!NOTE]
> Elke gebruiker die communiceert met toegangs beoordelingen moet beschikken over een betaalde Azure AD Premium P2-licentie.

## <a name="privileged-account-management"></a>Privileged account management

### <a name="privileged-account-usage"></a>Gebruik van een privileged account

Hackers richten vaak beheerders accounts en andere elementen van geprivilegieerde toegang om snel toegang te krijgen tot gevoelige gegevens en systemen.Omdat gebruikers met geprivilegieerde rollen meestal in de loop van de tijd verzamelen, is het belang rijk om regel matig beheerders toegang te controleren en te beheren en om toegang te bieden tot Azure AD en Azure-resources.

Als er in uw organisatie geen proces is voor het beheren van geprivilegieerde accounts of als u momenteel beheerders hebt die hun reguliere gebruikers accounts gebruiken om services en resources te beheren, moet u onmiddellijk beginnen met het gebruik van afzonderlijke accounts, bijvoorbeeld één voor reguliere dagelijkse activiteiten; de andere voor bevoegde toegang en geconfigureerd met MFA. Als uw organisatie echter een Azure AD Premium P2-abonnement heeft, moet u [Azure AD privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) (PIM) direct implementeren. In hetzelfde token moet u ook deze geprivilegieerde accounts bekijken en [minder geprivilegieerde rollen toewijzen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) , indien van toepassing.

Een ander aspect van privileged account management dat moet worden geïmplementeerd, is het definiëren van [toegangs beoordelingen](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) voor deze accounts, hetzij hand matig of [automatisch via PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review).

#### <a name="privileged-account-management-recommended-reading"></a>Aanbevolen account beheer voor lezen

- [Rollen in Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>Accounts voor toegang in nood gevallen

Organisaties moeten [nood accounts](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) maken die moeten worden voor bereid om Azure ad te beheren, zoals verificatie-uitval zoals:

- Uitval onderdelen van verificatie-infra structuren (AD FS, on-premises AD, MFA-service)
- Omzet van administratieve mede werkers

Als u wilt voor komen dat uw Tenant per ongeluk wordt vergrendeld omdat u zich niet kunt aanmelden of een bestaand account van een individuele gebruiker als beheerder wilt activeren, moet u twee of meer nood accounts maken en ervoor zorgen dat ze worden geïmplementeerd en afgestemd met [de best practices](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) en [afbreek glazen procedures](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)van micro soft.

### <a name="privileged-access-to-azure-ea-portal"></a>Privileged Access to Azure EA-Portal

Met de [azure Enterprise Agreement-Portal (Azure EA)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) kunt u Azure-abonnementen maken op basis van een hoofd Enterprise Agreement. Dit is een krachtige rol binnen de onderneming. Het is gebruikelijk om het maken van deze portal te Boots trappen voordat u Azure AD hebt geïmplementeerd. het is dus nood zakelijk om Azure AD-identiteiten te gebruiken om deze te vergren delen, persoonlijke accounts uit de portal te verwijderen, ervoor te zorgen dat de juiste delegering is ingesteld en het risico van vergren deling te verminderen.

Als u dit wilt doen, moet u, als het verificatie niveau van de EA-Portal op dit moment is ingesteld op ' gemengde modus ', een [micro soft-account](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) verwijderen van alle bevoegde toegang in de EA-Portal en de EA-Portal configureren voor het gebruik van Azure AD-accounts. Als de EA-Portal gedelegeerde rollen niet zijn geconfigureerd, moet u ook gedelegeerde rollen voor afdelingen en accounts zoeken en implementeren.

#### <a name="privileged-access-recommended-reading"></a>Bevoegde toegang aanbevolen lezen

- [Machtigingen voor beheerrol in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>Rechtenbeheer

Met het [rechten beheer (em)](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) kunnen app-eigen resources bronnen bundelen en toewijzen aan specifieke personen in de organisatie (zowel intern als extern). EM maakt het mogelijk om zich aan te melden en te delegeren aan zakelijke eigen aren, terwijl het beheer beleid wordt gehandhaafd om toegang te verlenen, toegangs duur in te stellen en goedkeurings werk stromen toe te staan. 

> [!NOTE]
> Voor het beheer van rechten van Azure AD zijn Azure AD Premium P2-licenties vereist.

## <a name="summary"></a>Samenvatting

Er zijn acht aspecten van een beveiligd identiteits bestuur. Deze lijst helpt u bij het identificeren van de acties die u moet uitvoeren om de toegang tot niet-privilegede en geprivilegieerde identiteiten, controles en controle wijzigingen in de omgeving te beoordelen en te controleren.

- Eigen aren toewijzen aan belang rijke taken.
- Implementeer een test strategie.
- Gebruik Azure AD-toegangs beoordelingen om groepslid maatschappen, de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren.
- Stel een regel matig, geautomatiseerd toegangs controle proces in voor alle typen externe identiteiten en toegang tot de toepassing.
- Stel een toegangs beoordelings proces in om regel matig beheerders toegang te controleren en te beheren en de toegang tot Azure AD en Azure-resources just-in-time te bieden.
- Stel nood accounts in die moeten worden voor bereid om Azure AD te beheren voor onverwachte storingen.
- Vergrendel de toegang tot de Azure EA-Portal.
- Implementeer het rechten beheer om beheerde toegang te bieden tot een verzameling resources.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de [operationele controles en acties van Azure AD](active-directory-ops-guide-ops.md).
