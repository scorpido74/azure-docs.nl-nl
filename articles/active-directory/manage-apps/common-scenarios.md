---
title: Algemene scenario's voor toepassings beheer voor Azure Active Directory | Microsoft Docs
description: Toepassings beheer centraliseren met Azure AD
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
ms.openlocfilehash: 1874a2f2cf96aaa905616bddcc6cb83c60c1d279
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115605"
---
# <a name="centralize-application-management-with-azure-ad"></a>Toepassings beheer centraliseren met Azure AD

Wacht woorden, zowel een IT-Nightmare als een pijn voor werk nemers over de hele wereld. Daarom worden er meer bedrijven Azure Active Directory, de oplossing voor identiteits-en toegangs beheer van micro soft voor de Cloud en al uw andere resources. Van toepassing naar toepassing gaan zonder dat u een wacht woord hoeft in te voeren. Ga van Outlook naar werkdag naar ADP net zo snel als u ze snel en veilig kunt openen. Vervolgens kunt u samen werken met partners en zelfs anderen buiten uw organisatie, zonder dat u deze hoeft aan te roepen. Wat meer is, met Azure AD kunt u het risico beheren door de apps te beveiligen die u gebruikt met zaken als multi-factor Authentication om te controleren wie u bent, met behulp van continue adaptieve machine learning en beveiligings informatie voor het detecteren van verdachte aanmeldingen die u de toegang biedt tot de apps die u nodig hebt, waar u ook bent. Het is niet alleen geweldig voor gebruikers, maar ook voor IT. Met Just-in-time-toegangs beoordelingen en een volledig schaal pakket kunt u met Azure AD uw beleids regels voor naleving blijven houden en beleid afdwingen. En dit te krijgen, kunt u zelfs het inrichten van gebruikers accounts automatiseren, zodat u een koud toegangs beheer hebt. Bekijk enkele van de algemene scenario's die klanten Azure Active Directory gebruiken om de toepassings beheer mogelijkheden van de klant voor te bekijken.

**Algemene scenario's**


> [!div class="checklist"]
> * Eenmalige aanmelding voor al uw toepassingen
> * Inrichting en ongedaan maken van inrichting automatiseren 
> * Uw toepassingen beveiligen
> * De toegang tot uw toepassingen regelen
> * Hybride beveiligde toegang

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scenario 1: SSO voor al uw toepassingen instellen

U hoeft geen wacht woord meer te beheren. U hebt veilig toegang tot alle resources die u nodig hebt met uw bedrijfs referenties. 

|Onderdeel  | Beschrijving | Aanbeveling |
|---------|---------|---------|
|SSO|Op standaarden gebaseerde federatieve SSO met behulp van vertrouwde industrie normen.|Gebruik altijd [SAML-OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) om SSO in te scha kelen wanneer uw toepassing dit ondersteunt.|
|Toegangsvenster|Bied uw gebruikers een eenvoudige hub voor het detecteren en openen van al hun toepassingen. Stel ze in staat om productiever te zijn met selfservice mogelijkheden, zoals het aanvragen van toegang tot apps en groepen, of het beheren van de toegang tot bronnen namens anderen.| Implementeer het [toegangs venster](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) in uw organisatie wanneer u uw apps hebt geïntegreerd met Azure AD voor SSO.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scenario 2: de inrichting en het ongedaan maken van de inrichting automatiseren 


De meeste toepassingen moeten een gebruiker in de toepassing inrichten voordat ze toegang krijgen tot de resources die ze nodig hebben. Het gebruik van CSV-bestanden of complexe scripts kan kostbaar en moeilijk te beheren zijn. Bovendien moeten klanten ervoor zorgen dat accounts worden verwijderd wanneer iemand geen toegang meer heeft. Gebruik de onderstaande hulpprogram ma's voor het automatiseren van inrichting en het ongedaan maken van de inrichting. 


