---
title: Een Azure Service Fabric-cluster maken
description: Meer informatie over het instellen van een beveiligd cluster van ServiceFabric in Azure met Azure Resource Manager.  U een cluster maken met een standaardsjabloon of met uw eigen clustersjabloon.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: 24bc80a7ab3ce61b79466cdd943c60efd367ce01
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458297"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Een cluster van servicefabric maken met Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Een [Azure Service Fabric-cluster](service-fabric-deploy-anywhere.md) is een met een netwerk verbonden set virtuele machines waarin uw microservices worden geïmplementeerd en beheerd.  Een Service Fabric-cluster dat wordt uitgevoerd in Azure is een Azure-bron en wordt geïmplementeerd met Azure Resource Manager. In dit artikel wordt beschreven hoe u een beveiligd cluster van servicefabric in Azure implementeert met behulp van ResourceBeheer. U een standaardclustersjabloon of een aangepaste sjabloon gebruiken.  Als u nog geen aangepaste sjabloon hebt, u leren hoe u [er een maakt.](service-fabric-cluster-creation-create-template.md)

Het type beveiliging dat is gekozen om het cluster te beveiligen (d.w.z. Windows-identiteit, X509 enz.) moet worden opgegeven voor de eerste creatie van het cluster en kan daarna niet worden gewijzigd. Lees voor het instellen van een cluster [beveiligingsscenario's voor servicefabric.][service-fabric-cluster-security] In Azure gebruikt Service Fabric het X509-certificaat om uw cluster en de eindpunten te beveiligen, clients te verifiëren en gegevens te versleutelen. Azure Active Directory wordt ook aanbevolen om de toegang tot beheereindpunten te beveiligen. Lees [Azure AD instellen om clients te verifiëren voor](service-fabric-cluster-creation-setup-aad.md)meer informatie.

Als u een productiecluster maakt om productieworkloads uit te voeren, raden we u aan eerst de [checklist voor productiegereedheid](service-fabric-production-readiness-checklist.md)door te lezen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten 
Gebruik in dit artikel de PowerShell-powershell- of Azure CLI-modules servicestructuur om een cluster te implementeren:

* [Azure PowerShell 4.1 en hoger][azure-powershell]
* [Azure CLI-versie 2.0 en hoger][azure-CLI]

De referentiedocumentatie voor de Service Fabric-modules vindt u hier:
* [Az.ServiceFabric](https://docs.microsoft.com/powershell/module/az.servicefabric)
* [az SF CLI module](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Voordat u een van de opdrachten in dit artikel uitvoert, meldt u zich eerst aan bij Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Een nieuw cluster maken met behulp van een door het systeem gegenereerd zelfondertekend certificaat

Gebruik de volgende opdrachten om een cluster te maken dat is beveiligd met een door het systeem gegenereerd zelfondertekend certificaat. Met deze opdracht wordt een primair clustercertificaat ingesteld dat wordt gebruikt voor clusterbeveiliging en om beheerderstoegang in te stellen om beheerbewerkingen uit te voeren met dat certificaat.  Zelfondertekende certificaten zijn handig voor het beveiligen van testclusters.  Productieclusters moeten worden beveiligd met een certificaat van een certificaatautoriteit (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>De standaardclustersjabloon gebruiken die wordt verzonden in de module

Gebruik de volgende opdracht om snel een cluster te maken door minimale parameters op te geven met behulp van de standaardsjabloon.

De sjabloon die wordt gebruikt, is beschikbaar op de sjabloonvoorbeelden van [Azure Service Fabric: windows-sjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) en [Ubuntu-sjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Met de volgende opdracht u Windows- of Linux-clusters maken, u moet het besturingssysteem dienovereenkomstig opgeven. De powershell/CLI-opdrachten voeren het certificaat ook uit in de opgegeven *CertificateOutputFolder*; Zorg er echter voor dat de certificaatmap al is gemaakt. De opdracht neemt ook andere parameters in, zoals VM SKU.

> [!NOTE]
> De volgende PowerShell-opdracht werkt alleen `Az` met de Azure PowerShell-module. Als u de huidige versie van Azure Resource Manager PowerShell-versie wilt controleren, voert u de volgende PowerShell-opdracht 'Get-Module Az' uit. Volg [deze koppeling](/powershell/azure/install-Az-ps) om uw Azure Resource Manager PowerShell-versie te upgraden. 
>
>

Implementeer het cluster met PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Het cluster implementeren met Azure CLI:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Uw eigen aangepaste sjabloon gebruiken

Als u een aangepaste sjabloon moet maken die aan uw behoeften voldoet, wordt het ten zeerste aanbevolen om te beginnen met een van de sjablonen die beschikbaar zijn op de sjabloonvoorbeelden van [Azure Service Fabric.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Meer informatie over het [aanpassen van uw clustersjabloon][customize-your-cluster-template].

Als u al een aangepaste sjabloon hebt, controleert u of alle drie de certificaatgerelateerde parameters in de sjabloon en het parameterbestand als volgt worden genoemd en dat waarden als volgt niet worden vermeld:

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Implementeer het cluster met PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Het cluster implementeren met Azure CLI:

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Een nieuw cluster maken met uw eigen X.509-certificaat

Gebruik de volgende opdracht om een cluster te maken als u een certificaat hebt waarmee u uw cluster wilt beveiligen.

Als dit een CA ondertekend certificaat is dat u uiteindelijk ook voor andere doeleinden gebruikt, wordt aanbevolen dat u een afzonderlijke brongroep specifiek voor uw sleutelkluis opgeeft. We raden u aan de sleutelkluis in een eigen resourcegroep te plaatsen. Met deze actie u de compute- en opslagbrongroepen verwijderen, inclusief de brongroep die uw Cluster Servicefabric bevat, zonder uw sleutels en geheimen te verliezen. **De brongroep die uw sleutelkluis *bevat, moet zich in dezelfde regio bevinden* als het cluster dat het wordt gebruikt.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Gebruik de standaardsjabloon vijf knooppunt, één knooppunttype dat in de module wordt verzonden
De sjabloon die wordt gebruikt, is beschikbaar op de [Azure-voorbeelden: Windows-sjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) en [Ubuntu-sjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Implementeer het cluster met PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Het cluster implementeren met Azure CLI:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Uw eigen aangepaste clustersjabloon gebruiken
Als u een aangepaste sjabloon moet maken die aan uw behoeften voldoet, wordt het ten zeerste aanbevolen om te beginnen met een van de sjablonen die beschikbaar zijn op de sjabloonvoorbeelden van [Azure Service Fabric.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Meer informatie over het [aanpassen van uw clustersjabloon][customize-your-cluster-template].

Als u al een aangepaste sjabloon hebt, moet u controleren of alle drie de certificaatgerelateerde parameters in de sjabloon en het parameterbestand als volgt worden genoemd en dat waarden als volgt worden uitgevoerd.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Implementeer het cluster met PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Het cluster implementeren met Azure CLI:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Een aanwijzer gebruiken voor een geheim dat is geüpload naar een sleutelkluis

Als u een bestaande sleutelkluis wilt gebruiken, moet de sleutelkluis zijn [ingeschakeld voor implementatie,](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI) zodat de compute resource provider certificaten ervan kan ophalen en installeren op clusterknooppunten.

Implementeer het cluster met PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Het cluster implementeren met Azure CLI:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>Volgende stappen
Op dit moment wordt er een beveiligd cluster uitgevoerd in Azure. Maak vervolgens [verbinding met uw cluster](service-fabric-connect-to-secure-cluster.md) en leer hoe u [toepassingsgeheimen beheert.](service-fabric-application-secret-management.md)

Zie [Microsoft.ServiceFabric/clusters sjabloonverwijzing](/azure/templates/microsoft.servicefabric/clusters)voor de syntaxis en eigenschappen van JSON om een sjabloon te gebruiken.

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
