---
title: 'Maak een Kubernetes dev-ruimte: Visual Studio & .NET core'
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: In deze zelf studie leert u hoe u Azure dev Spaces en Visual Studio kunt gebruiken om fouten op te sporen en snel een .NET-kern toepassing te herhalen in azure Kubernetes service
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
ms.openlocfilehash: f3be10929a9a0df23529348f2c62e35f2ebaa850
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770710"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-and-net-core-with-azure-dev-spaces"></a>Maak een Kubernetes dev-ruimte: Visual Studio en .NET core met Azure dev Spaces

In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Iteratief code ontwikkelen in containers met Visual Studio.
- Twee afzonderlijke services ontwikkelen en de DNS-servicedetectie van Kubernetes gebruiken om een andere service aan te roepen.
- Uw code op een productieve manier ontwikkelen en testen in een teamomgeving.

> [!Note]
> Zie de sectie [probleem oplossing](troubleshooting.md) **Als u** op elk gewenst moment aan de slag gaat.


## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Een Kubernetes-cluster maken dat is ingeschakeld voor Azure Dev Spaces

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Kies **Een resource maken** > ga naar **Kubernetes** > selecteer **Kubernetes-service** > **Maken**.

   Voer de volgende stappen uit onder elke kop van het formulier *Create Kubernetes cluster* en controleer of de geselecteerde [regio Azure dev-ruimten ondersteunt][supported-regions].

   - **Project Details**: Selecteer een Azure-abonnement en een nieuwe of bestaande Azure-resource groep.
   - **CLUSTERDETAILS**: voer een naam, regio, versie en DNS-voorvoegsel voor het AKS-cluster in.
   - **SCHAAL**: selecteer een VM-grootte voor de AKS-agentknooppunten en het aantal knooppunten. Als u begint met Azure Dev Spaces, is één knooppunt voldoende voor het verkennen van alle functies. Het aantal knooppunten kan op elk gewenst moment na de implementatie van het cluster gemakkelijk worden aangepast. Let op: de VM-grootte kan niet meer worden gewijzigd als een AKS-cluster eenmaal is gemaakt. Zodra een AKS-cluster is geïmplementeerd, kunt u echter eenvoudig een nieuw AKS-cluster met grotere virtuele machines maken en Dev Spaces gebruiken om dat grotere cluster opnieuw te implementeren als u wilt opschalen.

   ![Configuratie-instellingen voor Kubernetes](media/common/Kubernetes-Create-Cluster-2.PNG)


   Selecteer **Volgende: verificatie** wanneer u klaar bent.

1. Kies de gewenste instelling voor RBAC (op rollen gebaseerd toegangsbeheer). Azure Dev Spaces biedt ondersteuning voor clusters waarop RBAC is ingeschakeld of uitgeschakeld.

    ![RBAC-instelling](media/common/k8s-RBAC.PNG)

1. Selecteer **Controleren + maken** en vervolgens **Maken** wanneer u klaar bent.

