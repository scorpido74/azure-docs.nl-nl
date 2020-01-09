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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460488"
---
Voordat u een RBAC-rol toewijst aan een beveiligingsprincipal, bepaalt u het bereik van toegang dat de beveiligingsprincipal moet hebben. Aanbevolen procedures bepalen dat het altijd het beste is om alleen het smalle mogelijke bereik toe te kennen.

In de volgende lijst worden de niveaus beschreven waarmee u toegang tot Azure Blob-en wachtrij bronnen kunt bereiken, te beginnen met het smalle bereik:

- **Een afzonderlijke container.** In dit bereik is een roltoewijzing van toepassing op alle blobs in de container, evenals container eigenschappen en meta gegevens.
- **Een afzonderlijke wachtrij.** In dit bereik geldt een roltoewijzing voor berichten in de wachtrij, evenals wachtrij-eigenschappen en meta gegevens.
- **Het opslag account.** In dit bereik is een roltoewijzing van toepassing op alle containers en de bijbehorende blobs, of op alle wacht rijen en hun berichten.
- **De resource groep.** In dit bereik is een roltoewijzing van toepassing op alle containers of wacht rijen in alle opslag accounts in de resource groep.
- **Het abonnement.** In dit bereik is een roltoewijzing van toepassing op alle containers of wacht rijen in alle opslag accounts in alle resource groepen in het abonnement.

> [!IMPORTANT]
> Als uw abonnement een Azure DataBricks-naam ruimte bevat, zullen rollen die zijn gericht aan het abonnement geen toegang verlenen tot Blob-en wachtrij gegevens. Bereik rollen in plaats daarvan naar de resource groep, het opslag account of de container of de wachtrij.     
