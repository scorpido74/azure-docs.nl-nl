---
title: Beveiligings kenmerken voor Azure Service Bus berichten
description: Een controle lijst met beveiligings kenmerken voor het evalueren van Azure Service Bus berichten
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 875754282f9e7a2b8dd92b0eb2d7e277ac842696
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013111"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Beveiligings kenmerken voor Azure Service Bus berichten

In dit artikel worden de beveiligings kenmerken gedocumenteerd die zijn ingebouwd in Azure Service Bus berichten.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventie

| Beveiligings kenmerk | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling bij rest (zoals versleuteling aan server zijde, versleuteling aan server zijde met door de klant beheerde sleutels en andere versleutelings functies)|  Ja voor versleuteling aan de server zijde is standaard ingesteld op rest. | Door de klant beheerde sleutels en BYOK worden nog niet ondersteund. Versleuteling aan client zijde is de verantwoordelijkheid van de client |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Ondersteunt het standaard HTTPS/TLS-mechanisme. |
| Verwerking van versleutelings sleutels (CMK, BYOK, enz.)| Nee |   |
| Versleuteling op kolom niveau (Azure Data Services)| N/A | |
| Versleutelde API-aanroepen| Ja | API-aanroepen worden gedaan via [Azure Resource Manager](../azure-resource-manager/index.yml) en HTTPS. |

## <a name="network-segmentation"></a>Netwerk segmentatie

| Beveiligings kenmerk | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja (alleen Premium-laag) | VNet-service-eind punten worden alleen ondersteund voor [Service Bus-laag Premium](service-bus-premium-messaging.md) . |
| Ondersteuning voor VNet-injectie| Nee | |
| Ondersteuning voor netwerk isolatie en firewalling| Ja (alleen Premium-laag) |  |
| Ondersteuning voor geforceerde tunneling| Nee |  |

## <a name="detection"></a>Detectie

| Beveiligings kenmerk | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Ondersteund via [Azure monitor en waarschuwingen](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

| Beveiligings kenmerk | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Beheerd via [Azure Active Directory Managed Service Identity](service-bus-managed-service-identity.md); Zie [Service Bus verificatie en autorisatie](service-bus-authentication-and-authorization.md).|
| Authorization| Ja | Ondersteunt verificatie via [RBAC](authenticate-application.md) en SAS-token; Zie [Service Bus verificatie en autorisatie](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Audittrail

| Beveiligings kenmerk | Ja/Nee | Opmerkingen|
|---|---|--|
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Er zijn bewerkings logboeken beschikbaar. Zie [Service Bus Diagnostische logboeken](service-bus-diagnostic-logs.md).  |
| Logboek registratie en controle van het gegevens vlak| Nee |  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings kenmerk | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Ondersteunt versie beheer van de resource provider via de [Azure Resource Manager-API](/rest/api/resources/).|
