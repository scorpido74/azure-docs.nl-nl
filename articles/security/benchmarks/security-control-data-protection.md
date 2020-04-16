---
title: Azure-beveiligingsbeheer - gegevensbeveiliging
description: Gegevensbeveiliging van Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 035894c80e619851264aae91daa2d7852d156964
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408548"
---
# <a name="security-control-data-protection"></a>Beveiligingscontrole: gegevensbescherming

De aanbevelingen voor gegevensbescherming richten zich op het aanpakken van problemen in verband met versleuteling, toegangscontrolelijsten, identiteitsgebaseerde toegangscontrole en controlelogboekregistratie voor gegevenstoegang.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie bijhouden

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.1 | 13.1 | Klant |

Gebruik tags om te helpen bij het bijhouden van Azure-bronnen die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.2 | 13.2, 2.10 | Klant |

Isolatie implementeren met afzonderlijke abonnementen en beheergroepen voor afzonderlijke beveiligingsdomeinen, zoals omgevingstype en gegevensgevoeligheidsniveau. U het toegangsniveau beperken tot uw Azure-resources die uw toepassingen en bedrijfsomgevingen vereisen. U de toegang tot Azure-bronnen beheren via azure Active Directory-toegangsbeheer op basis van azure directory-rollen. 

- [Extra Azure-abonnementen maken](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Managementgroepen maken](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Toezicht houden op en blokkeren van ongeoorloofde overdracht van gevoelige informatie

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.3 | 13.3 | Gedeeld |

Maak gebruik van een oplossing van derden van Azure Marketplace op netwerkperimeters die controleert op ongeautoriseerde overdracht van gevoelige informatie en dergelijke overdrachten blokkeert, terwijl informatiebeveiligingsprofessionals worden gewaarschuwd.

Voor het onderliggende platform dat door Microsoft wordt beheerd, beschouwt Microsoft alle klantinhoud als gevoelig en waakt het tegen verlies en blootstelling van klantgegevens. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

- [Inzicht in de bescherming van klantgegevens in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige informatie tijdens het transport versleutelen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.4 | 14.4 | Gedeeld |

Versleutel alle gevoelige informatie tijdens het transport. Zorg ervoor dat clients die verbinding maken met uw Azure-bronnen, kunnen onderhandelen over TLS 1.2 of hoger.

Volg de aanbevelingen van azure security center voor versleuteling in rust en versleuteling tijdens het transport, indien van toepassing.

- [Versleuteling tijdens het transport met Azure begrijpen](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Gebruik een actief detectiehulpmiddel om gevoelige gegevens te identificeren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.5 | 14.5 | Gedeeld |

Wanneer er geen functie beschikbaar is voor uw specifieke service in Azure, gebruikt u een actief detectiehulpprogramma van derden om alle gevoelige informatie te identificeren die is opgeslagen, verwerkt of verzonden door de technologiesystemen van de organisatie, inclusief die op locatie, of bij een externe serviceprovider, en werkt u de gevoelige informatievoorraad van de organisatie bij.

Gebruik Azure Information Protection voor het identificeren van gevoelige informatie in Office 365-documenten.

Gebruik Azure SQL Information Protection om te helpen bij het classificeren en labelen van informatie die is opgeslagen in Azure SQL-databases.

- [Azure SQL Data Discovery implementeren](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [Azure-informatiebeveiliging implementeren](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Inzicht in de bescherming van klantgegevens in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Gebruik op rollen gebaseerd toegangscontrole om de toegang tot bronnen te beheren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.6 | 14.6 | Klant |

Gebruik Azure AD RBAC om de toegang tot gegevens en bronnen te beheren en gebruik anders servicespecifieke toegangsbeheermethoden.

- [RBAC configureren in Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Gebruik hostgebaseerde preventie van gegevensverlies om toegangscontrole af te dwingen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.7 | 14.7 | Gedeeld |

Indien vereist voor naleving van compute resources, implementeert u een tool van derden, zoals een geautomatiseerde oplossing voor gegevensverliespreventie op basis van een host, om toegangscontroles tot gegevens af te dwingen, zelfs wanneer gegevens van een systeem worden gekopieerd.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

- [Inzicht in de bescherming van klantgegevens in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Versleutel gevoelige informatie in rust

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.8 | 14.8 | Klant |

Gebruik versleuteling in rust op alle Azure-bronnen. Microsoft raadt azure aan om uw versleutelingssleutels te beheren, maar er is in sommige gevallen de optie om uw eigen sleutels te beheren. 

- [Versleuteling in rust in Azure begrijpen](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Beheerde versleutelingssleutels van klanten configureren](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwing en waarschuwing over wijzigingen in kritieke Azure-resources

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 4.9 | 14.9 | Klant |

Gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer er wijzigingen plaatsvinden in kritieke Azure-bronnen.

- [Waarschuwingen maken voor azure-activiteitenlogboekgebeurtenissen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>Volgende stappen

- Bekijk de volgende security control: [Vulnerability Management](security-control-vulnerability-management.md)