---
title: Algemene fout codes voor Azure Key Vault | Microsoft Docs
description: Algemene fout codes voor Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: 25c79229a09db912903fba825f0d48f571880745
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876935"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Algemene fout codes voor Azure Key Vault

De fout codes die in de volgende tabel worden weer gegeven, kunnen worden geretourneerd door een bewerking op Azure Key kluis

| Foutcode | Gebruikers bericht |
|--|--|
| VaultAlreadyExists |  De opgegeven sleutel kluis bestaat al (in de modus voor zacht verwijderen of in een ander abonnement). |
| VaultNameNotValid |  De naam van de kluis moet 24 tekens en alfanumeriek zijn en begint met een alfabet |
| AccessDenied |  Mogelijk ontbreken er machtigingen in het toegangs beleid om deze bewerking uit te voeren. |
| ForbiddenByFirewall |  Het client adres is niet geautoriseerd en de aanroeper is geen vertrouwde service. |
| ConflictError |  U vraagt meerdere bewerkingen aan op hetzelfde item.  |
| RegionNotSupported |  De opgegeven Azure-regio wordt niet ondersteund voor deze resource. |
| SkuNotSupported |  Het opgegeven SKU-type wordt niet ondersteund voor deze resource. |
| ResourceNotFound |  De opgegeven Azure-resource is niet gevonden. |
| CertificateExpired |  Controleer de verval datum en de geldigheids periode van het certificaat. |


## <a name="next-steps"></a>Volgende stappen

- Zie de [gids voor Azure Key Vault-ontwikkelaars](developers-guide.md) (vooralsnog Engelstalig).
- Meer informatie over het [verifiëren bij Key Vault](authentication.md)
