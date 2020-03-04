---
title: Upgrade uitvoeren van hulpprogramma's voor Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Meer informatie over het upgraden van de opdracht regel Programma's van Azure dev Spaces, Visual Studio code extension en de Visual Studio-extensie
keywords: Docker, Kubernetes, azure, AKS, Azure Container Service, containers
ms.openlocfilehash: 748ed4699591a21a5a09da924e093011b0813b46
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252352"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Upgrade uitvoeren van hulpprogramma's voor Azure Dev Spaces

Als er een nieuwe versie is en u gebruikt al Azure Dev Spaces, dan moet u mogelijk uw clienthulpprogramma's voor Azure Dev Spaces upgraden.

## <a name="update-the-azure-cli"></a>De Azure CLI bijwerken

Als u de nieuwste Azure CLI hebt bijgewerkt, krijgt u ook de meest recente versie van de Dev Spaces CLI-extensie.

U hoeft de vorige versie niet te verwijderen, maar u hoeft alleen de juiste down load te vinden in [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Bijwerken van de Dev Spaces CLI-extensie en de opdrachtregelprogramma's

Voer de volgende opdracht uit:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>De VS code-extensie bijwerken

Eenmaal geïnstalleerd wordt de extensie automatisch bijgewerkt. U moet mogelijk de extensie opnieuw laden voor het gebruik van de nieuwe functies. Open in VS code het deel venster **uitbrei dingen** , kies de **Azure dev Space** -extensies en kies **opnieuw laden**.

## <a name="update-the-visual-studio-extension"></a>De Visual Studio-extensie bijwerken

Zoals met andere extensies en updates, toont Visual Studio een melding wanneer er een update beschikbaar is voor de Visual Studio Tools voor Kubernetes, waar Azure Dev Spaces onderdeel van is. Zoek naar een vlag pictogram in de rechter bovenhoek van het scherm.

Als u de hulpprogram ma's in Visual Studio wilt bijwerken, kiest u het menu-item **Hulpprogram ma's > extensies en updates** en kiest u aan de linkerkant de optie **updates**. Zoek de **Hulpprogram ma's voor Visual Studio voor Kubernetes** en klik op de knop **bijwerken** .

## <a name="next-steps"></a>Volgende stappen

Test de nieuwe hulpprogramma's door een nieuw cluster te maken. Probeer de Snelstartgids en zelf studies op [Azure dev Spaces](/azure/dev-spaces).
