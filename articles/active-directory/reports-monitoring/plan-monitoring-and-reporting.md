---
title: Rapporten plannen & bewakingsimplementatie - Azure AD
description: Beschrijft hoe te plannen en uit te voeren implmentation van rapportage en monitoring.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232106"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Een Azure Active Directory-rapportage- en bewakingsimplementatie plannen

Uw Azure Active Directory (Azure AD)-rapportage- en bewakingsoplossing is afhankelijk van uw wettelijke, beveiligings- en operationele vereisten en uw bestaande omgeving en processen. In dit artikel worden de verschillende ontwerpopties gepresenteerd en wordt u naar de juiste implementatiestrategie geloodst.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Voordelen van Azure AD-rapportage en -monitoring

Azure AD-rapportage biedt een uitgebreide weergave en logboeken van Azure AD-activiteit in uw omgeving, inclusief aanmeldingsgebeurtenissen, controlegebeurtenissen en wijzigingen in uw directory.

Met de gegevens kunt u het volgende doen:

* bepalen hoe uw apps en services worden gebruikt.

* potentiële risico's die de gezondheid van uw omgeving beïnvloeden.

* problemen op te lossen waardoor uw gebruikers hun werk niet kunnen doen.

* inzicht krijgen door controlegebeurtenissen van wijzigingen in uw Azure AD-map te bekijken.

> [!IMPORTANT]
> Met Azure AD-controle u uw logboeken die zijn gegenereerd door Azure AD-rapportage doorsturen naar verschillende doelsystemen. U kunt deze vervolgens behouden voor later gebruik of integreren met SIEM-hulpprogramma's (Security Information and Event Management) van derden om meer inzicht in uw omgeving te verkrijgen.

Met Azure AD-bewaking u logboeken routeren naar:

* een Azure-opslagaccount voor archiveringsdoeleinden.
* Azure Monitor-logboeken, voorheen bekend als Azure Log Analytics-werkruimte, waar u de gegevens analyseren, dashboards maken en waarschuwen voor specifieke gebeurtenissen.
* een Azure-gebeurtenishub waar u integreren met uw bestaande SIEM-hulpprogramma's zoals Splunk, Sumologic of QRadar.

> [!NOTE]
We zijn onlangs begonnen met het gebruik van de term Azure Monitor logs in plaats van Log Analytics. Loggegevens worden nog steeds opgeslagen in een Log Analytics-werkruimte en worden nog steeds verzameld en geanalyseerd door dezelfde Log Analytics-service. We werken de terminologie bij om de rol van [logboeken in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection)beter weer te geven. Zie [terminologiewijzigingen in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand) voor meer informatie.

