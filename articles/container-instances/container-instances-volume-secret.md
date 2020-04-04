---
title: Geheime volume monteren aan containergroep
description: Meer informatie over het monteren van een geheim volume om gevoelige informatie op te slaan voor toegang door uw containerexemplaren
ms.topic: article
ms.date: 04/03/2020
ms.openlocfilehash: 756828e71174246450245938595c8872afc62961
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657155"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Een geheim volume in Azure Container Instances monteren

Gebruik een *geheim* volume om gevoelige informatie te verstrekken aan de containers in een containergroep. Het *geheime* volume slaat uw geheimen op in bestanden binnen het volume, toegankelijk voor de containers in de containergroep. Door geheimen op te slaan in een *geheim* volume, u voorkomen dat gevoelige gegevens zoals SSH-sleutels of databasereferenties aan uw toepassingscode worden toegevoegd.

* Eenmaal geïmplementeerd met geheimen in een containergroep, is een geheim volume *alleen-lezen*.
* Alle geheime volumes worden ondersteund door [tmpfs][tmpfs], een RAM-backed filesystem; de inhoud ervan wordt nooit naar niet-vluchtige opslag geschreven.

> [!NOTE]
> *Geheime* volumes zijn momenteel beperkt tot Linux containers. Meer informatie over het doorgeven van veilige omgevingsvariabelen voor zowel Windows- als Linux-containers in [Omgevingsvariabelen instellen.](container-instances-environment-variables.md) Terwijl we werken aan het brengen van alle functies naar Windows-containers, u actuele platformverschillen vinden in het [overzicht.](container-instances-overview.md#linux-and-windows-containers)

## <a name="mount-secret-volume---azure-cli"></a>Geheime volumes monteren - Azure CLI

Als u een container met een of meer geheimen `--secrets` `--secrets-mount-path` wilt implementeren met de Azure CLI, neemt u de parameters en parameters op in de opdracht [az-containermaken.][az-container-create] In dit voorbeeld wordt een *geheim* volume ingevoegd dat bestaat uit twee bestanden met `/mnt/secrets`geheimen, 'mysecret1' en 'mysecret2', op :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

De volgende [az container exec][az-container-exec] output toont het openen van een shell in de lopende container, een lijst van de bestanden binnen het geheime volume, dan weergeven van de inhoud ervan:

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Zet geheim volume - YAML

U ook containergroepen implementeren met de Azure CLI- en een [YAML-sjabloon.](container-instances-multi-container-yaml.md) Implementeren op YAML-sjabloon is de voorkeursmethode bij het implementeren van containergroepen bestaande uit meerdere containers.

Wanneer u een YAML-sjabloon implementeert, moeten de geheime waarden **Base64-gecodeerd** in de sjabloon zijn. De geheime waarden worden echter in plaintext weergegeven in de bestanden in de container.

Met de volgende YAML-sjabloon definieert u een containergroep met één container die een *geheim* volume monteert op `/mnt/secrets`. Het geheime boek bevat twee bestanden met geheimen, 'mysecret1' en 'mysecret2'.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Als u wilt implementeren met de YAML-sjabloon, slaat `deploy-aci.yaml`u de voorgaande YAML op in een bestand met de naam , en voert u de opdracht [az-containermaken][az-container-create] uit met de `--file` parameter:

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Geheime volume monteren - Resource Manager

Naast de CLI- en YAML-implementatie u een containergroep implementeren met behulp van een [Azure Resource Manager-sjabloon.](/azure/templates/microsoft.containerinstance/containergroups)

Vul eerst `volumes` de array in `properties` het gedeelte containergroep van de sjabloon in. Wanneer u implementeert met een resourcemanagersjabloon, moeten de geheime waarden **Base64-gecodeerd** zijn in de sjabloon. De geheime waarden worden echter in plaintext weergegeven in de bestanden in de container.

Vul vervolgens voor elke container in de containergroep waarin *secret* u het `volumeMounts` geheime volume `properties` wilt monteren, de array in het gedeelte van de containerdefinitie.

Met de volgende sjabloon Resourcebeheer definieert u een containergroep met één container die een *geheim* volume monteert op `/mnt/secrets`. Het geheime boek heeft twee geheimen, "mysecret1" en "mysecret2."

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Als u wilt implementeren met de sjabloon Resourcemanager, `deploy-aci.json`slaat u de voorgaande JSON `--template-file` op in een bestand met de naam , en voert u de opdracht voor het maken van [az-implementatiegroepen][az-deployment-group-create] uit met de parameter:

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Volgende stappen

### <a name="volumes"></a>Volumes

Meer informatie over het monteren van andere volumetypen in Azure Container Instances:

* [Een Azure-bestandsshare koppelen in Azure Container Instances](container-instances-volume-azure-files.md)
* [Een leegDir-volume monteren in Azure Container Instances](container-instances-volume-emptydir.md)
* [Een gitRepo-volume monteren in Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Veilige omgevingsvariabelen

Een andere methode voor het verstrekken van gevoelige informatie aan containers (inclusief Windows-containers) is het gebruik van [veilige omgevingsvariabelen.](container-instances-environment-variables.md#secure-values)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
