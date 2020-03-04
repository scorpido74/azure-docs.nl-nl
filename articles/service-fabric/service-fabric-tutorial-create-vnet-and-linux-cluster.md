---
title: Een Linux Service Fabric-cluster maken in azure
description: Informatie over hoe u een Linux Service Fabric-cluster implementeert in een bestaand virtueel Azure-netwerk met behulp van Azure CLI.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: mvc
ms.openlocfilehash: f5788f07dd4a4f03a95efaea4b741cd64c930ac5
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251787"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Een Linux Service Fabric-cluster implementeren in een virtueel Azure-netwerk

In dit artikel leert u hoe u een Linux-Service Fabric cluster kunt implementeren in een [virtueel Azure-netwerk (VNET)](../virtual-network/virtual-networks-overview.md) met behulp van Azure CLI en een sjabloon. Wanneer u klaar bent, wordt er in de cloud een cluster uitgevoerd waarin u toepassingen kunt implementeren. Als u met behulp van PowerShell een Windows-cluster wilt maken, raadpleegt u [Een Service Fabric Windows-cluster in een Azure-netwerk implementeren](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="prerequisites"></a>Vereisten

Voordat u begint:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installeer de [Service Fabric CLI](service-fabric-cli.md)
* Installeer de [Azure CLI](/cli/azure/install-azure-cli)
* Lees [overzicht van Azure-clusters](service-fabric-azure-clusters-overview.md) voor meer informatie over de belangrijkste concepten van clusters
* [Plan en bereid](service-fabric-cluster-azure-deployment-preparation.md) u voor op een productie cluster implementatie.

Met de volgende procedures wordt er een Service Fabric-cluster met zeven knooppunten gemaakt. Gebruik de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te berekenen voor het uitvoeren van een Service Fabric-cluster in Azure.

## <a name="download-and-explore-the-template"></a>De sjabloon downloaden en verkennen

Download de volgende Resource Manager-sjabloonbestanden:

* [AzureDeploy. json][template]
* [AzureDeploy. para meters. json][parameters]

Met deze sjabloon implementeert u een veilig cluster van zeven virtuele machines en drie knooppunt typen in een virtueel netwerk.  Andere voorbeeldsjablonen zijn te vinden op [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). De [AzureDeploy. json][template] implementeert een aantal resources, waaronder de volgende.

### <a name="service-fabric-cluster"></a>Service Fabric-cluster

In de resource **Microsoft.ServiceFabric/clusters** wordt een Linux-cluster geïmplementeerd met de volgende kenmerken:

* drie knooppunttypen
* vijf knoop punten in het primaire knooppunt type (configureerbaar in de sjabloon parameters), één knoop punt in elk van de andere knooppunt typen
* een besturingssysteem: Ubuntu 16.04 LTS (configureerbaar in de sjabloonparameters)
* een beveiligd certificaat (configureerbaar in de sjabloonparameters)
* [DNS-service](service-fabric-dnsservice.md) is ingeschakeld
* een bronzen [duurzaamheidsniveau](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) (configureerbaar in de sjabloonparameters)
* een zilveren [betrouwbaarheidsniveau](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) (configureerbaar in de sjabloonparameters)
* eindpunt van de clientverbinding: 19000 (configureerbaar in de sjabloonparameters)
* eindpunt van de HTTP-gateway: 19080 (configureerbaar in de sjabloonparameters)

### <a name="azure-load-balancer"></a>Azure Load Balancer

In de resource **Microsoft.Network/loadBalancers** wordt een load balancer geconfigureerd en worden tests en regels ingesteld voor de volgende poorten:

* het eindpunt van de clientverbinding: 19000
* het eindpunt van de HTTP-gateway: 19080
* toepassingspoort: 80
* toepassingspoort: 443

### <a name="virtual-network-and-subnet"></a>Virtueel netwerk en subnet

De namen van het virtuele netwerk en het subnet worden gedeclareerd in de sjabloonparameters.  Adresruimten van het virtuele netwerk en subnet worden ook gedeclareerd in de sjabloonparameters en geconfigureerd in de resource **Microsoft.Network/virtualNetworks**:

* virtuele netwerkadresruimte: 10.0.0.0/16
* Service Fabric-subnetadresruimte: 10.0.2.0/24

Als er andere toepassingspoorten nodig zijn, moet u de resource Microsoft.Network/loadBalancers zo wijzigen dat verkeer kan binnenkomen.

## <a name="set-template-parameters"></a>De sjabloonparameters instellen

Het [AzureDeploy. para][parameters] meters-parameter bestand declareert veel waarden die worden gebruikt voor het implementeren van het cluster en de bijbehorende resources. Enkele van de parameters die u mogelijk moet wijzigen voor uw implementatie:

|Parameter|Voorbeeldwaarde|Opmerkingen|
|---|---||
|adminUserName|vmadmin| De gebruikersnaam van de beheerder van de cluster-VM's. |
|adminPassword|Password#1234| Het wachtwoord van de beheerder van de cluster-VM's.|
|clusterName|mysfcluster123| De naam van het cluster. |
|locatie|southcentralus| De locatie van het cluster. |
|certificateThumbprint|| <p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft.</p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de SHA1-waarde van de certificaatvingerafdruk in. Bijvoorbeeld 6190390162C988701DB5676EB81083EA608DCCF3. </p>|
|certificateUrlValue|| <p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft.</p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de URL van het certificaat in. Bijvoorbeeld ' https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346 '.</p>|
|sourceVaultValue||<p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft.</p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de waarde van de bronkluis in. Bijvoorbeeld /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT.</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Het virtuele netwerk en het cluster implementeren

Stel vervolgens de netwerktopologie in en implementeer het Service Fabric-cluster. Het Resource Manager-sjabloon [AzureDeploy. json][template] maakt een virtueel netwerk (VNET) en een subnet voor service Fabric. De sjabloon implementeert ook een cluster met certificaatbeveiliging ingeschakeld.  Gebruik voor productieclusters een certificaat van een certificeringsinstantie (CA) als clustercertificaat. Een zelfondertekend certificaat kan worden gebruikt om testclusters te beveiligen.

In de sjabloon in dit artikel wordt een cluster geïmplementeerd dat gebruikmaakt van de vinger afdruk van het certificaat om het cluster certificaat te identificeren.  Geen twee certificaten kunnen dezelfde vingerafdruk hebben, waardoor certificaatbeheer moeilijker wordt. Schakelen tussen een geïmplementeerd cluster vanuit vingerafdrukken voor certificaten naar het gebruik van gewone namen voor certificaten maakt het beheer van certificaten veel eenvoudiger.  Lees [Cluster wijzigen naar certificaatbeheer met gewone namen](service-fabric-cluster-change-cert-thumbprint-to-cn.md) voor informatie over het bijwerken van het cluster voor het gebruik van gewone namen voor certificaten voor het beheren van certificaten.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Een cluster maken met behulp van een bestaand certificaat

Het volgende script maakt gebruik van de opdracht en de sjabloon [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) om een nieuw cluster te implementeren dat met een bestaand certificaat is beveiligd. De opdracht maakt ook een nieuwe sleutelkluis in Azure en uploadt uw certificaat.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Een cluster met een nieuw, zelfondertekend certificaat maken

Het volgende script maakt gebruik van de opdracht [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) en een sjabloon om een nieuw cluster te implementeren in Azure. Met de opdracht wordt ook een nieuwe sleutelkluis in Azure gemaakt, een nieuw zelfondertekend certificaat toegevoegd aan de sleutelkluis en het certificaatbestand lokaal gedownload.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json \
   --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password \
   --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Verbinding maken met het beveiligde cluster

Maak verbinding met het cluster met behulp van de Service Fabric CLI-opdracht `sfctl cluster select` en uw sleutel.  Opmerking: gebruik voor een zelfondertekend certificaat alleen de optie **--no-verify**.

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Controleer of u bent verbonden en of het cluster goed werkt door de opdracht `sfctl cluster health` uit te voeren.

```console
sfctl cluster health
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet meteen verdergaat met het volgende artikel, is het wellicht een goed idee om [het cluster te verwijderen](service-fabric-cluster-delete.md). U bespaart dan kosten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [schalen van een cluster](service-fabric-tutorial-scale-cluster.md).

In de sjabloon in dit artikel wordt een cluster geïmplementeerd dat gebruikmaakt van de vinger afdruk van het certificaat om het cluster certificaat te identificeren.  Geen twee certificaten kunnen dezelfde vingerafdruk hebben, waardoor certificaatbeheer moeilijker wordt. Schakelen tussen een geïmplementeerd cluster vanuit vingerafdrukken voor certificaten naar het gebruik van gewone namen voor certificaten maakt het beheer van certificaten veel eenvoudiger.  Lees [Cluster wijzigen naar certificaatbeheer met gewone namen](service-fabric-cluster-change-cert-thumbprint-to-cn.md) voor informatie over het bijwerken van het cluster voor het gebruik van gewone namen voor certificaten voor het beheren van certificaten.

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
