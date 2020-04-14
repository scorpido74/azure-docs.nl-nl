---
title: Algemene scenario's voor toepassingsbeheer voor Azure Active Directory | Microsoft Documenten
description: Toepassingsbeheer centraliseren met Azure AD
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21caec50bf9c20ec60194976b1bfbf16e4815914
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261037"
---
# <a name="centralize-application-management-with-azure-ad"></a>Toepassingsbeheer centraliseren met Azure AD

Wachtwoorden, zowel een IT-nachtmerrie als een pijn voor werknemers over de hele wereld. Dit is de reden waarom steeds meer bedrijven zich wenden tot Azure Active Directory, microsoft's Identity and Access Management-oplossing voor de cloud en al uw andere bronnen. Ga van toepassing naar toepassing zonder dat u voor elk wachtwoord een wachtwoord hoeft in te voeren. Ga van Outlook naar Workday, naar ADP zo snel als u ze openen, snel en veilig. Werk vervolgens samen met partners en zelfs anderen buiten uw organisatie zonder IT te hoeven bellen. Bovendien helpt Azure AD risico's te beheren door de apps die u gebruikt te beveiligen met bijvoorbeeld meervoudige verificatie om te controleren wie u bent, met behulp van continu adaptieve machine learning en beveiligingsinformatie om verdachte aanmeldingen te detecteren, zodat u veilige toegang hebt tot de apps die u nodig hebt, waar u ook bent. Het is niet alleen geweldig voor gebruikers, maar ook voor IT. Met just-in-time toegangsbeoordelingen en een volledige governancesuite helpt Azure AD u om ook in overeenstemming te blijven en beleid af te dwingen. En krijg dit, u zelfs automatiseren provisioning gebruikersaccounts, waardoor access management een fluitje van een cent. bekijk enkele van de veelvoorkomende scenario's waarvoor de klant de toepassingsbeheermogelijkheden van Azure Active Directory gebruikt.

**Algemene scenario's**


> [!div class="checklist"]
> * SSO voor al uw toepassingen
> * Inrichting en deprovisioning automatiseren 
> * Beveilig uw toepassingen
> * Toegang tot uw toepassingen regelen
> * Hybride beveiligde toegang

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scenario 1: SSO instellen voor al uw toepassingen

Geen wachtwoord meer beheren. Krijg veilig toegang tot alle resources die u nodig hebt met uw bedrijfsreferenties. 

|Functie  | Beschrijving | Aanbeveling |
|---------|---------|---------|
|SSO|Op standaarden gebaseerde federated SSO met behulp van vertrouwde industriestandaarden.|Gebruik [SAML / OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) altijd om SSO in te schakelen wanneer uw toepassing dit ondersteunt.|
|Toegangsvenster|Bied uw gebruikers een eenvoudige hub om al hun toepassingen te ontdekken en te openen. Laat ze productiever zijn met selfservicemogelijkheden, zoals het aanvragen van toegang tot apps en groepen of het beheren van toegang tot bronnen namens anderen.| Implementeer het [toegangspaneel](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) in uw organisatie zodra u uw apps hebt geïntegreerd met Azure AD voor SSO.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scenario 2: Provisioning en deprovisioning automatiseren 


Voor de meeste toepassingen moet een gebruiker in de toepassing worden ingericht voordat hij toegang krijgt tot de resources die hij nodig heeft. Het gebruik van CSV-bestanden of complexe scripts kan kostbaar en moeilijk te beheren zijn. Bovendien moeten klanten ervoor zorgen dat accounts worden verwijderd wanneer iemand geen toegang meer zou moeten hebben. Maak gebruik van de onderstaande tools om provisioning en deprovisioning te automatiseren. 


