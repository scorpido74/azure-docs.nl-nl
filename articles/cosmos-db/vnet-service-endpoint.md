---
title: Toegang tot Azure Cosmos DB accounts beveiligen met behulp van het service-eind punt van de virtuele netwerk
description: Dit document bevat informatie over het beheer van virtuele netwerken en subnetten voor een Azure Cosmos-account.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d264ead87e7fa638830bf25fdb07983b164334b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83698660"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Toegang tot Azure Cosmos DB vanuit virtuele netwerken (VNet)

U kunt het Azure Cosmos-account configureren om alleen toegang toe te staan vanuit een specifiek subnet van een virtueel netwerk (VNet). Door [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) in te scha kelen voor toegang tot Azure Cosmos DB op het subnet binnen een virtueel netwerk, wordt het verkeer van dat subnet verzonden naar Azure Cosmos DB met de identiteit van het subnet en Virtual Network. Zodra het eind punt van de Azure Cosmos DB service is ingeschakeld, kunt u de toegang tot het subnet beperken door het toe te voegen aan uw Azure Cosmos-account.

Een Azure Cosmos-account is standaard toegankelijk vanuit elke bron als de aanvraag vergezeld gaat van een geldig autorisatie token. Wanneer u een of meer subnetten in VNets toevoegt, krijgen alleen aanvragen die afkomstig zijn van deze subnetten een geldig antwoord. Aanvragen die afkomstig zijn van een andere bron, ontvangen een antwoord van 403 (verboden). 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

Hier volgen enkele veelgestelde vragen over het configureren van toegang vanaf virtuele netwerken:

### <a name="are-notebooks-and-mongo-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Zijn momenteel notebooks en Mongo shell compatibel met Virtual Network ingeschakelde accounts?

Op het moment dat de [integratie van de Mongo-shell in de Cosmos DB Data Explorer](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) en de [Jupyter-notitieblok service](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-jupyter-notebooks) wordt niet ondersteund met VNET-toegang. Dit is momenteel actief in ontwikkeling.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Kan ik zowel het virtuele netwerk service-eind punt als het IP-toegangs beheer beleid voor een Azure Cosmos-account opgeven? 

U kunt zowel het service-eind punt van de virtuele netwerk als een IP-toegangs beheer beleid (ook wel-firewall) inschakelen voor uw Azure Cosmos-account. Deze twee functies zijn complementair en samen zorgen voor isolatie en beveiliging van uw Azure Cosmos-account. Met IP-firewall weet u zeker dat statische IP-adressen toegang hebben tot uw account. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Hoe kan ik de toegang tot subnet binnen een virtueel netwerk beperken? 

Er zijn twee stappen vereist om de toegang tot het Azure Cosmos-account te beperken vanuit een subnet. Eerst staat u verkeer van subnet toe om de identiteit van het subnet en het virtuele netwerk te Azure Cosmos DB. Dit wordt gedaan door het inschakelen van service-eind punten voor Azure Cosmos DB in het subnet. Vervolgens wordt een regel toegevoegd aan het Azure Cosmos-account dat dit subnet specificeert als bron van welk account toegang kan worden verkregen.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Worden door Acl's van virtuele netwerken en IP-firewall aanvragen of verbindingen geweigerd? 

Wanneer IP-firewall of toegangs regels voor virtuele netwerken worden toegevoegd, krijgen alleen aanvragen van toegestane bronnen geldige reacties. Andere aanvragen worden afgewezen met een 403 (verboden). Het is belang rijk om de firewall van een Azure Cosmos-account te onderscheiden van een firewall op verbindings niveau. De bron kan nog steeds verbinding maken met de service en de verbindingen zelf worden niet geweigerd.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Mijn aanvragen die zijn gestart, worden geblokkeerd wanneer het service-eind punt is ingeschakeld voor Azure Cosmos DB op het subnet. Wat is er gebeurd?

