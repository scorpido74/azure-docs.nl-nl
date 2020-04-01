---
title: Een app installeren op een zelfstandig cluster
description: In deze zelfstudie leert u hoe u een toepassing in uw standalone Service Fabric-cluster installeert.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 124fc6035c1d0ad504a34b7db227f0f4675f24f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75613955"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Zelfstudie: een toepassing implementeren in een zelfstandig Service Fabric-cluster

Zelfstandige Service Fabric-clusters bieden u de mogelijkheid om uw eigen omgeving te kiezen en een cluster te maken als onderdeel van de benadering "Elk besturingssysteem, elke cloud" die we in Service Fabric hanteren. In deze zelfstudiereeks maakt u een zelfstandig cluster dat wordt gehost op AWS en implementeert u er een toepassing in.

Deze zelfstudie is deel drie van een serie.  Service Fabric standalone clusters bieden u de mogelijkheid om uw eigen omgeving te kiezen en een cluster te maken als onderdeel van onze "elk besturingssysteem, elke cloud"-benadering met Service Fabric. Deze zelfstudie laat zien hoe u de AWS-infrastructuur kunt maken die nodig is om dit zelfstandige cluster te hosten.

In deel drie van de serie leert u het volgende:

> [!div class="checklist"]
> * De voorbeeld-app downloaden
> * In het cluster implementeren

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* [Installeer Visual Studio 2019](https://www.visualstudio.com/) en installeer de **Azure-ontwikkel-** en **ASP.NET- en webontwikkelingsworkloads.**
* [Installeer de Service Fabric-SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>De voorbeeldtoepassing om te stemmen downloaden

Voer in een opdrachtvenster de volgende opdracht uit om de opslagplaats van de voorbeeld-app te klonen op uw lokale computer.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>De app in het Service Fabric-cluster implementeren

Wanneer de toepassing is gedownload, kunt u deze rechtstreeks vanuit Visual Studio implementeren naar een cluster.

1. Open Visual Studio

2. **Bestand** > **openen selecteren**

3. Navigeer naar de map waarin u de git-opslagplaats hebt gekloond en selecteer Voting.sln

4. Klik met de `Voting` rechtermuisknop op het toepassingsproject in de Solution Explorer en kies **Publiceren**

5. Selecteer de vervolgkeuzelijst voor het **verbindingseindpunt** en voer de openbare DNS-naam van een van de knooppunten in uw cluster in.  Bijvoorbeeld `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Houd er rekening mee dat in Azure een volledig gekwalificeerde domeinnaam (FQDN) niet automatisch wordt gegeven, maar eenvoudig kan [worden ingesteld op de pagina VM-overzicht.](https://docs.microsoft.com/azure/virtual-machines/linux/portal-create-fqdn)

6. Open uw voorkeursbrowser en typ het clusteradres in (het eindpunt van de verbinding, deze app wordt geïmplementeerd op poort 8080 - bijvoorbeeld ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![API-reactie van cluster](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Volgende stappen

In deel drie van de serie hebt u geleerd hoe u een toepassing in uw cluster kunt implementeren:

> [!div class="checklist"]
> * De voorbeeld-app downloaden
> * In het cluster implementeren

Ga door naar deel van de serie om uw cluster op te schonen.

> [!div class="nextstepaction"]
> [Uw resources opschonen](service-fabric-tutorial-standalone-clean-up.md)