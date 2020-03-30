---
title: Zelfstudie - Groep met meerdere containers implementeren - sjabloon
description: In deze zelfstudie leert u hoe u een containergroep met meerdere containers in Azure Container Instances implementeert met behulp van een Azure Resource Manager-sjabloon met de Azure CLI.
ms.topic: article
ms.date: 04/03/2019
ms.custom: mvc
ms.openlocfilehash: d2b4e20520cad28c5d62118f6c9d10fcc43ac89e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533626"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Zelfstudie: Een groep met meerdere containers implementeren met een sjabloon Resourcebeheer

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances ondersteunt de implementatie van meerdere containers op één host met behulp van een [containergroep.](container-instances-container-groups.md) Een containergroep is handig bij het bouwen van een toepassingszijspan voor logboekregistratie, bewaking of een andere configuratie waarbij een service een tweede gekoppeld proces nodig heeft.

In deze zelfstudie voert u stappen uit om een eenvoudige sidecar-configuratie met twee containers uit te voeren door een Azure Resource Manager-sjabloon te implementeren met behulp van de Azure CLI. Procedures voor:

> [!div class="checklist"]
> * Een sjabloon voor een groep met meerdere containers configureren
> * De containergroep implementeren
> * Bekijk de logboeken van de containers

Een resourcebeheersjabloon kan gemakkelijk worden aangepast voor scenario's wanneer u extra Azure-serviceresources (bijvoorbeeld een share Azure Files of een virtueel netwerk) moet implementeren met de containergroep. 

> [!NOTE]
> Multicontainergroepen zijn momenteel beperkt tot Linux-containers. 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Een sjabloon configureren

Begin met het kopiëren van de volgende `azuredeploy.json`JSON naar een nieuw bestand met de naam . In Azure Cloud Shell u Visual Studio Code gebruiken om het bestand in uw werkmap te maken:

```
code azuredeploy.json
```

Met deze sjabloon Resourcebeheer definieert u een containergroep met twee containers, een openbaar IP-adres en twee blootgestelde poorten. De eerste container in de groep draait een internet-facing webapplicatie. De tweede container, de zijspan, doet een HTTP-aanvraag voor de belangrijkste webapplicatie via het lokale netwerk van de groep.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
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
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
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
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Als u een register van privécontainerafbeeldingen wilt gebruiken, voegt u een object toe aan het JSON-document met de volgende indeling. Zie de referentiedocumentatie van de [ACI Resource Manager-sjabloon voor][template-reference] een voorbeeldimplementatie van deze configuratie.

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>De sjabloon implementeren

Een resourcegroep maken met de opdracht [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementeer de sjabloon met de [opdracht Implementatie van az-groep.][az-group-deployment-create]

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

U ontvangt binnen enkele seconden een eerste reactie van Azure.

## <a name="view-deployment-state"></a>Implementatiestatus weergeven

Als u de status van de implementatie wilt weergeven, gebruikt u de volgende opdracht [az-containershow:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Als u de lopende toepassing wilt bekijken, navigeert u naar het IP-adres in uw browser. Het IP-adres `52.168.26.124` is bijvoorbeeld in dit voorbeeld uitvoer:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Containerlogboeken ophalen

Bekijk de logoutput van een container met de opdracht [AZ-containerlogboeken.][az-container-logs] Het `--container-name` argument geeft aan van welke container logboeken moet worden opgehaald. In dit voorbeeld `aci-tutorial-app` wordt de container opgegeven.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Uitvoer:

```bash
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Als u de logboeken voor de zijspancontainer `aci-tutorial-sidecar` wilt bekijken, voert u een vergelijkbare opdracht uit waarin de container wordt opgegeven.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Uitvoer:

```bash
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

Zoals u zien, is de zijspan periodiek het maken van een HTTP-aanvraag om de belangrijkste webapplicatie via het lokale netwerk van de groep om ervoor te zorgen dat het wordt uitgevoerd. Dit zijspanvoorbeeld kan worden uitgebreid om een waarschuwing te activeren `200 OK`als deze een andere HTTP-reactiecode heeft ontvangen dan .

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure Resource Manager-sjabloon gebruikt om een groep met meerdere containers te implementeren in Azure Container Instances. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een sjabloon voor een groep met meerdere containers configureren
> * De containergroep implementeren
> * Bekijk de logboeken van de containers

Zie Azure Resource [Manager-sjablonen voor Azure Container Instances voor](container-instances-samples-rm.md)aanvullende sjabloonvoorbeelden .

U ook een groep met meerdere containers opgeven met behulp van een [YAML-bestand.](container-instances-multi-container-yaml.md) Vanwege de beknoptere aard van de YAML-indeling is implementatie met een YAML-bestand een goede keuze wanneer uw implementatie alleen containerexemplaren bevat.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
