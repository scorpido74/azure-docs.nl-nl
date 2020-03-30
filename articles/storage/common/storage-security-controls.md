---
title: Beveiligingsbesturingselementen voor Azure Storage
description: Een checklist met beveiligingsbesturingselementen voor de evaluatie van Azure Storage
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 379acaf48c02f0a579c07773cd48366d962a44f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061118"
---
# <a name="security-controls-for-azure-storage"></a>Beveiligingsbesturingselementen voor Azure Storage

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Azure Storage, opgeslagen. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels | Ja |  |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | Ja | Zie [Opslagserviceversleuteling met door de klant beheerde sleutels in Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Versleuteling op kolomniveau (Azure Data Services)| N.v.t. |  |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Ja | Ondersteuning voor standaard HTTPS/TLS-mechanismen.  Gebruikers kunnen ook gegevens versleutelen voordat deze naar de service worden verzonden. |
| API-aanroepen versleuteld| Ja |  |

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor serviceeindpunten| Ja |  |
| Ondersteuning voor VNet-injectie| N.v.t. |  |
| Ondersteuning voor netwerkisolatie en firewalling| Ja | |
| Ondersteuning voor gedwongen tunneling| N.v.t. |  |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | Azure-monitorstatistieken|
| Logboekregistratie en audit van het controle- en beheervlak | Ja | Activiteitenlogboek Azure Resource Manager |
| Logboekregistratie en -audit van gegevensvliegtuigen| Ja | Diagnostische logboeken voor service.|

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Azure Active Directory, Gedeelde sleutel, Gemeenschappelijke toegangstoken. |
| Autorisatie| Ja | Ondersteuningsautorisatie via RBAC-, POSIX-ACL's en SAS-tokens |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| Ja | Versiebeheer van ondersteuningsbronnen via Azure Resource Manager-API's |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../../security/fundamentals/security-controls.md).