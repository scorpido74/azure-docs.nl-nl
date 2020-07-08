---
title: Regels voor de logicaverwerking in Azure Firewall
description: Meer informatie over het verwerken van logica voor Azure Firewall-regels
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 70026173d1cb932d30a59ea2b876ef22217a81bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563669"
---
# <a name="azure-firewall-rule-processing-logic"></a>Regels voor de logicaverwerking in Azure Firewall

Azure Firewall heeft NAT-regels, netwerk regels en toepassings regels. De regels worden verwerkt volgens het regel type.

## <a name="network-rules-and-applications-rules"></a>Netwerk regels en toepassings regels

Netwerk regels worden eerst toegepast, vervolgens toepassings regels. De regels worden afgesloten. Dus als er een overeenkomst wordt gevonden in netwerk regels, worden de toepassings regels niet verwerkt.  Als er geen netwerk regel overeenkomt en als het pakket protocol HTTP/HTTPS is, wordt het pakket vervolgens geëvalueerd op basis van de toepassings regels. Als er nog geen overeenkomst wordt gevonden, wordt het pakket vergeleken met de verzameling van de infrastructuur regel. Als er nog steeds geen overeenkomst is, wordt het pakket standaard geweigerd.

## <a name="nat-rules"></a>NAT-regels

Binnenkomende connectiviteit kan worden ingeschakeld door het configureren van DNAT (Destination Network Address Translation), zoals beschreven in [zelf studie: inkomend verkeer filteren met Azure firewall DNAT met behulp van de Azure Portal](../firewall/tutorial-firewall-dnat.md). DNAT-regels worden eerst toegepast. Als er een overeenkomst wordt gevonden, wordt er een impliciet overeenkomende netwerk regel toegevoegd om het vertaalde verkeer toe te staan. U kunt dit gedrag overschrijven door expliciet een verzameling netwerkregels toe te voegen met regels voor weigeren die overeenkomen met het omgezette verkeer. Er worden geen toepassings regels toegepast voor deze verbindingen.

## <a name="inherited-rules"></a>Overgenomen regels

Netwerk regel verzamelingen die zijn overgenomen van een bovenliggend beleid, worden altijd in prioriteit gegeven boven netwerk regel verzamelingen die zijn gedefinieerd als onderdeel van het nieuwe beleid. Dezelfde logica is ook van toepassing op toepassings regel verzamelingen. Netwerk regel verzamelingen worden echter altijd vóór de verzameling van toepassings regels verwerkt, ongeacht de overname.

Standaard neemt uw beleid de bovenliggende beleids bedreigings informatie modus over. U kunt dit negeren door de modus voor bedreigings informatie in te stellen op een andere waarde op de pagina beleids instellingen. Het is alleen mogelijk om te overschrijven met een striktere waarde. Als u bijvoorbeeld bovenliggend beleid is ingesteld op *waarschuwing*, kunt u dit lokale beleid configureren om te *waarschuwen en weigeren*, maar u kunt het niet uitschakelen.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure Firewall Manager](overview.md)