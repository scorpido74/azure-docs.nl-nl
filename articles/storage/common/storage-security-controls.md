---
title: Beveiligings controles voor Azure Storage
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Storage
services: storage
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 8cb14c19e8816d53c7d9385563f916bee5d4a6af
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886402"
---
# <a name="security-controls-for-azure-storage"></a>Beveiligings controles voor Azure Storage

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Storage. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels | Ja |  |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Ja | Zie [Storage service Encryption door de klant beheerde sleutels gebruiken in azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Versleuteling op kolom niveau (Azure Data Services)| N/A |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Ondersteuning voor standaard HTTPS/TLS-mechanismen.  Gebruikers kunnen ook gegevens versleutelen voordat deze naar de service worden verzonden. |
| Versleutelde API-aanroepen| Ja |  |

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja |  |
| Ondersteuning voor VNet-injectie| N/A |  |
| Ondersteuning voor netwerk isolatie en firewalling| Ja | |
| Ondersteuning voor geforceerde tunneling| N/A |  |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Azure Monitor metrische gegevens nu beschikbaar, logboeken beginnen met preview |
| Logboek registratie en controle op het vlak van controle en beheer | Ja | Azure Resource Manager activiteiten logboek |
| Logboek registratie en controle van het gegevens vlak| Ja | Diagnostische logboeken voor services en Azure Monitor logboek registratie starten  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Azure Active Directory, gedeelde sleutel, gedeeld toegangs token. |
| Authorization| Ja | Ondersteuning voor autorisatie via RBAC, POSIX Acl's en SAS-tokens |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Ondersteuning van resource provider versie beheer via Azure Resource Manager-Api's |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../../security/fundamentals/security-controls.md).