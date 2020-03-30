---
title: Overzicht van Azure Firewall-servicetags
description: Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels die het maken van beveiligingsregel vereenvoudigt.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168687"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall-servicetags

Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels die het maken van beveiligingsregel vereenvoudigt. U kunt niet uw eigen servicetag maken en ook niet opgeven welke IP-adressen zijn opgenomen in een tag. Microsoft beheert de adresvoorvoegsels die de servicetag omvat en werkt de servicetag automatisch bij wanneer adressen veranderen.

Azure Firewall-servicetags kunnen worden gebruikt in het doelveld netwerkregels. U ze gebruiken in plaats van specifieke IP-adressen.

## <a name="supported-service-tags"></a>Ondersteunde servicetags

Zie [Beveiligingsgroepen](../virtual-network/security-overview.md#service-tags) voor een lijst met servicetags die beschikbaar zijn voor gebruik in Azure-firewallnetwerkregels.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Firewall-regelverwerkingslogica voor](rule-processing.md)meer informatie over Azure Firewall-regels.