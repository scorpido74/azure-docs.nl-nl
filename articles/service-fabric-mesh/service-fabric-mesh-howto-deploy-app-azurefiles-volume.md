---
title: Een op Azure Files gebaseerd volume gebruiken in een Mesh-app voor ServiceFabric
description: Meer informatie over het opslaan van status in een Azure Service Fabric-mesh-toepassing door een op Azure Files gebaseerd volume in een service te monteren met behulp van de Azure CLI.
author: dkkapur
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 5bb7ab6c861d958f6811ca852363c59cfced3940
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718817"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Een op Azure Files gebaseerd volume monteren in een Service Fabric Mesh-toepassing 

In dit artikel wordt beschreven hoe u een op Azure Files gebaseerd volume monteert in een service van een Service Fabric Mesh-toepassing.  Het volumestuurprogramma voor Azure Files is een Docker-volumestuurprogramma dat wordt gebruikt om een Azure-bestandenaandeel te koppelen aan een container, waarmee u de servicestatus blijft volhouden. Volumes geven u bestandsopslag voor algemene doeleinden en stellen u in staat om bestanden te lezen/schrijven met behulp van normale I/O-bestands-API's op schijf.  Lees de status opslaan voor meer informatie over volumes en opties voor het opslaan van [toepassingsgegevens.](service-fabric-mesh-storing-state.md)

Als u een volume in een service wilt monteren, maakt u een volumebron in uw Service Fabric Mesh-toepassing en verwijst u naar dat volume in uw service.  Het declareren van de volumebron en het verwijzen naar de resource in de servicebron kan worden gedaan in de [op YAML gebaseerde bronbestanden](#declare-a-volume-resource-and-update-the-service-resource-yaml) of in de [op JSON gebaseerde implementatiesjabloon](#declare-a-volume-resource-and-update-the-service-resource-json). Maak voordat u het volume monteert eerst een Azure-opslagaccount en een [bestandsshare in Azure-bestanden.](/azure/storage/files/storage-how-to-create-file-share)

## <a name="prerequisites"></a>Vereisten
> [!NOTE]
> **Bekend probleem met implementatie op Windows RS5-ontwikkelingsmachine:** Er is open bug met Powershell cmdlet New-SmbGlobalMapping op RS5 Windows-machines die de montage van Azurefile Volumes voorkomt. Hieronder vindt u de voorbeeldfout die wordt aangetroffen wanneer op AzureFile gebaseerd volume wordt gemonteerd op de lokale ontwikkelingsmachine.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
De tijdelijke oplossing voor het probleem is om 1)Voer onder opdracht uit als Powershell-beheerder en 2) Start de machine opnieuw op.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

U de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om dit artikel te voltooien. 

Als u de Azure CLI lokaal wilt `az --version` gebruiken `azure-cli (2.0.43)`in dit artikel, moet u ervoor zorgen dat het rendement ten minste .  Installeer (of update) de Azure Service Fabric Mesh CLI-extensiemodule door deze instructies te [volgen.](service-fabric-mesh-howto-setup-cli.md)

U zich aanmelden bij Azure en uw abonnement instellen:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Een opslagaccount en bestandsshare maken (optioneel)
Voor het monteren van een Azure Files-volume is een opslagaccount en bestandsshare vereist.  U een bestaand Azure-opslagaccount en bestandsshare gebruiken of resources maken:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>De naam en sleutel van het opslagaccount en de naam van het bestandsaandeel opvragen
De naam van het opslagaccount, de opslagaccountsleutel `<storageAccountName>`en `<storageAccountKey>`de `<fileShareName>` naam van het bestandsaandeel worden verwezen als , en in de volgende secties. 

Vermeld uw opslagaccounts en krijg de naam van het opslagaccount met het bestandsaandeel dat u wilt gebruiken:
```azurecli-interactive
az storage account list
```

De naam van het bestandsaandeel opvragen:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Download de opslagaccountsleutel ("key1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

U deze waarden ook vinden in de [Azure-portal:](https://portal.azure.com)
* `<storageAccountName>`- Onder **Opslagaccounts**wordt de naam van het opslagaccount gebruikt om het bestandsshare te maken.
* `<storageAccountKey>`- Selecteer uw opslagaccount onder **Opslagaccounts** en selecteer **vervolgens Toegangssleutels** en gebruik de waarde onder **toets1**.
* `<fileShareName>`- Selecteer uw opslagaccount onder **Opslagaccounts** en selecteer **Vervolgens Bestanden**. De te gebruiken naam is de naam van de bestandsshare die u hebt gemaakt.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Een volumebron declareren en de servicebron (JSON) bijwerken

Parameters toevoegen `<fileShareName>`voor `<storageAccountName>`de `<storageAccountKey>` , en waarden die u in een vorige stap hebt gevonden. 

Een bron Volume maken als peer van de toepassingsbron. Geef een naam op en de provider ('SFAzureFile' om het op Azure Files gebaseerde volume te gebruiken). Geef `azureFileParameters`in , de `<fileShareName>` `<storageAccountName>`parameters `<storageAccountKey>` voor de , en waarden op die u in een vorige stap hebt gevonden.

Als u het volume in `volumeRefs` uw `codePackages` service wilt monteren, voegt u een toe aan het element van de service.  `name`is de resource-id voor het volume (of een parameter voor de implementatiesjabloon voor de volumebron) en de naam van het volume dat is opgegeven in het resourcebestand volume.yaml.  `destinationPath`is de lokale directory waar het volume op wordt gemonteerd.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Een volumebron declareren en de servicebron (YAML) bijwerken

Voeg een nieuw *volume.yaml-bestand* toe in de *app-bronnenmap* voor uw toepassing.  Geef een naam op en de provider ('SFAzureFile' om het op Azure Files gebaseerde volume te gebruiken). `<fileShareName>`, `<storageAccountName>`en `<storageAccountKey>` zijn de waarden die u in een vorige stap hebt gevonden.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Werk het *bestand service.yaml* bij in de map *Serviceresources* om het volume in uw service te monteren.  Voeg `volumeRefs` het element `codePackages` toe aan het element.  `name`is de resource-id voor het volume (of een parameter voor de implementatiesjabloon voor de volumebron) en de naam van het volume dat is opgegeven in het resourcebestand volume.yaml.  `destinationPath`is de lokale directory waar het volume op wordt gemonteerd.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk de voorbeeldtoepassing azure-bestanden op [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Zie [Inleiding tot Service Fabric Resource-model](service-fabric-mesh-service-fabric-resources.md) voor meer informatie over het Service Fabric Resource-model.
- Lees [Wat is Service Fabric Mesh?](service-fabric-mesh-overview.md) voor meer informatie over Service Fabric Mesh.
