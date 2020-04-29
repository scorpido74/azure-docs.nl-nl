---
title: Beleid voor uitvoerings taken opnieuw starten
description: Meer informatie over het gebruik van Azure Container Instances voor het uitvoeren van taken die worden uitgevoerd voor voltooiing, zoals het bouwen, testen of het renderen van afbeeldingen.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 8ef4ef228038242f53abc8041470f7f596ab1157
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80131499"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Taken met containers uitvoeren met beleids regels voor opnieuw opstarten

Het gemak en de snelheid waarmee containers in Azure Container Instances worden geïmplementeerd, zorgt voor een aantrekkelijk platform voor het uitvoeren van eenmalige taken zoals bouwen, testen en rendering van afbeeldingen in een containerinstantie.

Met een configureerbaar beleid voor opnieuw starten kunt u opgeven dat uw containers worden gestopt wanneer hun processen zijn voltooid. Omdat containerinstanties per seconde worden gefactureerd, betaalt u alleen voor de rekenresources die worden gebruikt terwijl de container waar uw taak wordt uitgevoerd, actief is.

In de voor beelden in dit artikel wordt gebruikgemaakt van de Azure CLI. U moet Azure CLI versie 2.0.21 of hoger hebben [geïnstalleerd][azure-cli-install], of de CLI gebruiken in de [Azure Cloud shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Beleid voor opnieuw opstarten van container

Wanneer u een [container groep](container-instances-container-groups.md) maakt in azure container instances, kunt u een van de drie beleids instellingen voor het opnieuw opstarten opgeven.

| Beleid voor opnieuw starten   | Beschrijving |
| ---------------- | :---------- |
| `Always` | Containers in de containergroep worden altijd opnieuw gestart. Dit is de **standaard**instelling die wordt toegepast wanneer er geen beleid voor opnieuw starten wordt opgegeven bij het maken van een container. |
| `Never` | Containers in de containergroep worden nooit opnieuw gestart. De containers worden maximaal één keer uitgevoerd. |
| `OnFailure` | Containers in de containergroep worden alleen opnieuw gestart als het proces in de container mislukt (wanneer deze wordt afgesloten met een andere afsluitcode dan nul). De containers worden ten minste één keer uitgevoerd. |

## <a name="specify-a-restart-policy"></a>Beleid voor opnieuw opstarten opgeven

Hoe u een beleid voor opnieuw opstarten opgeeft, is afhankelijk van hoe u de container instanties maakt, zoals met de Azure CLI, Azure PowerShell-cmdlets of in de Azure Portal. Geef in de Azure CLI de `--restart-policy` para meter op wanneer u [AZ container Create][az-container-create]aanroept.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Voor beeld van uitvoeren naar voltooid

Als u het beleid voor opnieuw opstarten in actie wilt zien, maakt u een container exemplaar van de micro soft [ACI-WordCount-][aci-wordcount-image] installatie kopie en geeft u het `OnFailure` beleid voor opnieuw opstarten op. In deze voorbeeld container wordt een python-script uitgevoerd dat standaard de tekst van de [Hamlet](http://shakespeare.mit.edu/hamlet/full.html)van Shakespeare analyseert, de tien meest voorkomende woorden naar stdout schrijft en vervolgens afsluit.

Voer de voorbeeld container uit met de volgende opdracht [AZ container Create][az-container-create] :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances start de container en stopt deze wanneer de toepassing (of het script in dit geval) wordt afgesloten. Wanneer Azure Container Instances een container stopt waarvoor het beleid voor `Never` opnieuw `OnFailure`opstarten is of, wordt de status van de container ingesteld op **beëindigd**. U kunt de status van een container controleren met de opdracht [AZ container show][az-container-show] :

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
