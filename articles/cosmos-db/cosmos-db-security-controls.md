---
title: Beveiligings controles voor Azure Cosmos DB
description: Vraag een controle lijst op met beveiligings controles zoals netwerk, bewaking, identiteit en gegevens beveiliging om Azure Cosmos DB te evalueren
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.openlocfilehash: 1ac4d12f58977497642cdb0706ab7e85e9a4db64
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913077"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Beveiligings controles voor Azure Cosmos DB

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Cosmos DB.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja |  |
| Ondersteuning voor VNet-injectie| Ja | Met het VNet-service-eind punt kunt u een Azure Cosmos DB-account configureren om alleen toegang toe te staan vanuit een specifiek subnet van een virtueel netwerk (VNet). U kunt ook VNet-toegang combi neren met firewall regels. Zie [toegang tot Azure Cosmos DB van virtuele netwerken](VNet-service-endpoint.md)voor meer informatie. |
| Netwerk isolatie en firewall ondersteuning| Ja | Met firewall ondersteuning kunt u uw Azure Cosmos-account zodanig configureren dat toegang alleen is toegestaan vanuit een goedgekeurde set van IP-adressen, een bereik van IP-adressen en/of Cloud Services. Zie [IP-Firewall configureren in azure Cosmos DB](how-to-configure-firewall.md)voor meer informatie.|
| Ondersteuning voor geforceerde tunneling| Ja | Kan worden geconfigureerd aan de client zijde op het VNet waar de virtuele machines zich bevinden.   |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Alle aanvragen die naar Azure Cosmos DB worden verzonden, worden geregistreerd. [Azure-bewaking](../azure-monitor/overview.md), metrische gegevens van Azure, Azure audit logging wordt ondersteund.  U kunt informatie vastleggen die overeenkomt met gegevens vlak aanvragen, runtime statistieken voor query's, query tekst, MongoDB aanvragen. U kunt ook waarschuwingen instellen. |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Azure-activiteiten logboek voor bewerkingen op account niveau, zoals firewalls, VNets, sleutels toegang en IAM. |
| Logboek registratie en controle van het gegevens vlak | Ja | Diagnostische gegevens voor controle van de logboek registratie voor bewerkingen op container niveau, zoals het maken van een container, het inrichten van de door Voer, het indexeren van beleid en ruwe bewerkingen op documenten. |

## <a name="identity"></a>Identity

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Ja op het account niveau van de data base. op het niveau van het gegevensvlak Cosmos DB maakt gebruik van bron tokens en toegang tot sleutels. |
| Autorisatie| Ja | Ondersteund in het Azure Cosmos-account met hoofd sleutels (primair en secundair) en bron tokens. U kunt alleen lees-en schrijf toegang krijgen tot gegevens met hoofd sleutels. Met bron tokens kunt u beperkte tijd toegang tot resources, zoals documenten en containers, toestaan. |

## <a name="data-protection"></a>Databeveiliging

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Ja | Alle Azure Cosmos-data bases en-back-ups worden standaard versleuteld. Zie [gegevens versleuteling in azure Cosmos DB](database-encryption-at-rest.md). |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Ja | Zie door [de klant beheerde sleutels voor uw Azure Cosmos DB account configureren](how-to-setup-cmk.md)  |
| Versleuteling op kolom niveau (Azure Data Services)| Ja | Alleen in de Table API Premium. Niet alle Api's ondersteunen deze functie. Zie [Inleiding tot Azure Cosmos DB: Table-API](table-introduction.md). |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Alle Azure Cosmos DB gegevens worden tijdens de overdracht versleuteld. |
| Versleutelde API-aanroepen| Ja | Alle verbindingen met Azure Cosmos DB ondersteuning voor HTTPS. Azure Cosmos DB ondersteunt ook TLS 1,2.<br>Het is mogelijk om een minimale TLS-versie aan de server zijde af te dwingen. Als u dit wilt doen, neemt u contact op met [cosmosdbpm@microsoft.com](maito:cosmosdbpm@microsoft.com]). |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Nee  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Aanvullende beveiligings controles voor Cosmos DB

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Cross Origin Resource Sharing (CORS) | Ja | Zie [Configure cross-Origin Resource Sharing (CORS)](how-to-configure-cross-origin-resource-sharing.md). |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).