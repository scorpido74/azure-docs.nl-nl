---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529382"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Als uw gegevensarchief zich in een on-premises netwerk, een virtueel Azure-netwerk of een virtuele particuliere cloud van Amazon bevindt, moet u een [zelf-hostende Integration Runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) instellen om er verbinding mee te maken.

Als uw gegevensarchief een beheerde cloudgegevensservice is, kunt u Azure Integration Runtime gebruiken. Als de toegang is beperkt tot IP-adressen op de goedgekeurde lijst in de firewallregels, kunt u ervoor kiezen om de [IP-adressen van Azure Integration Runtime](../articles/data-factory/azure-integration-runtime-ip-addresses.md) toe te voegen aan de acceptatielijst. 

Zie [Strategieën voor gegevenstoegang](../articles/data-factory/data-access-strategies.md) voor meer informatie over de netwerkbeveiligingsmechanismen en -opties die door Data Factory worden ondersteund.
