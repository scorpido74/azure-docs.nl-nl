---
title: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: 96d8100f2cffcfb001a693575128ce19e742225d
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534126"
---
Voordat u een Azure-rol toewijst een beveiligingsprincipal, moet u het toegangsbereik bepalen dat de beveiligingsprincipal moet hebben. Uit best practices blijkt dat het het beste is om het nauwst mogelijke bereik toe te wijzen.

In de volgende lijst worden de niveaus beschreven waarop u toegang tot Azure-blob en -wachtrijresources kunt bepalen, beginnend met het kleinste bereik:

- **Een afzonderlijke container.** In dit bereik is een roltoewijzing van toepassing op alle blobs in de container, evenals containereigenschappen en metagegevens.
- **Een afzonderlijke wachtrij.** In dit bereik is een roltoewijzing van toepassing op berichten in de wachtrij, evenals op wachtrij-eigenschappen en metagegevens.
- **Het opslagaccount.** In dit bereik is een roltoewijzing van toepassing op alle containers en de bijbehorende blobs, of op alle wachtrijen en hun berichten.
- **De resourcegroep.** In dit bereik is een roltoewijzing van toepassing op alle containers of wachtrijen in alle opslagaccounts in de resourcegroep.
- **Het abonnement.** In dit bereik is een roltoewijzing van toepassing op alle containers of wachtrijen in alle opslagaccounts in alle resourcegroepen in het abonnement.
- **Een beheergroep.** In dit bereik is een roltoewijzing van toepassing op alle containers of wachtrijen in alle opslagaccounts in alle resourcegroepen in alle abonnementen van de beheergroep.

Raadpleeg [Wat is op rollen gebaseerd toegangsbeheer in Azure (Azure RBAC)?](../articles/role-based-access-control/overview.md) voor meer informatie over Azure-roltoewijzingen.
