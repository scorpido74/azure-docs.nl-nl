---
title: Updates van basisafbeeldingen - Taken
description: Meer informatie over basisafbeeldingen voor toepassingscontainerafbeeldingen en over hoe een basisafbeeldingsupdate een Azure Container Registry-taak kan activeren.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617929"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Informatie over basisafbeeldingsupdates voor ACR-taken

In dit artikel vindt u achtergrondinformatie over updates van de basisafbeelding van een toepassing en hoe deze updates een Azure Container Registry-taak kunnen activeren.

## <a name="what-are-base-images"></a>Wat zijn basisafbeeldingen?

Dockerfiles die de meeste containerafbeeldingen definiëren, geven een bovenliggende afbeelding op waarvan de afbeelding is gebaseerd, vaak aangeduid als *de basisafbeelding.* Basisinstallatiekopieën bevatten doorgaans het besturingssysteem, bijvoorbeeld [Alpine Linux][base-alpine] of [Windows Nano Server][base-windows], waarop de overige lagen van de container worden toegepast. Ook kunnen basisinstallatiekopieën toepassingsframeworks zoals [Node.js][base-node] of [.NET Core][base-dotnet] bevatten. Deze basisbeelden zijn zelf meestal gebaseerd op openbare upstream beelden. Verschillende van uw toepassingsafbeeldingen kunnen een gemeenschappelijke basisafbeelding delen.

Een basisinstallatiekopie wordt vaak door de installatiekopie-maintainer bijgewerkt om nieuwe functies of verbeteringen toe te voegen aan het besturingssysteem of framework in de installatiekopie. Beveiligingspatches zijn een andere veelvoorkomende oorzaak van een update van de basisinstallatiekopie. Wanneer deze upstream-updates plaatsvinden, moet u ook uw basisafbeeldingen bijwerken om de kritieke oplossing op te nemen. Elke toepassingsafbeelding moet vervolgens ook worden herbouwd om deze upstream-fixes die nu in uw basisafbeelding zijn opgenomen, op te nemen.

In sommige gevallen, zoals een privé-ontwikkelingsteam, kan een basisafbeelding meer opgeven dan besturingssysteem of framework. Een basisafbeelding kan bijvoorbeeld een afbeelding van een gedeelde servicecomponent zijn die moet worden bijgehouden. Leden van een team moeten deze basisafbeelding mogelijk bijhouden om te testen of moeten de afbeelding regelmatig bijwerken bij het ontwikkelen van toepassingsafbeeldingen.

## <a name="track-base-image-updates"></a>Updates van basisafbeeldingen bijhouden

Met ACR Tasks hebt u de mogelijkheid om automatisch installatiekopieën te maken wanneer een containerinstallatiekopie wordt bijgewerkt.

ACR Tasks detecteert dynamisch basisafbeeldingsafhankelijkheden wanneer het een containerafbeelding bouwt. Als gevolg hiervan kan het detecteren wanneer de basisafbeelding van een toepassingsafbeelding wordt bijgewerkt. Met één vooraf geconfigureerde buildtaak kan ACR Tasks elke toepassingsafbeelding die naar de basisafbeelding verwijst, automatisch opnieuw opbouwen. Met deze automatische detectie en wederopbouw bespaart ACR Tasks u de tijd en moeite die normaal gesproken nodig is om elke toepassingsafbeelding handmatig bij te houden en bij te werken die verwijst naar uw bijgewerkte basisafbeelding.

## <a name="base-image-locations"></a>Locaties voor basisafbeeldingen

Voor afbeeldingsbuilds van een Dockerfile detecteert een ACR-taak afhankelijkheden van basisafbeeldingen op de volgende locaties:

* Hetzelfde Azure-containerregister waar de taak wordt uitgevoerd
* Een ander privé-Azure-containerregister in dezelfde of een andere regio 
* Een openbare repo in Docker Hub 
* Een openbare repo in Microsoft Container Registry

Als de basisafbeelding `FROM` die in de instructie is opgegeven zich op een van deze locaties bevindt, voegt de ACR-taak een haak toe om ervoor te zorgen dat de afbeelding opnieuw wordt opgebouwd wanneer de basis wordt bijgewerkt.

## <a name="additional-considerations"></a>Aanvullende overwegingen

* **Basisafbeeldingen voor toepassingsafbeeldingen** - Momenteel houdt een ACR-taak alleen basisafbeeldingsupdates bij voor afbeeldingen van toepassingen *(runtime).* Het houdt geen basisafbeeldingsupdates bij voor tussentijdse *(buildtime)* afbeeldingen die worden gebruikt in dockerbestanden met meerdere fasen.  

* **Standaard ingeschakeld** - Wanneer u een ACR-taak maakt met de opdracht [az acr-taakmaken,][az-acr-task-create] wordt de taak standaard *ingeschakeld* voor trigger door een basisafbeeldingsupdate. Dat wil `base-image-trigger-enabled` zeggen, het pand is ingesteld op True. Als u dit gedrag in een taak wilt uitschakelen, werkt u de eigenschap bij naar False. Voer bijvoorbeeld de volgende opdracht [voor az acr-taakupdate][az-acr-task-update] uit:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Trigger om afhankelijkheden bij te houden** - Om een ACR-taak in staat te stellen de afhankelijkheden van een containerafbeelding te bepalen en bij te houden - inclusief de basisafbeelding - moet u eerst de taak activeren om de afbeelding **minstens één keer**te bouwen. Activeer de taak bijvoorbeeld handmatig met de opdracht [AZ ACR Task Run.][az-acr-task-run]

* **Stabiele tag voor basisafbeelding** - Als u een taak wilt activeren op basisafbeeldingsupdate, moet de basisafbeelding een *stabiele* tag hebben, zoals `node:9-alpine`. Deze tagging is typisch voor een basisafbeelding die wordt bijgewerkt met OS- en frameworkpatches naar een nieuwste stabiele release. Als de basisafbeelding wordt bijgewerkt met een nieuwe versietag, wordt een taak niet geactiveerd. Zie de richtlijnen voor aanbevolen procedures voor meer informatie over het [taggen](container-registry-image-tag-version.md)van afbeeldingen. 

* **Andere taaktriggers** - In een taak die wordt geactiveerd door basisafbeeldingsupdates, u ook triggers inschakelen op basis van [broncodecommit](container-registry-tutorial-build-task.md) of [een planning.](container-registry-tasks-scheduled.md) Een basisafbeeldingsupdate kan ook leiden tot een [taak in meerdere stappen.](container-registry-tasks-multi-step.md)

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende zelfstudies voor scenario's om het bouwen van toepassingsafbeeldingen te automatiseren nadat een basisafbeelding is bijgewerkt:

* [Containerimagebuilds automatiseren wanneer een basisafbeelding wordt bijgewerkt in hetzelfde register](container-registry-tutorial-base-image-update.md)

* [Containerimagebuilds automatiseren wanneer een basisafbeelding wordt bijgewerkt in een ander register](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