Zodra het service-eind punt voor Azure Cosmos DB op een subnet is ingeschakeld, wordt de bron van het verkeer dat de account overschakelt van het open bare IP-adres naar het virtuele netwerk en subnet. Als uw Azure Cosmos-account alleen een IP-firewall heeft, komt het verkeer van het subnet waarvoor de service is ingeschakeld niet meer overeen met de IP-firewall regels en wordt daarom geweigerd. Lees de stappen voor probleemloze migratie van een firewall op basis van een IP-adres naar het toegangs beheer via een virtueel netwerk.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Zijn er extra RBAC-machtigingen nodig voor Azure Cosmos-accounts met de VNET-service-eind punten?

Nadat u de VNet-service-eind punten aan een Azure Cosmos-account hebt toegevoegd, moet u toegang hebben tot de `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` actie voor alle VNETs die zijn geconfigureerd voor uw Azure Cosmos-account om wijzigingen aan te brengen in de account instellingen. Deze machtiging is vereist omdat het autorisatie proces de toegang tot bronnen (zoals de resources van de data base en het virtuele netwerk) valideert voordat de eigenschappen worden geëvalueerd.
 
De autorisatie valideert de machtiging voor de VNet-resource actie, zelfs als de gebruiker niet de VNET-Acl's opgeeft met behulp van Azure CLI. Op dit moment ondersteunt het besturings vlak van het Azure Cosmos-account de voltooiings status van het Azure Cosmos-account. Een van de para meters voor de besturings vlak-aanroepen is `virtualNetworkRules` . Als deze para meter niet wordt opgegeven, wordt met de Azure CLI een Get data base-aanroep opgehaald `virtualNetworkRules` en wordt deze waarde in de update aanroep gebruikt.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Hebben de gekoppelde virtuele netwerken ook toegang tot het Azure Cosmos-account? 
Alleen virtuele netwerken en hun subnetten die zijn toegevoegd aan het Azure Cosmos-account hebben toegang. Hun peered VNets heeft geen toegang tot het account totdat de subnetten in gekoppelde virtuele netwerken aan het account worden toegevoegd.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Wat is het maximum aantal subnetten dat is toegestaan voor toegang tot één Cosmos-account? 
Op dit moment kunt u Maxi maal 256 subnetten hebben die zijn toegestaan voor een Azure Cosmos-account.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Kan ik de toegang vanaf VPN en Express route inschakelen? 
Voor toegang tot het Azure Cosmos-account via Express route van on-premises moet u micro soft-peering inschakelen. Zodra u de toegangs regels voor de IP-firewall of het virtuele netwerk hebt ingesteld, kunt u de open bare IP-adressen die worden gebruikt voor micro soft-peering toevoegen aan de IP-firewall van uw Azure Cosmos-account, zodat lokale services toegang hebben tot het Azure Cosmos-account. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Moet ik de regels voor netwerk beveiligings groepen (NSG) bijwerken? 
NSG regels worden gebruikt om de verbinding met en van een subnet met een virtueel netwerk te beperken. Wanneer u een service-eind punt voor Azure Cosmos DB aan het subnet toevoegt, is het niet nodig om uitgaande connectiviteit in NSG te openen voor uw Azure Cosmos-account. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Zijn service-eind punten beschikbaar voor alle VNets?
Nee, alleen Azure Resource Manager virtuele netwerken kunnen service-eind punten hebben ingeschakeld. Klassieke virtuele netwerken bieden geen ondersteuning voor service-eind punten.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Kan ik ' verbindingen accepteren vanuit open bare Azure-data centers ' wanneer toegang tot het service-eind punt is ingeschakeld voor Azure Cosmos DB?  
Dit is alleen vereist als u wilt dat uw Azure Cosmos DB-account wordt geopend door andere Azure-Services voor de eerste partij, zoals Azure Data Factory, Azure Cognitive Search of een service die in de opgegeven Azure-regio is geïmplementeerd.


## <a name="next-steps"></a>Volgende stappen

* [Toegang tot subnet (s) met een Azure Cosmos-account in virtuele netwerken beperken](how-to-configure-vnet-service-endpoint.md)
* [IP-Firewall configureren voor uw Azure Cosmos-account](how-to-configure-firewall.md)

