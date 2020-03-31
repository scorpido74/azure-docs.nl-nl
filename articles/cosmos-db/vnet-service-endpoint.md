---
title: Beveiligde toegang tot Azure Cosmos DB-accounts met behulp van eindpunt van de virtuele netwerkservice
description: In dit document wordt beschreven over virtueel netwerk- en subnettoegangscontrole voor een Azure Cosmos-account.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: af1032de9aabac45ad7a86cfe1f36ed2c04c0f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444625"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Toegang tot Azure Cosmos DB vanuit virtuele netwerken (VNet)

U het Azure Cosmos-account configureren om alleen toegang toe te staan vanaf een specifiek subnet van virtueel netwerk (VNet). Door [serviceeindpunt](../virtual-network/virtual-network-service-endpoints-overview.md) toegang te geven tot Azure Cosmos DB op het subnet binnen een virtueel netwerk, wordt het verkeer van dat subnet verzonden naar Azure Cosmos DB met de identiteit van het subnet en virtueel netwerk. Zodra het eindpunt van de Azure Cosmos DB-service is ingeschakeld, u de toegang tot het subnet beperken door het toe te voegen aan uw Azure Cosmos-account.

Standaard is een Azure Cosmos-account toegankelijk vanaf elke bron als de aanvraag vergezeld gaat van een geldig autorisatietoken. Wanneer u een of meer subnetten toevoegt binnen VNets, krijgen alleen aanvragen die afkomstig zijn van deze subnetten een geldig antwoord. Verzoeken afkomstig van een andere bron ontvangen een reactie van 403 (Verboden). 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

Hier volgen enkele veelgestelde vragen over het configureren van toegang via virtuele netwerken:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Kan ik zowel het eindpunt van de virtuele netwerkservice als het IP-toegangscontrolebeleid opgeven op een Azure Cosmos-account? 

U zowel het eindpunt van de virtuele netwerkservice als een IP-toegangsbeheerbeleid (ook wel firewall) inschakelen op uw Azure Cosmos-account. Deze twee functies zijn complementair en zorgen gezamenlijk voor isolatie en beveiliging van uw Azure Cosmos-account. Het gebruik van IP-firewall zorgt ervoor dat statische IP's toegang hebben tot uw account. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Hoe beperk ik de toegang tot subnet binnen een virtueel netwerk? 

Er zijn twee stappen nodig om de toegang tot azure cosmos-account vanaf een subnet te beperken. Eerst u verkeer van subnet toestaan om de subnet- en virtuele netwerkidentiteit naar Azure Cosmos DB te brengen. Dit wordt gedaan door serviceeindpunt voor Azure Cosmos DB in te schakelen op het subnet. Volgende is het toevoegen van een regel in het Azure Cosmos-account waarin dit subnet wordt opgegeven als een bron waaruit het account kan worden geopend.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Worden aanvragen of verbindingen voor virtuele netwerk-ACL's en IP Firewall afgewezen? 

Wanneer IP-firewall- of virtuele netwerktoegangsregels worden toegevoegd, krijgen alleen verzoeken van toegestane bronnen geldige antwoorden. Andere verzoeken worden afgewezen met een 403 (Verboden). Het is belangrijk om de firewall van azure cosmos-account te onderscheiden van een firewall op verbindingsniveau. De bron kan nog steeds verbinding maken met de service en de verbindingen zelf worden niet geweigerd.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Mijn aanvragen begonnen geblokkeerd te raken toen ik serviceeindpunt inschakelt naar Azure Cosmos DB op het subnet. Wat is er gebeurd?

Zodra serviceeindpunt voor Azure Cosmos DB is ingeschakeld op een subnet, schakelt de bron van het verkeer dat het account bereikt over van openbaar IP naar virtueel netwerk en subnet. Als uw Azure Cosmos-account alleen een IP-gebaseerde firewall heeft, komt verkeer van subnet met serviceservice niet langer overeen met de IP-firewallregels en wordt daarom geweigerd. Bekijk de stappen om naadloos te migreren van IP-gebaseerde firewall naar virtueel netwerkgebaseerdtoegangsbeheer.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Zijn er extra RBAC-machtigingen nodig voor Azure Cosmos-accounts met VNET-serviceeindpunten?

Nadat u de VNet-serviceeindpunten hebt toegevoegd aan een Azure Cosmos-account om `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` wijzigingen aan te brengen in de accountinstellingen, hebt u toegang nodig tot de actie voor alle VNET's die zijn geconfigureerd op uw Azure Cosmos-account. Deze toestemming is vereist omdat het autorisatieproces de toegang tot bronnen (zoals database- en virtuele netwerkbronnen) valideert voordat de eigenschappen worden geëvalueerd.
 
De autorisatie valideert de machtiging voor VNet-bronactie, zelfs als de gebruiker de VNET-ACL's met Azure CLI niet opgeeft. Momenteel ondersteunt het controlevlak van het Azure Cosmos-account het instellen van de volledige status van het Azure Cosmos-account. Een van de parameters voor `virtualNetworkRules`de aanroepen van het controlevlak is . Als deze parameter niet is opgegeven, voert de Azure `virtualNetworkRules` CLI een aanroep voor de get-database om de waarde op te halen en gebruikt deze waarde in de update-oproep.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Hebben de peered virtuele netwerken ook toegang tot azure cosmos-account? 
Alleen virtuele netwerken en hun subnetten die zijn toegevoegd aan het Azure Cosmos-account hebben toegang. Hun peered VNets hebben geen toegang tot het account totdat de subnetten binnen peered virtuele netwerken zijn toegevoegd aan het account.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Wat is het maximum aantal subnetten dat toegang heeft tot één Cosmos-account? 
Momenteel u maximaal 64 subnetten hebben die zijn toegestaan voor een Azure Cosmos-account.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Kan ik toegang inschakelen via VPN en Express Route? 
Voor toegang tot Azure Cosmos-account via Express-route van on-premises, moet u Microsoft-peering inschakelen. Zodra u IP-firewall- of virtuele netwerktoegangsregels hebt geplaatst, u de openbare IP-adressen die worden gebruikt voor Microsoft-peering toevoegen op uw IP-firewall voor Azure Cosmos-account om on-premises services toegang te geven tot Azure Cosmos-account. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Moet ik de NSG-regels (Network Security Groups) bijwerken? 
NSG-regels worden gebruikt om de connectiviteit van en naar een subnet met virtueel netwerk te beperken. Wanneer u serviceeindpunt voor Azure Cosmos DB aan het subnet toevoegt, hoeft u geen uitgaande connectiviteit in NSG te openen voor uw Azure Cosmos-account. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Zijn serviceeindpunten beschikbaar voor alle VNets?
Nee, alleen virtuele netwerken van Azure Resource Manager kunnen serviceeindpunt hebben ingeschakeld. Klassieke virtuele netwerken ondersteunen geen serviceeindpunten.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Kan ik 'Verbindingen accepteren vanuit openbare Azure-datacenters' wanneer serviceeindpunttoegang is ingeschakeld voor Azure Cosmos DB?  
Dit is alleen vereist wanneer u wilt dat uw Azure Cosmos DB-account wordt geopend door andere Azure first party-services zoals Azure Data factory, Azure Cognitive Search of een service die wordt geïmplementeerd in een bepaalde Azure-regio.


## <a name="next-steps"></a>Volgende stappen

* [Azure Cosmos-accounttoegang beperken tot subnet(s) binnen virtuele netwerken](how-to-configure-vnet-service-endpoint.md)
* [IP-firewall configureren voor uw Azure Cosmos-account](how-to-configure-firewall.md)

