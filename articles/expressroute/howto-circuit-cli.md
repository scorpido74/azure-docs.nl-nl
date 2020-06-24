---
title: 'Een ExpressRoute-circuit maken en wijzigen: Azure CLI'
description: In dit artikel wordt beschreven hoe u een ExpressRoute-circuit maakt, provisioneert, controleert, bijwerkt, verwijdert en uitschakelt met behulp van CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: cherylmc
ms.openlocfilehash: 56af984ef83d2e237f0aa05af5cfef4dd6205256
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84738325"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Een ExpressRoute-circuit maken en wijzigen met behulp van CLI


In dit artikel wordt beschreven hoe u een Azure ExpressRoute-circuit maakt met behulp van de opdracht regel interface (CLI). In dit artikel leest u ook hoe u de status controleert, bijwerkt of verwijdert en de inrichting van een circuit ongedaan maakt. Als u een andere methode wilt gebruiken om te werken met ExpressRoute-circuits, kunt u het artikel selecteren in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure-CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video-Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [Power shell (klassiek)](expressroute-howto-circuit-classic.md)
>

## <a name="before-you-begin"></a>Voordat u begint

* Installeer eerst de meest recente versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI installeren](/cli/azure/install-azure-cli) en [Aan de slag met Azure CLI](/cli/azure/get-started-with-azure-cli) voor meer informatie over de CLI-opdrachten.
* Controleer de [vereisten](expressroute-prerequisites.md) en [werk stromen](expressroute-workflows.md) voordat u begint met de configuratie.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Een ExpressRoute-circuit maken en inrichten

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Meld u aan bij uw Azure-account en selecteer uw abonnement

Meld u aan bij uw Azure-account om de configuratie te starten. Als u de Cloud shell ' try it ' gebruikt, bent u automatisch aangemeld. Gebruik de volgende voor beelden om u te helpen bij het maken van verbinding:

```azurecli-interactive
az login
```

Controleer de abonnementen voor het account.

```azurecli-interactive
az account list
```

Selecteer het abonnement waarvoor u een ExpressRoute-circuit wilt maken.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. de lijst met ondersteunde providers, locaties en band breedten ophalen

Voordat u een ExpressRoute-circuit maakt, hebt u de lijst met ondersteunde connectiviteits providers, locaties en bandbreedte opties nodig. De CLI `az network express-route list-service-providers` -opdracht retourneert deze informatie, die u in latere stappen gaat gebruiken:

```azurecli-interactive
az network express-route list-service-providers
```

Het antwoord is vergelijkbaar met het volgende voorbeeld:

