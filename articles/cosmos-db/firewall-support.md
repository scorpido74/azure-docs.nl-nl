---
title: IP-Firewall voor Azure Cosmos-accounts
description: Meer informatie over het beveiligen van Azure Cosmos DB gegevens met behulp van IP-toegangs beheer beleid voor firewall ondersteuning.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "66241085"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>IP-firewall in Azure Cosmos DB

Voor het beveiligen van gegevens die zijn opgeslagen in uw account, ondersteunt Azure Cosmos DB een geheim autorisatie model dat gebruikmaakt van een sterk op hash gebaseerde Message Authentication Code (HMAC). Daarnaast ondersteunt Azure Cosmos DB ondersteuning voor toegangs beheer op basis van IP-adressen voor binnenkomende firewalls. Dit model is vergelijkbaar met de firewall regels van een traditioneel database systeem en biedt een extra beveiligings niveau voor uw account. Met firewalls kunt u uw Azure Cosmos-account zodanig configureren dat dit alleen toegankelijk is vanuit een goedgekeurde set machines en/of Cloud Services. Voor toegang tot gegevens die zijn opgeslagen in uw Azure Cosmos-Data Base op basis van deze goedgekeurde sets van machines en services, moet de aanroeper nog steeds een geldig autorisatie token presen teren.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>Overzicht van IP-toegangs beheer

Uw Azure Cosmos-account is standaard toegankelijk via internet, zolang de aanvraag vergezeld gaat van een geldig autorisatie token. Voor het configureren van toegangs beheer op basis van IP-beleid, moet de gebruiker de set IP-adressen of IP-adresbereiken in CIDR (Klasseloze route ring van het ene domein) opgeven die moet worden opgenomen als de lijst met toegestane client-Ip's om toegang te krijgen tot een opgegeven Azure Cosmos-account. Zodra deze configuratie is toegepast, ontvangen aanvragen die afkomstig zijn van computers buiten deze toegestane lijst, het antwoord 403 (verboden). Wanneer u IP-firewall gebruikt, is het raadzaam om Azure Portal toegang te geven tot uw account. Toegang is vereist om het gebruik van Data Explorer toe te staan en om de metrische gegevens voor uw account op te halen die worden weer gegeven op de Azure Portal. Wanneer u Data Explorer gebruikt, moet u niet alleen Azure Portal toegang geven tot uw account, maar ook uw firewall instellingen bijwerken om uw huidige IP-adres toe te voegen aan de firewall regels. Houd er rekening mee dat de wijzigingen van de firewall kunnen worden doorgevoerd in 15min. 

U kunt op IP gebaseerde firewalls combi neren met toegangs beheer voor subnetten en VNET. Door ze te combi neren, kunt u de toegang tot een bron met een openbaar IP-adres en/of van een specifiek subnet in VNET beperken. Zie [toegang Azure Cosmos DB bronnen vanuit virtuele netwerken](vnet-service-endpoint.md)voor meer informatie over het gebruik van het subnet en op VNET gebaseerd toegangs beheer.

Om samen te vatten is het autorisatie token altijd vereist voor toegang tot een Azure Cosmos-account. Als de IP-firewall en de VNET-Access Control lijst (Acl's) niet zijn ingesteld, is het Azure Cosmos-account toegankelijk met het autorisatie token. Nadat de IP-firewall-of VNET-Acl's of beide zijn ingesteld op het Azure Cosmos-account, krijgen alleen aanvragen die afkomstig zijn van de bronnen die u hebt opgegeven (en met het autorisatie token) geldige reacties. 

## <a name="next-steps"></a>Volgende stappen

Vervolgens kunt u de IP-firewall of het VNET-service-eind punt voor uw account configureren met behulp van de volgende documenten:

* [IP-Firewall configureren voor uw Azure Cosmos-account](how-to-configure-firewall.md)
* [Toegang tot Azure Cosmos DB bronnen vanuit virtuele netwerken](vnet-service-endpoint.md)
* [Service-eind punten voor virtuele netwerken configureren voor uw Azure Cosmos-account](how-to-configure-vnet-service-endpoint.md)




