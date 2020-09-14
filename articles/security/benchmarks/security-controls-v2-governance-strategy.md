---
title: Azure Security Bench Mark v2-governance en strategie
description: Azure Security Bench Mark v2 governance en strategie
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e8a5196bf71712caae1218933ed13345f4cecd99
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059197"
---
# <a name="security-control-governance-and-strategy"></a>Beveiligings controle: governance en strategie

Back-up en herstel bestrijkt de controles om ervoor te zorgen dat de gegevens en configuratie back-ups in de verschillende service lagen worden uitgevoerd, gevalideerd en beveiligd.

## <a name="gs-1-define-asset-management-and-protection-strategy"></a>GS-1: de strategie voor het beheren en beveiligen van activa definiëren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Zorg ervoor dat u een duidelijke strategie documenteert en communiceert voor continue bewaking en bescherming van systemen en gegevens. Volg prioriteiten voor detectie, beoordeling, beveiliging en bewaking van bedrijfs kritieke gegevens en systemen. 

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Standaard gegevens classificatie in overeenstemming met de zakelijke Risico's

-   Zicht baarheid van beveiligings organisaties in Risico's en activa-inventaris 

-   Goed keuring van beveiligings organisaties van Azure-Services voor gebruik 

-   Beveiliging van assets via hun levens cyclus

-   Vereiste strategie voor toegangs beheer in overeenstemming met organisatie gegevens classificatie

-   Gebruik van functies voor gegevens bescherming van Azure native en derden

-   Gegevens versleutelings vereisten voor in-transit-en rest-use cases

-   Juiste cryptografische normen

Opmerking: uw beheer-en beveiligings benadering voor de Cloud en on-premises kunnen afwijken, afhankelijk van meerdere factoren, zoals toepassings service/hosting model, bedrijfs Risico's en vereisten voor naleving. 

- [Aanbeveling van Azure-beveiligings architectuur-opslag, gegevens en versleuteling](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Basis beginselen van Azure-beveiliging-Azure-gegevens beveiliging,-versleuteling en-opslag](../fundamentals/encryption-overview.md)

- [Cloud-acceptatie Framework-aanbevolen procedures voor Azure Data Security en versleuteling](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Bench Mark-Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security Bench Mark-gegevens beveiliging](/azure/security/benchmarks/security-controls-v2-data-protection)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-security-posture-management-strategy"></a>GS-2: Security postuur management-strategie definiëren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| GS-2 | 20, 3, 5 | RA, CM, SC |

U kunt de Risico's voor uw afzonderlijke assets en de omgeving die ze hosten, voortdurend meten en beperken. Volg prioriteiten voor hoogwaardige assets en zeer belichte aanvallen, zoals gepubliceerde toepassingen, netwerk binnenkomend en uitgevende punten, gebruikers-en beheerders eindpunten, enzovoort.

