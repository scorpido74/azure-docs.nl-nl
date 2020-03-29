---
title: IP-firewall voor Azure Cosmos-accounts
description: Meer informatie over het beveiligen van Azure Cosmos DB-gegevens met behulp van IP-toegangscontrolebeleid voor firewallondersteuning.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241085"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>IP-firewall in Azure Cosmos DB

Azure Cosmos DB ondersteunt een geheim autorisatiemodel dat gebruikmaakt van een sterke Hash-gebaseerde Message Authentication Code (HMAC). Daarnaast ondersteunt Azure Cosmos DB IP-gebaseerde toegangscontroles voor inkomende firewallondersteuning. Dit model is vergelijkbaar met de firewallregels van een traditioneel databasesysteem en biedt een extra beveiligingsniveau voor uw account. Met firewalls u instellen dat uw Azure Cosmos-account alleen toegankelijk is vanaf een goedgekeurde set machines en/of cloudservices. Toegang tot gegevens die zijn opgeslagen in uw Azure Cosmos-database vanuit deze goedgekeurde sets machines en services vereist nog steeds dat de beller een geldig autorisatietoken moet presenteren.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>Overzicht van IP-toegangscontrole

Standaard is uw Azure Cosmos-account toegankelijk vanaf internet, zolang de aanvraag vergezeld gaat van een geldig autorisatietoken. Om IP-beleidsgebaseerde toegangscontrole te configureren, moet de gebruiker de set IP-adressen of IP-adresbereiken opgeven in cidr-formulier (Classless Inter-Domain Routing) om te worden opgenomen als de toegestane lijst met client-IP-adressen om toegang te krijgen tot een bepaald Azure Cosmos-account. Zodra deze configuratie is toegepast, ontvangen alle aanvragen die afkomstig zijn van machines buiten deze toegestane lijst 403 (Verboden) respons. Bij het gebruik van IP-firewall wordt aanbevolen om Azure-portal toegang te geven tot uw account. Toegang is vereist om het gebruik van data explorer toe te staan en om statistieken op te halen voor uw account die worden weergegeven op de Azure-portal. Wanneer u data explorer gebruikt, moet u niet alleen azure portal toestaan om toegang te krijgen tot uw account, maar ook uw firewall-instellingen bijwerken om uw huidige IP-adres toe te voegen aan de firewallregels. Houd er rekening mee dat het tot 15 min kan duren voordat firewallwijzigingen worden doorgevoerd. 

U IP-gebaseerde firewall combineren met subnet en VNET access control. Door ze te combineren, u de toegang beperken tot elke bron die een openbaar IP en/of van een specifiek subnet binnen VNET heeft. Zie [Toegang tot Azure Cosmos DB-bronnen van virtuele netwerken](vnet-service-endpoint.md)voor meer informatie over het gebruik van subnet en VNET-gebaseerde toegangscontrole.

Om samen te vatten dat autorisatietoken altijd vereist is om toegang te krijgen tot een Azure Cosmos-account. Als IP-firewall en VNET Access Control List (ACL's) niet zijn ingesteld, kan het Azure Cosmos-account worden geopend met het autorisatietoken. Nadat de IP-firewall of VNET-ACL's of beide zijn ingesteld op het Azure Cosmos-account, krijgen alleen aanvragen afkomstig van de bronnen die u hebt opgegeven (en met het autorisatietoken) geldige antwoorden. 

## <a name="next-steps"></a>Volgende stappen

Vervolgens u IP-firewall of VNET-serviceeindpunt voor uw account configureren met behulp van de volgende documenten:

* [IP-firewall configureren voor uw Azure Cosmos-account](how-to-configure-firewall.md)
* [Toegang tot Azure Cosmos DB-bronnen via virtuele netwerken](vnet-service-endpoint.md)
* [Eindpunt van virtuele netwerkservice configureren voor uw Azure Cosmos-account](how-to-configure-vnet-service-endpoint.md)




