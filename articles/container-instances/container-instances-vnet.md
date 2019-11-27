---
title: Container groep implementeren in een virtueel Azure-netwerk
description: Meer informatie over het implementeren van container groepen in een nieuw of bestaand virtueel Azure-netwerk.
ms.topic: article
ms.date: 07/11/2019
ms.openlocfilehash: f211924eb74035f4bb30db2d2b848e0a2591de09
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533270"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Container instanties implementeren in een virtueel Azure-netwerk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biedt veilige, persoonlijke netwerken voor uw Azure-en on-premises resources. Door container groepen te implementeren in een virtueel Azure-netwerk, kunnen uw containers veilig communiceren met andere resources in het virtuele netwerk.

Met container groepen die zijn geïmplementeerd in een virtueel Azure-netwerk, kunnen scenario's zoals:

* Directe communicatie tussen container groepen in hetzelfde subnet
* Uitvoer van een werk belasting op basis van een [taak](container-instances-restart-policy.md) naar een data base in het virtuele netwerk verzenden
* Inhoud ophalen voor container exemplaren van een [service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) in het virtuele netwerk
* Container communicatie met virtuele machines in het virtuele netwerk
* Container communicatie met on-premises bronnen via een [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er [zijn enkele beperkingen van toepassing](#preview-limitations). Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functie worden mogelijk nog gewijzigd voordat de functie algemeen beschikbaar wordt.


## <a name="virtual-network-deployment-limitations"></a>Beperkingen voor de implementatie van virtuele netwerken

Bepaalde beperkingen zijn van toepassing wanneer u container groepen implementeert in een virtueel netwerk.

* Als u container groepen wilt implementeren in een subnet, mag het subnet geen andere resource typen bevatten. Verwijder alle bestaande resources uit een bestaand subnet voordat u container groepen implementeert, of maak een nieuw subnet.
* U kunt geen [beheerde identiteit](container-instances-managed-identity.md) gebruiken in een container groep die is geïmplementeerd in een virtueel netwerk.
* Als gevolg van de betrokken extra netwerk bronnen is het implementeren van een container groep naar een virtueel netwerk meestal trager dan het implementeren van een standaard container exemplaar.

## <a name="preview-limitations"></a>Preview-beperkingen

Hoewel deze functie in preview is, zijn de volgende beperkingen van toepassing bij het implementeren van container groepen in een virtueel netwerk. 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Container resource limieten kunnen verschillen van de limieten voor niet-gewerkte container instanties in deze regio's. Momenteel worden alleen Linux-containers ondersteund voor deze functie. Windows-ondersteuning staat op de planning.

### <a name="unsupported-networking-scenarios"></a>Niet-ondersteunde netwerk scenario's 

* **Azure Load Balancer** het plaatsen van een Azure Load Balancer vóór container instanties in een groep met netwerk containers wordt niet ondersteund
* **Peering op virtueel netwerk** : u kunt geen peering van een virtueel netwerk met een subnet dat is overgedragen aan Azure container instances naar een ander virtueel netwerk
* **Route tabellen** -door de gebruiker gedefinieerde routes kunnen niet worden ingesteld in een subnet dat is overgedragen aan Azure container instances
* **Netwerk beveiligings groepen** : uitgaande beveiligings regels in nsg's die zijn toegepast op een subnet dat is overgedragen aan Azure container instances, worden momenteel niet afgedwongen 
* **Openbaar IP-of DNS-label** -container groepen die zijn geïmplementeerd in een virtueel netwerk ondersteunen momenteel geen ondersteuning voor het rechtstreeks weer geven van containers op internet met een openbaar IP-adres of een Fully Qualified Domain name
* **Interne naam omzetting** : naam omzetting voor Azure-resources in het virtuele netwerk via de interne Azure DNS wordt niet ondersteund

Voor het **verwijderen van netwerk resources** zijn [extra stappen](#delete-network-resources) vereist wanneer u container groepen hebt geïmplementeerd in het virtuele netwerk.

## <a name="required-network-resources"></a>Vereiste netwerk bronnen

Er zijn drie Azure Virtual Network-resources vereist voor het implementeren van container groepen in een virtueel netwerk: het [virtuele netwerk](#virtual-network) zelf, een [overgedragen subnet](#subnet-delegated) binnen het virtuele netwerk en een [netwerk profiel](#network-profile). 

### <a name="virtual-network"></a>Virtueel netwerk

Een virtueel netwerk definieert de adres ruimte waarin u een of meer subnetten maakt. Vervolgens implementeert u Azure-resources (zoals container groepen) in de subnetten in uw virtuele netwerk.

### <a name="subnet-delegated"></a>Subnet (gedelegeerd)

Met subnetten wordt het virtuele netwerk gesegmenteerd tot afzonderlijke adres ruimten die kunnen worden gebruikt door de Azure-resources die u in deze ruimte plaatst. U maakt een of meer subnetten binnen een virtueel netwerk.

Het subnet dat u voor container groepen gebruikt, mag alleen container groepen bevatten. Wanneer u een container groep voor het eerst naar een subnet implementeert, wordt dat subnet door Azure gedelegeerd aan Azure Container Instances. Wanneer het subnet is gedelegeerd, kan het alleen worden gebruikt voor container groepen. Als u probeert andere resources dan container groepen te implementeren op een overgedragen subnet, mislukt de bewerking.

### <a name="network-profile"></a>Netwerk profiel

Een netwerk profiel is een netwerk configuratie sjabloon voor Azure-resources. Hiermee geeft u bepaalde netwerk eigenschappen voor de resource op, bijvoorbeeld het subnet waarin het moet worden geïmplementeerd. Wanneer u de opdracht [AZ container Create][az-container-create] gebruikt voor het implementeren van een container groep naar een subnet (en dus een virtueel netwerk), maakt Azure een netwerk profiel voor u. U kunt dat netwerk profiel vervolgens gebruiken voor toekomstige implementaties naar het subnet. 

Als u een resource manager-sjabloon, een YAML-bestand of een programmatische methode wilt gebruiken om een container groep naar een subnet te implementeren, moet u de volledige Resource Manager-Resource-ID van een netwerk profiel opgeven. U kunt een profiel gebruiken dat eerder is gemaakt met [AZ container Create][az-container-create]of een profiel maken met een resource manager-sjabloon (Zie [voor beeld van sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) en [Naslag informatie](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Als u de ID van een eerder gemaakt profiel wilt ophalen, gebruikt u de opdracht [AZ Network profile list][az-network-profile-list] . 

In het volgende diagram zijn verschillende container groepen geïmplementeerd naar een subnet dat is overgedragen aan Azure Container Instances. Zodra u één container groep naar een subnet hebt geïmplementeerd, kunt u extra container groepen implementeren door hetzelfde netwerk profiel op te geven.

![Container groepen binnen een virtueel netwerk][aci-vnet-01]

## <a name="deployment-scenarios"></a>Implementatiescenario’s

U kunt [AZ container Create][az-container-create] gebruiken om container groepen te implementeren in een nieuw virtueel netwerk en Azure toe te staan de vereiste netwerk resources te maken, of te implementeren in een bestaand virtueel netwerk. 

### <a name="new-virtual-network"></a>Nieuw virtueel netwerk

Als u wilt implementeren in een nieuw virtueel netwerk en Azure de netwerk resources automatisch voor u wilt maken, geeft u het volgende op wanneer u [AZ container Create][az-container-create]uitvoert:

* Naam van virtueel netwerk
* Adres voorvoegsel van het virtuele netwerk in CIDR-indeling
* Subnetnaam
* Adres voorvoegsel van het subnet in CIDR-indeling

Met de voor voegsels voor het virtuele netwerk en het subnet adres worden de adres ruimten voor het virtuele netwerk en het subnet opgegeven. Deze waarden worden weer gegeven in een CIDR-notatie (Classless Inter-Domain Routing), bijvoorbeeld `10.0.0.0/16`. Zie [een subnet van een virtueel netwerk toevoegen, wijzigen of verwijderen](../virtual-network/virtual-network-manage-subnet.md)voor meer informatie over het werken met subnetten.

Wanneer u uw eerste container groep met deze methode hebt geïmplementeerd, kunt u in hetzelfde subnet implementeren door het virtuele netwerk en de subnetnaam op te geven, of het netwerk profiel dat door Azure automatisch voor u wordt gemaakt. Omdat Azure het subnet delegeert naar Azure Container Instances, kunt u *alleen* container groepen implementeren naar het subnet.

### <a name="existing-virtual-network"></a>Bestaand virtueel netwerk

Een container groep implementeren in een bestaand virtueel netwerk:

1. Een subnet maken binnen uw bestaande virtuele netwerk of een bestaand subnet van *alle* andere resources leegmaken
1. Implementeer een container groep met [AZ container Create][az-container-create] en geef een van de volgende opties op:
   * Naam van het virtuele netwerk en het subnet
   * Bron-ID van het virtuele netwerk en de resource-ID van het subnet, waarmee een virtueel netwerk van een andere resource groep kan worden gebruikt
   * Naam of ID van het netwerk profiel, dat u kunt verkrijgen met de [lijst AZ Network profile][az-network-profile-list]

Wanneer u uw eerste container groep implementeert in een bestaand subnet, delegeert azure dat subnet aan Azure Container Instances. U kunt niet langer andere resources dan container groepen implementeren op dat subnet.

## <a name="deployment-examples"></a>Voor beelden van implementaties

In de volgende secties wordt beschreven hoe u container groepen implementeert in een virtueel netwerk met de Azure CLI. De opdracht voorbeelden zijn ingedeeld voor de **bash** -shell. Als u de voor keur geeft aan een andere shell, zoals Power shell of opdracht prompt, past u de regel vervolg tekens dienovereenkomstig aan.

### <a name="deploy-to-a-new-virtual-network"></a>Implementeren in een nieuw virtueel netwerk

Implementeer eerst een container groep en geef de para meters op voor een nieuw virtueel netwerk en subnet. Wanneer u deze para meters opgeeft, maakt Azure het virtuele netwerk en het subnet, delegeert het subnet naar Azure container instances en maakt ook een netwerk profiel. Zodra deze resources zijn gemaakt, wordt uw container groep geïmplementeerd in het subnet.

Voer de volgende opdracht [AZ container Create][az-container-create] uit om de instellingen voor een nieuw virtueel netwerk en subnet op te duiden. U moet de naam opgeven van een resource groep die is gemaakt in een regio die container groepen in een virtueel netwerk [ondersteunt](#preview-limitations) . Met deze opdracht wordt de open bare container van micro soft [ACI-HelloWorld][aci-helloworld] geïmplementeerd die een kleine node. js-webserver voor een statische webpagina uitvoert. In de volgende sectie implementeert u een tweede container groep naar hetzelfde subnet en test u de communicatie tussen de twee container exemplaren.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

Wanneer u deze methode gebruikt voor het implementeren van een nieuw virtueel netwerk, kan het enkele minuten duren voordat de implementatie van de netwerk bronnen is gemaakt. Na de eerste implementatie worden extra implementaties van container groepen sneller uitgevoerd.

### <a name="deploy-to-existing-virtual-network"></a>Implementeren in een bestaand virtueel netwerk

Nu u een container groep hebt geïmplementeerd naar een nieuw virtueel netwerk, implementeert u een tweede container groep naar hetzelfde subnet en verifieert u de communicatie tussen de twee container exemplaren.

Haal eerst het IP-adres op van de eerste container groep die u hebt geïmplementeerd, de *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

In de uitvoer moet het IP-adres van de container groep in het privé-subnet worden weer gegeven:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Stel nu `CONTAINER_GROUP_IP` in op het IP-adres dat u hebt opgehaald met de opdracht `az container show` en voer de volgende `az container create` opdracht uit. Deze tweede container, *commchecker*, voert een alpine Linux-installatie kopie uit en voert `wget` uit op basis van het IP-adres van het particuliere subnet van de container groep.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

Nadat deze tweede container implementatie is voltooid, haalt u de logboeken op zodat u de uitvoer van de `wget` opdracht die deze heeft uitgevoerd, kunt zien:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Als de tweede container met het eerste heeft gecommuniceerd, moet de uitvoer er ongeveer als volgt uitzien:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

De logboek uitvoer geeft aan dat `wget` verbinding kon maken en het index bestand van de eerste container kunt downloaden met behulp van het privé-IP-adres op het lokale subnet. Het netwerk verkeer tussen de twee container groepen bleef binnen het virtuele netwerk.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Implementeren in een bestaand virtueel netwerk-YAML

U kunt ook een container groep implementeren in een bestaand virtueel netwerk met behulp van een YAML-bestand. Als u wilt implementeren in een subnet in een virtueel netwerk, geeft u verschillende extra eigenschappen op in de YAML:

* `ipAddress`: de IP-adres instellingen voor de container groep.
  * `ports`: de poorten die moeten worden geopend, indien van toepassing.
  * `protocol`: het Protocol (TCP of UDP) voor de geopende poort.
* `networkProfile`: Hiermee worden netwerk instellingen opgegeven, zoals het virtuele netwerk en het subnet voor een Azure-resource.
  * `id`: de volledige Resource Manager-Resource-ID van de `networkProfile`.

Als u een container groep wilt implementeren in een virtueel netwerk met een YAML-bestand, moet u eerst de ID van het netwerk profiel ophalen. Voer de opdracht [AZ Network profile list][az-network-profile-list] uit en geef de naam op van de resource groep die het virtuele netwerk en het overgedragen subnet bevat.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

De uitvoer van de opdracht geeft de volledige Resource-ID voor het netwerk profiel weer:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Wanneer u de netwerk profiel-ID hebt, kopieert u de volgende YAML naar een nieuw bestand met de naam *vnet-Deploy-ACI. yaml*. Vervang onder `networkProfile`de `id` waarde met de ID die u zojuist hebt opgehaald en sla het bestand op. Met deze YAML maakt u een container groep met de naam *appcontaineryaml* in uw virtuele netwerk.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Implementeer de container groep met de opdracht [AZ container Create][az-container-create] en geef de yaml-bestands naam op voor de para meter `--file`:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Zodra de implementatie is voltooid, voert u de opdracht [AZ container show][az-container-show] uit om de status weer te geven:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="delete-container-instances"></a>Container instanties verwijderen

Wanneer u klaar bent met de container instanties die u hebt gemaakt, verwijdert u deze met de volgende opdrachten:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Netwerk bronnen verwijderen


> [!NOTE]
> Als er een fout optreedt tijdens het verwijderen van het netwerk profiel, zijn 2-3 dagen toegestaan voor het platform om het probleem automatisch te verhelpen en de verwijdering opnieuw uit te voeren. Als u nog steeds problemen ondervindt met het verwijderen van het netwerk profiel, [opent u een ondersteunings reqest.](https://azure.microsoft.com/support/create-ticket/)

Voor de eerste preview van deze functie zijn verschillende extra opdrachten nodig om de netwerk resources te verwijderen die u eerder hebt gemaakt. Als u de voorbeeld opdrachten in vorige secties van dit artikel hebt gebruikt om uw virtuele netwerk en subnet te maken, kunt u het volgende script gebruiken om die netwerk resources te verwijderen.

Voordat u het script uitvoert, stelt u de `RES_GROUP`-variabele in op de naam van de resource groep met het virtuele netwerk en het subnet dat moet worden verwijderd. Werk de naam van het virtuele netwerk bij als u de naam van de `aci-vnet` eerder hebt voorgesteld. Het script is geformatteerd voor de bash-shell. Als u de voor keur geeft aan een andere shell, zoals Power shell of opdracht prompt, moet u de toewijzings-en toegangs rechten van de variabele dienovereenkomstig aanpassen.

> [!WARNING]
> Met dit script worden resources verwijderd! Hiermee verwijdert u het virtuele netwerk en alle subnetten die het bevat. Zorg ervoor dat u *een* van de resources in het virtuele netwerk niet meer nodig hebt, met inbegrip van de subnetten die het bevat, voordat u dit script uitvoert. Nadat **deze bronnen zijn verwijderd, kunnen ze onherstelbaar zijn**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Volgende stappen

Zie [een Azure-container groep met VNet maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)voor het implementeren van een nieuw virtueel netwerk, subnet, netwerk profiel en container groep met behulp van een resource manager-sjabloon.

In dit artikel worden kortere bronnen en functies voor het virtuele netwerk beschreven. De Azure Virtual Network-documentatie behandelt deze onderwerpen uitgebreid:

* [Virtueel netwerk](../virtual-network/manage-virtual-network.md)
* [Subnet](../virtual-network/virtual-network-manage-subnet.md)
* [Service-eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