- [Azure Security Bench Mark-postuur en beveiligings beheer](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-align-organization-roles-responsibilities-and-accountabilities"></a>GS-3: organisatie rollen, verantwoordelijkheden en accountabilities uitlijnen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| GS-3 | N.v.t. | PL, PM |

Zorg ervoor dat u een duidelijke strategie voor rollen en verantwoordelijkheden in uw beveiligings organisatie documenteert en communiceert. Volg prioriteiten om een duidelijke verantwoordelijkheid te bieden voor beveiligings beslissingen, onderwijs op het gedeelde verantwoordelijkheids model en technisch onderwijs voor Cloud beveiliging. 

- [Best Practice voor Azure-beveiliging 1: personen: teams trainen in Cloud Security traject](https://aka.ms/AzSec1)

- [Aanbevolen beveiligings procedure voor Azure-gebruikers: teams in de Cloud-beveiligings technologie trainen](https://aka.ms/AzSec2)

- [Best Practice voor Azure-beveiliging 3-proces: verantwoordelijkheid toewijzen voor Cloud beveiligings beslissingen](https://aka.ms/AzSec3)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-define-network-security-strategy"></a>GS-4: netwerk beveiligings strategie definiëren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| GS-4 | 9 | CA, SC |

Stel een Azure-netwerk beveiligings benadering in als onderdeel van de algehele strategie voor het toegangs beheer van uw organisatie.  

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Gecentraliseerd netwerk beheer en beveiligings verantwoordelijkheid

-   Segment model voor virtuele netwerken dat is afgestemd op de strategie voor bedrijfs segmentatie

-   Herstel strategie in verschillende scenario's voor bedreigingen en aanvallen

-   Internet-en ingangs-en uitgangs strategie

-   Hybride Cloud en on-premises interconnectiviteit-strategie

-   Up-to-date netwerk beveiligings artefacten (zoals netwerk diagrammen, referentie netwerk architectuur)

Opmerking: de netwerk beveiligings benadering voor Cloud en on-premises kan afwijken, afhankelijk van meerdere factoren, zoals het toepassings service model, de bloot stelling aan bedreigingen en de configuratie van het hybride netwerk.

- [Aanbevolen procedures voor Azure-beveiliging 11-architectuur. Eén uniforme beveiligings strategie](https://aka.ms/AzSec11)

- [Azure Security Bench Mark-netwerk beveiliging](/azure/security/benchmarks/security-controls-v2-network-security)

- [Overzicht van Azure-netwerk beveiliging](../fundamentals/network-overview.md)

- [Strategie voor bedrijfs netwerk architectuur](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-identity-and-privileged-access-strategy"></a>GS-5: identiteits-en bevoorrechte toegangs strategie definiëren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| GS-5 | 16, 4 | AC, AU, SC |

Stel een Azure Identity and privileged Access benaderingen in als onderdeel van de algehele strategie voor beveiligings beheer van uw organisatie.  

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Een gecentraliseerd identiteits-en verificatie systeem en de interconnectiviteit daarvan met andere interne en externe identiteits systemen

-   Sterke authenticatie methoden in verschillende use-cases en-voor waarden

-   Beveiliging van gebruikers met hoge bevoegdheden

-   Bewaking en verwerking van afwijkende gebruikers activiteiten  

-   Beoordelings-en afstemmings proces voor gebruikers-id en-toegang

Opmerking: uw identiteits-en bevoorrechte toegangs benadering voor Cloud-en on-premises kan verschillen, afhankelijk van meerdere factoren, zoals het toegangs traject voor gegevens/toepassingen, het service model en de toegangs strategie voor klanten en partners.

- [Azure Security Bench Mark-Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security-benchmark-privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Aanbevolen procedures voor Azure-beveiliging 11-architectuur. Eén uniforme beveiligings strategie](https://aka.ms/AzSec11)

- [Overzicht van Azure Identity Management-beveiliging](../fundamentals/identity-management-overview.md) 

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-logging-and-threat-response-strategy"></a>GS-6: de logboek registratie en de reactie strategie voor bedreigingen bepalen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| GS-6 | 19 | IR, AU, RA, SC |

Stel een strategie voor logboek registratie en reactie op bedreigingen in om bedreigingen snel te detecteren en op te lossen tijdens het voldoen aan nalevings vereisten. Volg prioriteiten om analisten te voorzien van waarschuwingen met hoge kwaliteit en naadloze ervaring, zodat ze zich kunnen concentreren op bedreigingen in plaats van integratie en hand matige stappen. 

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   De rol en verantwoordelijkheden van de organisatie voor beveiligings bewerkingen (SecOps) 

-   Een goed gedefinieerd respons proces voor incidenten dat wordt afgestemd op het NIST of een ander branche raamwerk 

-   Vastleggen en bewaren in Logboeken voor ondersteuning van detectie van bedreigingen, reacties op incidenten en nalevings behoeften

-   Gecentraliseerde zicht baarheid van en correlatie-informatie over bedreigingen, met behulp van SIEM, systeem eigen Azure-mogelijkheden en andere bronnen 

-   Communicatie-en meldings abonnement met uw klanten, leveranciers en open bare partijen

-   Gebruik van Azure native en platforms van derden voor het verwerken van incidenten, zoals logboek registratie en detectie van bedreigingen, forensische, herstel en uitroeiing van aanvallen

-   Processen voor het verwerken van incidenten en activiteiten na incidenten, zoals geleerde lessen en bewijs behoud

Opmerking: de aanpak van de logboek registratie en de bedreigings detectie voor Cloud en on-premises kan verschillen, afhankelijk van meerdere factoren, zoals nalevings vereisten, Threat landschap en detectie-en herstel capaciteit. 

- [Azure Security Bench Mark-logboek registratie en detectie van bedreigingen](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Bench Mark-incident respons](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Aanbevolen procedure voor Azure-beveiliging 4-proces. Reactie processen voor incidenten bijwerken voor Cloud](https://aka.ms/AzSec11)

- [Hand leiding Azure-acceptatie raamwerk, logboek registratie en rapportage](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Schaal, beheer en bewaking van Azure Enter prise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-backup-and-recovery-strategy"></a>GS-7: de strategie voor back-up en herstel bepalen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| GS-7 | 10 | CP (consistentie en partitietolerantie) |

Stel een Azure-strategie voor back-up en herstel in voor uw organisatie. 

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Definities van de beoogde herstel tijd (RTO) en Recovery Point Objective (RPO) in overeenstemming met uw bedrijfs tolerantie doelstellingen

-   Het ontwerp van redundantie in uw toepassingen en de configuratie van de infra structuur

-   Beveiliging van back-ups met behulp van toegangs beheer en gegevens versleuteling

Opmerking: uw methode voor back-up en herstel voor Cloud en on-premises kan afwijken, afhankelijk van de verschillende factoren, zoals infrastructuur redundantie, toepassings service/hosting model en nalevings vereisten.

- [Azure Security Bench Mark-back-up en herstel](/azure/security/benchmarks/security-controls-v2-backup-recovery)

- [Azure goed architectuur Framework: back-up en herstel na nood gevallen voor Azure-toepassingen](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure-acceptatie Framework-bedrijfs continuïteit en herstel na nood gevallen](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden**van de klant:

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

