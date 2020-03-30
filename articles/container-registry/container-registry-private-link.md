---
title: Privékoppeling instellen
description: Een privéeindpunt instellen in een containerregister en een privékoppeling in een lokaal virtueel netwerk inschakelen
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498911"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Azure Private Link configureren voor een Azure-containerregister 

U een [privéeindpunt](../private-link/private-endpoint-overview.md) instellen voor uw Azure-containerregister, zodat clients in een virtueel Azure-netwerk veilig toegang krijgen tot het register via een [privékoppeling.](../private-link/private-link-overview.md) Het privéeindpunt gebruikt een IP-adres uit de virtuele netwerkadresruimte voor uw register. Netwerkverkeer tussen de clients op het virtuele netwerk en het register doorkruist het virtuele netwerk en een privékoppeling op het Microsoft-backbonenetwerk, waardoor blootstelling van het openbare internet wordt geëlimineerd.

U [DNS-instellingen configureren](../private-link/private-endpoint-overview.md#dns-configuration) voor uw privéeindpunt, zodat de instellingen worden opgelost naar het toegewezen privé-IP-adres van het register. Met DNS-configuratie kunnen clients en services in het netwerk toegang blijven krijgen tot het register op de volledig gekwalificeerde domeinnaam van het register, zoals *myregistry.azurecr.io.*

Deze functie is beschikbaar in de servicelaag Van het Premium-containerregister. **Premium** Zie [SKU's](container-registry-skus.md)voor Azure Container Registry voor informatie over lagen en limieten voor registerservice.

> [!IMPORTANT]
> Deze functie is momenteel in preview en er zijn enkele [beperkingen](#preview-limitations) van toepassing. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="preview-limitations"></a>Preview-beperkingen

* Momenteel u geen privékoppeling instellen met een privéeindpunt in een [geo-gerepliceerd register.](container-registry-geo-replication.md) 

## <a name="prerequisites"></a>Vereisten

* Als u de Azure CLI-stappen in dit artikel wilt gebruiken, wordt Azure CLI-versie 2.2.0 of hoger aanbevolen. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli]. Of uitgevoerd in [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Als u nog geen containerregister hebt, maakt u één (Premium-laag `hello-world` vereist) en drukt u op een voorbeeldafbeelding, zoals vanuit Docker Hub. Gebruik bijvoorbeeld de [Azure-portal][quickstart-portal] of de [Azure CLI][quickstart-cli] om een register te maken.
* Als u registertoegang wilt configureren met behulp van een privékoppeling in een ander Azure-abonnement, moet u de bronprovider voor Azure Container Registry in dat abonnement registreren. Bijvoorbeeld:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

De Azure CLI-voorbeelden in dit artikel gebruiken de volgende omgevingsvariabelen. Vervang waarden die geschikt zijn voor uw omgeving. Alle voorbeelden zijn opgemaakt voor de Bash-shell:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Een docker-enabled virtuele machine maken

Gebruik voor testdoeleinden een Ubuntu VM met Docker om toegang te krijgen tot een Azure-containerregister. Als u Azure Active Directory-verificatie wilt gebruiken in het register, installeert u ook de [Azure CLI][azure-cli] op de VM. Als u al een virtuele Azure-machine hebt, slaat u deze creatiestap over.

U dezelfde resourcegroep gebruiken voor uw virtuele machine en uw containerregister. Deze installatie vereenvoudigt het opruimen aan het einde, maar is niet vereist. Als u ervoor kiest om een aparte brongroep voor de virtuele machine en het virtuele netwerk te maken, voert u [de AZ-groep uit:][az-group-create]

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Implementeer nu een standaard Ubuntu Azure virtuele machine met [az vm maken.][az-vm-create] In het volgende voorbeeld wordt een VM met de naam myDockerVM met de naam *MYDockerVM .*

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt. Wanneer de opdracht is voltooid, `publicIpAddress` neemt u nota van de weergave door de Azure CLI. Gebruik dit adres om SSH-verbindingen met de VM te maken.

### <a name="install-docker-on-the-vm"></a>Docker installeren op de VM

Nadat de VM is uitgevoerd, maakt u een SSH-verbinding met de VM. Vervang *publicIpAddress* door het openbare IP-adres van uw vm.

```bash
ssh azureuser@publicIpAddress
```

Voer de volgende opdrachten uit om Docker op de Ubuntu VM te installeren:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Voer na de installatie de volgende opdracht uit om te controleren of Docker correct op de VM wordt uitgevoerd:

```bash
sudo docker run -it hello-world
```

Uitvoer:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure-CLI installeren

Volg de stappen in [Azure CLI installeren met de toepasselijke inteom](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) de Azure CLI op uw virtuele Ubuntu-machine te installeren. Bijvoorbeeld:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Sluit de SSH-verbinding af.

## <a name="set-up-private-link---cli"></a>Privékoppeling instellen - CLI

### <a name="get-network-and-subnet-names"></a>Netwerk- en subnetnamen weergeven

Als je ze nog niet hebt, heb je de namen van een virtueel netwerk en subnet nodig om een privélink in te stellen. In dit voorbeeld gebruikt u hetzelfde subnet voor de VM en het privéeindpunt van het register. In veel scenario's stelt u het eindpunt echter in een apart subnet in. 

Wanneer u een vm maakt, maakt Azure standaard een virtueel netwerk in dezelfde brongroep. De naam van het virtuele netwerk is gebaseerd op de naam van de virtuele machine. Als u bijvoorbeeld uw virtuele machine *myDockerVM noemt,* is de standaardnaam van het virtuele netwerk *myDockerVMVNET*, met een subnet met de naam *myDockerVMSubnet*. Stel deze waarden in omgevingsvariabelen in door de opdracht [vnet-lijst van az-netwerk][az-network-vnet-list] uit te voeren:

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>Netwerkbeleid uitschakelen in subnet

[Netwerkbeleid uitschakelen,](../private-link/disable-private-endpoint-network-policy.md) zoals netwerkbeveiligingsgroepen in het subnet voor het privéeindpunt. Werk uw subnetconfiguratie bij met [de vnet-subnetupdate van het AZ-netwerk:][az-network-vnet-subnet-update]

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>De privé-DNS-zone configureren

Maak een privé-DNS-zone voor het priviate Azure-containerregisterdomein. In latere stappen maakt u DNS-records voor uw registerdomein binnen deze DNS-zone.

Als u een privézone wilt gebruiken om de standaardDNS-resolutie voor uw Azure-containerregister te overschrijven, moet de zone **privatelink.azurecr.io**worden genoemd. Voer de volgende [private-dns-zone van az-netwerk uit om][az-network-private-dns-zone-create] de privézone te maken:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Een koppeling maken

Run [az network private-dns link vnet create][az-network-private-dns-link-vnet-create] to associate your private zone with the virtual network. In dit voorbeeld wordt een koppeling met de naam *myDNSLink aanknopingspunt .*

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Een eindpunt voor een privéregister maken

Maak in deze sectie het privéeindpunt van het register in het virtuele netwerk. Download eerst de bron-ID van uw register:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Voer de [opdracht privé-eindpunt van het AZ-netwerk uit][az-network-private-endpoint-create] om het privéeindpunt van het register te maken.

In het volgende voorbeeld wordt het eindpunt *myPrivateEndpoint* en serviceverbinding *myConnection*gemaakt. Als u een containerregisterbron voor `--group-ids registry`het eindpunt wilt opgeven, geeft u het volgende door:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Privé-IP-adressen opmaken

Voer [het privé-eindpunt van het AZ-netwerk uit][az-network-private-endpoint-show] om het eindpunt voor de netwerkinterface-id op te vragen:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Gekoppeld aan de netwerkinterface zijn twee privé IP-adressen voor het containerregister: een voor het register zelf en een voor het eindpunt van de gegevens van het register. Voer de volgende [az-resourceshowopdrachten uit][az-resource-show] om de privé-IP-adressen voor het containerregister en het eindpunt van de gegevens van het register op te halen:

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>DNS-records maken in de privézone

Met de volgende opdrachten worden DNS-records gemaakt in de privézone voor het eindpunt van het register en het eindpunt van de gegevens. Als u bijvoorbeeld een register met de naam *myregistry* in de `myregistry.azurecr.io` `myregistry.westeurope.data.azurecr.io`regio *West-Europa* hebt, zijn de eindpuntnamen en . 

Eerste run [az network private-dns record-set een maken][az-network-private-dns-record-set-a-create] om lege A record sets voor het register eindpunt en gegevens eindpunt te maken:

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

Voer de [private-dns-recordset van het AZ-netwerk uit, een addrecordopdracht][az-network-private-dns-record-set-a-add-record] om de A-records voor het eindpunt en het eindpunt van gegevens te maken:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

De privékoppeling is nu geconfigureerd en klaar voor gebruik.

## <a name="set-up-private-link---portal"></a>Privékoppeling instellen - portal

In de volgende stappen wordt ervan uitgegaan dat u al een virtueel netwerk en subnet hebt ingesteld met een VM om te testen. U ook [een nieuw virtueel netwerk en subnet maken.](../virtual-network/quick-create-portal.md)

### <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

1. Navigeer in de portal naar uw containerregister.
1. Selecteer **onder Instellingen**de optie **Privé-eindpuntverbindingen (Voorbeeld)**.
1. Selecteer **+ Privé eindpunt**.
1. Voer op het tabblad **Basisbeginselen** de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectdetails** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Voer de naam van een bestaande groep in of maak een nieuwe groep.|
    | **Instantiedetails** |  |
    | Name | Voer een unieke naam in. |
    |Regio|Selecteer een regio.|
    |||
5. Selecteer **Volgende: Resource**.
6. Voer de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer **Verbinding maken met een Azure-bron in mijn map**.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **Microsoft.ContainerRegistry/registries**. |
    | Resource |Selecteer de naam van uw register|
    |Subresource target |Register **selecteren**|
    |||
7. Selecteer **Volgende: Configuratie**.
8. Voer de gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**Networking**| |
    | Virtueel netwerk| Selecteer het virtuele netwerk waar uw virtuele machine wordt geïmplementeerd, zoals *myDockerVMVNET*. |
    | Subnet | Selecteer een subnet, zoals *myDockerVMSubnet* waar uw virtuele machine wordt geïmplementeerd. |
    |**Private DNS-integratie**||
    |Integreren met private DNS-zone |Selecteer **Ja**. |
    |Privé-DNS-zone |Selecteer *(Nieuw) privatelink.azurecr.io* |
    |||

1. Selecteer **Controleren + maken**. U wordt naar de **pagina Controleren + maken geleid,** waar Azure uw configuratie valideert. 
2. Wanneer u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken**.

Nadat het privéeindpunt is gemaakt, worden DNS-instellingen in de privézone weergegeven op de **pagina Overzicht** van het eindpunt.

![DNS-instellingen voor eindpunten](./media/container-registry-private-link/private-endpoint-overview.png)

Uw privékoppeling is nu geconfigureerd en klaar voor gebruik.

## <a name="validate-private-link-connection"></a>Privékoppeling valideren

U moet valideren dat de bronnen binnen het subnet van het privé-eindpunt verbinding maken met uw register via een privé-IP-adres en de juiste integratie van de privé-DNS-zone hebben.

Om de privékoppelingsverbinding te valideren, gaat SSH naar de virtuele machine die u in het virtuele netwerk hebt ingesteld.

Voer `nslookup` de opdracht uit om het IP-adres van uw register via de privékoppeling op te lossen:

```bash
nslookup $registryName.azurecr.io
```

Voorbeelduitvoer toont het IP-adres van het register in de adresruimte van het subnet:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Vergelijk dit resultaat met het `nslookup` openbare IP-adres in uitvoer voor hetzelfde register over een openbaar eindpunt:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Registerbewerkingen via privékoppeling

Controleer ook of u registerbewerkingen uitvoeren vanaf de virtuele machine in het subnet. Maak een SSH-verbinding met uw virtuele machine en voer [az acr-inloginlog][az-acr-login] uit om in te loggen op uw register. Afhankelijk van uw VM-configuratie moet u mogelijk de `sudo`volgende opdrachten vooraf maken met .

```bash
az acr login --name $registryName
```

Voer registerbewerkingen `docker pull` uit, bijvoorbeeld om een voorbeeldafbeelding uit het register te halen. Vervang `hello-world:v1` door een afbeelding en tag die geschikt is voor uw register, vooraf bevestigd door de naam van de registerloginserver (alle kleine letters):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker trekt de afbeelding naar de VM.

## <a name="manage-private-endpoint-connections"></a>Privé-eindpuntverbindingen beheren

Beheer de privéeindpuntverbindingen van een register met behulp van de Azure-portal of met opdrachten in de opdrachtgroep [az acr private-endpoint-connection.][az-acr-private-endpoint-connection] Bewerkingen omvatten het goedkeuren, verwijderen, aanbieden, weigeren of weergeven van details over de privéeindpuntverbindingen van een register.

Als u bijvoorbeeld de privéeindpuntverbindingen van een register wilt weergeven, voert u de [opdracht az acr private-endpoint-connection][az-acr-private-endpoint-connection-list] uit. Bijvoorbeeld:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Wanneer u een privéeindpuntverbinding instelt met de stappen in dit artikel, accepteert het register automatisch verbindingen van clients en services met RBAC-machtigingen in het register. U het eindpunt zo instellen dat verbindingen handmatig moeten worden goedgekeurd. Zie [Een privé-eindpuntverbinding beheren](../private-link/manage-private-endpoint.md)voor informatie over het goedkeuren en weigeren van privéeindpuntverbindingen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle Azure-bronnen in dezelfde brongroep hebt gemaakt en deze niet meer nodig hebt, u de resources optioneel verwijderen met één opdracht [voor het verwijderen van az-groepen:](/cli/azure/group)

```azurecli
az group delete --name $resourceGroup
```

Als u uw resources in de portal wilt opschonen, navigeert u naar uw resourcegroep. Zodra de resourcegroep is geladen, klikt u op **Resourcegroep verwijderen** om de resourcegroep en de bronnen die daar zijn opgeslagen te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Zie de [Azure Private Link-documentatie](../private-link/private-link-overview.md) voor meer informatie over Private Link.
* Een alternatief voor privékoppeling is het instellen van netwerktoegangsregels om de toegang tot het register te beperken. Zie [Toegang tot een Azure-containerregister beperken met behulp van een Azure-virtueel netwerk of firewallregels](container-registry-vnet.md)voor meer informatie.

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