## <a name="get-the-visual-studio-tools"></a>Visual Studio-hulpprogramma's downloaden
Installeer de nieuwste versie van [Visual Studio](https://www.visualstudio.com/vs/). Voor Visual Studio 2019 in Windows moet u de werk belasting voor Azure Development installeren. Voor Visual Studio 2017 in Windows moet u de ASP.NET-en Web Development-werk belasting installeren, evenals [Visual Studio Tools voor Kubernetes](https://aka.ms/get-azds-visualstudio).

## <a name="create-a-web-app-running-in-a-container"></a>Een web-app maken die wordt uitgevoerd in een container

In deze sectie maakt u een ASP.NET Core web-app en haalt u deze op in een container in Kubernetes.

### <a name="create-an-aspnet-web-app"></a>Een ASP.NET-web-app maken

Maak in Visual Studio een nieuw project. Op dit moment moet het project een **ASP.NET Core-webtoepassing** zijn. Geef het project de naam **webfrontend**.

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Selecteer de sjabloon **Web Application (Model-View-Controller)** en selecteer **.NET Core** en **ASP.NET Core 2.0** in de twee vervolgkeuzelijsten boven in het dialoogvenster. Klik op **OK** om het project te maken.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Dev Spaces inschakelen voor een AKS-cluster

Met het project dat u zojuist hebt gemaakt, selecteert u **Azure Dev Spaces** in de vervolgkeuzelijst met opstartinstellingen, zoals hieronder wordt weergegeven.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Controleer in het dialoogvenster dat vervolgens wordt weergegeven, of u bent aangemeld bij het juiste account, en selecteer vervolgens een bestaand Kubernetes-cluster.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

Laat de waarde in de vervolgkeuzelijst **Space** voorlopig op `default` staan. Later besteden we meer aandacht aan deze optie. Schakel het selectievakje **Publicly Accessible** in zodat de web-app toegankelijk is via een openbaar eindpunt. Deze instelling is niet vereist, maar is wel handig om verderop in dit scenario enkele dingen te demonstreren. Maar u hoeft u geen zorgen te maken, want in beide gevallen kunt u fouten op de website opsporen met Visual Studio.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Klik op **OK** om het cluster te selecteren of een cluster te maken.

Als u een cluster kiest dat niet is ingeschakeld om te werken met Azure Dev Spaces, ziet u een bericht waarin u wordt gevraagd of u dit cluster wilt configureren.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Kies **OK**.

> [!IMPORTANT]
> Met het Azure Dev Spaces-configuratieproces wordt de eventueel aanwezige `azds`-naamruimte in het cluster verwijderd.

 Er wordt een achtergrondtaak gestart om dit te doen. Het uitvoeren van deze taak duurt enkele minuten. Als u wilt zien of de taak nog steeds actief is, plaatst u de muisaanwijzer op het pictogram **Background tasks** in de linkerhoek onder in de statusbalk, zoals wordt weergegeven in de volgende afbeelding.

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> U kunt pas fouten in een toepassing oplossen wanneer de ontwikkelomgeving is gemaakt.

### <a name="look-at-the-files-added-to-project"></a>Bestanden bekijken die zijn toegevoegd aan het project
Terwijl u wacht totdat de ontwikkelomgeving is gemaakt, kunt u bekijken welke bestanden aan het project zijn toegevoegd toen u ervoor koos om een ontwikkelomgeving te gebruiken.

Allereerst ziet u dat een map met de naam `charts` is toegevoegd, en dat er binnen deze map een [Helm-grafiek](https://docs.helm.sh) voor de toepassing is klaargezet. Deze bestanden worden gebruikt om de toepassing in de ontwikkelomgeving te implementeren.

U ziet dat er een bestand met de naam `Dockerfile` is toegevoegd. Dit bestand bevat gegevens die nodig zijn om van de toepassing een pakket te maken met de standaard-Docker-indeling.

Tot slot ziet u een bestand met de naam `azds.yaml`. Dit bestand bevat configuratie over de ontwikkeltijd die de ontwikkelomgeving nodig heeft.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Fouten opsporen in een Kubernetes-container
Zodra de ontwikkelomgeving is gemaakt, kunt u fouten oplossen voor de toepassing. Stel een onderbrekingspunt in de code in, bijvoorbeeld op regel 20 in het bestand `HomeController.cs`, waarbij de variabele `Message` wordt ingesteld. Druk op **F5** om de foutopsporing te starten. 

Visual Studio communiceert met de ontwikkelomgeving om de toepassing te compileren en te implementeren. Vervolgens wordt een browser geopend met de web-app. Het lijkt misschien alsof de container lokaal wordt uitgevoerd, maar dat is niet zo. De container wordt uitgevoerd in de ontwikkelomgeving in Azure. De reden voor het localhost-adres is dat Azure Dev Spaces een tijdelijke SSH-tunnel maakt naar de container die wordt uitgevoerd in AKS.

Klik boven aan de pagina op de koppeling **About** om het onderbrekingspunt te activeren. U hebt volledige toegang tot debug-gegevens, net alsof de code lokaal wordt uitgevoerd. Denk hierbij aan de aanroep-stack, lokale variabelen en informatie over uitzonderingen.

## <a name="iteratively-develop-code"></a>Code iteratief ontwikkelen

Azure Dev Spaces draait niet alleen om het ophalen van code die wordt uitgevoerd in Kubernetes. Het gaat er om dat u de codewijzigingen snel en iteratief toegepast kunt zien in een Kubernetes-omgeving in de cloud.

### <a name="update-a-content-file"></a>Een inhoudsbestand bijwerken


1. Zoek het bestand `./Views/Home/Index.cshtml` en bewerk de HTML-code. Wijzig bijvoorbeeld [regel 73 die `<h2>Application uses</h2>`leest](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Views/Home/Index.cshtml#L73) naar iets zoals: 
  
    ```html
    <h2>Hello k8s in Azure!</h2>`
    ```

2. Sla het bestand op.
3. Ga naar de browser en vernieuw de pagina. De bijgewerkte HTML-code wordt op de webpagina weergegeven.

Wat is er gebeurd? Voor bewerkingen van inhoudsbestanden, zoals HTML en CSS, hoeft een .NET Core-web-app niet opnieuw te worden gecompileerd. Tijdens een actieve F5-sessie worden alle gewijzigde inhoudsbestanden dus automatisch gesynchroniseerd in de actieve container in AKS, zodat u de bewerkingen van de inhoud direct kunt zien.

### <a name="update-a-code-file"></a>Een codebestand bijwerken
Het bijwerken van codebestanden vereist iets meer werk, omdat een .NET Core-app bijgewerkte binaire toepassingsbestanden moet bouwen en produceren.

1. Stop de foutopsporing in Visual Studio.
1. Open het codebestand met de naam `Controllers/HomeController.cs` en bewerk het bericht dat op de pagina Info wordt weergegeven: `ViewData["Message"] = "Your application description page.";`
1. Sla het bestand op.
1. Druk op **F5** als u de foutopsporing weer wilt starten. 

In plaats van telkens als codewijzigingen zijn aangebracht een nieuwe containerinstallatiekopie opnieuw te bouwen en opnieuw te implementeren, wat vaak behoorlijk wat tijd kost, hercompileert Azure Dev Spaces incrementeel code binnen de bestaande container voor een snellere bewerkings-/foutopsporingslus.

Vernieuw de web-app in de browser en ga naar de pagina Info. U ziet dat uw aangepaste bericht wordt weergegeven in de gebruikersinterface.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Informatie over ontwikkelen van meerdere services](multi-service-netcore-visualstudio.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service