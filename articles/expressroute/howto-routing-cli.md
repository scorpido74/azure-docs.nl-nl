---
title: 'Azure ExpressRoute: peering configureren: CLI'
description: Dit artikel helpt u bij het maken en inrichten van de persoonlijke, open bare en micro soft-peering van een ExpressRoute-circuit. In dit artikel leest u hoe u de status controleert en peerings voor uw circuit bijwerkt of verwijdert.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: cbde41bd79409255e1ebf5145548ea260ac8581d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84727038"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Peering voor een ExpressRoute-circuit maken en wijzigen met behulp van CLI

Dit artikel helpt u bij het maken en beheren van routerings configuratie/peering voor een ExpressRoute-circuit in het Resource Manager-implementatie model met behulp van CLI. U kunt ook de status van peerings voor een ExpressRoute-circuit controleren, bijwerken of verwijderen en de inrichting ongedaan maken. Als u een andere methode wilt gebruiken om met uw circuit te werken, selecteert u een artikel in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure-CLI](howto-routing-cli.md)
> * [Open bare peering](about-public-peering.md)
> * [Video-persoonlijke peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video-micro soft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [Power shell (klassiek)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Configuratievereisten

* Installeer eerst de meest recente versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli) voor meer informatie over het installeren van de CLI-opdrachten.
* Zorg ervoor dat [u de vereisten](expressroute-prerequisites.md), [routerings vereisten](expressroute-routing.md)en [werk stroom](expressroute-workflows.md) pagina's hebt gecontroleerd voordat u begint met de configuratie.
* U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet zich in een ingerichte en ingeschakelde staat bevindt, zodat u de opdrachten in dit artikel kunt uitvoeren.

Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met Laag-2-connectiviteit aanbieden. Als u een service provider gebruikt die Managed Layer 3-services biedt (meestal een IPVPN, zoals MPLS), zal uw connectiviteits provider route ring voor u configureren en beheren.

U kunt privé-peering en micro soft-peering configureren voor een ExpressRoute-circuit (open bare Azure-peering is afgeschaft voor nieuwe circuits). Peerings kunnen in elke gewenste volg orde worden geconfigureerd. U moet er echter wel voor zorgen dat u de configuratie van elke peering een voor een voltooit. Voor meer informatie over routerings domeinen en peerings, Zie [ExpressRoute-routerings domeinen](expressroute-circuit-peerings.md). Zie [ExpressRoute Public peering](about-public-peering.md)(Engelstalig) voor meer informatie over open bare peering.

## <a name="microsoft-peering"></a><a name="msft"></a>Micro soft-peering

In deze sectie leert u hoe u de micro soft-peering-configuratie voor een ExpressRoute-circuit maakt, ontvangt, bijwerkt en verwijdert.

> [!IMPORTANT]
> Micro soft-peering van ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017, heeft alle service voorvoegsels die worden geadverteerd via de micro soft-peering, zelfs als er geen route filters zijn gedefinieerd. Micro soft-peering van ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017, heeft geen voor voegsels die worden geadverteerd totdat een route filter aan het circuit is gekoppeld. Zie [Configure a route filter for micro soft peering](how-to-routefilter-powershell.md)(Engelstalig) voor meer informatie.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft-peering maken

1. Installeer de nieuwste versie van Azure CLI. Gebruik de nieuwste versie van de Azure-opdracht regel interface (CLI). Controleer de [vereisten](expressroute-prerequisites.md) en [werk stromen](expressroute-workflows.md) voordat u begint met de configuratie.

   ```azurecli
   az login
   ```

   Selecteer het abonnement waarvoor u een ExpressRoute-circuit wilt maken.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Maak een ExpressRoute-circuit. Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteits provider beheerde Layer 3-services biedt, kunt u uw connectiviteits provider vragen om micro soft-peering voor u in te scha kelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteits provider echter geen route ring voor u beheert, kunt u na het maken van het circuit uw configuratie voortzetten met de volgende stappen. 

3. Controleer het ExpressRoute-circuit om er zeker van te zijn dat het is ingericht en ook is ingeschakeld. Gebruik het volgende voorbeeld:

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
   * Geadverteerde voorvoegsels: u moet een lijst verstrekken van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. Als u van plan bent een aantal voor voegsels te verzenden, kunt u een door komma's gescheiden lijst verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR.
   * **Optioneel-** Klant-ASN: als u voor voegsels adverteert die niet zijn geregistreerd voor het peering als-nummer, kunt u het AS-nummer opgeven waarop ze zijn geregistreerd.
   * Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.
   * **Optioneel-** Een MD5-hash als u ervoor kiest om er een te gebruiken.

   Voer het volgende voor beeld uit om micro soft-peering voor uw circuit te configureren:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>De details van Microsoft-peering weergeven

U kunt Configuratie Details ophalen met behulp van het volgende voor beeld:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Micro soft controleert of de opgegeven ' aangekondigde open bare voor voegsels ' en ' peer-ASN ' (of ' klant-ASN ') aan u zijn toegewezen in het Internet Routing REGI ster. Als u de open bare voor voegsels van een andere entiteit krijgt en de toewijzing niet is vastgelegd met het routerings register, wordt de automatische validatie niet voltooid en moet hand matig worden gevalideerd. Als de automatische validatie mislukt, ziet u ' AdvertisedPublicPrefixesState ' als ' validatie vereist ' bij de uitvoer van de bovenstaande opdracht. 
> 
> Als u het bericht validatie vereist ziet, verzamelt u de documenten die de open bare voor voegsels bevatten, worden toegewezen aan uw organisatie door de entiteit die wordt vermeld als de eigenaar van de voor voegsels in het routerings register en verzendt deze documenten voor hand matige validatie door een ondersteunings ticket te openen, zoals hieronder wordt weer gegeven. 
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

U kunt elk deel van de configuratie bijwerken. De aangekondigde voor voegsels van het circuit worden bijgewerkt van 123.1.0.0/24 naar 124.1.0.0/24 in het volgende voor beeld:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>Instellingen voor het toevoegen van IPv6-micro soft-peering aan een bestaande IPv4-configuratie

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft-peering verwijderen

U kunt de peering-configuratie verwijderen door het volgende voor beeld uit te voeren:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="azure-private-peering"></a><a name="private"></a>Persoonlijke Azure-peering

In deze sectie leert u hoe u de persoonlijke Azure-peering-configuratie voor een ExpressRoute-circuit maakt, ontvangt, bijwerkt en verwijdert.

### <a name="to-create-azure-private-peering"></a>Persoonlijke Azure-peering maken

1. Installeer de nieuwste versie van Azure CLI. U moet de nieuwste versie van de Azure-opdracht regel interface (CLI) gebruiken. * Controleer de [vereisten](expressroute-prerequisites.md) en [werk stromen](expressroute-workflows.md) voordat u begint met de configuratie.

   ```azurecli
   az login
   ```

   Het abonnement selecteren waarmee u een ExpressRoute-circuit wilt maken

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Maak een ExpressRoute-circuit. Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteits provider beheerde Layer 3-services biedt, kunt u uw connectiviteits provider vragen om persoonlijke Azure-peering voor u in te scha kelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteits provider echter geen route ring voor u beheert, kunt u na het maken van het circuit uw configuratie voortzetten met de volgende stappen.

3. Controleer het ExpressRoute-circuit om er zeker van te zijn dat het is ingericht en ook is ingeschakeld. Gebruik het volgende voorbeeld:

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

   * Een /30-subnet voor de primaire koppeling. Het subnet mag geen deel uitmaken van een adres ruimte die is gereserveerd voor virtuele netwerken.
   * Een /30-subnet voor de secundaire koppeling. Het subnet mag geen deel uitmaken van een adres ruimte die is gereserveerd voor virtuele netwerken.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken. U kunt een persoonlijk AS-nummer voor deze peering gebruiken. Zorg dat u niet 65515 gebruikt.
   * **Optioneel-** Een MD5-hash als u ervoor kiest om er een te gebruiken.

   Gebruik het volgende voor beeld om persoonlijke Azure-peering voor uw circuit te configureren:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Als u een MD5-hash wilt gebruiken, gebruikt u het volgende voor beeld:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Zorg dat u uw AS-nummer als peering-ASN opgeeft, niet als klant-ASN.
   > 
   > 

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>De details van persoonlijke Azure-peering weergeven

U kunt Configuratie Details ophalen met behulp van het volgende voor beeld:

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

U kunt elk deel van de configuratie bijwerken met behulp van het volgende voor beeld. In dit voor beeld wordt de VLAN-ID van het circuit bijgewerkt van 100 naar 500.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Persoonlijke Azure-peering verwijderen

U kunt de peering-configuratie verwijderen door het volgende voor beeld uit te voeren:

> [!WARNING]
> U moet ervoor zorgen dat alle virtuele netwerken en ExpressRoute Global Reach verbindingen worden verwijderd voordat u dit voor beeld uitvoert. 
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
