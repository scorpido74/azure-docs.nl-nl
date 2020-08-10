---
title: 'Quickstart: een Docker-container implementeren in een containerinstantie - Portal'
description: In deze quickstart gebruikt u Azure Portal om snel een in een container geplaatste web-app te implementeren die wordt uitgevoerd in een geïsoleerde Azure-containerinstantie
ms.topic: quickstart
ms.date: 03/09/2020
ms.custom: seodec18, mvc, devx-track-javascript
ms.openlocfilehash: 5f36c81abd462f6dfd59c42da71e096e07206cae
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408064"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Quickstart: Een containerinstantie in Azure implementeren met behulp van Azure Portal

Gebruik Azure Container Instances om snel en eenvoudig serverloze Docker-containers uit te voeren in Azure. Een toepassing implementeren in een containerinstantie op aanvraag, wanneer u geen volledig indelingsplatform voor containers nodig hebt zoals Azure Kubernetes Service.

In deze quickstart gebruikt u Azure Portal om een geïsoleerde Docker-container te implementeren en maakt u de bijbehorende toepassing beschikbaar met een FQDN (Fully Qualified Domain Name). Na het configureren van een paar instellingen en de implementatie van de container kunt u bladeren naar de toepassing die wordt uitgevoerd:

![App die is geïmplementeerd in Azure Container Instances, weergegeven in de browser][aci-portal-07]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][azure-free-account] aan voordat u begint.

## <a name="create-a-container-instance"></a>Een containerinstantie maken

Selecteer **Een nieuwe resource maken** > **Containers** > **Container Instances**.

![Begin met het maken van een nieuwe containerinstantie in Azure Portal][aci-portal-01]

Voer op de pagina **Basisgegevens** de volgende waarden in voor de tekstvakken **Resourcegroep**, **Containernaam** en **Containerinstallatiekopie**. Laat de andere waarden op de standaardwaarden staan en selecteer vervolgens **OK**.

* Resourcegroep: **Nieuwe maken** > `myresourcegroup`
* Containernaam: `mycontainer`
* Bron van installatiekopie: **Quickstart-installatiekopieën**
* Containerinstallatiekopie: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

![Basisinstellingen configureren voor een nieuwe containerinstantie in Azure Portal][aci-portal-03]

Voor deze quickstart gebruikt u de standaardinstellingen voor het implementeren van de openbare Microsoft `aci-helloworld`-installatiekopie. Dit voorbeeld van een Linux-installatiekopie bevat een kleine web-app die is geschreven in Node.js en die een statische HTML-pagina dient. U kunt ook uw eigen containerinstallatiekopieën gebruiken die zijn opgeslagen in Azure Container Registry, Docker Hub of andere registers.

Geef op de pagina **Netwerken** een **DNS-naamlabel** op voor uw container. De naam moet uniek zijn voor de Azure-regio waarin u de containerinstantie maakt. De container zal openbaar bereikbaar zijn op `<dns-name-label>.<region>.azurecontainer.io`. Als u een foutbericht 'DNS-naamlabel niet beschikbaar' ontvangt, probeert u een ander DNS-naamlabel.

![Netwerkinstellingen configureren voor een nieuwe containerinstantie in Azure Portal][aci-portal-04]

Laat de standaardwaarden van de andere instellingen ongewijzigd en selecteer vervolgens **Controleren en maken**.

Wanneer de validatie is voltooid, ziet u een overzicht van de containerinstellingen. Selecteer **Maken** om de aanvraag voor de containerimplementatie te verzenden.

![Overzicht van de instellingen voor een nieuwe containerinstantie in Azure Portal][aci-portal-05]

Wanneer de implementatie is gestart, wordt een melding weergegeven die aangeeft dat de implementatie wordt uitgevoerd. Er wordt nog een melding weergegeven wanneer de containergroep is geïmplementeerd.

Open het overzicht voor de containergroep door naar **Resourcegroepen** > **myresourcegroup** > **mycontainer** te navigeren. Noteer de **FQDN** (de FQDN-naam) van de container-instantie, evenals de **Status**.

![Overzicht van containergroepen in Azure Portal][aci-portal-06]

Wanneer de **Status** eenmaal *Uitvoeren* is, gaat u naar de FQDN van de container in uw browser.

![App die is geïmplementeerd met Azure Container Instances, weergegeven in de browser][aci-portal-07]

Gefeliciteerd! Als u slechts enkele instellingen configureert, hebt u een openbaar toegankelijke toepassing in Azure Container Instances geïmplementeerd.

## <a name="view-container-logs"></a>Containerlogboeken ophalen

Het weergeven van de logboeken voor een exemplaar van de container is handig bij het oplossen van problemen met de container of de toepassing die wordt uitgevoerd.

Selecteer om de container-logboeken weer te geven onder **Instellingen** de optie **Containers** en vervolgens **Logboeken**. De HTTP GET-aanvraag wordt als het goed is gegenereerd wanneer u de toepassing in uw browser hebt bekeken.

![Container-logboeken in Azure Portal][aci-portal-11]

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u met de container klaar bent, selecteert u **Overzicht** voor de container-instantie *mycontainer* en vervolgens **Verwijderen**.

![De containerinstantie verwijderen in de Azure-portal][aci-portal-09]

Selecteer **Ja** als het bevestigingsvenster verschijnt.

![Een containerinstantie in de Azure-portal verwijderen][aci-portal-10]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een Azure-containerinstantie gemaakt van een openbare Microsoft-installatiekopie. Als u zelf een containerinstallatiekopie wilt bouwen en deze wilt implementeren met behulp van een privé Azure Container-register, gaat u verder met de zelfstudie voor Azure Container Instances.

> [!div class="nextstepaction"]
> [Zelfstudie voor Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/