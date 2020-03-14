---
title: KEUR Azure Kubernetes-cluster beheren met de Web-UI
description: De Kubernetes-webgebruikersinterface in Azure Container Service gebruiken
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 01abcc961d1c2ad9d3e2cf35f82e62929bc2fb89
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371134"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>KEUR De Kubernetes-webgebruikersinterface gebruiken met Azure Container Service

> [!TIP]
> Voor de bijgewerkte versie van dit artikel die gebruikmaakt van de Azure Kubernetes-service, raadpleegt u [toegang tot het Kubernetes-Web dash board in azure Kubernetes service (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Vereisten
In dit scenario wordt ervan uitgegaan dat u [een Kubernetes-cluster hebt gemaakt met behulp van Azure container service](container-service-kubernetes-walkthrough.md).


Er wordt ook van uitgegaan dat u de Azure CLI-en `kubectl`-hulpprogram ma's hebt geïnstalleerd.

U kunt testen of u het `az`-hulp programma hebt geïnstalleerd door uit te voeren:

```azurecli
az --version
```

Als u het hulp programma `az` niet hebt geïnstalleerd, zijn er [hier](https://github.com/azure/azure-cli#installation)instructies.

U kunt testen of u het `kubectl`-hulp programma hebt geïnstalleerd door uit te voeren:

```console
kubectl version
```

Als `kubectl` niet is geïnstalleerd, kunt u het volgende uitvoeren:

```azurecli
az acs kubernetes install-cli
```

## <a name="overview"></a>Overzicht

### <a name="connect-to-the-web-ui"></a>Verbinding maken met de Web-UI
U kunt de Kubernetes-webgebruikersinterface starten door het volgende uit te voeren:

```azurecli
az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Hiermee opent u een webbrowser die is geconfigureerd om te communiceren met een beveiligde proxy die uw lokale machine verbindt met de Kubernetes-webgebruikersinterface.

### <a name="create-and-expose-a-service"></a>Een service maken en weer geven
1. In de Kubernetes-webgebruikersinterface klikt u op de knop **maken** in het rechter venster.

    ![Gebruikers interface maken Kubernetes](./media/container-service-kubernetes-ui/create.png)

    Er wordt een dialoog venster geopend waarin u kunt beginnen met het maken van uw toepassing.

2. Geef deze de naam `hello-nginx`. Gebruik de [`nginx` container van docker](https://hub.docker.com/_/nginx/) en implementeer drie replica's van deze webservice.

    ![Dialoog venster Kubernetes pod maken](./media/container-service-kubernetes-ui/nginx.png)

3. Onder **service**selecteert u **extern** en voert u poort 80 in.

    Met deze instelling wordt de taak verdeling van verkeer naar de drie replica's.

    ![Dialoog venster Kubernetes-service maken](./media/container-service-kubernetes-ui/service.png)

4. Klik op **implementeren** om deze containers en services te implementeren.

    ![Kubernetes-implementatie](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Uw containers weer geven
Nadat u op **implementeren**hebt geklikt, toont de gebruikers interface een weer gave van uw service tijdens de implementatie:

![Kubernetes-status](./media/container-service-kubernetes-ui/status.png)

U kunt de status van elk Kubernetes-object in de cirkel aan de linkerkant van de gebruikers interface zien, onder **peul**. Als het een gedeeltelijk volledige cirkel is, wordt het object nog steeds geïmplementeerd. Wanneer een object volledig is geïmplementeerd, wordt een groen vinkje weer gegeven:

![Kubernetes geïmplementeerd](./media/container-service-kubernetes-ui/deployed.png)

Zodra alles actief is, klikt u op een van de peulen om details over de actieve webservice weer te geven.

![Kubernetese peul](./media/container-service-kubernetes-ui/pods.png)

In de weer gave **peul** ziet u informatie over de containers in de Pod, evenals de CPU-en geheugen bronnen die door deze containers worden gebruikt:

![Kubernetes-resources](./media/container-service-kubernetes-ui/resources.png)

Als u de resources niet ziet, moet u mogelijk enkele minuten wachten totdat de bewakings gegevens zijn door gegeven.

Klik op **Logboeken weer geven**om de logboeken voor de container te bekijken.

![Kubernetes-logboeken](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Uw service weer geven
Naast het uitvoeren van uw containers heeft de Kubernetes-gebruikers interface een externe `Service` gemaakt waarmee een load balancer wordt ingericht om verkeer naar de containers in uw cluster te brengen.

Klik in het navigatie deel venster links op **Services** om alle services weer te geven (er mag slechts één) zijn.

![Kubernetes Services](./media/container-service-kubernetes-ui/service-deployed.png)

In deze weer gave ziet u een extern eind punt (IP-adres) dat aan uw service is toegewezen.
Als u op dat IP-adres klikt, wordt de nginx-container weer geven achter de load balancer.

![nginx weer geven](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Het formaat van uw service wijzigen
Naast het weer geven van uw objecten in de gebruikers interface, kunt u de Kubernetes API-objecten bewerken en bijwerken.

Klik eerst op **implementaties** in het navigatie deel venster links om de implementatie van uw service te bekijken.

Zodra u in die weer gave bent, klikt u op de replicaset en klikt u vervolgens op **bewerken** in de bovenste navigatie balk:

![Kubernetes bewerken](./media/container-service-kubernetes-ui/edit.png)

Bewerk het `spec.replicas` veld dat u wilt `2`en klik op **bijwerken**.

Dit zorgt ervoor dat het aantal replica's wordt afgehaald naar twee door een van uw peulen te verwijderen.

 

