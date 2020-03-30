---
title: Entrypoint in containerinstantie overschrijven
description: Een opdrachtregel instellen om het invoerpunt in een containerafbeelding te overschrijven wanneer u een Azure-containerinstantie implementeert
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247122"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>De opdrachtregel in een containerinstantie instellen om de standaardopdrachtregelbewerking te overschrijven

Wanneer u een containerinstantie maakt, geeft u optioneel een opdracht op om de standaardopdrachtregelinstructie die in de containerafbeelding is gebakken, te overschrijven. Dit gedrag is `--entrypoint` vergelijkbaar met het `docker run`argument opdrachtregel met .

Net als het instellen van [omgevingsvariabelen](container-instances-environment-variables.md) voor containerinstanties is het opgeven van een opdrachtregel voor batchtaken handig voor batchtaken waarbij u elke container dynamisch moet voorbereiden met taakspecifieke configuratie.

## <a name="command-line-guidelines"></a>Richtlijnen voor opdrachtregel

* Standaard geeft de opdrachtregel één proces op dat zonder shell in de container *begint.* De opdrachtregel kan bijvoorbeeld een Python-script of uitvoerbaar bestand uitvoeren. Het proces kan aanvullende parameters of argumenten opgeven.

* Als u meerdere opdrachten wilt uitvoeren, begint u de opdrachtregel door een shell-omgeving in te stellen die wordt ondersteund in het besturingssysteem voor containers. Voorbeelden:

  |Besturingssysteem  |Standaardshell  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Volg de conventies van de shell om meerdere opdrachten te combineren om achter elkaar uit te voeren.

* Afhankelijk van de containerconfiguratie moet u mogelijk een volledig pad instellen op de uitvoerbare of argumenten van de opdrachtregel.

* Stel een geschikt [herstartbeleid](container-instances-restart-policy.md) in voor de containerinstantie, afhankelijk van of de opdrachtregel een langlopende taak of een run-once-taak opgeeft. Bijvoorbeeld een herstartbeleid `Never` van `OnFailure` of wordt aanbevolen voor een run-once-taak. 

* Als u informatie nodig hebt over de standaardinvoerset in een containerafbeelding, gebruikt u de opdracht [Docker Image Inspect.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Syntaxis van de opdrachtregel

De syntaxis van de opdrachtregel is afhankelijk van de Azure API of het hulpprogramma dat wordt gebruikt om de instanties te maken. Als u een shell-omgeving opgeeft, houdt u ook de opdrachtsyntaxisconventies van de shell in acht.

* [az container maken][az-container-create] opdracht: Geef `--command-line` een tekenreeks met de parameter. Voorbeeld: `--command-line "python myscript.py arg1 arg2"`).

* [Nieuw-AzurermContainerGroup][new-azurermcontainergroup] Azure PowerShell-cmdlet: geef `-Command` een tekenreeks door aan de parameter. Bijvoorbeeld: `-Command "echo hello"`.

* Azure-portal: geef in de eigenschap **Opdracht overschrijven** van de containerconfiguratie een door komma's gescheiden lijst met tekenreeksen op, zonder aanhalingstekens. Voorbeeld: `python, myscript.py, arg1, arg2`). 

* Resource manager-sjabloon of YAML-bestand of een van de Azure SDK's: geef de eigenschap opdrachtregel op als een array met tekenreeksen. Voorbeeld: de JSON-array `["python", "myscript.py", "arg1", "arg2"]` in een resourcemanagersjabloon. 

  Als u bekend bent met de syntaxis [van Dockerfile,](https://docs.docker.com/engine/reference/builder/) is deze indeling vergelijkbaar met de *exec-vorm* van de CMD-instructie.

### <a name="examples"></a>Voorbeelden

|    |  Azure-CLI   | Portal | Template | 
| ---- | ---- | --- | --- |
| Eén opdracht | `--command-line "python myscript.py arg1 arg2"` | **Opdracht overschrijven:**`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Meerdere opdrachten | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Opdracht overschrijven:**`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI-voorbeeld

Wijzig bijvoorbeeld het gedrag van de containerafbeelding [microsoft/aci-wordcount,][aci-wordcount] die tekst analyseert in Shakespeare's *Hamlet* om de meest voorkomende woorden te vinden. In plaats van *Hamlet*te analyseren, u een opdrachtregel instellen die naar een andere tekstbron verwijst.

Voer de uitvoer uit met de volgende opdracht [az-container maken][az-container-create] om de uitvoer van de [microsoft/aci-wordcount-container][aci-wordcount] te bekijken wanneer deze de standaardtekst analyseert. Er is geen opdrachtregel voor starten opgegeven, dus de standaardcontaineropdracht wordt uitgevoerd. Ter illustratie stelt dit voorbeeld [omgevingsvariabelen](container-instances-environment-variables.md) in om de top 3-woorden te vinden die ten minste vijf letters lang zijn:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Zodra de status van de container wordt weergegeven als *Beëindigd* (gebruik [de AZ-containerweergave][az-container-show] om de status te controleren), geeft u het logboek met [az-containerlogboeken][az-container-logs] weer om de uitvoer te zien.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Uitvoer:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Stel nu een tweede voorbeeldcontainer in om verschillende tekst te analyseren door een andere opdrachtregel op te geven. Het Python-script dat door de container wordt uitgevoerd, *wordcount.py,* accepteert een URL als argument en verwerkt de inhoud van die pagina in plaats van de standaardinhoud.

Bijvoorbeeld, om de top 3 woorden die ten minste vijf letters lang in *Romeo en Julia*te bepalen:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Nogmaals, zodra de container is *beëindigd,* bekijk de uitvoer door de logboeken van de container weer te geven:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Uitvoer:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Volgende stappen

Op taken gebaseerde scenario's, zoals batchverwerking van een grote gegevensset met meerdere containers, kunnen profiteren van aangepaste opdrachtregels tijdens runtime. Zie [Containertaken uitvoeren met beleid voor opnieuw opstarten](container-instances-restart-policy.md)voor meer informatie over het uitvoeren van op taken gebaseerde containers.

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
