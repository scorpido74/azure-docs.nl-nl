---
title: Snelstart - Dockercontainer implementeren in containerinstantie - Portal
description: In deze quickstart gebruikt u de Azure-portal om snel een gecontaineriseerde webapp te implementeren die wordt uitgevoerd in een geïsoleerde Azure-containerinstantie
ms.topic: quickstart
ms.date: 03/09/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 7a872e955db46b76d3b12f8ffc38d4a8e497ea63
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79087998"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Snelstart: een containerinstantie implementeren in Azure met behulp van de Azure-portal

Gebruik Azure Container Instances om serverloze Docker-containers in Azure uit te voeren met eenvoud en snelheid. Implementeer een toepassing op een containerexemplaar on-demand wanneer u geen volledig containerorchestration-platform zoals Azure Kubernetes Service nodig hebt.

In deze quickstart gebruikt u de Azure-portal om een geïsoleerde Docker-container te implementeren en de toepassing ervan beschikbaar te maken met een volledig gekwalificeerde domeinnaam (FQDN). Na het configureren van een paar instellingen en de implementatie van de container kunt u bladeren naar de toepassing die wordt uitgevoerd:

![App die is geïmplementeerd in Azure Container Instances, weergegeven in de browser][aci-portal-07]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

Als u geen Azure-abonnement hebt, maakt u een [gratis account][azure-free-account] voordat u begint.

## <a name="create-a-container-instance"></a>Een containerinstantie maken

Selecteer de **instanties voor het maken van een** > **resourcecontainercontainer** > **Container Instances**.

![Begin met het maken van een nieuwe containerinstantie in Azure Portal][aci-portal-01]

Voer op de pagina **Basisbeginselen** de volgende waarden in de **groep Resource**, **Containernaam**en **Tekstvakken voor containerafbeelding** in. Laat de andere waarden op de standaardwaarden staan en selecteer vervolgens **OK**.

* Resourcegroep: **nieuwe maken** > `myresourcegroup`
* Containernaam: `mycontainer`
* Afbeeldingsbron: **Quickstart-afbeeldingen**
* Containerafbeelding: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

![Basisinstellingen configureren voor een nieuwe containerinstantie in Azure Portal][aci-portal-03]

Voor deze quickstart gebruikt u standaardinstellingen `aci-helloworld` om de openbare Microsoft-afbeelding te implementeren. Deze voorbeeld Linux-afbeelding verpakt een kleine web-app geschreven in Node.js die een statische HTML-pagina bedient. U ook uw eigen containerafbeeldingen meenemen die zijn opgeslagen in Azure Container Registry, Docker Hub of andere registers.

Geef **op** de pagina Netwerken een **DNS-naamlabel** op voor uw container. De naam moet uniek zijn in het Azure-gebied waar u de containerinstantie maakt. De container zal openbaar bereikbaar zijn op `<dns-name-label>.<region>.azurecontainer.io`. Als u een foutbericht 'DNS-naamlabel niet beschikbaar' ontvangt, probeert u een ander DNS-naamlabel.

![Netwerkinstellingen configureren voor een nieuwe containerinstantie in de Azure-portal][aci-portal-04]

Laat de andere instellingen op hun standaardinstellingen staan en selecteer **Controleren + maken**.

Wanneer de validatie is voltooid, ziet u een overzicht van de containerinstellingen. Selecteer **Maken** om uw aanvraag voor het implementeren van de container in te dienen.

![Overzicht van de instellingen voor een nieuwe containerinstantie in Azure Portal][aci-portal-05]

Wanneer de implementatie wordt gestart, verschijnt er een melding dat de implementatie aan de gang is. Er wordt nog een melding weergegeven wanneer de containergroep is geïmplementeerd.

Open het overzicht voor de containergroep door te navigeren naar **Resourcegroepen** > **myresourcegroup** > **mycontainer**. Noteer de **FQDN** (de FQDN-naam) van de container-instantie, evenals de **Status**.

![Overzicht van containergroepen in Azure Portal][aci-portal-06]

Wanneer de **Status** eenmaal *Uitvoeren* is, gaat u naar de FQDN van de container in uw browser.

![App die is geïmplementeerd met Azure Container Instances, weergegeven in de browser][aci-portal-07]

Gefeliciteerd! Als u slechts enkele instellingen configureert, hebt u een openbaar toegankelijke toepassing in Azure Container Instances geïmplementeerd.

## <a name="view-container-logs"></a>Containerlogboeken ophalen

Het weergeven van de logboeken voor een exemplaar van de container is handig bij het oplossen van problemen met de container of de toepassing die wordt uitgevoerd.

Als u de logboeken van de container wilt weergeven, selecteert u onder **Instellingen**de optie **Containers**, vervolgens **Logboeken**. De HTTP GET-aanvraag wordt als het goed is gegenereerd wanneer u de toepassing in uw browser hebt bekeken.

![Container-logboeken in Azure Portal][aci-portal-11]

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u met de container klaar bent, selecteert u **Overzicht** voor de container-instantie *mycontainer* en vervolgens **Verwijderen**.

![De containerinstantie verwijderen in de Azure-portal][aci-portal-09]

Selecteer **Ja** als het bevestigingsvenster verschijnt.

![Een containerinstantie in de Azure-portal verwijderen][aci-portal-10]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure-containerexemplaar gemaakt op basis van een openbare Microsoft-afbeelding. Als u zelf een containerinstallatiekopie wilt bouwen en deze wilt implementeren met behulp van een privé Azure Container-register, gaat u verder met de zelfstudie voor Azure Container Instances.

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