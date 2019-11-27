---
title: 'Quick Start: REGI ster maken in de portal'
description: Leer snel hoe u een persoonlijk docker-REGI ster maakt in Azure Container Registry met de Azure Portal.
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c5e6de0fdb7d0e46fa9be28483fd894289baf1a3
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455218"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Snelstartgids: een persoonlijk container register maken met behulp van de Azure Portal

Een Azure-containerregister is een persoonlijk Docker-register in Azure waar u uw persoonlijke installatiekopieën van de Docker-container kunt opslaan en beheren. In deze quickstart maakt u een containerregister in Azure Portal. Gebruik vervolgens Docker-opdrachten om een containerinstallatiekopie naar het register pushen, waarna u de installatiekopie ophaalt en uitvoert vanuit het register.

Als u zich wilt aanmelden bij het register om met containerinstallatiekopieën te werken, moet u in deze quickstart de Azure CLI uitvoeren (versie 2.0.55 of hoger wordt aanbevolen). Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Docker moet ook lokaal zijn geïnstalleerd. Docker biedt pakketten die eenvoudig Docker op elke configureren op elk [Mac][docker-mac]-, [Windows][docker-windows]- of [Linux][docker-linux]-systeem.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-container-registry"></a>Een containerregister maken

Selecteer **Een resource maken** > **Containers** > **Container Registry**.

![Een containerregister maken met Azure Portal][qs-portal-01]

Voer waarden in voor **Registernaam** en **Resourcegroep**. De registernaam moet uniek zijn binnen Azure en mag 5 tot 50 alfanumerieke tekens bevatten. Maak voor deze snelstart een nieuwe resourcegroep met de naam `West US` in locatie `myResourceGroup`. Kies voor **SKU** de optie Basic. Selecteer **Maken** om de ACR-instantie te implementeren.

![Een containerregister maken in de Azure-portal][qs-portal-03]

In deze quickstart maakt u een *Basic*-register. Dit is een voor kosten geoptimaliseerde optie voor ontwikkelaars die meer willen leren over Azure Container Registry. Zie [sku's voor container registers][container-registry-skus]voor meer informatie over de beschik bare service lagen.

Als het bericht **Implementatie voltooid** wordt weergegeven, selecteert u het containerregister in de portal. 

![Overzicht van containerregisters in de Azure-portal][qs-portal-05]

Noteer de waarde van de **aanmeldingsserver**. U gebruikt deze waarden in de volgende stappen wanneer u het register gebruikt met de Docker-opdrachtregelinterface.

## <a name="log-in-to-registry"></a>Aanmelden bij register

Voordat u installatiekopieën van containers gaat pushen en pullen, moet u zich aanmelden bij het ACR-exemplaar. Open een opdracht shell in het besturings systeem en gebruik de opdracht [AZ ACR login][az-acr-login] in de Azure cli.

```azurecli
az acr login --name <acrName>
```

De opdracht retourneert `Login Succeeded` nadat deze is voltooid. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Containerinstallatiekopieën opvragen

Als u de installatiekopieën in uw register wilt weergeven, gaat u naar het register in de portal en selecteert u **Opslagplaatsen**. Selecteer vervolgens de opslagplaats die u met `docker push` hebt gemaakt.

In dit voor beeld selecteren we de opslag plaats **Hallo wereld** en zien we de afbeelding met `v1`Tags onder **labels**.

![Containerinstallatiekopieën weergeven in de Azure-portal][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u uw resources wilt opschonen, navigeert u naar de resourcegroep **myResourceGroup** in de portal. Wanneer de resourcegroep is geladen, klikt u op **Resourcegroep verwijderen** om de resourcegroep, het containerregister en alle daarin opgeslagen containerinstallatiekopieën te verwijderen.

![Een resourcegroep verwijderen in de Azure-portal][qs-portal-08]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure Container Registry gemaakt met de Azure-portal. U hebt een containerinstallatiekopie gepusht en de installatiekopie uit het register opgehaald en uitgevoerd. Ga verder met de zelfstudies voor Azure Container Registry om meer te leren over ACR.

> [!div class="nextstepaction"]
> [Azure Container Registry zelf studies][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
