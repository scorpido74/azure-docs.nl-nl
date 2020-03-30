---
title: 'Azure ExpressRoute: Peering configureren: CLI'
description: In dit artikel u de private, publieke en Microsoft-peering van een ExpressRoute-circuit maken en inrichten. In dit artikel leest u hoe u de status controleert en peerings voor uw circuit bijwerkt of verwijdert.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 91a1b6cc877b31fbcef638e34d3147d3377ce85c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476114"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Peering voor een ExpressRoute-circuit maken en wijzigen met CLI

Met dit artikel u routeringsconfiguratie/-peering voor een ExpressRoute-circuit maken en beheren in het implementatiemodel resourcebeheer met CLI. U ook de status controleren, bijwerken of peerings verwijderen en deprovisionen voor een ExpressRoute-circuit. Als u een andere methode wilt gebruiken om met uw circuit te werken, selecteert u een artikel in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Powershell](expressroute-howto-routing-arm.md)
> * [Azure-CLI](howto-routing-cli.md)
> * [Openbare peering](about-public-peering.md)
> * [Video - Privé-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Configuratievereisten

* Installeer eerst de meest recente versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli) voor meer informatie over het installeren van de CLI-opdrachten.
* Zorg ervoor dat u de [vereisten,](expressroute-prerequisites.md) [routeringsvereisten](expressroute-routing.md)en [werkstroompagina's](expressroute-workflows.md) hebt gecontroleerd voordat u met de configuratie begint.
* U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet in een ingerichte en ingeschakelde staat staan om de opdrachten in dit artikel uit te kunnen voeren.

Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met Laag-2-connectiviteit aanbieden. Als u een serviceprovider gebruikt die beheerde Layer 3-services aanbiedt (meestal een IPVPN, zoals MPLS), configureert en beheert uw connectiviteitsprovider de routering voor u.

U private peering en Microsoft-peering configureren voor een ExpressRoute-circuit (Azure public peering is afgeschaft voor nieuwe circuits). Peerings kunnen worden geconfigureerd in elke volgorde die u kiest. U moet er echter wel voor zorgen dat u de configuratie van elke peering een voor een voltooit. Zie [Routeringsdomeinen expressroute](expressroute-circuit-peerings.md)voor meer informatie over routeringsdomeinen en peerings. Zie [ExpressRoute public peering](about-public-peering.md)voor informatie over openbare peering.

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-peering

Met deze sectie u de Microsoft-peeringconfiguratie voor een ExpressRoute-circuit maken, ontvangen, bijwerken en verwijderen.

> [!IMPORTANT]
> Microsoft-peering van ExpressRoute-circuits die vóór 1 augustus 2017 zijn geconfigureerd, hebben alle servicevoorvoegsels die worden geadverteerd via de Microsoft-peering, zelfs als routefilters niet zijn gedefinieerd. Microsoft-peering van ExpressRoute-circuits die op of na 1 augustus 2017 zijn geconfigureerd, worden pas geadverteerd als er een routefilter aan het circuit is gekoppeld. Zie [Een routefilter configureren voor Microsoft-peering voor](how-to-routefilter-powershell.md)meer informatie.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft-peering maken

1. Installeer de nieuwste versie van Azure CLI. Gebruik de nieuwste versie van de Azure Command-line Interface (CLI). Bekijk de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u met de configuratie begint.

   ```azurecli
   az login
   ```

   Selecteer het abonnement waarvoor u het ExpressRoute-circuit wilt maken.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Maak een ExpressRoute-circuit. Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteitsprovider beheerde Layer 3-services aanbiedt, u uw connectiviteitsprovider vragen microsoft-peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteitsprovider echter geen routering voor u beheert, gaat u na het maken van uw circuit door met uw configuratie met behulp van de volgende stappen. 

3. Controleer het ExpressRoute-circuit om te controleren of het is ingericht en ook ingeschakeld. Gebruik het volgende voorbeeld:

   ```azurecli
   az network express-route list
   ```

   Het antwoord is vergelijkbaar met het volgende voorbeeld:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Configureer Microsoft-peering voor het circuit. Zorg ervoor dat u over de volgende informatie beschikt voordat u verder gaat.

   * Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR.
   * Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * Geadverteerde voorvoegsels: u moet een lijst verstrekken van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. Als u van plan bent een set voorvoegsels te verzenden, u een door komma's gescheiden lijst verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR.
   * **Optioneel -** Klant ASN: Als u reclame maakt voorvoegsels die niet zijn geregistreerd op het as-nummer peering, u het AS-nummer opgeven waarop ze zijn geregistreerd.
   * Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.
   * **Optioneel -** Een MD5 hash als u ervoor kiest om er een te gebruiken.

   Voer het volgende voorbeeld uit om Microsoft-peering voor uw circuit te configureren:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>De details van Microsoft-peering weergeven

U configuratiegegevens opvragen via het volgende voorbeeld:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Microsoft controleert of de opgegeven 'Geadverteerde openbare voorvoegsels' en 'Peer ASN' (of 'Klant ASN') aan u zijn toegewezen in het internetrouteringsregister. Als u de openbare voorvoegsels van een andere entiteit ontvangt en als de toewijzing niet wordt geregistreerd met het routeringsregister, wordt de automatische validatie niet voltooid en vereist de handmatige validatie. Als de automatische validatie mislukt, ziet u 'AdvertisedPublicPrefixesState' als 'Validatie nodig' op de uitvoer van de bovenstaande opdracht. 
> 
> Als u het bericht 'Validatie nodig' ziet, verzamelt u het document(en) waarop de openbare voorvoegsels worden weergegeven, worden toegewezen aan uw organisatie door de entiteit die wordt vermeld als de eigenaar van de voorvoegsels in het routeringsregister en dient u deze documenten in voor handmatige validatie door het openen van een support ticket zoals hieronder weergegeven. 
> 
>

De uitvoer lijkt op die in het volgende voorbeeld:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Configuratie van Microsoft-peering bijwerken

U elk deel van de configuratie bijwerken. De geadverteerde voorvoegsels van het circuit worden bijgewerkt van 123.1.0.0/24 naar 124.1.0.0/24 in het volgende voorbeeld:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>IPv6-peering-instellingen toevoegen aan een bestaande IPv4-configuratie

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft-peering verwijderen

U uw peeringconfiguratie verwijderen door het volgende voorbeeld uit te voeren:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="azure-private-peering"></a><a name="private"></a>Persoonlijke Azure-peering

Met deze sectie u de Azure private peering-configuratie voor een ExpressRoute-circuit maken, ontvangen, bijwerken en verwijderen.

### <a name="to-create-azure-private-peering"></a>Persoonlijke Azure-peering maken

1. Installeer de nieuwste versie van Azure CLI. U moet de nieuwste versie van de Azure Command-line Interface (CLI)* Controleer de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u met de configuratie begint.

   ```azurecli
   az login
   ```

   Het abonnement selecteren waarmee u een ExpressRoute-circuit wilt maken

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Maak een ExpressRoute-circuit. Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteitsprovider beheerde Layer 3-services aanbiedt, u uw connectiviteitsprovider vragen azure private peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteitsprovider echter geen routering voor u beheert, gaat u na het maken van uw circuit door met uw configuratie met behulp van de volgende stappen.

3. Controleer het ExpressRoute-circuit om te controleren of het is ingericht en ook ingeschakeld. Gebruik het volgende voorbeeld:

   ```azurecli
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   Het antwoord is vergelijkbaar met het volgende voorbeeld:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Silicon Valley",
   "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Configureer persoonlijke Azure-peering voor het circuit. Zorg ervoor dat u de volgende items hebt voordat u verdergaat met de volgende stappen:

   * Een /30-subnet voor de primaire koppeling. Het subnet mag geen deel uitmaken van een adresruimte die is gereserveerd voor virtuele netwerken.
   * Een /30-subnet voor de secundaire koppeling. Het subnet mag geen deel uitmaken van een adresruimte die is gereserveerd voor virtuele netwerken.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken. U kunt een persoonlijk AS-nummer voor deze peering gebruiken. Zorg dat u niet 65515 gebruikt.
   * **Optioneel -** Een MD5 hash als u ervoor kiest om er een te gebruiken.

   Gebruik het volgende voorbeeld om Azure private peering voor uw circuit te configureren:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Als u ervoor kiest een MD5-hash te gebruiken, gebruikt u het volgende voorbeeld:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Zorg dat u uw AS-nummer als peering-ASN opgeeft, niet als klant-ASN.
   > 
   > 

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>De details van persoonlijke Azure-peering weergeven

U configuratiegegevens opvragen via het volgende voorbeeld:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>De configuratie van persoonlijke Azure-peering bijwerken

U elk deel van de configuratie bijwerken met behulp van het volgende voorbeeld. In dit voorbeeld wordt de VLAN-ID van het circuit bijgewerkt van 100 naar 500.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Persoonlijke Azure-peering verwijderen

U uw peeringconfiguratie verwijderen door het volgende voorbeeld uit te voeren:

> [!WARNING]
> U moet ervoor zorgen dat alle virtuele netwerken en ExpressRoute Global Reach-verbindingen worden verwijderd voordat u dit voorbeeld uitvoert. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```


## <a name="next-steps"></a>Volgende stappen

Volgende stap, [Een VNet koppelen aan een ExpressRoute-circuit](howto-linkvnet-cli.md).

* Voor meer informatie over ExpressRoute-werkstromen raadpleegt u [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-werkstromen).
* Voor meer informatie over circuitpeering raadpleegt u [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuits en -routeringsdomeinen).
* Bekijk het [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtual Network-overzicht) voor meer informatie over het gebruik van virtuele netwerken.
