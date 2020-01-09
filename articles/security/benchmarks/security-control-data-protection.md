---
title: Azure-beveiligings beheer-gegevens beveiliging
description: Beveiliging van beveiligings beheer gegevens
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 914d1af9793107031af9797cb965420443e80c4a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564333"
---
# <a name="security-control-data-protection"></a>Beveiligings beheer: gegevens beveiliging

Aanbevelingen voor gegevens beveiliging zijn gericht op het adresseren van problemen met betrekking tot versleuteling, Toegangs beheer lijsten, op id's gebaseerd toegangs beheer en controle logboek registratie voor gegevens toegang.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 4.1 | 13,1 | Klant |

Gebruik Tags om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 4.2 | 13.2 | Klant |

Implementeer afzonderlijke abonnementen en/of beheer groepen voor ontwikkeling, testen en productie. Resources moeten worden gescheiden door VNet/subnet, op de juiste wijze worden gelabeld en beveiligd door een NSG of Azure Firewall. Resources die gevoelige gegevens opslaan of verwerken, moeten voldoende geïsoleerd zijn. Voor Virtual Machines het opslaan of verwerken van gevoelige gegevens, implementeert u beleid en procedure (s) om ze uit te scha kelen wanneer ze niet worden gebruikt.

Aanvullende Azure-abonnementen maken:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken:

https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Een Virtual Network maken:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken met een beveiligings configuratie:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure Firewall implementeren:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Waarschuwing of waarschuwing configureren en weigeren met Azure Firewall:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 4.3 | 13,3 | Klant |

Implementeer een geautomatiseerd hulp programma op netwerk omtrek dat controleert op niet-geautoriseerde overdracht van gevoelige informatie en blokkeert deze overdrachten tijdens het melden van informatie over beveiligings specialisten.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 4.4 | 14,4 | Gedeeld |

Versleutel alle gevoelige gegevens tijdens de overdracht. Zorg ervoor dat alle clients die verbinding maken met uw Azure-resources, TLS 1,2 of hoger kunnen onderhandelen.

Volg Azure Security Center aanbevelingen voor het versleutelen van de rest en de versleuteling in de door Voer, indien van toepassing.

Informatie over versleuteling in transit met Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 4.5 | 14,5 | Klant |

Als er geen functie beschikbaar is voor uw specifieke service in azure, gebruikt u een actief detectie hulpprogramma van derden om alle gevoelige informatie te identificeren die is opgeslagen, verwerkt of verzonden door de technologie systemen van de organisatie, met inbegrip van de locaties op locatie of op een externe service provider en werk de voor Raad met gevoelige informatie van de organisatie bij.

Gebruik Azure Information Protection voor het identificeren van gevoelige gegevens in Office 365-documenten.

Gebruik Azure SQL Information Protection om u te helpen bij het classificeren en labelen van informatie die is opgeslagen in Azure SQL-data bases.

Azure SQL-gegevens detectie implementeren:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Azure Information Protection implementeren:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 4.6 | 14,6 | Klant |

Gebruik Azure AD RBAC om de toegang tot gegevens en resources te beheren. anders kunt u gebruikmaken van servicespecifieke methoden voor toegangs beheer.

Meer informatie over Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

RBAC configureren in Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 4.7 | 14,7 | Klant |

Implementeer een hulp programma van derden, zoals een geautomatiseerde oplossing voor preventie van gegevens verlies op basis van een host, voor het afdwingen van toegangs beheer voor gegevens, zelfs wanneer gegevens worden gekopieerd uit een systeem.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 4.8 | 14,8 | Klant |

Gebruik versleuteling in rust voor alle Azure-resources. Micro soft raadt u aan Azure te laten beheren van uw versleutelings sleutels, maar in sommige gevallen kunt u ook uw eigen sleutels beheren. 

Meer informatie over versleuteling in de rest van Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Door de klant beheerde versleutelings sleutels configureren:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 4,9 | 14,9 | Klant |

Gebruik Azure Monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer wijzigingen worden aangebracht in essentiële Azure-resources.

Waarschuwingen voor Azure-activiteiten logboek gebeurtenissen maken:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Volgende stappen

Zie het volgende beveiligings beheer: [beveiligings beheer](security-control-vulnerability-management.md)

