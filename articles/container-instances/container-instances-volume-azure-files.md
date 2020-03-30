---
title: Azure Files-volume inzetten op containergroep
description: Meer informatie over het monteren van een Azure Files-volume om de status van Azure Container Te blijven bestaan met Azure Container Instances
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f66890c503de8de9160f11fb28795012ae57daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561334"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Een Azure-bestandsshare koppelen in Azure Container Instances

Azure-containerinstanties zijn standaard staatloos. Als de container vastloopt of stopt, gaat alle status verloren. Als u een status langer wilt behouden dan de levensduur van de container, moet u een volume vanuit een externe opslag koppelen. Zoals in dit artikel wordt weergegeven, kan Azure Container Instances een Azure-bestandsshare monteren dat is gemaakt met [Azure Files.](../storage/files/storage-files-introduction.md) Azure Files biedt volledig beheerde bestandsshares die worden gehost in Azure Storage en die toegankelijk zijn via het smb-protocol (Industriestandaard Server Message Block). Het gebruik van een Azure-bestandsshare met Azure Container Instances biedt functies voor het delen van bestanden die vergelijkbaar zijn met het gebruik van een Azure-bestandsshare met virtuele Azure-machines.

> [!NOTE]
> Het monteren van een Azure Files-share is momenteel beperkt tot Linux-containers. Zoek actuele platformverschillen in het [overzicht.](container-instances-overview.md#linux-and-windows-containers)
>
> Het monteren van een Azure Files-share op een containerinstantie is vergelijkbaar met een [dockerbindingshouder.](https://docs.docker.com/storage/bind-mounts/) Houd er rekening mee dat als u een aandeel in een containermap monteert waarin bestanden of mappen bestaan, deze bestanden of mappen worden verduisterd door de houder en niet toegankelijk zijn terwijl de container wordt uitgevoerd.
>

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken

Voordat u een Azure-bestandsshare met Azure Container Instances kunt gebruiken, moet u deze maken. Voer het volgende script uit om een opslagaccount te maken om de bestandsshare en het aandeel zelf te hosten. De naam van het opslagaccount moet globaal uniek zijn, dus voegt het script een willekeurige waarde toe aan de basistekenreeks.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Opslagreferenties ophalen

Als u een Azure-bestandsshare wilt koppelen als een volume in Azure Container Instances, hebt u drie waarden nodig: de naam van het opslagaccount, de sharenaam en de toegangssleutel voor opslag.

* **Naam van het opslagaccount** - Als u het vorige script `$ACI_PERS_STORAGE_ACCOUNT_NAME` hebt gebruikt, is de naam van het opslagaccount in de variabele opgeslagen. Als u de accountnaam wilt zien, typt u het:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Naam delen** - Deze waarde is `acishare` al bekend (gedefinieerd als in het vorige script)

* **Opslagaccountsleutel** - Deze waarde kan worden gevonden met de volgende opdracht:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Container- en montagevolume implementeren - CLI

Als u een Azure-bestandsshare als een volume in een container wilt monteren met behulp van het Azure CLI, geeft u het deel- en volumebevestigingspunt op wanneer u de container met [az-container maakt.][az-container-create] Als u de vorige stappen hebt gevolgd, u het aandeel dat u eerder hebt gemaakt, monteren met de volgende opdracht om een container te maken:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

De `--dns-name-label` waarde moet uniek zijn binnen het Azure-gebied waar u de containerinstantie maakt. Werk de waarde in de vorige opdracht bij als u een **foutbericht van het DNS-naamlabel** ontvangt wanneer u de opdracht uitvoert.

## <a name="manage-files-in-mounted-volume"></a>Bestanden beheren in het gemonteerde volume

Zodra de container is opgestart, u de eenvoudige web-app gebruiken die is geïmplementeerd via de Microsoft [aci-hellofiles-afbeelding][aci-hellofiles] om kleine tekstbestanden te maken in de Azure-bestandsshare op het door u opgegeven mountpad. Verkrijg de volledig gekwalificeerde domeinnaam (FQDN) van de web-app met de opdracht [AZ Container Show:][az-container-show]

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Nadat u tekst hebt opgeslagen met de app, u de [Azure-portal][portal] of een hulpprogramma zoals de [Microsoft Azure Storage Explorer][storage-explorer] gebruiken om het bestand of de bestanden die naar de bestandsshare zijn geschreven, op te halen en te inspecteren.

## <a name="deploy-container-and-mount-volume---yaml"></a>Container- en montagevolume implementeren - YAML

U ook een containergroep implementeren en een volume in een container monteren met de Azure CLI- en een [YAML-sjabloon.](container-instances-multi-container-yaml.md) Implementeren op YAML-sjabloon is een voorkeursmethode bij het implementeren van containergroepen bestaande uit meerdere containers.

Met de volgende YAML-sjabloon definieert u `aci-hellofiles` een containergroep met één container die met de afbeelding is gemaakt. De container monteert de *Azure-bestandsshare-acishare* die eerder als een volume is gemaakt. Voer, indien aangegeven, de naam en opslagsleutel in voor het opslagaccount dat het bestandsshare host. 

Net als in het `dnsNameLabel` voorbeeld CLI moet de waarde uniek zijn binnen het Azure-gebied waar u de containerinstantie maakt. Werk indien nodig de waarde in het YAML-bestand bij.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Als u wilt implementeren met de YAML-sjabloon, slaat `deploy-aci.yaml`u de voorgaande YAML op in een bestand met de naam , en voert u de opdracht [az-containermaken][az-container-create] uit met de `--file` parameter:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Container implementeren en volume monteren - Resourcemanager

Naast de IMPLEMENTATIE van CLI en YAML u een containergroep implementeren en een volume in een container monteren met behulp van een [Azure Resource Manager-sjabloon.](/azure/templates/microsoft.containerinstance/containergroups)

Vul eerst `volumes` de array in `properties` het gedeelte containergroep van de sjabloon in. 

Vul vervolgens voor elke container waarin u het volume `volumeMounts` wilt monteren, de array in het `properties` gedeelte van de containerdefinitie.

Met de volgende sjabloon Resourcebeheer definieert u `aci-hellofiles` een containergroep met één container die met de afbeelding is gemaakt. De container monteert de *Azure-bestandsshare-acishare* die eerder als een volume is gemaakt. Voer, indien aangegeven, de naam en opslagsleutel in voor het opslagaccount dat het bestandsshare host. 

Net als in de `dnsNameLabel` vorige voorbeelden moet de waarde uniek zijn binnen het Azure-gebied waar u de containerinstantie maakt. Werk indien nodig de waarde in de sjabloon bij.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Als u wilt implementeren met de sjabloon Resourcemanager, `deploy-aci.json`slaat u de voorgaande JSON op in een bestand met de naam , en voert u de opdracht [voor het maken van de az-groep uit][az-group-deployment-create] met de `--template-file` parameter:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Meerdere volumes monteren

Als u meerdere volumes in een containerinstantie wilt monteren, moet u implementeren met behulp van een [Azure Resource Manager-sjabloon,](/azure/templates/microsoft.containerinstance/containergroups)een YAML-bestand of een andere programmatische methode. Als u een sjabloon of YAML-bestand wilt gebruiken, geeft `volumes` u `properties` de share-details op en definieert u de volumes door de array in het gedeelte van het bestand te vullen. 

Als u bijvoorbeeld twee Azure Files-shares met de naam *share1* en `volumes` *share2* hebt gemaakt in het opslagaccount *myStorageAccount,* lijkt de array in een resourcemanagersjabloon op het volgende:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Vul vervolgens voor elke container in de containergroep waarin u de `volumeMounts` volumes `properties` wilt monteren, de array in het gedeelte van de containerdefinitie. Hiermee worden bijvoorbeeld de twee volumes, *myvolume1* en *myvolume2*, eerder gedefinieerd, gemonteerd:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het monteren van andere volumetypen in Azure Container Instances:

* [Een leegDir-volume monteren in Azure Container Instances](container-instances-volume-emptydir.md)
* [Een gitRepo-volume monteren in Azure Container Instances](container-instances-volume-gitrepo.md)
* [Een geheim volume in Azure Container Instances monteren](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create