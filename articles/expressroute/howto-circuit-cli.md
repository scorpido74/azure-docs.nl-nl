---
title: 'Een ExpressRoute-circuit maken en wijzigen: Azure CLI'
description: In dit artikel ziet u hoe u een ExpressRoute-circuit maakt, indient, verifieert, verwijdert en deprovisionert met CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: cherylmc
ms.openlocfilehash: b967e1d8751a9c6a5214fef5241d57e954ad9f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476148"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Een ExpressRoute-circuit maken en wijzigen met behulp van CLI


In dit artikel wordt beschreven hoe u een Azure ExpressRoute-circuit maakt met behulp van de Command Line Interface (CLI). In dit artikel ziet u ook hoe u de status, update of verwijdering en deprovision van een circuit controleren. Als u een andere methode wilt gebruiken om met ExpressRoute-circuits te werken, u het artikel selecteren in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure-CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-circuit-classic.md)
>

## <a name="before-you-begin"></a>Voordat u begint

* Installeer eerst de meest recente versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI installeren](/cli/azure/install-azure-cli) en [Aan de slag met Azure CLI](/cli/azure/get-started-with-azure-cli) voor meer informatie over de CLI-opdrachten.
* Bekijk de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u met de configuratie begint.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Een ExpressRoute-circuit maken en inrichten

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Meld u aan bij uw Azure-account en selecteer uw abonnement

Als u uw configuratie wilt starten, meldt u zich aan bij uw Azure-account. Als u de CloudShell "Try It" gebruikt, bent u automatisch aangemeld. Gebruik de volgende voorbeelden om verbinding te maken:

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

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Download de lijst met ondersteunde providers, locaties en bandbreedtes

Voordat u een ExpressRoute-circuit maakt, hebt u de lijst met ondersteunde connectiviteitsproviders, locaties en bandbreedteopties nodig. Met de `az network express-route list-service-providers` opdracht CLI retourneert u deze informatie, die u in latere stappen zult gebruiken:

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

Controleer het antwoord om te zien of uw connectiviteitsprovider wordt vermeld. Noteer de volgende informatie, die u nodig hebt wanneer u een circuit maakt:

* Name
* PeeringLocaties
* Bandbreedtes aangeboden

Je bent nu klaar om een ExpressRoute circuit te maken.

### <a name="3-create-an-expressroute-circuit"></a>3. Maak een ExpressRoute-circuit

> [!IMPORTANT]
> Uw ExpressRoute-circuit wordt gefactureerd vanaf het moment dat een servicesleutel wordt uitgegeven. Voer deze bewerking uit wanneer de connectiviteitsprovider klaar is om het circuit in te richten.
>
>

Als u nog geen resourcegroep hebt, moet u er een maken voordat u uw ExpressRoute-circuit maakt. U een resourcegroep maken door de volgende opdracht uit te voeren:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

In het volgende voorbeeld ziet u hoe u een ExpressRoute-circuit van 200 Mbps maken via Equinix in Silicon Valley. Als u een andere provider en verschillende instellingen gebruikt, vervangt u die informatie wanneer u uw verzoek indient.

Zorg ervoor dat u de juiste SKU-laag en SKU-familie opgeeft:

* De SKU-laag bepaalt of een ExpressRoute-circuit [lokaal](expressroute-faqs.md#expressroute-local), standaard of [premium](expressroute-faqs.md#expressroute-premium)is. U *Lokaal,* *Standaard* of *Premium*opgeven.
* De SKU-familie bepaalt het factureringstype. U *Datadata* opgeven voor een data-abonnement met data met data met data en *Unlimiteddata* voor een onbeperkt data-abonnement. U het factureringstype wijzigen van *Gemetendata* naar *Unlimiteddata,* maar u het type niet wijzigen van *Unlimiteddata* naar *Gemetendata.* Een *lokaal* circuit is alleen *Unlimiteddata.*


Uw ExpressRoute-circuit wordt gefactureerd vanaf het moment dat een servicesleutel wordt uitgegeven. Het volgende voorbeeld is een aanvraag voor een nieuwe servicesleutel:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Het antwoord bevat de servicesleutel.

### <a name="4-list-all-expressroute-circuits"></a>4. Alle ExpressRoute-circuits vermelden

Voer de `az network express-route list` opdracht uit om een lijst te krijgen met alle ExpressRoute-circuits die u hebt gemaakt. U deze informatie op elk gewenst moment ophalen met deze opdracht. Als u alle circuits wilt weergeven, belt u zonder parameters.

```azurecli-interactive
az network express-route list
```

Uw servicesleutel wordt vermeld in het veld *ServiceKey* van het antwoord.

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

U gedetailleerde beschrijvingen van alle parameters krijgen door de opdracht uit te voeren met behulp van de parameter '-h'.

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Stuur de servicesleutel naar uw connectiviteitsprovider voor inlevering

'ServiceProviderProvisioningState' geeft informatie over de huidige stand van de voorziening aan de kant van de dienstverlener. De status geeft de status aan de Microsoft-kant. Zie het artikel [Werkstromen](expressroute-workflows.md#expressroute-circuit-provisioning-states)voor meer informatie .

Wanneer u een nieuw ExpressRoute-circuit maakt, bevindt het circuit zich in de volgende staat:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Het circuit verandert in de volgende status wanneer de connectiviteitsprovider het voor u inschakelt:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Om een ExpressRoute-circuit te kunnen gebruiken, moet het in de volgende staat zijn:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Controleer periodiek de status en de status van de circuitsleutel

Als u de status en de status van de circuitsleutel controleert, weet u wanneer uw provider uw circuit heeft ingeschakeld. Nadat het circuit is geconfigureerd, wordt 'ServiceProviderProvisioningState' weergegeven als 'Ingericht', zoals in het volgende voorbeeld wordt weergegeven:

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

### <a name="7-create-your-routing-configuration"></a>7. Uw routeringsconfiguratie maken

Zie het artikel voor het [routeringsconfiguratieartikel ExpressRoute-circuit](howto-routing-cli.md) om circuitpeeringen te maken en te wijzigen voor stapsgewijze instructies.

> [!IMPORTANT]
> Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die layer 2-connectiviteitsservices aanbieden. Als u een serviceprovider gebruikt die beheerde laag 3-services aanbiedt (meestal een IP VPN, zoals MPLS), configureert en beheert uw connectiviteitsprovider routering voor u.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Koppel een virtueel netwerk aan een ExpressRoute-circuit

Koppel vervolgens een virtueel netwerk aan uw ExpressRoute-circuit. Gebruik het artikel [Virtuele netwerken koppelen aan expressroute-circuits.](howto-linkvnet-cli.md)

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Een ExpressRoute-circuit wijzigen

U bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder dat dit gevolgen heeft voor de connectiviteit. U de volgende wijzigingen aanbrengen zonder downtime:

* U een ExpressRoute premium add-on voor uw ExpressRoute-circuit in- of uitschakelen.
* U de bandbreedte van uw ExpressRoute-circuit verhogen, mits er capaciteit beschikbaar is op de poort. Het verlagen van de bandbreedte van een circuit wordt echter niet ondersteund.
* U het meetplan wijzigen van gemeten data naar Onbeperkte gegevens. Het wijzigen van het meetplan van Unlimited Data naar Datametergegevens wordt echter niet ondersteund.
* U *Klassieke bewerkingen*toestaan en uitschakelen.

Zie de Veelgestelde vragen over [ExpressRoute voor](expressroute-faqs.md)meer informatie over limieten en beperkingen.

### <a name="to-enable-the-expressroute-premium-add-on"></a>De ExpressRoute premium add-on inschakelen

U de ExpressRoute premium add-on voor uw bestaande circuit inschakelen met behulp van de volgende opdracht:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Het circuit heeft nu de ExpressRoute premium add-on functies ingeschakeld. We beginnen met het factureren van u voor de premium add-on mogelijkheid zodra de opdracht is uitgevoerd.

### <a name="to-disable-the-expressroute-premium-add-on"></a>De ExpressRoute premium add-on uitschakelen

> [!IMPORTANT]
> Deze bewerking kan mislukken als u resources gebruikt die groter zijn dan wat is toegestaan voor het standaardcircuit.
>
>

Voordat u de ExpressRoute-premium-invoegtoepassing uitschakelt, begrijpt u de volgende criteria:

* Voordat u downgraden van premium naar standaard, moet u ervoor zorgen dat u minder dan 10 virtuele netwerken gekoppeld aan het circuit. Als u meer dan 10 hebt, mislukt uw updateverzoek en factureren we u tegen premiumtarieven.
* U moet alle virtuele netwerken in andere geopolitieke regio's ontkoppelen. Als u niet al uw virtuele netwerken loskoppelt, mislukt uw updateverzoek en factureren we u tegen premium tarieven.
* Uw routetabel moet minder dan 4.000 routes hebben voor privé-peering. Als de grootte van uw routetabel groter is dan 4.000 routes, daalt de BGP-sessie. De sessie wordt pas opnieuw ingeschakeld als het aantal geadverteerde voorvoegsels lager is dan 4.000.

U de ExpressRoute premium add-on voor het bestaande circuit uitschakelen met behulp van het volgende voorbeeld:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>De bandbreedte van het ExpressRoute-circuit bijwerken

Voor de ondersteunde bandbreedteopties voor uw provider raadpleegt u de [veelgestelde vragen van ExpressRoute.](expressroute-faqs.md) U elke grootte kiezen die groter is dan de grootte van uw bestaande circuit.

> [!IMPORTANT]
> Als er onvoldoende capaciteit is op de bestaande poort, moet u mogelijk het ExpressRoute-circuit opnieuw maken. U het circuit niet upgraden als er op die locatie geen extra capaciteit beschikbaar is.
>
> U de bandbreedte van een ExpressRoute-circuit niet zonder onderbreking verminderen. Voor het verlagen van de bandbreedte moet u het ExpressRoute-circuit deprovisioneren en vervolgens een nieuw ExpressRoute-circuit opnieuw inrichten.
>

Nadat u de gewenste grootte hebt bepaald, gebruikt u de volgende opdracht om het formaat van uw circuit te wijzigen:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Uw circuit is groot aan de Microsoft-kant. Vervolgens moet u contact opnemen met uw connectiviteitsprovider om configuraties aan hun kant bij te werken om deze wijziging te kunnen aanpassen. Nadat u deze melding hebt gedaan, beginnen we u te factureren voor de bijgewerkte bandbreedteoptie.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Om de SKU te verplaatsen van gemeten naar onbeperkt

U de SKU van een ExpressRoute-circuit wijzigen met behulp van het volgende voorbeeld:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>De toegang tot de klassieke en Resource Manager-omgevingen beheren

Bekijk de instructies in [de circuits Move ExpressRoute, van de klassieke naar het implementatiemodel resourcemanager.](expressroute-howto-move-arm.md)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Inrichting van een ExpressRoute-circuit ongedaan maken en het circuit verwijderen)

Als u een ExpressRoute-circuit wilt deprovisioneneren en verwijderen, moet u de volgende criteria begrijpen:

* U moet alle virtuele netwerken loskoppelen van het ExpressRoute-circuit. Als deze bewerking mislukt, controleert u of er virtuele netwerken zijn gekoppeld aan het circuit.
* Als de ExpressRoute-dienstverlener provisioning state is **Provisioning** or **Provisioned,** moet u samenwerken met uw serviceprovider om het circuit aan hun kant te deprovisioneren. We blijven resources reserveren en u factureren totdat de serviceprovider het circuit heeft voltooid en ons hiervan op de hoogte stelt.
* U het circuit verwijderen als de serviceprovider het circuit heeft gedeprovisioneerd. Wanneer een circuit wordt gedeprovisioneerd, is de voorzieningsstatus van de dienstverlener ingesteld op **Niet-ingericht**. Hiermee wordt ook de facturering voor het circuit gestopt.

U uw ExpressRoute-circuit verwijderen door de volgende opdracht uit te voeren:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Nadat u uw circuit hebt gemaakt, moet u ervoor zorgen dat u de volgende taken uitvoert:

* [Routering voor uw ExpressRoute-circuit maken en wijzigen](howto-routing-cli.md)
* [Koppel uw virtuele netwerk aan uw ExpressRoute-circuit](howto-linkvnet-cli.md)
