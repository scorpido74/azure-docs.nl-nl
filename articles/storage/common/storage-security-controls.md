---
title: Beveiligingsmaatregelen
titleSuffix: Azure Storage
description: Controle lijsten voor beveiligings controle weer geven voor het evalueren van Azure Storage. De behorende gebieden zijn gegevens beveiliging, netwerk, bewaking en logboek registratie, identiteit en configuratie.
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 3b4d74e7ba869e0438a936817d824e841823d472
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715721"
---
# <a name="security-controls-for-azure-storage"></a>Beveiligings controles voor Azure Storage

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Storage.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Ja |  |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Ja | Zie [Storage service Encryption door de klant beheerde sleutels gebruiken in azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Ondersteuning voor standaard HTTPS/TLS-mechanismen.  Gebruikers kunnen ook gegevens versleutelen voordat deze naar de service worden verzonden. |
| Versleutelde API-aanroepen| Ja |  |

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja |  |
| Ondersteuning voor service Tags| Ja | Zie [overzicht van Azure-service Tags](../../virtual-network/service-tags-overview.md) voor meer informatie over service tags die door Azure Storage worden ondersteund. |
| Ondersteuning voor VNet-injectie| N.v.t. |  |
| Netwerk isolatie en firewall ondersteuning| Ja | |
| Ondersteuning voor geforceerde tunneling| N.v.t. |  |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Azure Monitor metrische gegevens|
| Logboek registratie en controle op het vlak van controle en beheer | Ja | Azure-activiteitenlogboek |
| Logboek registratie en controle van het gegevens vlak| Ja | Azure Monitor bron logboeken |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Verificatie| Ja | Azure Active Directory, gedeelde sleutel, gedeeld toegangs token. |
| Autorisatie| Ja | Ondersteuning voor autorisatie via Azure RBAC, POSIX Acl's en SAS-tokens |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Ondersteuning van resource provider versie beheer via Azure Resource Manager-Api's |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../../security/fundamentals/security-controls.md).