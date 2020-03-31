---
title: Azure Service Fabric-infrastructuur als aanbevolen procedures voor code
description: Aanbevolen procedures en ontwerpoverwegingen voor het beheren van Azure Service Fabric als infrastructuur als code.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 1c044d5fd973d3c577088a887f2fac413d2ab79d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551808"
---
# <a name="infrastructure-as-code"></a>Infrastructure als code

Maak in een productiescenario Azure Service Fabric-clusters met resourcebeheersjablonen. Resourcebeheersjablonen bieden meer controle over resourceeigenschappen en zorgen ervoor dat u een consistent resourcemodel hebt.

Voorbeeldvan Resource Manager-sjablonen zijn beschikbaar voor Windows en Linux in de [Azure-voorbeelden op GitHub.](https://github.com/Azure-Samples/service-fabric-cluster-templates) Deze sjablonen kunnen worden gebruikt als uitgangspunt voor uw clustersjabloon. Download `azuredeploy.json` `azuredeploy.parameters.json` en bewerk ze om aan uw aangepaste vereisten te voldoen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u `azuredeploy.json` `azuredeploy.parameters.json` de bovenstaande en sjablonen wilt implementeren, gebruikt u de volgende Azure CLI-opdrachten:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Een resource maken met Powershell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric-bronnen

U toepassingen en services implementeren in uw Service Fabric-cluster via Azure Resource Manager. Zie [Toepassingen en services beheren als Azure Resource Manager-bronnen](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) voor meer informatie. De volgende zijn de toepassing seinen van best practices Service Fabric die u wilt opnemen in de sjabloonbronnen van Resource Manager.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Als u uw toepassing wilt implementeren met Azure Resource Manager, moet u eerst [een sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Service Fabric-toepassingspakket maken. Het volgende python-script is een voorbeeld van het maken van een sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Automatische upgradeconfiguratie van het Besturingssysteem Azure Virtual Machine 
Upgraden van uw virtuele machines is een door de gebruiker geïnitieerde bewerking en het wordt aanbevolen dat u [de upgrade van het automatische besturingssysteem](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) voor Azure Service Fabric-clusters voor azure servicefabric-clusters gebruikt. Patch Orchestration Application is een alternatieve oplossing die is bedoeld voor wanneer gehost buiten Azure, hoewel POA kan worden gebruikt in Azure, met overhead van hosting POA in Azure is een veel voorkomende reden om Virtual Machine Operating System Automatic Upgrade verkiezen boven POA. Hieronder volgen de eigenschappen van de sjabloon Resourcebeheer van de virtuele machineset van de berekening om de upgrade van Auto OS in te schakelen:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Wanneer u automatische os-upgrades met servicestructuur gebruikt, wordt de nieuwe OS-afbeelding één updatedomein tegelijk uitgerold om de hoge beschikbaarheid van de services die in Service Fabric worden uitgevoerd, te behouden. Als u automatische os-upgrades in servicestructuur wilt gebruiken, moet uw cluster zijn geconfigureerd om de Silver Sustainability Tier of hoger te gebruiken.

Controleer of de volgende registersleutel is ingesteld op false om te voorkomen dat uw windows host-machines ongecoördineerde updates initieert: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

De volgende zijn de eigenschappen van de Sjabloon Resourcebeheer van de virtuele machine berekenen om de windowsUpdate-registersleutel op false in te stellen:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Configuratie van Azure Service Fabric-clusterupgrade
Het volgende is de eigenschap Resourcemanager van het cluster Resource beheer van servicefabric om automatische upgrade in te schakelen:
```json
"upgradeMode": "Automatic",
```
Als u uw cluster handmatig wilt upgraden, downloadt u de cabine/deb-distributie naar een virtuele clustermachine en roept u vervolgens de volgende PowerShell op:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Volgende stappen

* Een cluster maken op VM's of computers met Windows Server: [clustercreatie van servicefabric voor Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Een cluster maken op VM's of computers met Linux: [een Linux-cluster maken](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)
