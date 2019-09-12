---
title: Beveiligings controles voor Azure Cosmos DB
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 20cdfc61a4cdfe5263e48d049aab14cad2458b06
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886448"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Beveiligings controles voor Azure Cosmos DB

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Cosmos DB.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja |  |
| Ondersteuning voor VNet-injectie| Ja | Met het VNet-service-eind punt kunt u een Azure Cosmos DB-account configureren om alleen toegang toe te staan vanuit een specifiek subnet van een virtueel netwerk (VNet). U kunt ook VNet-toegang combi neren met firewall regels.  Zie [toegang tot Azure Cosmos DB van virtuele netwerken](VNet-service-endpoint.md). |
| Ondersteuning voor netwerk isolatie en firewalling| Ja | Met firewall ondersteuning kunt u uw Azure Cosmos-account zodanig configureren dat toegang alleen is toegestaan vanuit een goedgekeurde set van IP-adressen, een bereik van IP-adressen en/of Cloud Services. Zie [IP-Firewall configureren in azure Cosmos DB](how-to-configure-firewall.md).|
| Ondersteuning voor geforceerde tunneling| Ja | Kan worden geconfigureerd aan de client zijde op het VNet waar de virtuele machines zich bevinden.   |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Alle aanvragen die naar Azure Cosmos DB worden verzonden, worden geregistreerd. [Azure-bewaking](../azure-monitor/overview.md), metrische gegevens van Azure, Azure audit logging wordt ondersteund.  U kunt informatie vastleggen die overeenkomt met gegevens vlak aanvragen, runtime statistieken voor query's, query tekst, MongoDB aanvragen. U kunt ook waarschuwingen instellen. |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Azure-activiteiten logboek voor bewerkingen op account niveau, zoals firewalls, VNets, sleutels toegang en IAM. |
| Logboek registratie en controle van het gegevens vlak | Ja | Diagnostische gegevens voor controle van de logboek registratie voor bewerkingen op container niveau, zoals het maken van een container, het inrichten van de door Voer, het indexeren van beleid en ruwe bewerkingen op documenten. |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Ja op het account niveau van de data base. op het niveau van het gegevensvlak Cosmos DB maakt gebruik van bron tokens en toegang tot sleutels. |
| Authorization| Ja | Ondersteund in het Azure Cosmos-account met hoofd sleutels (primair en secundair) en bron tokens. U kunt alleen lees-en schrijf toegang krijgen tot gegevens met hoofd sleutels. Met bron tokens kunt u beperkte tijd toegang tot resources, zoals documenten en containers, toestaan. |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels | Ja | Alle Cosmos-data bases en back-ups worden standaard versleuteld. Zie [gegevens versleuteling in azure Cosmos DB](database-encryption-at-rest.md). Versleuteling aan de server zijde met door de klant beheerde sleutels wordt niet ondersteund. |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Nee |  |
| Versleuteling op kolom niveau (Azure Data Services)| Ja | Alleen in de Table API Premium. Niet alle Api's ondersteunen deze functie. Zie [Inleiding tot Azure Cosmos DB: Table-API](table-introduction.md). |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Alle Azure Cosmos DB gegevens worden tijdens de overdracht versleuteld. |
| Versleutelde API-aanroepen| Ja | Alle verbindingen met Azure Cosmos DB ondersteuning voor HTTPS. Azure Cosmos DB ondersteunt ook TLS 1,2-verbindingen, maar dit is nog niet afgedwongen. Als klanten minder TLS op hun eind niveau uitschakelen, kunnen ze ervoor zorgen dat ze verbinding maken met Cosmos DB.  |

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