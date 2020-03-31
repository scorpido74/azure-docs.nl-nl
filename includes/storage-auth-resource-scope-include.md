---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460488"
---
Voordat u een RBAC-rol toewijst aan een beveiligingsprincipal, bepaalt u de toegangsruimte die de beveiligingsprincipal moet hebben. Best practices dicteren dat het altijd het beste is om alleen de smalst mogelijke ruimte te verlenen.

In de volgende lijst worden de niveaus beschreven waarop u toegang tot Azure-blob- en wachtrijbronnen bereiken, te beginnen met het smalste bereik:

- **Een individuele container.** In dit bereik is een roltoewijzing van toepassing op alle blobs in de container, evenals containereigenschappen en metagegevens.
- **Een individuele wachtrij.** In dit bereik is een roltoewijzing van toepassing op berichten in de wachtrij, evenals op wachtrijeigenschappen en metagegevens.
- **De opslagrekening.** In dit bereik is een roltoewijzing van toepassing op alle containers en hun blobs, of op alle wachtrijen en hun berichten.
- **De resourcegroep.** In dit bereik is een roltoewijzing van toepassing op alle containers of wachtrijen in alle opslagaccounts in de resourcegroep.
- **Het abonnement.** In dit bereik is een roltoewijzing van toepassing op alle containers of wachtrijen in alle opslagaccounts in alle resourcegroepen in het abonnement.

> [!IMPORTANT]
> Als uw abonnement een Azure DataBricks-naamruimte bevat, worden rollen die zijn beperkt tot het abonnement, geen toegang tot blob- en wachtrijgegevens. Bereikrollen naar de resourcegroep, het opslagaccount of de container of wachtrij.     
