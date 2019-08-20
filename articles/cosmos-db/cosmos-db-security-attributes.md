---
title: Beveiligings kenmerken voor Azure Cosmos DB
description: Een controle lijst met beveiligings kenmerken voor het evalueren van Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: f1b578ff70e8a2795ba886e6d0c75eff521f77c3
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615532"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Beveiligings kenmerken voor Azure Cosmos DB

In dit artikel worden de beveiligings kenmerken gedocumenteerd die zijn ingebouwd in Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventie

| Beveiligings kenmerk | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling bij rest (zoals versleuteling aan server zijde, versleuteling aan server zijde met door de klant beheerde sleutels en andere versleutelings functies) | Ja | Alle Cosmos-data bases en back-ups worden standaard versleuteld. Zie [gegevens versleuteling in azure Cosmos DB](database-encryption-at-rest.md). Versleuteling aan de server zijde met door de klant beheerde sleutels wordt niet ondersteund. |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Alle Azure Cosmos DB gegevens worden tijdens de overdracht versleuteld. |
| Verwerking van versleutelings sleutels (CMK, BYOK, enz.)| Nee |  |
| Versleuteling op kolom niveau (Azure Data Services)| Ja | Alleen in de Table API Premium. Niet alle Api's ondersteunen deze functie. Zie [Inleiding tot Azure Cosmos DB: Table-API](table-introduction.md). |
| Versleutelde API-aanroepen| Ja | Alle verbindingen met Azure Cosmos DB ondersteuning voor HTTPS. Azure Cosmos DB ondersteunt ook TLS 1,2-verbindingen, maar dit is nog niet afgedwongen. Als klanten minder TLS op hun eind niveau uitschakelen, kunnen ze ervoor zorgen dat ze verbinding maken met Cosmos DB.  |

## <a name="network-segmentation"></a>Netwerk segmentatie

| Beveiligings kenmerk | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja |  |
| Ondersteuning voor VNet-injectie| Ja | Met het VNet-service-eind punt kunt u een Azure Cosmos DB-account configureren om alleen toegang toe te staan vanuit een specifiek subnet van een virtueel netwerk (VNet). U kunt ook VNet-toegang combi neren met firewall regels.  Zie [toegang tot Azure Cosmos DB van virtuele netwerken](VNet-service-endpoint.md). |
| Ondersteuning voor netwerk isolatie en firewalling| Ja | Met firewall ondersteuning kunt u uw Azure Cosmos-account zodanig configureren dat toegang alleen is toegestaan vanuit een goedgekeurde set van IP-adressen, een bereik van IP-adressen en/of Cloud Services. Zie [IP-Firewall configureren in azure Cosmos DB](how-to-configure-firewall.md).|
| Ondersteuning voor geforceerde tunneling| Ja | Kan worden geconfigureerd aan de client zijde op het VNet waar de virtuele machines zich bevinden.   |

## <a name="detection"></a>Detectie

| Beveiligings kenmerk | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Alle aanvragen die naar Azure Cosmos DB worden verzonden, worden geregistreerd. [Azure-bewaking](../azure-monitor/overview.md), metrische gegevens van Azure, Azure audit logging wordt ondersteund.  U kunt informatie vastleggen die overeenkomt met gegevens vlak aanvragen, runtime statistieken voor query's, query tekst, MongoDB aanvragen. U kunt ook waarschuwingen instellen. |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

| Beveiligings kenmerk | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Ja op het account niveau van de data base. op het niveau van het gegevensvlak Cosmos DB maakt gebruik van bron tokens en toegang tot sleutels. |
| Authorization| Ja | Ondersteund in het Azure Cosmos-account met hoofd sleutels (primair en secundair) en bron tokens. U kunt alleen lees-en schrijf toegang krijgen tot gegevens met hoofd sleutels. Met bron tokens kunt u beperkte tijd toegang tot resources, zoals documenten en containers, toestaan. |


## <a name="audit-trail"></a>Audittrail

| Beveiligings kenmerk | Ja/Nee | Opmerkingen|
|---|---|--|
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Azure-activiteiten logboek voor bewerkingen op account niveau, zoals firewalls, VNets, sleutels toegang en IAM. |
| Logboek registratie en controle van het gegevens vlak | Ja | Diagnostische gegevens voor controle van de logboek registratie voor bewerkingen op container niveau, zoals het maken van een container, het inrichten van de door Voer, het indexeren van beleid en ruwe bewerkingen op documenten. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings kenmerk | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Nee  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Aanvullende beveiligings kenmerken voor Cosmos DB

| Beveiligings kenmerk | Ja/Nee | Opmerkingen|
|---|---|--|
| Cross Origin Resource Sharing (CORS) | Ja | Zie [Configure cross-Origin Resource Sharing (CORS)](how-to-configure-cross-origin-resource-sharing.md). |
