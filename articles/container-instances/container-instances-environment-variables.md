---
title: Omgevingsvariabelen instellen in containerinstantie
description: Meer informatie over het instellen van omgevingsvariabelen in de containers die u uitvoert in Azure Container Instances
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: c3c76ba0c6131a8ab3de68c13c9dfddaf7e8749a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247226"
---
# <a name="set-environment-variables-in-container-instances"></a>Omgevingsvariabelen instellen in containerinstanties

Door omgevingsvariabelen in uw containerinstanties in te stellen, kunt u dynamische configuratie mogelijk maken van de toepassing die of het script dat door de container wordt uitgevoerd. Dit is vergelijkbaar `--env` met het `docker run`argument van de opdrachtregel met . 

Als u omgevingsvariabelen in een container wilt instellen, geeft u deze op wanneer u een containerinstantie maakt. In dit artikel worden voorbeelden weergegeven van het instellen van omgevingsvariabelen wanneer u een container start met de [Azure CLI,](#azure-cli-example) [Azure PowerShell](#azure-powershell-example)en de [Azure-portal](#azure-portal-example). 

Als u bijvoorbeeld de containerafbeelding van Microsoft [aci-wordcount][aci-wordcount] uitvoert, u het gedrag ervan wijzigen door de volgende omgevingsvariabelen op te geven:

*NumWords:* het aantal woorden dat naar STDOUT wordt verzonden.

*MinLength*: Het minimum aantal tekens in een woord om het te tellen. Een hoger getal negeert veelvoorkomende woorden als 'van' en 'de'.

Als u geheimen als omgevingsvariabelen moet doorgeven, ondersteunt Azure Container Instances [veilige waarden](#secure-values) voor zowel Windows- als Linux-containers.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI-voorbeeld

Als u de standaarduitvoer van de [aci-wordcount-container][aci-wordcount] wilt weergeven, voert u deze eerst uit met deze opdracht [az-container maken][az-container-create] (geen opgegeven omgevingsvariabelen):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Als u de uitvoer wilt wijzigen, start u een tweede container met het `--environment-variables` argument toegevoegd, waarbij waarden voor de variabelen *NumWords* en *MinLength* worden opgegeven. (In dit voorbeeld wordt ervan uitgegaan dat u de CLI uitvoert in een Bash-shell of Azure Cloud Shell. Als u de Opdrachtprompt van Windows gebruikt, geeft u `--environment-variables "NumWords"="5" "MinLength"="8"`de variabelen op met dubbele aanhalingstekens, zoals .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Zodra de status van beide containers wordt weergegeven als *Beëindigd* (gebruik [de AZ-containershow][az-container-show] om de status te controleren), geeft u hun logboeken weer met [az-containerlogboeken][az-container-logs] om de uitvoer te zien.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

De uitvoer van de containers laat zien hoe u het scriptgedrag van de tweede container hebt gewijzigd door omgevingsvariabelen in te stellen.

**mycontainer1**
```output
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

**mycontainer2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell-voorbeeld

Het instellen van omgevingsvariabelen in PowerShell is `-EnvironmentVariable` vergelijkbaar met de CLI, maar gebruikt het argument opdrachtregel.

Start eerst de [aci-wordcount-container][aci-wordcount] in de standaardconfiguratie met deze opdracht [Nieuw-AzContainerGroup:][new-Azcontainergroup]

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Voer nu de volgende opdracht [Nieuw-AzContainerGroep][new-Azcontainergroup] uit. Deze geeft de *omgevingvariabelen NumWords* en *MinLength* op `envVars`nadat een matrixvariabele is opgetuerd:

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Zodra de status van beide containers is *beëindigd* (gebruik [Get-AzContainerInstanceLog][azure-instance-log] om de status te controleren), trekt u hun logboeken op met de opdracht [Get-AzContainerInstanceLog.][azure-instance-log]

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

De uitvoer voor elke container laat zien hoe u het script hebt gewijzigd dat door de container wordt uitgevoerd door omgevingsvariabelen in te stellen.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Voorbeeld van Azure-portal

Als u omgevingsvariabelen wilt instellen wanneer u een container start in de Azure-portal, geeft u deze op de **pagina Geavanceerd** op wanneer u de container maakt.

1. Stel op de pagina **Geavanceerd** het **beleid opnieuw opstarten** in op Op *fout*
2. Voer **onder Omgevingsvariabelen** `NumWords` in met `5` een waarde van `MinLength` voor de `8` eerste variabele en voer in met een waarde van voor de tweede variabele. 
1. Selecteer **Controleren + maken** om de container te verifiëren en vervolgens te implementeren.

![Portalpagina met omgevingsvariabele Knop inschakelen en tekstvakken][portal-env-vars-01]

Als u de logboeken van de container wilt weergeven, selecteert u onder **Instellingen** **Containers**, vervolgens **Logboeken**. Net als bij de uitvoer in de vorige CLI- en PowerShell-secties u zien hoe het gedrag van het script is gewijzigd door de omgevingsvariabelen. Er worden slechts vijf woorden weergegeven, elk met een minimale lengte van acht tekens.

![Portal met containerlogboekuitvoer][portal-env-vars-02]

## <a name="secure-values"></a>Waarden beveiligen

Objecten met veilige waarden zijn bedoeld om gevoelige informatie zoals wachtwoorden of sleutels voor uw toepassing te bevatten. Het gebruik van veilige waarden voor omgevingsvariabelen is zowel veiliger als flexibeler dan het opnemen in de afbeelding van uw container. Een andere optie is het gebruik van geheime volumes, beschreven in [Een geheim volume monteren in Azure Container Instances.](container-instances-volume-secret.md)

Omgevingsvariabelen met veilige waarden zijn niet zichtbaar in de eigenschappen van uw container- hun waarden zijn alleen toegankelijk vanuit de container. Containereigenschappen die bijvoorbeeld worden weergegeven in de Azure-portal of Azure CLI, geven alleen de naam van een beveiligde variabele weer, niet de waarde ervan.

Stel een variabele voor een `secureValue` veilige omgeving `value` in door de eigenschap op te geven in plaats van de normale voor het type van de variabele. De twee variabelen die in de volgende YAML zijn gedefinieerd, tonen de twee variabele typen aan.

### <a name="yaml-deployment"></a>YAML-implementatie

Maak `secure-env.yaml` een bestand met het volgende fragment.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Voer de volgende opdracht uit om de containergroep met YAML te implementeren (pas indien nodig de naam van de resourcegroep aan):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Omgevingsvariabelen verifiëren

Voer de opdracht [az-containershow uit][az-container-show] om de omgevingsvariabelen van uw container op te vragen:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

De JSON-respons toont zowel de sleutel en waarde van de onveilige omgevingsvariabele, maar alleen de naam van de variabele voor de beveiligde omgeving:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Met de [opdracht az-container exec,][az-container-exec] waarmee een opdracht in een lopende container kan worden uitgevoerd, u controleren of de variabele voor de beveiligde omgeving is ingesteld. Voer de volgende opdracht uit om een interactieve bashsessie in de container te starten:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Zodra u een interactieve shell in de container `SECRET` hebt geopend, hebt u toegang tot de waarde van de variabele:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Volgende stappen

Op taken gebaseerde scenario's, zoals batchverwerking van een grote gegevensset met meerdere containers, kunnen profiteren van aangepaste omgevingsvariabelen tijdens runtime. Zie [Containertaken uitvoeren met beleid voor opnieuw opstarten](container-instances-restart-policy.md)voor meer informatie over het uitvoeren van op taken gebaseerde containers.

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
