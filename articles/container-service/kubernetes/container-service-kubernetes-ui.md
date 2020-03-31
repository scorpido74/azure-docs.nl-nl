---
title: (AFGESCHAFT) Azure Kubernetes-cluster beheren met web-gebruikersinterface
description: De gebruikersinterface van het Kubernetes-web gebruiken in Azure Container Service
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 01abcc961d1c2ad9d3e2cf35f82e62929bc2fb89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371134"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(AFGESCHAFT) De gebruikersinterface van kubernetes web gebruiken met Azure Container Service

> [!TIP]
> Zie [Toegang tot het Kubernetes-webdashboard in Azure Kubernetes Service (AKS)](../../aks/kubernetes-dashboard.md)voor de bijgewerkte versie van dit artikel waarin Azure Kubernetes Service wordt gebruikt.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Vereisten
Deze walkthrough gaat ervan uit dat u [een Kubernetes-cluster](container-service-kubernetes-walkthrough.md)hebt gemaakt met Azure Container Service.


Er wordt ook van uitgegaan dat `kubectl` u de Azure CLI en hulpprogramma's hebt geïnstalleerd.

U testen of `az` u het gereedschap hebt geïnstalleerd door het uitvoeren van:

```azurecli
az --version
```

Als u het `az` gereedschap niet hebt geïnstalleerd, zijn er [hier](https://github.com/azure/azure-cli#installation)instructies.

U testen of `kubectl` u het gereedschap hebt geïnstalleerd door het uitvoeren van:

```console
kubectl version
```

Als u niet `kubectl` hebt geïnstalleerd, u het:

```azurecli
az acs kubernetes install-cli
```

## <a name="overview"></a>Overzicht

### <a name="connect-to-the-web-ui"></a>Verbinding maken met de webgebruikersinterface
U de gebruikersinterface van het Kubernetes-web starten door het volgende uit te voeren:

```azurecli
az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Hiermee moet een webbrowser worden geopend die is geconfigureerd om te praten met een beveiligde proxy die uw lokale machine verbindt met de Gebruikersinterface van het Kubernetes-web.

### <a name="create-and-expose-a-service"></a>Een service maken en blootleggen
1. Klik in de gebruikersinterface van het Web Kubernetes op **Knop Maken** in het rechterbovenvenster.

    ![Kubernetes Maken Gebruikersinterface](./media/container-service-kubernetes-ui/create.png)

    Er wordt een dialoogvenster geopend waar u beginnen met het maken van uw toepassing.

2. Geef het `hello-nginx`de naam. Gebruik de [ `nginx` container van Docker](https://hub.docker.com/_/nginx/) en implementeer drie replica's van deze webservice.

    ![Dialoogvenster Kubernetes Pod maken](./media/container-service-kubernetes-ui/nginx.png)

3. Selecteer **onder Service**de optie **Extern** en voer poort 80 in.

    Met deze instelling brengt u het verkeer in balans met de drie replica's.

    ![Dialoogvenster Kubernetes-service maken](./media/container-service-kubernetes-ui/service.png)

4. Klik **op Implementeren** om deze containers en services te implementeren.

    ![Kubernetes-implementatie](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Bekijk uw containers
Nadat u op **Implementeren**hebt geklikt, wordt in de gebruikersinterface een weergave van uw service weergegeven terwijl deze wordt geïmplementeerd:

![Kubernetes-status](./media/container-service-kubernetes-ui/status.png)

U de status van elk Kubernetes-object in de cirkel aan de linkerkant van de gebruikersinterface zien onder **Pods**. Als het een gedeeltelijk volledige cirkel is, wordt het object nog steeds geïmplementeerd. Wanneer een object volledig is geïmplementeerd, wordt een groen vinkje weergegeven:

![Kubernetes geïmplementeerd](./media/container-service-kubernetes-ui/deployed.png)

Zodra alles wordt uitgevoerd, klikt u op een van uw pods om details over de lopende webservice te zien.

![Kubernetes Pods](./media/container-service-kubernetes-ui/pods.png)

In de weergave **Pods** ziet u informatie over de containers in de pod en de CPU- en geheugenbronnen die door deze containers worden gebruikt:

![Kubernetes-bronnen](./media/container-service-kubernetes-ui/resources.png)

Als u de resources niet ziet, moet u mogelijk een paar minuten wachten voordat de bewakingsgegevens worden doorgegeven.

Als u de logboeken voor uw container wilt bekijken, klikt u op **Logboeken weergeven**.

![Kubernetes-logboeken](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Uw service bekijken
Naast het uitvoeren van uw containers, heeft `Service` de Kubernetes-gebruikersinterface een externe die een load balancer voorziet om verkeer naar de containers in uw cluster te brengen.

Klik in het linkernavigatiedeelvenster op **Services** om alle services weer te geven (er moet er maar één zijn).

![Kubernetes Services](./media/container-service-kubernetes-ui/service-deployed.png)

In die weergave ziet u een extern eindpunt (IP-adres) dat aan uw service is toegewezen.
Als u op dat IP-adres klikt, ziet u uw Nginx-container achter de load balancer lopen.

![nginx-weergave](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Het formaat van uw service wijzigen
Naast het bekijken van uw objecten in de gebruikersinterface, u de Kubernetes API-objecten bewerken en bijwerken.

Klik eerst op **Implementaties** in het linkernavigatiedeelvenster om de implementatie voor uw service te bekijken.

Als u zich in die weergave bevindt, klikt u op de replicaset en klikt u vervolgens op **Bewerken** in de bovenste navigatiebalk:

![Kubernetes Bewerken](./media/container-service-kubernetes-ui/edit.png)

Bewerk `spec.replicas` het te `2`beveelte veld en klik op **Bijwerken**.

Dit zorgt ervoor dat het aantal replica's dalen tot twee door het verwijderen van een van uw pods.

 

