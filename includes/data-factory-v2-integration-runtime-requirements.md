---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629477"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Als uw gegevens archief op een van de volgende manieren is geconfigureerd, moet u een [zelf-hostende Integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) instellen om verbinding te maken met het gegevens archief:

- Het gegevens archief bevindt zich in een on-premises netwerk, binnen een virtueel Azure-netwerk of in de virtuele particuliere cloud van Amazon.
- Het gegevens archief is een beheerde Cloud gegevens service waarbij de toegang wordt beperkt tot IP-adressen die worden white list in de firewall regels.
