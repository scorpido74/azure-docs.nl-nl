---
title: Regels voor de logicaverwerking in Azure Firewall
description: Azure Firewall heeft NAT-regels, netwerk regels en toepassings regels. De regels worden verwerkt volgens het regel type.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2018
ms.author: victorh
ms.openlocfilehash: 0fc11221e0ff79d6e17b93282403792fc135c2a4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166789"
---
# <a name="azure-firewall-rule-processing-logic"></a>Regels voor de logicaverwerking in Azure Firewall
Azure Firewall heeft NAT-regels, netwerk regels en toepassings regels. De regels worden verwerkt volgens het regel type.


## <a name="network-rules-and-applications-rules"></a>Netwerk regels en toepassings regels 
Netwerk regels worden eerst toegepast, vervolgens toepassings regels. De regels worden afgesloten. Dus als er een overeenkomst wordt gevonden in netwerk regels, worden de toepassings regels niet verwerkt.  Als er geen overeenkomst met de netwerk regel is en als het pakket protocol HTTP/HTTPS is, wordt het pakket vervolgens geëvalueerd op basis van de toepassings regels. Als er nog geen overeenkomst wordt gevonden, wordt het pakket vergeleken met de verzameling van de infrastructuur regel. Als er dan nog steeds geen overeenkomende regel is, wordt het pakket standaard afgewezen.

## <a name="nat-rules"></a>NAT-regels
Binnenkomende connectiviteit kan worden ingeschakeld door het configureren van DNAT (Destination Network Address Translation), zoals beschreven in [zelf studie: inkomend verkeer filteren met Azure firewall DNAT met behulp van de Azure Portal](tutorial-firewall-dnat.md). DNAT-regels worden eerst toegepast. Als er een overeenkomst wordt gevonden, wordt er een impliciet overeenkomende netwerk regel toegevoegd om het vertaalde verkeer toe te staan. U kunt dit gedrag overschrijven door expliciet een verzameling netwerkregels toe te voegen met regels voor weigeren die overeenkomen met het omgezette verkeer. Er worden geen toepassings regels toegepast voor deze verbindingen.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren en configureren van een Azure firewall](tutorial-firewall-deploy-portal.md).