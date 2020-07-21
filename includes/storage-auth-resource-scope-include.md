---
title: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: c222869df561a9a36ebd69eb9ae09fa688ba0086
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518674"
---
Voordat u een RBAC-rol toewijst aan een beveiligingsprincipal, bepaalt u het bereik van toegang dat de beveiligingsprincipal moet hebben. Aanbevolen procedures bepalen dat het altijd het beste is om alleen het smalle mogelijke bereik toe te kennen.

In de volgende lijst worden de niveaus beschreven waarmee u toegang tot Azure Blob-en wachtrij bronnen kunt bereiken, te beginnen met het smalle bereik:

- **Een afzonderlijke container.** In dit bereik is een roltoewijzing van toepassing op alle blobs in de container, evenals container eigenschappen en meta gegevens.
- **Een afzonderlijke wachtrij.** In dit bereik geldt een roltoewijzing voor berichten in de wachtrij, evenals wachtrij-eigenschappen en meta gegevens.
- **Het opslag account.** In dit bereik is een roltoewijzing van toepassing op alle containers en de bijbehorende blobs, of op alle wacht rijen en hun berichten.
- **De resourcegroep.** In dit bereik is een roltoewijzing van toepassing op alle containers of wacht rijen in alle opslag accounts in de resource groep.
- **Het abonnement.** In dit bereik is een roltoewijzing van toepassing op alle containers of wacht rijen in alle opslag accounts in alle resource groepen in het abonnement.
- **Een beheer groep.** In dit bereik is een roltoewijzing van toepassing op alle containers of wacht rijen in alle opslag accounts in alle-account groepen in alle abonnementen in de beheer groep.

Zie [Wat is Azure Role-based Access Control (Azure RBAC)?](../articles/role-based-access-control/overview.md)voor meer informatie over de toewijzingen en het bereik van RBAC-rollen.
