---
title: Containergroep implementeren in virtueel Azure-netwerk
description: Meer informatie over het implementeren van containergroepen naar een nieuw of bestaand virtueel Azure-netwerk.
ms.topic: article
ms.date: 01/06/2020
ms.author: danlep
ms.openlocfilehash: 318576e9b5c5b32bbc993ea16494c938b74bd2f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77200058"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Containerexemplaren implementeren in een virtueel Azure-netwerk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biedt veilige, privénetwerken voor uw Azure- en on-premises bronnen. Door containergroepen in een virtueel Azure-netwerk te implementeren, kunnen uw containers veilig communiceren met andere bronnen in het virtuele netwerk.

Containergroepen die zijn geïmplementeerd in een virtueel Azure-netwerk, maken scenario's mogelijk, zoals:

* Directe communicatie tussen containergroepen in hetzelfde subnet
* [Taakgebaseerde](container-instances-restart-policy.md) werkbelastinguitvoer van containerinstanties naar een database in het virtuele netwerk verzenden
* Inhoud voor containerexemplaren ophalen van een [serviceeindpunt](../virtual-network/virtual-network-service-endpoints-overview.md) in het virtuele netwerk
* Containercommunicatie met virtuele machines in het virtuele netwerk
* Containercommunicatie met on-premises bronnen via een [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Implementaties van containergroepen op een virtueel netwerk zijn over het algemeen alleen beschikbaar voor productieworkloads in de volgende regio's: **Oost-VS, South Central US en West US 2**. In andere regio's waar de functie beschikbaar is, zijn virtuele netwerkimplementaties momenteel in preview, waarbij de algemene beschikbaarheid in de nabije toekomst is gepland. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. 


## <a name="virtual-network-deployment-limitations"></a>Beperkingen voor virtuele netwerkimplementatie

Bepaalde beperkingen zijn van toepassing wanneer u containergroepen implementeert op een virtueel netwerk.

* Als u containergroepen wilt implementeren in een subnet, kan het subnet geen andere resourcetypen bevatten. Verwijder alle bestaande bronnen uit een bestaand subnet voordat u containergroepen naar het subnet implementeert of maak een nieuw subnet.
* U een [beheerde identiteit](container-instances-managed-identity.md) niet gebruiken in een containergroep die is geïmplementeerd in een virtueel netwerk.
* U een [liveness-sonde](container-instances-liveness-probe.md) of [gereedheidssssonde](container-instances-readiness-probe.md) niet inschakelen in een containergroep die is geïmplementeerd in een virtueel netwerk.
* Vanwege de extra netwerkbronnen die betrokken zijn, is het implementeren van een containergroep in een virtueel netwerk doorgaans trager dan het implementeren van een standaardcontainerinstantie.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Limieten voor containerresources kunnen verschillen van limieten voor containerexemplaren die niet in een netwerk zijn in deze regio's. Momenteel worden alleen Linux-containers ondersteund voor deze functie. Windows-ondersteuning staat op de planning.

### <a name="unsupported-networking-scenarios"></a>Niet-ondersteunde netwerkscenario's 

* **Azure Load Balancer** - Het plaatsen van een Azure Load Balancer voor containerexemplaren in een netwerkcontainergroep wordt niet ondersteund
* **Virtueel netwerk peering**
  * VNet-peering werkt niet voor ACI als het netwerk waar de ACI VNet naar peert, een openbare IP-ruimte gebruikt. Het peered netwerk heeft een RFC 1918 private IP-ruimte nodig om VNet-peering te laten werken. 
  * U uw VNet alleen naar een andere VNet
* **Routering van virtueel netwerkverkeer** - Aangepaste routes kunnen niet worden ingesteld rond openbare IP's. Routes kunnen worden ingesteld binnen de privé-IP-ruimte van het gedelegeerde subnet waarin de ACI-resources worden geïmplementeerd 
* **Netwerkbeveiligingsgroepen** - Uitgaande beveiligingsregels in NSG's die worden toegepast op een subnet dat is gedelegeerd aan Azure Container Instances, worden momenteel niet afgedwongen 
* **Openbaar IP- of DNS-label** - Containergroepen die zijn geïmplementeerd in een virtueel netwerk, ondersteunen momenteel geen ondersteuning voor het rechtstreeks blootstellen van containers aan het internet met een openbaar IP-adres of een volledig gekwalificeerde domeinnaam
* **Interne naamomzetting** - Naamomzetting voor Azure-bronnen in het virtuele netwerk via de interne Azure DNS wordt niet ondersteund

**Voor het verwijderen van netwerkbronnen** zijn [extra stappen](#delete-network-resources) vereist nadat u containergroepen hebt geïmplementeerd in het virtuele netwerk.

## <a name="required-network-resources"></a>Vereiste netwerkbronnen

Er zijn drie Azure Virtual Network-resources nodig voor het implementeren van containergroepen naar een virtueel netwerk: het [virtuele netwerk](#virtual-network) zelf, een [gedelegeerd subnet](#subnet-delegated) binnen het virtuele netwerk en een [netwerkprofiel.](#network-profile) 

### <a name="virtual-network"></a>Virtueel netwerk

Een virtueel netwerk definieert de adresruimte waarin u een of meer subnetten maakt. Vervolgens implementeert u Azure-resources (zoals containergroepen) in de subnetten in uw virtuele netwerk.

### <a name="subnet-delegated"></a>Subnet (gedelegeerd)

Subnetten segmenteren het virtuele netwerk in afzonderlijke adresruimten die kunnen worden gebruikt door de Azure-resources die u erin plaatst. U maakt een of meerdere subnetten binnen een virtueel netwerk.

Het subnet dat u voor containergroepen gebruikt, kan alleen containergroepen bevatten. Wanneer u een containergroep voor het eerst implementeert naar een subnet, wordt dat subnet door Azure-containerinstanties gedeerd. Eenmaal gedelegeerd, kan het subnet alleen worden gebruikt voor containergroepen. Als u andere resources dan containergroepen probeert te implementeren in een gedelegeerd subnet, mislukt de bewerking.

### <a name="network-profile"></a>Netwerkprofiel

Een netwerkprofiel is een sjabloon voor netwerkconfiguratie voor Azure-bronnen. Het geeft bepaalde netwerkeigenschappen voor de bron op, bijvoorbeeld het subnet waarin het moet worden geïmplementeerd. Wanneer u de opdracht [az-container maken][az-container-create] voor het eerst gebruikt om een containergroep te implementeren in een subnet (en dus een virtueel netwerk), maakt Azure een netwerkprofiel voor u. U dat netwerkprofiel vervolgens gebruiken voor toekomstige implementaties naar het subnet. 

Als u een resourcebeheersjabloon, YAML-bestand of een programmatische methode wilt gebruiken om een containergroep te implementeren in een subnet, moet u de volledige resource-id van resourcebeheer van een netwerkprofiel verstrekken. U een profiel gebruiken dat eerder is gemaakt met het maken van [AZ-containers][az-container-create]of een profiel maken met behulp van een resourcemanagersjabloon (zie [voorbeeld van sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) en [verwijzing).](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles) Als u de id van een eerder aangemaakt profiel wilt krijgen, gebruikt u de [opdracht az-netwerkprofiellijst.][az-network-profile-list] 

In het volgende diagram zijn verschillende containergroepen geïmplementeerd aan een subnet dat is gedelegeerd aan Azure Container Instances. Zodra u één containergroep hebt geïmplementeerd in een subnet, u er extra containergroepen op implementeren door hetzelfde netwerkprofiel op te geven.

![Containergroepen binnen een virtueel netwerk][aci-vnet-01]

## <a name="deployment-scenarios"></a>Implementatiescenario's

U [az-containermaken][az-container-create] gebruiken om containergroepen te implementeren in een nieuw virtueel netwerk en Azure in staat te stellen de vereiste netwerkbronnen voor u te maken of te implementeren in een bestaand virtueel netwerk. 

### <a name="new-virtual-network"></a>Nieuw virtueel netwerk

Als u wilt implementeren naar een nieuw virtueel netwerk en Azure automatisch de netwerkbronnen voor u wilt laten maken, geeft u het volgende op wanneer u [az-containermaakt:][az-container-create]

* Naam van virtueel netwerk
* Voorvoegsel voor virtueel netwerkadres in CIDR-indeling
* Subnetnaam
* Subnetadresvoorvoeging in CIDR-indeling

De voorvoegsels voor virtuele netwerk- en subnetadressen geven respectievelijk de adresruimten voor het virtuele netwerk en subnet op. Deze waarden worden bijvoorbeeld weergegeven in CIDR-notatie (Classless `10.0.0.0/16`Inter-Domain Routing). Zie [Een virtueel netwerksubnet toevoegen, wijzigen of verwijderen](../virtual-network/virtual-network-manage-subnet.md)voor meer informatie over het werken met subnetten.

Zodra u uw eerste containergroep met deze methode hebt geïmplementeerd, u deze implementeren naar hetzelfde subnet door de namen van het virtuele netwerk en subnet op te geven of door het netwerkprofiel op te geven dat Azure automatisch voor u maakt. Omdat Azure het subnet aan Azure Container Instances delegeert, u *alleen* containergroepen implementeren in het subnet.

### <a name="existing-virtual-network"></a>Bestaand virtueel netwerk

Een containergroep implementeren in een bestaand virtueel netwerk:

1. Maak een subnet binnen uw bestaande virtuele netwerk, gebruik een bestaand subnet waarin een containergroep al is geïmplementeerd of gebruik een bestaand subnet dat is geleegd van *alle* andere bronnen
1. Een containergroep met [az-container maken][az-container-create] en een van de volgende opgeven:
   * Naam van het virtuele netwerk en subnetnaam
   * Virtuele netwerkbron-ID en subnetbron-ID, waarmee een virtueel netwerk uit een andere brongroep kan worden gebruikt
   * Netwerkprofielnaam of ID, die u verkrijgen met behulp van [de az-netwerkprofiellijst][az-network-profile-list]

Zodra u uw eerste containergroep implementeert in een bestaand subnet, wordt dat subnet door Azure-containerimplementaties gedelegeerd naar Azure Container Instances. U geen andere resources dan containergroepen meer implementeren in dat subnet.

## <a name="deployment-examples"></a>Voorbeelden van implementatie

In de volgende secties wordt beschreven hoe u containergroepen implementeert naar een virtueel netwerk met de Azure CLI. De opdrachtvoorbeelden zijn opgemaakt voor de **bashshell.** Als u de voorkeur geeft aan een andere shell, zoals PowerShell of Command Prompt, past u de vervolgtekens van de regel dienovereenkomstig aan.

### <a name="deploy-to-a-new-virtual-network"></a>Implementeren in een nieuw virtueel netwerk

Implementeer eerst een containergroep en geef de parameters op voor een nieuw virtueel netwerk en subnet. Wanneer u deze parameters opgeeft, maakt Azure het virtuele netwerk en subnet, geeft het subnet de plaats aan Azure Container-exemplaren en maakt het ook een netwerkprofiel. Zodra deze resources zijn gemaakt, wordt uw containergroep geïmplementeerd in het subnet.

Voer de volgende opdracht [az-container maken][az-container-create] uit die instellingen opgeeft voor een nieuw virtueel netwerk en subnet. U moet de naam opgeven van een resourcegroep die is gemaakt in een gebied waar implementaties van containergroepen in een virtueel netwerk [beschikbaar](#virtual-network-deployment-limitations)zijn. Met deze opdracht wordt de openbare Microsoft [aci-helloworld-container][aci-helloworld] geïmplementeerd waarop een kleine Node.js-webserver wordt uitgevoerd die een statische webpagina bedient. In de volgende sectie implementeert u een tweede containergroep naar hetzelfde subnet en test u de communicatie tussen de twee containerinstanties.

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

Wanneer u met deze methode naar een nieuw virtueel netwerk implementeert, kan de implementatie enkele minuten duren voordat de netwerkbronnen worden gemaakt. Na de eerste implementatie worden extra implementaties van containergroepen sneller voltooid.

### <a name="deploy-to-existing-virtual-network"></a>Implementeren in bestaand virtueel netwerk

Nu u een containergroep hebt geïmplementeerd in een nieuw virtueel netwerk, implementeert u een tweede containergroep naar hetzelfde subnet en controleert u de communicatie tussen de twee containerinstanties.

Haal eerst het IP-adres op van de eerste containergroep die u hebt geïmplementeerd, de *appcontainer:*

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

De uitvoer moet het IP-adres van de containergroep weergeven in het privésubnet:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Stel nu `CONTAINER_GROUP_IP` het IP in dat `az container show` u met de `az container create` opdracht hebt opgehaald en voer de volgende opdracht uit. Deze tweede container, *commchecker,* draait een Alpine `wget` Linux-gebaseerde afbeelding en voert uit tegen het privé subnet IP-adres van de eerste containergroep.

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

Nadat deze tweede containerimplementatie is voltooid, trekt u de `wget` logboeken uit zodat u de uitvoer zien van de opdracht die is uitgevoerd:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Als de tweede container met de eerste met succes is gecommuniceerd, moet de uitvoer vergelijkbaar zijn met:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

De loguitvoer moet `wget` aantonen dat het indexbestand van de eerste container kon worden verbonden en gedownload met behulp van het privé-IP-adres op het lokale subnet. Het netwerkverkeer tussen de twee containergroepen bleef binnen het virtuele netwerk.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Implementeren in bestaand virtueel netwerk - YAML

U een containergroep ook implementeren in een bestaand virtueel netwerk met behulp van een YAML-bestand, een Resource Manager-sjabloon of een andere programmatische methode, zoals met de Python SDK. Als u wilt implementeren op een subnet in een virtueel netwerk, geeft u verschillende extra eigenschappen op in de YAML:

* `ipAddress`: De IP-adresinstellingen voor de containergroep.
  * `ports`: De poorten te openen, indien van toepassing.
  * `protocol`: Het protocol (TCP of UDP) voor de geopende poort.
* `networkProfile`: Hiermee geeft u netwerkinstellingen op, zoals het virtuele netwerk en subnet voor een Azure-bron.
  * `id`: De volledige resource-id `networkProfile`van Resource Manager van de .

Als u een containergroep wilt implementeren in een virtueel netwerk met een YAML-bestand, moet u eerst de id van het netwerkprofiel opvragen. Voer de [opdracht lijst met az-netwerkprofielen][az-network-profile-list] uit en geef de naam op van de resourcegroep die uw virtuele netwerk en gedelegeerd subnet bevat.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

De uitvoer van de opdracht geeft de volledige resource-id voor het netwerkprofiel weer:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Zodra u de netwerkprofiel-id hebt, kopieert u de volgende YAML naar een nieuw bestand met de naam *vnet-deploy-aci.yaml*. Vervang `networkProfile`onder `id` , vervang de waarde door ID die u zojuist hebt opgehaald en sla het bestand op. Deze YAML maakt een containergroep met de naam *appcontaineryaml* in uw virtuele netwerk.

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
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Implementeer de containergroep met de opdracht [AZ-container maken][az-container-create] en `--file` geef de YAML-bestandsnaam voor de parameter op:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Zodra de implementatie is voltooid, voert u de opdracht [AZ-containershow uit][az-container-show] om de status weer te geven:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="delete-container-instances"></a>Containerexemplaren verwijderen

Wanneer u klaar bent met het werken met de containerinstanties die u hebt gemaakt, verwijdert u deze met de volgende opdrachten:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Netwerkbronnen verwijderen

Deze functie vereist momenteel verschillende extra opdrachten om de netwerkbronnen die u eerder hebt gemaakt te verwijderen. Als u de voorbeeldopdrachten in eerdere delen van dit artikel hebt gebruikt om uw virtuele netwerk en subnet te maken, u het volgende script gebruiken om deze netwerkbronnen te verwijderen. Het script gaat ervan uit dat uw resourcegroep één virtueel netwerk bevat met één netwerkprofiel.

Voordat u het script `RES_GROUP` uitvoert, stelt u de variabele in op de naam van de resourcegroep die het virtuele netwerk en subnet bevat dat moet worden verwijderd. Werk de naam van het virtuele netwerk `aci-vnet` bij als u de eerder voorgestelde naam niet hebt gebruikt. Het script is opgemaakt voor de Bash-shell. Als u de voorkeur geeft aan een andere shell, zoals PowerShell of Command Prompt, moet u variabele toewijzing en accessoires dienovereenkomstig aanpassen.

> [!WARNING]
> Dit script verwijdert bronnen! Het verwijdert het virtuele netwerk en alle subnetten die het bevat. Zorg ervoor dat u *geen* van de bronnen in het virtuele netwerk meer nodig hebt, inclusief eventuele subnetten die het bevat, voordat u dit script uitvoert. Eenmaal verwijderd, **zijn deze bronnen niet meer intecupererbaar**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Volgende stappen

Zie [Een Azure-containergroep](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)maken met VNet als u een nieuw virtueel netwerk, subnet, netwerkprofiel en containergroep wilt implementeren met behulp van een resourcemanagersjabloon.

Verschillende virtuele netwerkbronnen en -functies werden besproken in dit artikel, hoewel kort. De documentatie van het Azure Virtual Network behandelt deze onderwerpen uitgebreid:

* [Virtueel netwerk](../virtual-network/manage-virtual-network.md)
* [Subnet](../virtual-network/virtual-network-manage-subnet.md)
* [Service-eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute ExpressRoute](../expressroute/expressroute-introduction.md)

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
