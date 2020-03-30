---
title: Beveiligingsbesturingselementen voor Azure Service Fabric
description: Meer informatie over beveiligingsbesturingselementen voor Azure Service Fabric. Bevat een checklist met ingebouwde beveiligingscontroles.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645426"
---
# <a name="security-controls-for-azure-service-fabric"></a>Beveiligingsbesturingselementen voor Azure Service Fabric

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Azure Service Fabric, document. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor serviceeindpunten| Ja |  |
| Ondersteuning voor VNet-injectie| Ja |  |
| Ondersteuning voor netwerkisolatie en firewalling| Ja | Met behulp van netwerkbeveiligingsgroepen (NSG). |
| Ondersteuning voor gedwongen tunneling| Ja | Azure-netwerken bieden gedwongen tunneling. |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | Azure-bewakingsondersteuning en ondersteuning van derden gebruiken. |
| Logboekregistratie en audit van het controle- en beheervlak| Ja | Alle besturingsvlakbewerkingen doorlopen processen voor controle en goedkeuringen. |
| Logboekregistratie en -audit van gegevensvliegtuigen| N.v.t. | De klant is eigenaar van het cluster.  |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Verificatie gebeurt via Azure Active Directory. |
| Autorisatie| Ja | Identity and access management (IAM) voor gesprekken via SFRP. Oproepen rechtstreeks naar het eindpunt van het cluster ondersteunen twee rollen: Gebruiker en beheerder. De klant kan de API's in kaart brengen voor beide rollen. |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels | Ja | De klant is eigenaar van het cluster en de virtuele machineschaal waarin het cluster is gebouwd. Azure-schijfversleuteling kan worden ingeschakeld op de detectievan de virtuele machineschaal. |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | Ja | De klant is eigenaar van het cluster en de virtuele machineschaal waarin het cluster is gebouwd. Azure-schijfversleuteling kan worden ingeschakeld op de detectievan de virtuele machineschaal. |
| Versleuteling op kolomniveau (Azure Data Services)| N.v.t. |  |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Ja |  |
| API-aanroepen versleuteld| Ja | Service Fabric API-aanroepen worden uitgevoerd via Azure Resource Manager. Een geldig JSON-webtoken (JWT) is vereist. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| Ja | |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../security/fundamentals/security-controls.md).