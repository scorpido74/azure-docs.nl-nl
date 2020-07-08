---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8b97a62626666fa39a5b0622852d9eec47c2410a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024882"
---
Voordat u een RBAC-rol toewijst aan een beveiligingsprincipal, bepaalt u het bereik van toegang dat de beveiligingsprincipal moet hebben. Aanbevolen procedures bepalen dat het altijd het beste is om alleen het smalle mogelijke bereik toe te kennen.

In de volgende lijst worden de niveaus beschreven waarmee u toegang tot Azure Blob-en wachtrij bronnen kunt bereiken, te beginnen met het smalle bereik:

- **Een afzonderlijke container.** In dit bereik is een roltoewijzing van toepassing op alle blobs in de container, evenals container eigenschappen en meta gegevens.
- **Een afzonderlijke wachtrij.** In dit bereik geldt een roltoewijzing voor berichten in de wachtrij, evenals wachtrij-eigenschappen en meta gegevens.
- **Het opslag account.** In dit bereik is een roltoewijzing van toepassing op alle containers en de bijbehorende blobs, of op alle wacht rijen en hun berichten.
- **De resourcegroep.** In dit bereik is een roltoewijzing van toepassing op alle containers of wacht rijen in alle opslag accounts in de resource groep.
- **Het abonnement.** In dit bereik is een roltoewijzing van toepassing op alle containers of wacht rijen in alle opslag accounts in alle resource groepen in het abonnement.
