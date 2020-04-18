---
title: Implementatieplannen - Azure Active Directory | Microsoft Documenten
description: End-to-end richtlijnen voor het implementeren van veel Azure Active Directory-mogelijkheden.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7a596454a48a1d6fcee77634363dd38f34a4d58
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603351"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-implementatieplannen
Op zoek naar end-to-end richtlijnen voor het implementeren van Azure AD-mogelijkheden (Azure Directory) (Azure DIRECTORY)? Azure AD-implementatieplannen leiden u door de bedrijfswaarde, planningsoverwegingen en operationele procedures die nodig zijn om gemeenschappelijke Azure AD-mogelijkheden met succes te implementeren.

Gebruik vanaf een van de planpagina's de afdruk- en PDF-mogelijkheden van uw browser om een up-to-date offlineversie van de documentatie te maken.
## <a name="include-the-right-stakeholders"></a>Neem de juiste stakeholders op

Wanneer u begint met uw implementatieplanning voor een nieuwe mogelijkheid, is het belangrijk om belangrijke belanghebbenden in uw organisatie op te nemen. We raden u aan de persoon of mensen die elk van de volgende rollen vervullen te identificeren en te documenteren en met hen samen te werken om hun betrokkenheid bij het project te bepalen.  

Rollen kunnen de volgende 

|Rol |Beschrijving |
|-|-|
|Eindgebruiker|Een representatieve groep gebruikers waarvoor de mogelijkheid zal worden geïmplementeerd. Vaak een voorbeeld van de wijzigingen in een pilotprogramma.
|IT-ondersteuningsmanager|IT-ondersteuningsorganisatievertegenwoordiger die input kan leveren over de ondersteuning van deze wijziging vanuit het perspectief van de helpdesk.  
|Identiteitsarchitect of Azure Global Administrator|Identity management team vertegenwoordiger die verantwoordelijk is voor het definiëren van hoe deze verandering is afgestemd op de kern identity management infrastructuur in uw organisatie.|
|Bedrijfseigenaar van toepassing |De algehele bedrijfseigenaar van de betreffende toepassing(en), waaronder mogelijk het beheren van toegang.Kan ook input geven over de gebruikerservaring en het nut van deze wijziging vanuit het perspectief van een eindgebruiker.
|Eigenaar beveiliging|Een vertegenwoordiger van het beveiligingsteam die kan ondertekenen dat het plan voldoet aan de beveiligingsvereisten van uw organisatie.|
|Compliance Manager|De persoon binnen uw organisatie die verantwoordelijk is voor de naleving van de eisen van bedrijven, bedrijven of overheden.|

**De mate van betrokkenheid kan bestaan uit:**

- **R**esponsible voor de uitvoering van projectplan en resultaat 

- **Een**pproval van projectplan en resultaat 

- **C**ontributor naar projectplan en resultaat 

- **Ik**nformed van projectplan en resultaat


## <a name="best-practices-for-a-pilot"></a>Aanbevolen procedures voor een pilot
Met een pilot u met een kleine groep testen voordat u een mogelijkheid voor iedereen inschakelt. Zorg ervoor dat als onderdeel van uw testen elke use case binnen uw organisatie grondig wordt getest. Het is het beste om een specifieke groep van pilot-gebruikers te targeten voordat u dit uitrolt naar uw organisatie als geheel.

In uw eerste golf, richten IT, bruikbaarheid, en andere geschikte gebruikers die kunnen testen en feedback geven. Deze feedback moet worden gebruikt om de communicatie en instructies die u naar uw gebruikers stuurt verder te ontwikkelen en om inzicht te geven in de soorten problemen die uw ondersteuningspersoneel kan zien. 

De uitrol naar grotere groepen gebruikers moet worden uitgebreid door de reikwijdte van de beoogde groep(en) te vergroten. Dit kan worden gedaan door [middel van dynamisch groepslidmaatschap](../users-groups-roles/groups-dynamic-membership.md)of door gebruikers handmatig toe te voegen aan de doelgroep(en).


## <a name="deploy-authentication"></a>Verificatie implementeren

| Mogelijkheid | Beschrijving|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| Azure Multi-Factor Authentication (MFA) is een Microsoft-oplossing voor verificatie in twee stappen. Met behulp van door de beheerder goedgekeurde verificatiemethoden helpt Azure MFA de toegang tot uw gegevens en toepassingen te waarborgen en tegelijkertijd te voldoen aan de vraag naar een eenvoudig aanmeldingsproces. |
| [Voorwaardelijke toegang](../conditional-access/plan-conditional-access.md)| Met Voorwaardelijke toegang u geautomatiseerde toegangscontrolebeslissingen implementeren voor wie toegang heeft tot uw cloud-apps, op basis van voorwaarden. |
| [Self-service voor wachtwoord opnieuw instellen](../authentication/howto-sspr-deployment.md)| Selfservice wachtwoord reset helpt uw gebruikers hun wachtwoorden te resetten zonder tussenkomst van de beheerder, waar en wanneer ze nodig hebben. |
| [Zonder wachtwoord](../authentication/howto-authentication-passwordless-deployment.md) | Wachtwoordloze verificatie implementeren met de Microsoft Authenticator-app of FIDO2-beveiligingssleutels in uw organisatie |

