---
title: Azure Dev Spaces-hulpprogramma's upgraden
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Meer informatie over het upgraden van de opdrachtenlijn tools voor Azure Dev Spaces, de extensie Visual Studio Code en de Visual Studio-extensie
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
ms.openlocfilehash: 748ed4699591a21a5a09da924e093011b0813b46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265231"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Azure Dev Spaces-hulpprogramma's upgraden

Als er een nieuwe release is en u azure dev spaces al gebruikt, moet u mogelijk uw Azure Dev Spaces-clienthulpprogramma's upgraden.

## <a name="update-the-azure-cli"></a>De Azure CLI bijwerken

Wanneer u de nieuwste Azure CLI bijwerkt, krijgt u ook de nieuwste versie van de DEv Spaces CLI-extensie.

U hoeft de vorige versie niet te verwijderen, zoek gewoon de juiste download bij [Azure CLI.](/cli/azure/install-azure-cli?view=azure-cli-latest)


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>De CLI-extensie en de opdrachtregelgereedschappen Dev Spaces bijwerken

Voer de volgende opdracht uit:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>De VS-code-extensie bijwerken

Eenmaal geïnstalleerd wordt de extensie automatisch bijgewerkt. Mogelijk moet u de extensie opnieuw laden om de nieuwe functies te gebruiken. Open in VS-code het deelvenster **Extensies,** kies de **Azure Dev Spaces-extensies** en kies **Opnieuw laden.**

## <a name="update-the-visual-studio-extension"></a>De Visual Studio-extensie bijwerken

Net als bij andere extensies en updates zal Visual Studio u op de hoogte stellen wanneer er een update beschikbaar is voor de Visual Studio Tools voor Kubernetes, waaronder Azure Dev Spaces. Zoek naar een vlagpictogram rechtsboven in het scherm.

Als u de hulpprogramma's in Visual Studio wilt bijwerken, kiest u het **menu-item Extra > extensies en updates** en kiest u aan de linkerkant **Updates**. Zoek **Visual Studio Tools voor Kubernetes** en kies de knop **Bijwerken.**

## <a name="next-steps"></a>Volgende stappen

Test de nieuwe tools door een nieuw cluster te maken. Probeer de quickstarts en tutorials bij [Azure Dev Spaces](/azure/dev-spaces).
