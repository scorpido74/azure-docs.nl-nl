---
title: Regels voor de logicaverwerking in Azure Firewall
description: Meer informatie over de logica voor het verwerken van azure firewall-regels
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518201"
---
# <a name="azure-firewall-rule-processing-logic"></a>Regels voor de logicaverwerking in Azure Firewall

Azure Firewall heeft NAT-regels, netwerkregels en toepassingenregels. De regels worden verwerkt op basis van het regeltype.

## <a name="network-rules-and-applications-rules"></a>Regels voor netwerkregels en toepassingen

Netwerkregels worden eerst toegepast, daarna toepassingsregels. De regels lopen af. Dus als een overeenkomst wordt gevonden in netwerkregels, worden toepassingsregels niet verwerkt.  Als er geen netwerkregelovereenkomst is en als het pakketprotocol HTTP/HTTPS is, wordt het pakket vervolgens geëvalueerd door de toepassingsregels. Als er nog steeds geen overeenkomst wordt gevonden, wordt het pakket geëvalueerd aan de hand van de verzameling infrastructuurregels. Als er dan nog steeds geen overeenkomende regel is, wordt het pakket standaard afgewezen.

## <a name="nat-rules"></a>NAT-regels

Binnenkomende connectiviteit kan worden ingeschakeld door de vertaling van het doelnetwerkadres (DNAT) te configureren zoals beschreven in [Zelfstudie: Filter binnenkomend verkeer met Azure Firewall DNAT met behulp van de Azure-portal.](../firewall/tutorial-firewall-dnat.md) DNAT-regels worden eerst toegepast. Als er een overeenkomst wordt gevonden, wordt een impliciete bijbehorende netwerkregel toegevoegd om het vertaalde verkeer toe te staan. U kunt dit gedrag overschrijven door expliciet een verzameling netwerkregels toe te voegen met regels voor weigeren die overeenkomen met het omgezette verkeer. Voor deze verbindingen worden geen toepassingsregels toegepast.

## <a name="inherited-rules"></a>Overgenomen regels

Netwerkregelverzamelingen die zijn overgenomen van een bovenliggend beleid, worden altijd geprioriteerd boven netwerkregelverzamelingen die zijn gedefinieerd als onderdeel van uw nieuwe beleid. Dezelfde logica is ook van toepassing op verzamelingen van toepassingsregels. Verzamelingen van netwerkregels worden echter altijd verwerkt vóór verzamelingen van toepassingsregels, ongeacht overerving.

Standaard erft uw beleid de modus voor bedreigingsinformatie voor het bovenliggende beleid. U dit overschrijven door de modus Bedreigingsinformatie in te stellen op een andere waarde op de pagina beleidsinstellingen. Het is alleen mogelijk om te overschrijven met een strengere waarde. Als u bijvoorbeeld alleen is ingesteld op *Waarschuwing,* u dit lokale beleid configureren op *Waarschuwen en weigeren,* maar u het niet uitschakelen.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure Firewall Manager Preview](overview.md)