---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544780"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Als uw gegevens archief zich in een on-premises netwerk, een virtueel Azure-netwerk of een virtuele particuliere cloud van Amazon bevindt, moet u een [zelf-hostende Integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) instellen om er verbinding mee te maken.

Als uw gegevens archief een beheerde Cloud gegevens service is, waarbij de toegang wordt beperkt tot IP-adressen die worden white list in de firewall regels, kunt u Azure Integration runtime gebruiken en [de bijbehorende IP-adressen](../articles/data-factory/azure-integration-runtime-ip-addresses.md) toevoegen aan de acceptatie lijst. 

Zie [strategieën voor gegevens toegang](../articles/data-factory/data-access-strategies.md) voor informatie over de netwerk beveiligings mechanismen die door Data Factory worden ondersteund voor toegang tot gegevens archieven in het algemeen.
