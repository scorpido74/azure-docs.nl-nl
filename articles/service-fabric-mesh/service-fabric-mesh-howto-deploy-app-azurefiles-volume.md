---
title: Een op Azure Files gebaseerd volume gebruiken in een Service Fabric mesh-app
description: Meer informatie over het opslaan van de status in een Azure Service Fabric mesh-toepassing door een Azure Files op basis van een volume in een service te koppelen met behulp van de Azure CLI.
author: dkkapur
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 5bb7ab6c861d958f6811ca852363c59cfced3940
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718817"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Een op Azure Files gebaseerd volume koppelen in een Service Fabric mesh-toepassing 

In dit artikel wordt beschreven hoe u een op Azure Files gebaseerd volume koppelt in een service van een Service Fabric mesh-toepassing.  Het Azure Files-volume stuur programma is een docker-volume stuur programma dat wordt gebruikt om een Azure Files share te koppelen aan een container, waarmee u de service status persistent kunt maken. Volumes bieden u bestands opslag voor algemeen gebruik en bieden de mogelijkheid om bestanden te lezen/schrijven met behulp van normale I/O-bestand-Api's van de schijf.  Lees de [opslag status](service-fabric-mesh-storing-state.md)voor meer informatie over de volumes en opties voor het opslaan van toepassings gegevens.

Als u een volume in een service wilt koppelen, maakt u een volume bron in uw Service Fabric mesh-toepassing en verwijst u naar dat volume in uw service.  Het declareren van de volume resource en het verwijzen naar deze in de service resource kan worden uitgevoerd in de [op YAML gebaseerde resource bestanden](#declare-a-volume-resource-and-update-the-service-resource-yaml) of in het [JSON-gebaseerde implementatie sjabloon](#declare-a-volume-resource-and-update-the-service-resource-json). Voordat u het volume koppelt, moet u eerst een Azure-opslag account en een [Bestands share in azure files](/azure/storage/files/storage-how-to-create-file-share)maken.

## <a name="prerequisites"></a>Vereisten
> [!NOTE]
> **Bekend probleem met de implementatie op Windows RS5 Development machine:** Er is een open bug met Power shell-cmdlet New-SmbGlobalMapping op RS5 Windows-machines die het koppelen van Azurefile-volumes voor komen. Hieronder vindt u een voorbeeld fout die wordt aangetroffen wanneer op AzureFile gebaseerd volume wordt gekoppeld op de lokale ontwikkel machine.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
De tijdelijke oplossing voor het probleem is 1) de onderstaande opdracht uitvoeren als Power shell-beheerder en 2) start de computer opnieuw op.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om dit artikel te volt ooien. 

Als u de Azure CLI lokaal wilt gebruiken met dit artikel, moet u ervoor zorgen dat `az --version` ten minste `azure-cli (2.0.43)`retourneert.  Installeer de module Azure Service Fabric mesh CLI (of werk deze bij) door deze [instructies](service-fabric-mesh-howto-setup-cli.md)te volgen.

Aanmelden bij Azure en uw abonnement instellen:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Een opslag account en bestands share maken (optioneel)
Voor het koppelen van een Azure Files-volume is een opslag account en een bestands share vereist.  U kunt een bestaand Azure Storage-account en een bestands share gebruiken of resources maken:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>De naam en sleutel van het opslag account en de naam van de bestands share ophalen
In de volgende secties wordt verwezen naar de naam van het opslag account, de sleutel van het opslag account en de naam van de bestands share als `<storageAccountName>`, `<storageAccountKey>`en `<fileShareName>`. 

Vermeld uw opslag accounts en haal de naam van het opslag account op met de bestands share die u wilt gebruiken:
```azurecli-interactive
az storage account list
```

De naam van de bestands share ophalen:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

De sleutel van het opslag account (' Key1 ') ophalen:
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

U kunt deze waarden ook vinden in de [Azure Portal](https://portal.azure.com):
* `<storageAccountName>`- **opslag accounts**, de naam van het opslag account dat wordt gebruikt voor het maken van de bestands share.
* `<storageAccountKey>`: Selecteer uw opslag account onder **opslag accounts** en selecteer vervolgens **toegangs sleutels** en gebruik de waarde onder **key1**.
* `<fileShareName>`: Selecteer uw opslag account onder **opslag accounts** en selecteer vervolgens **bestanden**. De naam die u moet gebruiken, is de naam van de bestands share die u hebt gemaakt.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Een volume resource declareren en de service resource (JSON) bijwerken

Voeg para meters toe voor de `<fileShareName>`, `<storageAccountName>`en `<storageAccountKey>` waarden die u hebt gevonden in een vorige stap. 

Maak een volume resource als een peer van de toepassings bron. Geef een naam en provider ("SFAzureFile") op om het op Azure Files gebaseerd volume te gebruiken). In `azureFileParameters`geeft u de para meters voor de waarden `<fileShareName>`, `<storageAccountName>`en `<storageAccountKey>` op die u in een vorige stap hebt gevonden.

Als u het volume in uw service wilt koppelen, voegt u een `volumeRefs` toe aan het `codePackages`-element van de service.  `name` is de resource-ID voor het volume (of een implementatie sjabloon parameter voor de volume bron) en de naam van het volume dat is gedeclareerd in het bron bestand van het volume. yaml.  `destinationPath` is de lokale map waaraan het volume wordt gekoppeld.

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

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Een volume resource declareren en de service resource bijwerken (YAML)

Voeg een nieuw *volume. yaml* -bestand toe aan de map *app-resources* voor uw toepassing.  Geef een naam en provider ("SFAzureFile") op om het op Azure Files gebaseerd volume te gebruiken). `<fileShareName>`, `<storageAccountName>`en `<storageAccountKey>` zijn de waarden die u in een vorige stap hebt gevonden.

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

Werk het bestand *service. yaml* in de map *service resources* bij om het volume te koppelen aan uw service.  Voeg het `volumeRefs`-element toe aan het element `codePackages`.  `name` is de resource-ID voor het volume (of een implementatie sjabloon parameter voor de volume bron) en de naam van het volume dat is gedeclareerd in het bron bestand van het volume. yaml.  `destinationPath` is de lokale map waaraan het volume wordt gekoppeld.

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

- Bekijk de voorbeeld toepassing Azure Files volume op [github](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Zie [Inleiding tot Service Fabric Resource-model](service-fabric-mesh-service-fabric-resources.md) voor meer informatie over het Service Fabric Resource-model.
- Lees [Wat is Service Fabric Mesh?](service-fabric-mesh-overview.md) voor meer informatie over Service Fabric Mesh.