[Meer informatie over beleid voor het bewaren van rapporten](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licenties en vereisten voor Azure AD-rapportage en -monitoring

U hebt een Azure AD PREMIUM-licentie nodig om toegang te krijgen tot de azure AD-aanmeldingslogboeken.

Voor gedetailleerde functie- en licentiegegevens in de [prijshandleiding van Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Als u Azure AD-bewaking en -rapportage wilt implementeren, hebt u een gebruiker nodig die een globale beheerder of beveiligingsbeheerder is voor de Azure AD-tenant.

Afhankelijk van de eindbestemming van uw loggegevens hebt u een van de volgende opties nodig:

* Een Azure-opslagaccount waarop u ListKeys-machtigingen hebt. We raden u aan om een algemeen opslagaccount te gebruiken en geen Blob Storage-account. Raadpleeg de [Azure Storage-prijscalculator](https://azure.microsoft.com/pricing/calculator/?service=storage) voor opslagprijzen.

* Een Azure Event Hubs-naamruimte die moet worden geïntegreerd met SIEM-oplossingen van derden.

* Een Azure Log Analytics-werkruimte om logboeken naar Azure Monitor-logboeken te verzenden.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Een Azure-rapportage- en bewakingsimplementatieproject plannen

In dit project definieert u de doelgroepen die rapporten verbruiken en controleren en definieert u uw Azure AD-bewakingsarchitectuur.

### <a name="engage-the-right-stakeholders"></a>Betrek de juiste stakeholders

Wanneer technologieprojecten mislukken, doen ze dit meestal vanwege de onjuiste verwachtingen over impact, resultaten en verantwoordelijkheden. Om deze valkuilen te vermijden, [moet u ervoor zorgen dat u de juiste belanghebbenden indienst gaat.](https://aka.ms/deploymentplans) Zorg er ook voor dat de rol van belanghebbenden in het project goed wordt begrepen door de belanghebbenden en hun projectinput en accountabilities te documenteren.

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is cruciaal voor het succes van elke nieuwe dienst. Communiceer proactief met uw gebruikers hoe hun ervaring zal veranderen, wanneer deze zal veranderen en hoe ze ondersteuning kunnen krijgen als ze problemen ervaren.

### <a name="document-your-current-infrastructure-and-policies"></a>Uw huidige infrastructuur en beleid documenteren

Uw huidige infrastructuur en beleid zullen uw rapportage- en monitoringontwerp stimuleren. Zorg ervoor dat u weet

* Wat, indien aanwezig, SIEM-tools die u gebruikt.

* Uw Azure-infrastructuur, inclusief bestaande opslagaccounts en het gebruik van bewaking.

* Uw beleid voor het bewaren van organisaties voor logboeken, inclusief alle vereiste nalevingskaders. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Een Azure AD-implementatie voor rapportage en bewaking plannen

Rapportage en monitoring worden gebruikt om te voldoen aan uw bedrijfsvereisten, inzicht te krijgen in gebruikspatronen en uw organisaties de beveiligingshouding te vergroten.

### <a name="business-use-cases"></a>Business use cases

* Vereist voor oplossing om aan de zakelijke behoeften te voldoen
* Leuk om te moeten voldoen aan zakelijke behoeften
* Niet van toepassing

|Onderwerp |Beschrijving |
|-|-|
|Bewaartermijn| **Logretentie van meer dan 30 dagen**. Vanwege wettelijke of zakelijke vereisten is het vereist om controlelogboeken op te slaan en logboeken van Azure AD langer dan 30 dagen in te loggen. |
|Analyse| **De logboeken moeten doorzoekbaar zijn.** De opgeslagen logboeken moeten doorzoekbaar zijn met analytische tools. |
| Operational Insights| **Inzichten voor verschillende teams.** De noodzaak om toegang te geven aan verschillende gebruikers om operationele inzichten te verkrijgen, zoals toepassingsgebruik, inlogfouten, selfservicegebruik, trends, enz. |
| Inzichten in beveiliging| **Inzichten voor verschillende teams.** De noodzaak om toegang te geven aan verschillende gebruikers om operationele inzichten te verkrijgen, zoals toepassingsgebruik, inlogfouten, selfservicegebruik, trends, enz. |
| Integratie in SIEM-systemen      | **SIEM-integratie**. De noodzaak om Azure AD-aanmeldingslogboeken en controlelogboeken te integreren en te streamen naar bestaande SIEM-systemen. |

### <a name="choose-a-monitoring-solution-architecture"></a>Kies een architectuur voor bewakingsoplossingen

Met Azure AD-bewaking u uw AZURE AD-activiteitslogboeken doorsturen naar een systeem dat het beste aan uw bedrijfsbehoeften voldoet. U ze vervolgens behouden voor langdurige rapportage en analyse om inzicht te krijgen in uw omgeving en deze te integreren met SIEM-tools.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Beslissingsstroomdiagram![Een afbeelding met de beschrijving van wat er in volgende secties wordt beschreven](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Logboeken in een opslagaccount archiveren

Door logboeken naar een Azure-opslagaccount te routeren, u deze langer bewaren dan de standaardbewaarperiode die in ons [bewaarbeleid](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)wordt beschreven. Gebruik deze methode als u uw logboeken moet archiveren, maar deze niet hoeft te integreren met een SIEM-systeem en geen lopende query's en analyses nodig hebt. U nog steeds on-demand zoekopdrachten uitvoeren.

Ontdek hoe u [gegevens routeert naar uw opslagaccount](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Logboeken verzenden naar logboeken van Azure Monitor

[Azure Monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) consolideren bewakingsgegevens uit verschillende bronnen. Het biedt ook een query taal en analytics engine die u inzicht geeft in de werking van uw toepassingen en het gebruik van middelen. Door Azure AD-activiteitslogboeken naar Azure Monitor-logboeken te verzenden, u snel verzamelde, controleren en waarschuwen voor verzamelde gegevens. Gebruik deze methode wanneer u geen bestaande SIEM-oplossing hebt waaru uw gegevens rechtstreeks naartoe wilt verzenden, maar wel query's en analyses wilt. Zodra uw gegevens in Azure Monitor-logboeken staan, u deze vervolgens naar gebeurtenishub en van daaruit naar een SIEM verzenden als u dat wilt.

Lees meer over [het verzenden van gegevens naar de logboeken van Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

U ook de vooraf gebouwde weergaven voor Azure AD-activiteitslogboeken installeren om veelvoorkomende scenario's met aanmeldings- en controlegebeurtenissen te controleren.

Ontdek hoe u [Log Analytics-weergaven voor activiteitenlogboeken van Azure AD installeert en gebruikt](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Logboeken streamen naar uw Azure-gebeurtenishub

Als u logboeken routert naar een Azure-gebeurtenishub, u worden geïm.m. Zo kunt u gegevens van Azure Active Directory-activiteitenlogboeken combineren met andere gegevens die worden beheerd door uw SIEM en meer inzicht krijgen in uw omgeving. 

Ontdek hoe u [logboeken streamt naar een Event Hub](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Operationele en beveiliging plannen voor Azure AD-rapportage en -bewaking

Belanghebbenden moeten toegang krijgen tot Azure AD-logboeken om operationele inzichten te verkrijgen. Waarschijnlijke gebruikers zijn leden van beveiligingsteams, interne of externe auditors en het team voor identiteits- en toegangsbeheer.

Met Azure AD-rollen u de mogelijkheid om Azure AD-rapporten te configureren en te bekijken op basis van uw rol delegeren. Bepaal wie in uw organisatie toestemming nodig heeft om Azure AD-rapporten te lezen en welke rol voor hen geschikt is. 

In de volgende rollen kunnen Azure AD-rapporten worden gelezen:

* Globale beheerder

* Beveiligingsbeheerder

* Beveiligingslezer

* Rapportlezer

Meer informatie over [azure AD-beheerrollen](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

*Pas altijd het concept van de minste privileges toe om het risico van een accountcompromis te verminderen.* Overweeg [privileged identity management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) te implementeren om uw organisatie verder te beveiligen.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Azure AD-rapportage en -bewaking implementeren

Afhankelijk van de beslissingen die u eerder hebt genomen met behulp van de bovenstaande ontwerprichtlijnen, zal deze sectie u begeleiden naar de documentatie over de verschillende implementatieopties.

### <a name="consume-and-archive-azure-ad-logs"></a>Azure AD-logboeken consumeren en archiveren

[Activiteitenrapporten zoeken in de Azure-portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Azure AD-logboeken archiveren naar een Azure Storage-account](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Monitoring en analytics implementeren

[Logboeken verzenden naar Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[De weergave van logboekanalyses voor Azure Active Directory installeren en gebruiken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Azure AD-activiteitslogboeken analyseren met Azure Monitor-logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Controlelogboekenschema interpreteren in Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Aanmeldingslogboekenschema interpreteren in Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Azure AD-logboeken streamen naar een Azure-gebeurtenishub](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Integrate Azure AD logs with Splunk by using Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk) (Azure AD-logboeken integreren met Splunk met behulp van Azure Monitor)

* [Integrate Azure AD logs with SumoLogic by using Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic) (Azure AD-logboeken integreren met SumoLogic met behulp van Azure Monitor)

 

 

## <a name="next-steps"></a>Volgende stappen

Overweeg privileged [identity management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) te implementeren 

Overweeg het implementeren van [role-based access control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)

 