|Onderdeel  |Beschrijving|Aanbeveling |
|---------|---------|---------|
|SCIM-inrichting|[Scim](https://aka.ms/SCIMOverview) is een branche best practice voor het automatiseren van de gebruikers inrichting. Elke toepassing die compatibel is met SCIM kan worden geïntegreerd met Azure AD. Automatisch maken, bijwerken en verwijderen van gebruikers accounts zonder CSV-bestanden, aangepaste scripts of on-premises oplossingen te hoeven houden.|Bekijk de groeiende lijst met [vooraf geïntegreerde](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) apps in de Azure AD-App-galerie|
|Microsoft Graph|Maak gebruik van de adem en diepte van gegevens die Azure AD nodig heeft om uw toepassing te verrijken met de benodigde gegevens.|Gebruik [micro soft Graph](https://developer.microsoft.com/graph/) om gegevens op te halen uit het micro soft-ecosysteem. |


## <a name="scenario-3-secure-your-applications"></a>Scenario 3: uw toepassingen beveiligen
De identiteit is de spil voor beveiliging. Als een identiteit wordt aangetast, is het lastig om het domino-effect te stoppen voordat het te laat is. Gemiddeld meer dan 100 dagen voordat organisaties ontdekken dat er een inbreuk is opgetreden. Gebruik de hulpprogram ma's van Azure AD om de beveiligings postuur van uw toepassingen te verbeteren. 

|Onderdeel  |Beschrijving| Aanbeveling |
|---------|---------| ---------|
|Azure MFA|Azure Multi-Factor Authentication (MFA) is een Microsoft-oplossing voor verificatie in twee stappen. Met door de beheerder goedgekeurde verificatie methoden helpt Azure MFA u bij het beveiligen van de toegang tot uw gegevens en toepassingen terwijl de vraag naar een eenvoudig aanmeldings proces beantwoordt.| [Schakel MFA in](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) voor uw gebruikers.  |
|Voorwaardelijke toegang|Met voorwaardelijke toegang kunt u geautomatiseerde beslissingen over toegangs beheer implementeren voor wie toegang heeft tot uw Cloud-apps, op basis van voor waarden.| Bekijk de [standaard instellingen voor beveiliging](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) en het [algemene beleid](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) dat klanten gebruiken. | 
|Identiteitsbeveiliging|Identiteits bescherming maakt gebruik van de informatie die micro soft heeft verkregen van hun positie in organisaties met Azure AD, de consument ruimte met micro soft-accounts en in games met Xbox om uw gebruikers te beschermen. Micro soft analyseert 6.500.000.000.000 signalen per dag om klanten te identificeren en beveiligen tegen bedreigingen.|Schakel het [standaard beleid voor identiteits beveiliging](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) in dat door onze service wordt meegeleverd. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scenario 4: de toegang tot uw toepassingen regelen
Identiteits beheer helpt organisaties bij het verkrijgen van een evenwicht tussen productiviteit: hoe snel kan een persoon toegang hebben tot de toepassingen die ze nodig hebben, bijvoorbeeld wanneer ze lid worden van mijn organisatie? En hoe moet de toegang tot de tijd worden gewijzigd, bijvoorbeeld als gevolg van wijzigingen in de werk status van die persoon? 

|Onderdeel  |Beschrijving|Aanbeveling |
|---------|---------| ---------|
|ELM|Met het beheer van rechten van Azure AD kunt u gebruikers binnen en buiten uw organisatie efficiëntere toegang tot hun toepassingen beheren.| Niet-beheerders toestaan om toegang tot hun toepassingen met [toegangs pakketten](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)te beheren.|
|Toegangsbeoordelingen|De toegang van gebruikers tot apps kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste personen toegang hebben.| [Controleer de toegang](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) tot uw meest gevoelige toepassingen. |
|Log Analytics|Genereer rapporten over wie toegang heeft tot welke toepassingen en sla ze op in uw SIEM-hulp programma om gegevens tussen gegevens bronnen en de tijd te correleren.| [Log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) in te scha kelen en waarschuwingen in te stellen voor kritieke gebeurtenissen met betrekking tot uw toepassingen. |


## <a name="scenario-5-hybrid-secure-access"></a>Scenario 5: hybride beveiligde toegang
De identiteit van het besturings element kan alleen worden gekoppeld aan alle Cloud-en on-premises toepassingen. Maak gebruik van de hulpprogram ma's van Azure AD en de bijbehorende partners om de toegang tot verouderde verificatie toepassingen te beveiligen.

|Onderdeel  |Beschrijving|Aanbeveling |
|---------|---------|---------|
|Toepassingsproxy|De huidige werknemer wil overal, op elke plek en op elk apparaat productief kunnen zijn. Ze moeten toegang hebben tot SaaS-apps in de Cloud en zakelijke apps on-premises. Azure AD-toepassings proxy maakt deze robuuste toegang mogelijk zonder kost bare en complexe virtuele particuliere netwerken (Vpn's) of gedemilitariseerde zones (Dmz's).|[Externe toegang](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) instellen voor uw on-premises apps. |
|F5, Akamai, Zscaler|Met uw bestaande netwerk-en bezorgings controller kunt u eenvoudig verouderde toepassingen beveiligen die nog essentieel zijn voor uw bedrijfs processen, maar die u niet kunt beveiligen voordat u met Azure AD werkt. Waarschijnlijk hebt u al alles wat u nodig hebt om deze toepassingen te beveiligen.| Akamai, Citrix, F5 of Zscaler gebruiken? Bekijk onze [vooraf ontwikkelde oplossingen](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access). | 

## <a name="related-articles"></a>Verwante artikelen:

- [Toepassingsbeheer](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Toepassingsinrichting](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Hybride beveiligde toegang](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [Identity governance](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Identiteits beveiliging](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
