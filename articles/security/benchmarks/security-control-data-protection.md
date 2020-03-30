---
title: Azure-beveiligingsbeheer - gegevensbeveiliging
description: Gegevensbeveiliging voor beveiligingscontrole
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934523"
---
# <a name="security-control-data-protection"></a>Beveiligingscontrole: gegevensbescherming

De aanbevelingen voor gegevensbescherming richten zich op het aanpakken van problemen in verband met versleuteling, toegangscontrolelijsten, identiteitsgebaseerde toegangscontrole en controlelogboekregistratie voor gegevenstoegang.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie bijhouden

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.1 | 13.1 | Klant |

Gebruik tags om te helpen bij het bijhouden van Azure-bronnen die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.2 | 13.2 | Klant |

Implementeer afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, testen en productie. Resources moeten worden gescheiden door VNet/Subnet, op de juiste manier worden getagd en beveiligd door een NSG- of Azure Firewall. Resources die gevoelige gegevens opslaan of verwerken, moeten voldoende geïsoleerd zijn. Voor virtuele machines die gevoelige gegevens opslaan of verwerken, implementeert u beleid en procedure(en) om deze uit te schakelen wanneer deze niet worden gebruikt.

Extra Azure-abonnementen maken:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Managementgroepen maken:

https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Een virtueel netwerk maken:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken met een Beveiligingsconfig:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure Firewall implementeren:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Waarschuwing of waarschuwing configureren en weigeren met Azure Firewall:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Toezicht houden op en blokkeren van ongeoorloofde overdracht van gevoelige informatie

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.3 | 13.3 | Klant |

Implementeer een geautomatiseerd hulpprogramma op netwerkperimeters die controleert op ongeautoriseerde overdracht van gevoelige informatie en dergelijke overdrachten blokkeert en tegelijkertijd informatiebeveiligingsprofessionals waarschuwt.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige informatie tijdens het transport versleutelen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.4 | 14.4 | Gedeeld |

Versleutel alle gevoelige informatie tijdens het transport. Zorg ervoor dat clients die verbinding maken met uw Azure-bronnen, kunnen onderhandelen over TLS 1.2 of hoger.

Volg de aanbevelingen van azure security center voor versleuteling in rust en versleuteling tijdens het transport, indien van toepassing.

Versleuteling tijdens het transport met Azure begrijpen:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Gebruik een actief detectiehulpmiddel om gevoelige gegevens te identificeren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.5 | 14.5 | Klant |

Wanneer er geen functie beschikbaar is voor uw specifieke service in Azure, gebruikt u een actief detectiehulpprogramma van derden om alle gevoelige informatie te identificeren die is opgeslagen, verwerkt of verzonden door de technologiesystemen van de organisatie, inclusief die op locatie of op een externe serviceprovider en de gevoelige informatievoorraad van de organisatie bijwerken.

Gebruik Azure Information Protection voor het identificeren van gevoelige informatie in Office 365-documenten.

Gebruik Azure SQL Information Protection om te helpen bij het classificeren en labelen van informatie die is opgeslagen in Azure SQL-databases.

Azure SQL Data Discovery implementeren:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Azure-informatiebeveiliging implementeren:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot bronnen te beheren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.6 | 14.6 | Klant |

Gebruik Azure AD RBAC om de toegang tot gegevens en bronnen te beheren en gebruik anders servicespecifieke toegangsbeheermethoden.

Azure RBAC begrijpen:

https://docs.microsoft.com/azure/role-based-access-control/overview

RBAC configureren in Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Gebruik hostgebaseerde preventie van gegevensverlies om toegangscontrole af te dwingen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.7 | 14.7 | Klant |

Implementeer een tool van derden, zoals een geautomatiseerde oplossing voor gegevensverliespreventie op basis van een host, om toegangscontroles tot gegevens af te dwingen, zelfs wanneer gegevens van een systeem worden gekopieerd.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Versleutel gevoelige informatie in rust

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.8 | 14.8 | Klant |

Gebruik versleuteling in rust op alle Azure-bronnen. Microsoft raadt azure aan om uw versleutelingssleutels te beheren, maar er is in sommige gevallen de optie om uw eigen sleutels te beheren. 

Versleuteling in rust in Azure begrijpen:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Hoe u door klanten beheerde versleutelingssleutels configureert:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwing en waarschuwing over wijzigingen in kritieke Azure-resources

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.9 | 14.9 | Klant |

Gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer er wijzigingen plaatsvinden in kritieke Azure-bronnen.

Waarschuwingen maken voor gebeurtenissen in Azure Activity Log:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Volgende stappen

Bekijk het volgende beveiligingsbesturingselement: [Vulnerability Management](security-control-vulnerability-management.md)
