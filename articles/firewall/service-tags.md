---
title: Overzicht van Azure Firewall-service Tags
description: Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels die het maken van beveiligingsregel vereenvoudigt.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74168687"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall-service Tags

Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels die het maken van beveiligingsregel vereenvoudigt. U kunt niet uw eigen servicetag maken en ook niet opgeven welke IP-adressen zijn opgenomen in een tag. Microsoft beheert de adresvoorvoegsels die de servicetag omvat en werkt de servicetag automatisch bij wanneer adressen veranderen.

Azure Firewall service tags kunnen worden gebruikt in het doel veld netwerk regels. U kunt deze gebruiken in plaats van specifieke IP-adressen.

## <a name="supported-service-tags"></a>Ondersteunde service Tags

Zie [beveiligings groepen](../virtual-network/security-overview.md#service-tags) voor een lijst met Service tags die beschikbaar zijn voor gebruik in azure firewall-netwerk regels.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure firewall logica voor regel verwerking](rule-processing.md)voor meer informatie over Azure firewall-regels.