## <a name="deploy-application-management"></a>Toepassingsbeheer implementeren

| Mogelijkheid | Beschrijving|
| -| - |
| [Eenmalige aanmelding](../manage-apps/plan-sso-deployment.md)| Met één aanmelding kunnen uw gebruikers de apps en bronnen openen die ze nodig hebben om zaken te doen terwijl ze zich slechts één keer aanmelden. Nadat ze zich hebben aangemeld, kunnen ze van Microsoft Office naar SalesForce naar Box naar interne toepassingen gaan zonder dat ze een tweede keer referenties hoeven in te voeren. |
| [Deelvenster Access](../manage-apps/access-panel-deployment-plan.md)| Bied uw gebruikers een eenvoudige hub om al hun toepassingen te ontdekken en te openen. Laat ze productiever zijn met selfservicemogelijkheden, zoals het aanvragen van toegang tot apps en groepen of het beheren van toegang tot bronnen namens anderen. |


## <a name="deploy-hybrid-scenarios"></a>Hybride scenario's implementeren

| Mogelijkheid | Beschrijving|
| -| -|
| [ADFS voor wachtwoordhash-synchronisatie](../hybrid/plan-migrate-adfs-password-hash-sync.md)| Met Password Hash Synchronization worden hashes van gebruikerswachtwoorden gesynchroniseerd van on-premises Active Directory naar Azure AD, zodat Azure AD gebruikers kan verifiëren zonder interactie met de on-premises Active Directory |
| [ADFS voor Pass Through-verificatie](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| Azure AD Pass-through Authentication helpt uw gebruikers zich aan te melden bij zowel on-premises als cloudtoepassingen met dezelfde wachtwoorden. Deze functie biedt gebruikers een betere ervaring - een wachtwoord minder om te onthouden - en vermindert de kosten van de IT-helpdesk omdat gebruikers minder snel vergeten hoe ze zich moeten aanmelden. Als iemand zich aanmeldt bij Azure AD, worden met deze functie de wachtwoorden rechtstreeks gecontroleerd tegen de on-premises Active Directory. |
| [Azure AD-toepassingsproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) |De huidige werknemer wil overal, op elke plek en op elk apparaat productief kunnen zijn. Ze moeten on-premises toegang krijgen tot SaaS-apps in de cloud en bedrijfsapps. Azure AD Application proxy maakt deze robuuste toegang mogelijk zonder dure en complexe virtual private networks (VPN's) of gedemilitariseerde zones (DMZs). |
| [Naadloze eenmalige aanmelding](../hybrid/how-to-connect-sso-quick-start.md)| Met Naadloze eenmalige aanmelding van Azure Active Directory (Naadloze SSO van Azure AD) worden gebruikers aangemeld als hun bedrijfsapparaten zijn verbonden met net bedrijfsnetwerk. Met deze functie hoeven gebruikers hun wachtwoorden niet in te voeren om zich aan te melden bij Azure AD en hoeven ze meestal hun gebruikersnamen niet in te voeren. Deze functie biedt geautoriseerde gebruikers eenvoudige toegang tot uw cloudgebaseerde toepassingen zonder extra on-premises componenten nodig te hebben. |

## <a name="deploy-user-provisioning"></a>Gebruikersvoorziening implementeren

| Mogelijkheid | Beschrijving|
| -| -|
| [Inrichten van gebruikers](../app-provisioning/plan-auto-user-provisioning.md)| Met Azure AD kunt u het maken, onderhouden en verwijderen van gebruikers-id's in cloud(SaaS)-toepassingen als Dropbox, Salesforce, ServiceNow en andere, automatiseren. |
| [Inrichten van HR-gebruikers in de cloud](../app-provisioning/plan-cloud-hr-provision.md)| Cloud HR-gebruikersvoorziening voor Active Directory creëert een basis voor voortdurende identiteitsbeheer en verbetert de kwaliteit van bedrijfsprocessen die afhankelijk zijn van gezaghebbende identiteitsgegevens. Met deze functie met uw HR-cloudproduct, zoals Workday of Successfactors, u naadloos de identiteitslevenscyclus van werknemers en voorwaardelijke werknemers beheren door regels te configureren die Joiner-Mover-Leaver-processen (zoals Nieuwe huur, beëindiging, overdracht) in kaart brengen voor IT-provisioningacties (zoals Maken, Inschakelen, Uitschakelen) |

## <a name="deploy-governance-and-reporting"></a>Governance en rapportage implementeren

| Mogelijkheid | Beschrijving|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PIM) helpt u bij het beheren van bevoorrechte beheerrollen in Azure AD, Azure-bronnen en andere Microsoft Online Services. PIM biedt oplossingen zoals just-in-time toegang, goedkeuringsworkflows en volledig geïntegreerde toegangsbeoordelingen, zodat u schadelijke activiteiten van bevoorrechte rollen in realtime identificeren, ontdekken en voorkomen. |
| [Rapportage en monitoring](../reports-monitoring/plan-monitoring-and-reporting.md)| Het ontwerp van uw Azure AD-rapportage- en bewakingsoplossing is afhankelijk van uw wettelijke, beveiligings- en operationele vereisten en uw bestaande omgeving en processen. In dit artikel worden de verschillende ontwerpopties gepresenteerd en wordt u naar de juiste implementatiestrategie geloodst. |
