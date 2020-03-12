---
title: Persoonlijke koppeling instellen
description: Een persoonlijk eind punt instellen in een container register en een persoonlijke koppeling inschakelen in een lokaal virtueel netwerk
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: b7dcf2d1eb1a77ea8b9660318ed2a7d4ec183b42
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128395"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Een persoonlijke Azure-koppeling configureren voor een Azure container Registry 

U kunt een [persoonlijk eind punt](../private-link/private-endpoint-overview.md) instellen voor uw Azure container Registry, zodat clients op een virtueel Azure-netwerk veilig toegang hebben tot het REGI ster via een [privé-koppeling](../private-link/private-link-overview.md). Het persoonlijke eind punt gebruikt een IP-adres uit de adres ruimte van het virtuele netwerk voor uw REGI ster. Netwerk verkeer tussen de clients in het virtuele netwerk en het REGI ster gaat over het virtuele netwerk en een persoonlijke koppeling in het micro soft backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt voor komen.

U kunt [DNS-instellingen configureren](../private-link/private-endpoint-overview.md#dns-configuration) voor uw persoonlijke eind punt, zodat de instellingen kunnen worden omgezet in het toegewezen privé IP-adres van het REGI ster. Met DNS-configuratie kunnen clients en services in het netwerk het REGI ster blijven gebruiken op het Fully Qualified Domain Name van het REGI ster, zoals *myregistry.azurecr.io*.

Deze functie is beschikbaar in de service tier van het **Premium** -container register. Zie [Azure container Registry sku's](container-registry-skus.md)voor meer informatie over de service lagen en limieten voor het REGI ster.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er zijn enkele [beperkingen](#preview-limitations) van toepassing. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functie worden mogelijk nog gewijzigd voordat de functie algemeen beschikbaar wordt.

## <a name="preview-limitations"></a>Preview-beperkingen

* Op dit moment kunt u geen persoonlijke koppeling instellen met een persoonlijk eind punt in een [geo-gerepliceerd REGI ster](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>Vereisten

* Als u de stappen van Azure CLI in dit artikel wilt gebruiken, wordt Azure CLI-versie 2.2.0 of hoger aanbevolen. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren. Of voer uit in [Azure Cloud shell](../cloud-shell/quickstart.md).
* Als u nog geen container register hebt, maakt u er een (vereist een Premium-laag) en pusht u een voorbeeld installatie kopie, zoals `hello-world` van docker hub. Gebruik bijvoorbeeld de [Azure Portal][quickstart-portal] of de [Azure cli][quickstart-cli] om een REGI ster te maken. 

De Azure CLI-voor beelden in dit artikel gebruiken de volgende omgevings variabelen. Vervang de waarden die van toepassing zijn voor uw omgeving. Alle voor beelden zijn opgemaakt voor de bash-shell:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Een virtuele machine maken die is ingeschakeld voor docker

Gebruik voor test doeleinden een docker-Ubuntu-VM om toegang te krijgen tot een Azure container Registry. Als u Azure Active Directory verificatie wilt gebruiken voor het REGI ster, installeert u ook de [Azure cli][azure-cli] op de VM. Als u al een virtuele machine van Azure hebt, kunt u deze stap voor het maken overs Laan.

U kunt dezelfde resource groep gebruiken voor uw virtuele machine en het container register. Deze installatie vereenvoudigt aan het einde van het opschonen, maar is niet vereist. Als u ervoor kiest om een afzonderlijke resource groep te maken voor de virtuele machine en het virtuele netwerk, voert u [AZ Group Create][az-group-create]:

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Implementeer nu een standaard Ubuntu Azure-virtuele machine met [AZ VM Create][az-vm-create]. In het volgende voor beeld wordt een VM gemaakt met de naam *myDockerVM*.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt. Wanneer de opdracht is voltooid, noteert u de `publicIpAddress` die worden weer gegeven door de Azure CLI. Gebruik dit adres om SSH-verbindingen met de virtuele machine te maken.

### <a name="install-docker-on-the-vm"></a>Docker installeren op de VM

Nadat de virtuele machine is uitgevoerd, maakt u een SSH-verbinding met de VM. Vervang *publicIpAddress* door het open bare IP-adres van uw virtuele machine.

```bash
ssh azureuser@publicIpAddress
```

Voer de volgende opdrachten uit om docker te installeren op de Ubuntu-VM:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Na de installatie voert u de volgende opdracht uit om te controleren of docker correct wordt uitgevoerd op de VM:

```bash
sudo docker run -it hello-world
```

Uitvoer:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI installeren

Volg de stappen in [Azure cli installeren met apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) om de Azure CLI op uw virtuele Ubuntu-machine te installeren. Bijvoorbeeld:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Sluit de SSH-verbinding.

## <a name="set-up-private-link---cli"></a>Privé-koppeling instellen-CLI

### <a name="get-network-and-subnet-names"></a>Netwerk-en subnet namen ophalen

Als u deze nog niet hebt, hebt u de namen van een virtueel netwerk en subnet nodig om een persoonlijke koppeling in te stellen. In dit voor beeld gebruikt u hetzelfde subnet voor de virtuele machine en het persoonlijke eind punt van het REGI ster. In veel gevallen zou u het eind punt echter instellen in een afzonderlijk subnet. 

Wanneer u een virtuele machine maakt, maakt Azure standaard een virtueel netwerk in dezelfde resource groep. De naam van het virtuele netwerk is gebaseerd op de naam van de virtuele machine. Als u bijvoorbeeld een *myDockerVM*naam voor de virtuele machine hebt, is de standaard naam van het virtuele netwerk *myDockerVMVNET*, met een subnet met de naam *myDockerVMSubnet*. Stel deze waarden in omgevings variabelen in door de opdracht [AZ Network vnet List][az-network-vnet-list] uit te voeren:

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

### <a name="disable-network-policies-in-subnet"></a>Netwerk beleid in subnet uitschakelen

[Netwerk beleidsregels](../private-link/disable-private-endpoint-network-policy.md) , zoals netwerk beveiligings groepen, uitschakelen in het subnet voor het persoonlijke eind punt. De configuratie van uw subnet bijwerken met [AZ Network vnet subnet update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>De privé-DNS-zone configureren

Maak een privé-DNS-zone voor het priviate Azure container Registry-domein. In latere stappen maakt u DNS-records voor uw register domein in deze DNS-zone.

Als u een privé zone wilt gebruiken om de standaard DNS-omzetting voor uw Azure container Registry te overschrijven, moet de zone de naam **privatelink.azurecr.io**. Voer de volgende [AZ Network private-DNS zone Create][az-network-private-dns-zone-create] opdracht uit om de privé zone te maken:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Een koppelings koppeling maken

Voer [AZ Network private-DNS link vnet maken][az-network-private-dns-link-vnet-create] uit om uw persoonlijke zone te koppelen aan het virtuele netwerk. In dit voor beeld wordt een koppeling gemaakt met de naam *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Een persoonlijk REGI ster-eind punt maken

In deze sectie maakt u het persoonlijke eind punt van het REGI ster in het virtuele netwerk. Haal eerst de resource-ID van het REGI ster op:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Voer de opdracht [AZ Network private-endpoint Create][az-network-private-endpoint-create] uit om het persoonlijke eind punt van het REGI ster te maken.

In het volgende voor beeld wordt het eind punt *myPrivateEndpoint* en de *myConnection*voor de service verbinding gemaakt. Als u een container register bron voor het eind punt wilt opgeven, geeft u `--group-ids registry`:

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

### <a name="get-private-ip-addresses"></a>Privé-IP-adressen ophalen

Voer [AZ Network private-endpoint show][az-network-private-endpoint-show] uit om een query uit te voeren op het eind punt voor de netwerk interface-id:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Gekoppeld aan de netwerk interface zijn twee privé-IP-adressen voor het container register: een voor het REGI ster zelf en een voor het gegevens eindpunt van het REGI ster. Voer de volgende [AZ resource][az-resource-show] -opdrachten uit om de privé-IP-adressen voor het container register en het gegevens eindpunt van het REGI ster op te halen:

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

### <a name="create-dns-records-in-the-private-zone"></a>DNS-records in de privé zone maken

Met de volgende opdrachten maakt u DNS-records in de privé zone voor het eind punt van het REGI ster en het bijbehorende gegevens eindpunt. Als u bijvoorbeeld een REGI ster met de naam *myregistry* in de *Europa West* -regio hebt, worden de namen van eind punten `myregistry.azurecr.io` en `myregistry.westeurope.data.azurecr.io`. 

Eerst uitvoeren [AZ Network private-DNS record: Stel een Create][az-network-private-dns-record-set-a-create] in voor het maken van lege a-record sets voor het REGI ster-eind punt en het gegevens eindpunt:

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

Voer de opdracht [AZ Network private-DNS record-set a add-record][az-network-private-dns-record-set-a-add-record] uit om de a-records voor het REGI ster-eind punt en gegevens eindpunt te maken:

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

De persoonlijke koppeling is nu geconfigureerd en gereed voor gebruik.

## <a name="set-up-private-link---portal"></a>Een persoonlijke koppeling instellen-Portal

Bij de volgende stappen wordt ervan uitgegaan dat u al een virtueel netwerk en een subnet hebt ingesteld met een VM om te testen. U kunt ook [een nieuw virtueel netwerk en subnet maken](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

1. Navigeer in de portal naar het container register.
1. Selecteer onder **instellingen** **persoonlijke eindpunt verbindingen (preview-versie)** .
1. Selecteer **+ persoonlijk eind punt**.
1. Voer op het tabblad **basis beginselen** de volgende informatie in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Project Details** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Voer de naam van een bestaande groep in of maak een nieuwe.|
    | **Exemplaar Details** |  |
    | Naam | Voer een unieke naam in. |
    |Regio|Selecteer een regio.|
    |||
5. Selecteer **volgende: resource**.
6. Voer de volgende informatie in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer **verbinding maken met een Azure-resource in mijn Directory**.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **micro soft. ContainerRegistry/registers**. |
    | Resource |De naam van het REGI ster selecteren|
    |Doel-subresource |**REGI ster** selecteren|
    |||
7. Selecteer **volgende: Configuratie**.
8. Voer de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**Netwerken**| |
    | Virtueel netwerk| Selecteer het virtuele netwerk waarin uw virtuele machine is geïmplementeerd, zoals *myDockerVMVNET*. |
    | Subnet | Selecteer een subnet, zoals *myDockerVMSubnet* waarop uw virtuele machine is geïmplementeerd. |
    |**Integratie van Privé-DNS**||
    |Integreren met een privé-DNS-zone |Selecteer **Ja**. |
    |Privé-DNS zone |Selecteer *(nieuw) privatelink.azurecr.io* |
    |||

1. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert. 
2. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

Nadat het persoonlijke eind punt is gemaakt, worden de DNS-instellingen in de privé zone weer gegeven op de **overzichts** pagina van het eind punt.

![DNS-instellingen voor het eind punt](./media/container-registry-private-link/private-endpoint-overview.png)

Uw persoonlijke koppeling is nu geconfigureerd en gereed voor gebruik.

## <a name="validate-private-link-connection"></a>Verbinding met persoonlijke koppeling valideren

U moet controleren of de resources binnen het subnet van het particuliere eind punt verbinding maken met uw REGI ster via een particulier IP-adres en de juiste persoonlijke DNS-zone integratie hebben.

Als u de verbinding met de persoonlijke koppeling wilt valideren, moet u SSH naar de virtuele machine die u in het virtuele netwerk hebt ingesteld.

Voer de `nslookup` opdracht uit om het IP-adres van het REGI ster via de persoonlijke koppeling op te lossen:

```bash
nslookup $registryName.azurecr.io
```

Voorbeeld uitvoer toont het IP-adres van het REGI ster in de adres ruimte van het subnet:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Vergelijk dit resultaat met het open bare IP-adres in `nslookup` uitvoer voor hetzelfde REGI ster via een openbaar eind punt:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Register bewerkingen via een persoonlijke koppeling

Controleer ook of u register bewerkingen kunt uitvoeren vanaf de virtuele machine in het subnet. Maak een SSH-verbinding met uw virtuele machine en voer [AZ ACR login][az-acr-login] uit om u aan te melden bij het REGI ster. Afhankelijk van uw VM-configuratie moet u mogelijk de volgende opdrachten met `sudo`voor voegsel.

```bash
az acr login --name $registryName
```

Voer register bewerkingen, zoals `docker pull`, uit om een voorbeeld installatie kopie uit het REGI ster te halen. Vervang `hello-world:v1` door een afbeelding en code die geschikt is voor uw REGI ster, voorafgegaan door de naam van de aanmeldings server van het REGI ster (alle kleine letters):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker haalt de installatie kopie op naar de virtuele machine.

## <a name="manage-private-endpoint-connections"></a>Verbindingen met privé-eind punten beheren

Beheer van de privé-eind punten van een REGI ster met de Azure Portal, of met behulp van opdrachten in de opdracht groep [AZ ACR private-endpoint-Connection][az-acr-private-endpoint-connection] . Bewerkingen omvatten goed keuren, verwijderen, weer geven, afwijzen of Details van de particuliere eindpunt verbindingen van een REGI ster weer gegeven.

Als u bijvoorbeeld de verbindingen van het privé-eind punt van een REGI ster wilt weer geven, voert u de opdracht [AZ ACR private-endpoint-Connection List][az-acr-private-endpoint-connection-list] uit. Bijvoorbeeld:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Wanneer u een verbinding met een privé-eind punt instelt met behulp van de stappen in dit artikel, accepteert het REGI ster automatisch verbindingen van clients en services met RBAC-machtigingen voor het REGI ster. U kunt het eind punt zo instellen dat hand matige goed keuring van verbindingen is vereist. Zie [een particuliere-eindpunt verbinding beheren](../private-link/manage-private-endpoint.md)voor meer informatie over het goed keuren en afwijzen van privé-eindpunt verbindingen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle Azure-resources in dezelfde resource groep hebt gemaakt en deze niet meer nodig hebt, kunt u de resources eventueel verwijderen met behulp van één opdracht [AZ Group delete](/cli/azure/group) :

```azurecli
az group delete --name $resourceGroup
```

Als u uw resources in de portal wilt opschonen, gaat u naar de resource groep. Zodra de resource groep is geladen, klikt u op **resource groep verwijderen** om de resource groep en de resources die daar zijn opgeslagen, te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Zie de documentatie van [Azure private link](../private-link/private-link-overview.md) voor meer informatie over persoonlijke koppelingen.
* Een alternatief voor persoonlijke koppeling is het instellen van netwerk toegangs regels voor het beperken van de toegang tot het REGI ster. Zie [toegang tot een Azure container Registry beperken met behulp van een virtueel netwerk of een firewall-regel voor Azure](container-registry-vnet.md)voor meer informatie.

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
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns-zone/create#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns-link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