```output
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Controleer het antwoord om te zien of uw connectiviteits provider wordt vermeld. Noteer de volgende informatie die u nodig hebt om een circuit te maken:

* Name
* PeeringLocations
* BandwidthsOffered

U bent nu klaar om een ExpressRoute-circuit te maken.

### <a name="3-create-an-expressroute-circuit"></a>3. een ExpressRoute-circuit maken

> [!IMPORTANT]
> Uw ExpressRoute-circuit wordt gefactureerd vanaf het moment dat een service sleutel wordt uitgegeven. Voer deze bewerking uit wanneer de connectiviteits provider gereed is om het circuit in te richten.
>
>

Als u nog geen resource groep hebt, moet u er een maken voordat u uw ExpressRoute-circuit maakt. U kunt een resource groep maken door de volgende opdracht uit te voeren:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

In het volgende voor beeld ziet u hoe u een ExpressRoute-circuit van 200 Mbps maakt met behulp van Equinix in silicone dal. Als u een andere provider en andere instellingen gebruikt, moet u deze informatie vervangen wanneer u uw aanvraag doet.

Zorg ervoor dat u de juiste SKU-laag en SKU-familie opgeeft:

* SKU-laag bepaalt of een ExpressRoute-circuit [lokaal](expressroute-faqs.md#expressroute-local), standaard of [Premium](expressroute-faqs.md#expressroute-premium)is. U kunt *lokaal*, *standaard* of *Premium*opgeven.
* De SKU-familie bepaalt het facturerings type. U kunt *Metereddata* opgeven voor een data-abonnement met data limiet en *Unlimiteddata* voor een onbeperkt data-abonnement. U kunt het facturerings type wijzigen van *Metereddata* in *Unlimiteddata*, maar u kunt het type niet wijzigen van *Unlimiteddata* in *Metereddata*. Een *lokaal* circuit is alleen *Unlimiteddata* .


Uw ExpressRoute-circuit wordt gefactureerd vanaf het moment dat een service sleutel wordt uitgegeven. Het volgende voor beeld is een aanvraag voor een nieuwe service sleutel:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Het antwoord bevat de service sleutel.

### <a name="4-list-all-expressroute-circuits"></a>4. alle ExpressRoute-circuits weer geven

Voer de opdracht uit om een lijst op te halen met alle ExpressRoute-circuits die u hebt gemaakt `az network express-route list` . U kunt deze informatie op elk gewenst moment ophalen met behulp van deze opdracht. Als u alle circuits wilt weer geven, maakt u de aanroep zonder para meters.

```azurecli-interactive
az network express-route list
```

Uw service sleutel wordt vermeld in het veld *ServiceKey* van het antwoord.

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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

U kunt gedetailleerde beschrijvingen van alle para meters verkrijgen door de opdracht uit te voeren met de para meter-h.

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. de service sleutel naar uw connectiviteits provider verzenden voor het inrichten

' ServiceProviderProvisioningState ' bevat informatie over de huidige status van de inrichting van de service provider. De status biedt de status aan de kant van micro soft. Zie het [artikel workflows](expressroute-workflows.md#expressroute-circuit-provisioning-states)voor meer informatie.

Wanneer u een nieuw ExpressRoute-circuit maakt, heeft het circuit de volgende status:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Het circuit verandert in de volgende status wanneer de connectiviteits provider het proces voor u inschakelt:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Als u een ExpressRoute-circuit wilt gebruiken, moet dit een van de volgende statussen hebben:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Controleer de status en de status van de circuit sleutel regel matig

Door de status en de status van de circuit sleutel te controleren, kunt u weten wanneer uw provider uw circuit heeft ingeschakeld. Nadat het circuit is geconfigureerd, wordt ' ServiceProviderProvisioningState ' weer gegeven als ' ingericht ', zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. de routerings configuratie maken

Zie het artikel [ExpressRoute circuit routerings configuratie](howto-routing-cli.md) voor het maken en wijzigen van circuit peerings voor stapsgewijze instructies.

> [!IMPORTANT]
> Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met service providers die Layer 2-connectiviteits services aanbieden. Als u een service provider gebruikt die Managed Layer 3-services biedt (meestal een IP VPN, zoals MPLS), wordt door uw connectiviteits provider route ring voor u geconfigureerd en beheerd.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. een virtueel netwerk koppelen aan een ExpressRoute-circuit

Vervolgens koppelt u een virtueel netwerk aan uw ExpressRoute-circuit. Gebruik het artikel [koppel virtuele netwerken aan ExpressRoute-circuits](howto-linkvnet-cli.md) .

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Een ExpressRoute-circuit wijzigen

U kunt bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder dat dit van invloed is op de verbinding. U kunt de volgende wijzigingen aanbrengen zonder downtime:

* U kunt een ExpressRoute Premium-invoeg toepassing voor uw ExpressRoute-circuit in-of uitschakelen.
* U kunt de band breedte van uw ExpressRoute-circuit verg Roten, omdat er capaciteit beschikbaar is op de poort. Het downgradeen van de band breedte van een circuit wordt echter niet ondersteund.
* U kunt het meet plan wijzigen van gegevens met data limiet in onbeperkte gegevens. Het wijzigen van het meet plan van een onbeperkt aantal gegevens in gegevens met data limiet wordt echter niet ondersteund.
* U kunt *klassieke bewerkingen toestaan*in-en uitschakelen.

Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over limieten en beperkingen.

### <a name="to-enable-the-expressroute-premium-add-on"></a>De Premium-invoeg toepassing voor ExpressRoute inschakelen

U kunt de ExpressRoute Premium-invoeg toepassing voor uw bestaande circuit inschakelen met behulp van de volgende opdracht:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Het circuit heeft nu de ExpressRoute Premium-invoeg toepassingen ingeschakeld. U wordt gefactureerd voor de Premium-invoeg functie zodra de opdracht is uitgevoerd.

### <a name="to-disable-the-expressroute-premium-add-on"></a>De Premium-invoeg toepassing voor ExpressRoute uitschakelen

> [!IMPORTANT]
> Deze bewerking kan mislukken als u resources gebruikt die groter zijn dan is toegestaan voor het standaard circuit.
>
>

Voordat u de ExpressRoute Premium-invoeg toepassing uitschakelt, moet u de volgende criteria begrijpen:

* Voordat u van Premium naar standaard downgradet, moet u ervoor zorgen dat er minder dan 10 virtuele netwerken zijn gekoppeld aan het circuit. Als u meer dan 10 hebt, mislukt de update aanvraag en worden de Premium-tarieven in rekening gebracht.
* U moet alle virtuele netwerken in andere geopolitieke regio's ontkoppelen. Als u niet alle virtuele netwerken ontkoppelt, mislukt de update aanvraag en worden de Premium-tarieven in rekening gebracht.
* De route tabel moet kleiner zijn dan 4.000 routes voor privé-peering. Als uw route tabel groter is dan 4.000 routes, wordt de BGP-sessie verbroken. De sessie wordt niet opnieuw ingeschakeld totdat het aantal geadverteerde voor voegsels onder 4.000 is.

U kunt de ExpressRoute Premium-invoeg toepassing voor het bestaande circuit uitschakelen met behulp van het volgende voor beeld:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>De band breedte van het ExpressRoute-circuit bijwerken

Raadpleeg de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor de ondersteunde bandbreedte opties voor uw provider. U kunt elke grootte kiezen die groter is dan de grootte van uw bestaande circuit.

> [!IMPORTANT]
> Als er onvoldoende capaciteit is op de bestaande poort, moet u het ExpressRoute-circuit wellicht opnieuw maken. U kunt het circuit niet upgraden als er geen extra capaciteit beschikbaar is op die locatie.
>
> U kunt de band breedte van een ExpressRoute-circuit zonder onderbreking niet verkleinen. Voor downgrade-band breedte moet u de inrichting van het ExpressRoute-circuit ongedaan maken en vervolgens een nieuw ExpressRoute-circuit opnieuw inrichten.
>

Nadat u hebt bepaald welke grootte u nodig hebt, gebruikt u de volgende opdracht om het formaat van het circuit te wijzigen:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Uw circuit wordt op de micro soft-kant ingesteld. Vervolgens moet u contact opnemen met uw connectiviteits provider om configuraties aan hun zijde bij te werken om deze wijziging aan te passen. Nadat u deze melding hebt gemaakt, wordt u gefactureerd voor de bijgewerkte bandbreedte optie.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>De SKU verplaatsen van naar een onbeperkte data limiet

U kunt de SKU van een ExpressRoute-circuit wijzigen met behulp van het volgende voor beeld:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>De toegang tot de klassieke en Resource Manager-omgevingen beheren

Bekijk de instructies in [ExpressRoute-circuits verplaatsen van het klassieke naar het Resource Manager-implementatie model](expressroute-howto-move-arm.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Inrichting van een ExpressRoute-circuit ongedaan maken en het circuit verwijderen)

Als u het inrichten en verwijderen van een ExpressRoute-circuit wilt opheffen, moet u de volgende criteria kennen:

* U moet alle virtuele netwerken loskoppelen van het ExpressRoute-circuit. Als deze bewerking mislukt, controleert u of er virtuele netwerken zijn gekoppeld aan het circuit.
* Als de inrichtings status van de ExpressRoute-circuit service provider is **ingericht** of **ingericht**, moet u samen met uw service provider de inrichting van het circuit aan hun zijde opheffen. We blijven resources reserveren en u factureren totdat de service provider de inrichting van het circuit ongedaan heeft gemaakt en ons op de hoogte stelt.
* U kunt het circuit verwijderen als de inrichting van het circuit is ongedaan gemaakt door de service provider. Wanneer de inrichting van een circuit ongedaan wordt gemaakt, wordt de inrichtings status van de service provider ingesteld op **niet ingericht**. Hiermee wordt ook de facturering voor het circuit gestopt.

U kunt het ExpressRoute-circuit verwijderen door de volgende opdracht uit te voeren:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Nadat u het circuit hebt gemaakt, moet u ervoor zorgen dat u de volgende taken uitvoert:

* [Route ring voor uw ExpressRoute-circuit maken en wijzigen](howto-routing-cli.md)
* [Uw virtuele netwerk koppelen aan uw ExpressRoute-circuit](howto-linkvnet-cli.md)
