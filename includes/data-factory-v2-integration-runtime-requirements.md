---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68966368"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Als uw gegevensarchief op een van de volgende manieren is geconfigureerd, moet u een [self-hosted Integration Runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) instellen om verbinding te maken met dit gegevensarchief:

- Het gegevensarchief bevindt zich in een on-premises netwerk, in Azure Virtual Network of in Amazon Virtual Private Cloud.
- Het gegevensarchief is een beheerde cloudgegevensservice waarbij de toegang beperkt is tot IP's die op de witte lijst staan in de firewallregels.
