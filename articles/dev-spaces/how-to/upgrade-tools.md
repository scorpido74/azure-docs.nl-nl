---
title: Upgrade uitvoeren van hulpprogramma's voor Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Meer informatie over het upgraden van de opdracht regel Programma's van Azure dev Spaces, de Visual, de code-extensie en de Visual Studio-extensie
keywords: Docker, Kubernetes, azure, AKS, Azure Container Service, containers
ms.openlocfilehash: 07d55689ac94a865527f4b595765d67b28ddb97a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438409"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Upgrade uitvoeren van hulpprogramma's voor Azure Dev Spaces

Als er een nieuwe versie is en u gebruikt al Azure Dev Spaces, dan moet u mogelijk uw clienthulpprogramma's voor Azure Dev Spaces upgraden.

## <a name="update-the-azure-cli"></a>De Azure CLI bijwerken

Als u de nieuwste Azure CLI hebt bijgewerkt, krijgt u ook de meest recente versie van de Dev Spaces CLI-extensie.

U hoeft de vorige versie niet te verwijderen, maar slechts de juiste download te vinden op [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Bijwerken van de Dev Spaces CLI-extensie en de opdrachtregelprogramma's

Voer de volgende opdracht uit:

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>De VS code-extensie bijwerken

Eenmaal geïnstalleerd wordt de extensie automatisch bijgewerkt. U moet mogelijk de extensie opnieuw laden voor het gebruik van de nieuwe functies. Open in Visual Studio Code, in het deelvenster **extensies** kies de **Azure Dev Spaces** extensies, en kies **opnieuw laden**.

## <a name="update-the-visual-studio-extension"></a>De Visual Studio-extensie bijwerken

Zoals met andere extensies en updates, toont Visual Studio een melding wanneer er een update beschikbaar is voor de Visual Studio Tools voor Kubernetes, waar Azure Dev Spaces onderdeel van is. Zoek naar een vlag pictogram in de rechter bovenhoek van het scherm.

Als u de hulpprogram ma's in Visual Studio wilt bijwerken, kiest u het menu-item **Hulpprogram ma's > extensies en updates** en kiest u aan de linkerkant de optie **updates**. Zoek de **Hulpprogram ma's voor Visual Studio voor Kubernetes** en klik op de knop **bijwerken** .

## <a name="next-steps"></a>Volgende stappen

Test de nieuwe hulpprogramma's door een nieuw cluster te maken. Probeer de snelstartgidsen en zelfstudies op [Azure Dev Spaces](/azure/dev-spaces).