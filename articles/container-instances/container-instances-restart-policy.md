---
title: Beleid opnieuw starten voor run-once-taken
description: Meer informatie over het gebruik van Azure Container Instances om taken uit te voeren die worden uitgevoerd tot voltooiing, zoals in build-, test- of imagerenderingtaken.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 8ef4ef228038242f53abc8041470f7f596ab1157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131499"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Containertaken uitvoeren met beleid voor opnieuw opstarten

Het gemak en de snelheid waarmee containers in Azure Container Instances worden geïmplementeerd, zorgt voor een aantrekkelijk platform voor het uitvoeren van eenmalige taken zoals bouwen, testen en rendering van afbeeldingen in een containerinstantie.

Met een configureerbaar beleid voor opnieuw starten kunt u opgeven dat uw containers worden gestopt wanneer hun processen zijn voltooid. Omdat containerinstanties per seconde worden gefactureerd, betaalt u alleen voor de rekenresources die worden gebruikt terwijl de container waar uw taak wordt uitgevoerd, actief is.

De voorbeelden in dit artikel gebruiken de Azure CLI. U moet Azure CLI-versie 2.0.21 of hoger lokaal hebben [geïnstalleerd][azure-cli-install]of de CLI gebruiken in de [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Beleid voor het opnieuw opstarten van containers

Wanneer u een [containergroep](container-instances-container-groups.md) maakt in Azure Container Instances, u een van de drie beleidsinstellingen voor opnieuw opstarten opgeven.

| Beleid voor opnieuw starten   | Beschrijving |
| ---------------- | :---------- |
| `Always` | Containers in de containergroep worden altijd opnieuw gestart. Dit is de **standaard**instelling die wordt toegepast wanneer er geen beleid voor opnieuw starten wordt opgegeven bij het maken van een container. |
| `Never` | Containers in de containergroep worden nooit opnieuw gestart. De containers worden maximaal één keer uitgevoerd. |
| `OnFailure` | Containers in de containergroep worden alleen opnieuw gestart als het proces in de container mislukt (wanneer deze wordt afgesloten met een andere afsluitcode dan nul). De containers worden ten minste één keer uitgevoerd. |

## <a name="specify-a-restart-policy"></a>Een herstartbeleid opgeven

Hoe u een herstartbeleid opgeeft, is afhankelijk van hoe u uw containerexemplaren maakt, zoals met de Azure CLI-, Azure PowerShell-cmdlets of in de Azure-portal. Geef in Azure CLI `--restart-policy` de parameter op wanneer u [az-containermaken aanroept.][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Voorbeeld van voltooiing uitvoeren

Als u het beleid voor opnieuw starten in actie wilt bekijken, maakt `OnFailure` u een containerinstantie vanuit de microsoft [aci-wordcount-afbeelding][aci-wordcount-image] en geeft u het beleid voor opnieuw opstarten op. In deze voorbeeldcontainer wordt een Python-script uitgevoerd dat standaard de tekst van Shakespeare's [Hamlet](http://shakespeare.mit.edu/hamlet/full.html)analyseert , de 10 meest voorkomende woorden aan SOAOUT schrijft en vervolgens afgaat.

Voer de voorbeeldcontainer uit met de volgende opdracht [AZ-container maken:][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances start de container en stopt deze wanneer de toepassing (of het script in dit geval) wordt afgesloten. Wanneer Azure Container Instances een container `Never` `OnFailure`stopt waarvan het herstartbeleid is of wanneer de status van de container is ingesteld op **Beëindigd**. U de status van een container controleren met de opdracht [AZ Container Show:][az-container-show]

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

Voorbeelduitvoer:

```bash
"Terminated"
```

Wanneer de status van de voorbeeldcontainer *Beëindigd* is, kunt u de taakuitvoer zien door de containerlogboeken te bekijken. Voer de opdracht [az container logs][az-container-logs] uit om de uitvoer van het script te bekijken:

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

In dit voorbeeld wordt de uitvoer weergegeven die het script naar SODOUT heeft verzonden. Uw gecontaineriseerde taken kunnen echter in plaats daarvan hun uitvoer naar permanente opslag schrijven voor later ophalen. Bijvoorbeeld naar een [Azure-bestandsshare](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Volgende stappen

Op taken gebaseerde scenario's, zoals batchverwerking van een grote gegevensset met meerdere containers, kunnen profiteren van aangepaste [omgevingsvariabelen](container-instances-environment-variables.md) of [opdrachtregels](container-instances-start-command.md) tijdens runtime.

Zie [Een Azure-bestandsshare met Azure Container Instances monteren voor](container-instances-mounting-azure-files-volume.md)meer informatie over het uitvoeren van de uitvoer van uw containers die tot voltooiing worden uitgevoerd.

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
