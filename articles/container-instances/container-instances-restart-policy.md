---
title: Beleid voor uitvoerings taken opnieuw starten
description: Meer informatie over het gebruik van Azure Container Instances voor het uitvoeren van taken die worden uitgevoerd voor voltooiing, zoals het bouwen, testen of het renderen van afbeeldingen.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: f814b1c99827c07f8dadfb0cfd80c87a93377cdc
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533461"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Taken met containers uitvoeren met beleids regels voor opnieuw opstarten

Het gemak en de snelheid van het implementeren van containers in Azure Container Instances biedt een fascinerend platform voor het uitvoeren van eenmalige taken, zoals het bouwen, testen en het weer geven van afbeeldingen in een container exemplaar.

Met een configureerbaar beleid voor opnieuw opstarten kunt u opgeven dat uw containers worden gestopt wanneer hun processen zijn voltooid. Omdat container instanties worden gefactureerd op basis van de tweede, worden er alleen kosten in rekening gebracht voor de reken resources die worden gebruikt tijdens het uitvoeren van de container waarop de taak wordt uitgevoerd.

In de voor beelden in dit artikel wordt gebruikgemaakt van de Azure CLI. U moet Azure CLI versie 2.0.21 of hoger hebben [geïnstalleerd][azure-cli-install], of de CLI gebruiken in de [Azure Cloud shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Beleid voor opnieuw opstarten van container

Wanneer u een [container groep](container-instances-container-groups.md) maakt in azure container instances, kunt u een van de drie beleids instellingen voor het opnieuw opstarten opgeven.

| Beleid opnieuw opstarten   | Beschrijving |
| ---------------- | :---------- |
| `Always` | Containers in de container groep worden altijd opnieuw opgestart. Dit is de **standaard** instelling die wordt toegepast wanneer er geen beleid voor opnieuw opstarten is opgegeven bij het maken van de container. |
| `Never` | Containers in de container groep worden nooit opnieuw opgestart. De containers worden Maxi maal één keer uitgevoerd. |
| `OnFailure` | Containers in de container groep worden alleen opnieuw opgestart wanneer het proces dat wordt uitgevoerd in de container mislukt (wanneer dit wordt beëindigd met een afsluit code die niet gelijk is aan nul). De containers worden ten minste één keer uitgevoerd. |

## <a name="specify-a-restart-policy"></a>Beleid voor opnieuw opstarten opgeven

Hoe u een beleid voor opnieuw opstarten opgeeft, is afhankelijk van hoe u de container instanties maakt, zoals met de Azure CLI, Azure PowerShell-cmdlets of in de Azure Portal. Geef in de Azure CLI de para meter `--restart-policy` op wanneer u [AZ container Create][az-container-create]aanroept.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Voor beeld van uitvoeren naar voltooid

Als u het beleid voor het opnieuw opstarten in actie wilt zien, maakt u een container exemplaar van de micro soft [ACI-WordCount-][aci-wordcount-image] installatie kopie en geeft u het beleid voor het opnieuw opstarten van `OnFailure` op. In deze voorbeeld container wordt een python-script uitgevoerd dat standaard de tekst van de [Hamlet](http://shakespeare.mit.edu/hamlet/full.html)van Shakespeare analyseert, de tien meest voorkomende woorden naar stdout schrijft en vervolgens afsluit.

Voer de voorbeeld container uit met de volgende opdracht [AZ container Create][az-container-create] :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances start de container en stopt deze wanneer de toepassing (of het script in dit geval) wordt afgesloten. Als Azure Container Instances een container stopt waarvan het beleid voor opnieuw opstarten is `Never` of `OnFailure`, wordt de status van de container ingesteld op **beëindigd**. U kunt de status van een container controleren met de opdracht [AZ container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Voorbeelduitvoer:

```bash
"Terminated"
```

Zodra de status van de voorbeeld container is *beëindigd*, kunt u de taak uitvoer zien door de container logboeken te bekijken. Voer de opdracht [AZ container logs][az-container-logs] uit om de uitvoer van het script weer te geven:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Uitvoer:

```bash
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

In dit voor beeld ziet u de uitvoer die het script naar STDOUT heeft verzonden. De taken in de container kunnen echter worden uitgevoerd in plaats daarvan naar permanente opslag om ze later op te halen. Bijvoorbeeld naar een Azure- [Bestands share](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Volgende stappen

Op taak gebaseerde scenario's, zoals batch verwerking van een grote gegevensset met verschillende containers, kunnen profiteren van aangepaste [omgevings variabelen](container-instances-environment-variables.md) of [opdracht regels](container-instances-start-command.md) tijdens runtime.

Zie [een Azure-bestands share koppelen met Azure container instances](container-instances-mounting-azure-files-volume.md)voor meer informatie over het persistent maken van de uitvoer van de containers die worden uitgevoerd om te worden voltooid.

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