|Functie  |Beschrijving|Aanbeveling |
|---------|---------|---------|
|SCIM-voorziening|[SCIM](https://aka.ms/SICMOverview) is een best practice voor het automatiseren van gebruikersvoorzieningen. Elke SCIM-compatibele toepassing kan worden geïntegreerd met Azure AD. Maak, werk en verwijder automatisch gebruikersaccounts zonder CSV-bestanden, aangepaste scripts of on-prem-oplossingen te hoeven onderhouden.|Bekijk de groeiende lijst met vooraf geïntegreerde apps in de Azure [AD-app-galerie](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)|
|Microsoft Graph|Maak gebruik van de adem en diepte van gegevens die Azure AD heeft om uw toepassing te verrijken met de gegevens die het nodig heeft.|Maak gebruik van de [Microsoft-grafiek](https://developer.microsoft.com/graph/) om gegevens uit het hele Microsoft-ecosysteem te halen. |


## <a name="scenario-3-secure-your-applications"></a>Scenario 3: Uw toepassingen beveiligen
Identiteit is de spil voor veiligheid. Als een identiteit wordt aangetast, is het ongelooflijk moeilijk om het domino-effect te stoppen voordat het te laat is. Gemiddeld meer dan 100 dagen voorbij voordat organisaties ontdekken dat er een compromis. Gebruik de hulpprogramma's van Azure AD om de beveiligingshouding van uw toepassingen te verbeteren. 

|Functie  |Beschrijving| Aanbeveling |
|---------|---------| ---------|
|Azure MFA|Azure Multi-Factor Authentication (MFA) is een Microsoft-oplossing voor verificatie in twee stappen. Met behulp van door de beheerder goedgekeurde verificatiemethoden helpt Azure MFA de toegang tot uw gegevens en toepassingen te waarborgen en tegelijkertijd te voldoen aan de vraag naar een eenvoudig aanmeldingsproces.| [Mfa inschakelen](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) voor uw gebruikers.  |
|Voorwaardelijke toegang|Met Voorwaardelijke toegang u geautomatiseerde toegangscontrolebeslissingen implementeren voor wie toegang heeft tot uw cloud-apps, op basis van voorwaarden.| Bekijk de [beveiligingsstandaards](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) en [algemene beleidsregels die](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) klanten gebruiken. | 
|Identiteitsbeveiliging|Identiteitsbescherming maakt gebruik van de learnings die Microsoft heeft verworven vanuit hun positie in organisaties met Azure AD, de consumentenruimte met Microsoft-accounts en in gaming met Xbox om uw gebruikers te beschermen. Microsoft analyseert 6,5 biljoen signalen per dag om klanten te identificeren en te beschermen tegen bedreigingen.|Schakel het [standaardbeleid voor identiteitsbescherming](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) in dat door onze service wordt geboden. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scenario 4: De toegang tot uw toepassingen regelen
Identity Governance helpt organisaties een balans te vinden tussen productiviteit - Hoe snel kan een persoon toegang hebben tot de applicaties die hij nodig heeft, bijvoorbeeld wanneer ze lid worden van mijn organisatie? En veiligheid - Hoe moet hun toegang veranderen in de tijd, zoals als gevolg van veranderingen in de arbeidsstatus van die persoon? 

|Functie  |Beschrijving|Aanbeveling |
|---------|---------| ---------|
|Elm|Azure AD-rechtenbeheer kan gebruikers zowel binnen als buiten uw organisatie helpen de toegang tot hun toepassingen efficiënter te beheren.| Sta niet-beheerders toe om toegang tot hun toepassingen te beheren met [toegangspakketten.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)|
|Toegangsbeoordelingen|De toegang van de gebruiker tot apps kan regelmatig worden beoordeeld om ervoor te zorgen dat alleen de juiste mensen toegang hebben.| [Bekijk de toegang tot](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) uw meest gevoelige toepassingen. |
|Log Analytics|Genereer rapporten over wie toegang heeft tot welke toepassingen en sla ze op in uw SIEM-tool naar keuze om gegevens tussen gegevensbronnen en in de loop van de tijd te correleren.| Schakel [logboekanalyses](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) in en stel waarschuwingen in voor kritieke gebeurtenissen met betrekking tot uw toepassingen. |


## <a name="scenario-5-hybrid-secure-access"></a>Scenario 5: Hybride beveiligde toegang
Identiteit kan alleen uw controlevlak zijn als het alles kan verbinden via cloud- en on-premises toepassingen. Maak gebruik van de hulpprogramma's van Azure AD en haar partners om toegang te krijgen tot op oudere auth gebaseerde toepassingen.

|Functie  |Beschrijving|Aanbeveling |
|---------|---------|---------|
|Toepassingsproxy|De huidige werknemer wil overal, op elke plek en op elk apparaat productief kunnen zijn. Ze moeten on-premises toegang krijgen tot SaaS-apps in de cloud en bedrijfsapps. Azure AD Application proxy maakt deze robuuste toegang mogelijk zonder dure en complexe virtual private networks (VPN's) of gedemilitariseerde zones (DMZs).|Stel [externe toegang in](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) voor uw on-prem-apps. |
|F5, Akamai, Zscaler|Met behulp van uw bestaande netwerk- en leveringscontroller u oudere toepassingen die nog steeds van cruciaal belang zijn voor uw bedrijfsprocessen eenvoudig beschermen, maar die u eerder niet beschermen met Azure AD. Het is waarschijnlijk dat je al alles hebt wat je nodig hebt om te beginnen met het beschermen van deze toepassingen.| Met behulp van Akamai, Citrix, F5, of Zscaler? Bekijk onze [vooraf gebouwde oplossingen.](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access) | 

## <a name="related-articles"></a>Verwante artikelen:

- [Toepassingsbeheer](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Toepassingsinrichting](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Hybride beveiligde toegang](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [Identiteitsbestuur](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Identiteitsbeveiliging](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
