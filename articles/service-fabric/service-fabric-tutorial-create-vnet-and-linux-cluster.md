---
title: Een Linux-servicestructuurcluster maken in Azure
description: Informatie over hoe u een Linux Service Fabric-cluster implementeert in een bestaand virtueel Azure-netwerk met behulp van Azure CLI.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: mvc
ms.openlocfilehash: f5788f07dd4a4f03a95efaea4b741cd64c930ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251787"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Een Linux Service Fabric-cluster implementeren in een virtueel Azure-netwerk

In dit artikel leert u hoe u een Linux Service Fabric-cluster implementeert in een [Virtual Network (Azure)](../virtual-network/virtual-networks-overview.md) met Azure CLI en een sjabloon. Wanneer u klaar bent, wordt er in de cloud een cluster uitgevoerd waarin u toepassingen kunt implementeren. Als u met behulp van PowerShell een Windows-cluster wilt maken, raadpleegt u [Een Service Fabric Windows-cluster in een Azure-netwerk implementeren](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="prerequisites"></a>Vereisten

Voordat u begint:

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* De CLI van de [servicestructuur installeren](service-fabric-cli.md)
* De [Azure CLI installeren](/cli/azure/install-azure-cli)
* Lees [Overzicht van Azure-clusters](service-fabric-azure-clusters-overview.md) voor meer informatie over de belangrijkste concepten van clusters
* [Plannen en voorbereiden op](service-fabric-cluster-azure-deployment-preparation.md) een implementatie van een productiecluster.

Met de volgende procedures wordt er een Service Fabric-cluster met zeven knooppunten gemaakt. Gebruik de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te berekenen voor het uitvoeren van een Service Fabric-cluster in Azure.

## <a name="download-and-explore-the-template"></a>De sjabloon downloaden en verkennen

Download de volgende Resource Manager-sjabloonbestanden:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

Deze sjabloon implementeert een beveiligd cluster van zeven virtuele machines en drie knooppunttypen in een virtueel netwerk.  Andere voorbeeldsjablonen zijn te vinden op [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Met [AzureDeploy.json][template] wordt een aantal resources geïmplementeerd, waaronder de volgende.

### <a name="service-fabric-cluster"></a>Service Fabric-cluster

In de resource **Microsoft.ServiceFabric/clusters** wordt een Linux-cluster geïmplementeerd met de volgende kenmerken:

* drie knooppunttypen
* vijf knooppunten in het primaire knooppunttype (configureerbaar in de sjabloonparameters), één knooppunt in elk van de andere knooppunttypen
* een besturingssysteem: Ubuntu 16.04 LTS (configureerbaar in de sjabloonparameters)
* beveiligd met een certificaat (configureerbaar in de sjabloonparameters)
* een ingeschakelde [DNS-service](service-fabric-dnsservice.md)
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

In het parameterbestand [AzureDeploy.Parameters][parameters] worden veel waarden gedeclareerd die worden gebruikt om het cluster en bijbehorende resources te implementeren. Enkele van de parameters die u mogelijk moet wijzigen voor uw implementatie:

|Parameter|Voorbeeldwaarde|Opmerkingen|
|---|---||
|adminUserName|vmadmin| De gebruikersnaam van de beheerder van de cluster-VM's. |
|adminPassword|Password#1234| Het wachtwoord van de beheerder van de cluster-VM's.|
|clusterName|mysfcluster123| De naam van het cluster. |
|location|southcentralus| De locatie van het cluster. |
|certificateThumbprint|| <p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft.</p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de SHA1-waarde van de certificaatvingerafdruk in. Bijvoorbeeld 6190390162C988701DB5676EB81083EA608DCCF3. </p>|
|certificateUrlValue|| <p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft.</p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de URL van het certificaat in. Bijvoorbeeld "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>De waarde moet leeg zijn als u een zelfondertekend certificaat maakt of als u een certificaatbestand opgeeft.</p><p>Als u een bestaand certificaat wilt gebruiken dat u eerder hebt geüpload naar een sleutelkluis, vult u de waarde van de bronkluis in. Bijvoorbeeld /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT.</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Het virtuele netwerk en het cluster implementeren

Stel vervolgens de netwerktopologie in en implementeer het Service Fabric-cluster. De Resource Manager-sjabloon [AzureDeploy.json][template] maakt een virtueel netwerk (VNET) en een subnet voor Service Fabric. De sjabloon implementeert ook een cluster met certificaatbeveiliging ingeschakeld.  Gebruik voor productieclusters een certificaat van een certificeringsinstantie (CA) als clustercertificaat. Een zelfondertekend certificaat kan worden gebruikt om testclusters te beveiligen.

De sjabloon in dit artikel implementeert een cluster dat de duimafdruk van het certificaat gebruikt om het clustercertificaat te identificeren.  Geen twee certificaten kunnen dezelfde vingerafdruk hebben, waardoor certificaatbeheer moeilijker wordt. Schakelen tussen een geïmplementeerd cluster vanuit vingerafdrukken voor certificaten naar het gebruik van gewone namen voor certificaten maakt het beheer van certificaten veel eenvoudiger.  Lees [Cluster wijzigen naar certificaatbeheer met gewone namen](service-fabric-cluster-change-cert-thumbprint-to-cn.md) voor informatie over het bijwerken van het cluster voor het gebruik van gewone namen voor certificaten voor het beheren van certificaten.

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

Als u niet onmiddellijk naar het volgende artikel gaat, u [het cluster verwijderen](service-fabric-cluster-delete.md) om te voorkomen dat er kosten in rekening worden gebracht.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [schalen van een cluster](service-fabric-tutorial-scale-cluster.md).

De sjabloon in dit artikel implementeert een cluster dat de duimafdruk van het certificaat gebruikt om het clustercertificaat te identificeren.  Geen twee certificaten kunnen dezelfde vingerafdruk hebben, waardoor certificaatbeheer moeilijker wordt. Schakelen tussen een geïmplementeerd cluster vanuit vingerafdrukken voor certificaten naar het gebruik van gewone namen voor certificaten maakt het beheer van certificaten veel eenvoudiger.  Lees [Cluster wijzigen naar certificaatbeheer met gewone namen](service-fabric-cluster-change-cert-thumbprint-to-cn.md) voor informatie over het bijwerken van het cluster voor het gebruik van gewone namen voor certificaten voor het beheren van certificaten.

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
