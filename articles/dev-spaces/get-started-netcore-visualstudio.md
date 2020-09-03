---
title: 'Een Kubernetes-ontwikkelaarsruimte maken: Visual Studio en .NET Core'
services: azure-dev-spaces
ms.custom: vs-azure, devx-track-azurecli, devx-track-csharp
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: In deze zelfstudie ziet u hoe u Azure Dev Spaces en Visual Studio gebruikt om fouten op te sporen en snel een .NET Core-toepassing te herhalen in Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s
ms.openlocfilehash: 78ad3c643b839d2a0901ac2c1d930d73b718cd8d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999254"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-and-net-core-with-azure-dev-spaces"></a>Een Kubernetes-ontwikkelaarsruimte maken: Visual Studio en .NET Core met Azure Dev Spaces

In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Iteratief code ontwikkelen in containers met Visual Studio.
- Twee afzonderlijke services ontwikkelen en de DNS-servicedetectie van Kubernetes gebruiken om een andere service aan te roepen.
- Uw code op een productieve manier ontwikkelen en testen in een teamomgeving.

> [!NOTE]
> **Als u op enig moment niet verder kunt**, kunt u de [probleemoplossingssectie](troubleshooting.md) raadplegen.

## <a name="install-the-azure-cli"></a>Azure-CLI installeren
Azure Dev Spaces vereist minimale instellingen voor de lokale computer. De configuratie van uw ontwikkelomgeving wordt grotendeels opgeslagen in de cloud en kan worden gedeeld met andere gebruikers. Begin met het downloaden en uitvoeren van de [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Aanmelden bij Azure CLI
Meld u aan bij Azure. Typ de volgende opdracht in een terminalvenster:

```azurecli
az login
```

> [!NOTE]
> Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Als u meerdere Azure-abonnementen hebt...
U kunt uw abonnementen bekijken door het volgende uit te voeren: 

```azurecli
az account list --output table
```

Zoek naar het abonnement met de waarde *True* voor *IsDefault*.
Als dit niet het abonnement is dat u wilt gebruiken, kunt u het standaardabonnement wijzigen:

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Een Kubernetes-cluster maken dat is ingeschakeld voor Azure Dev Spaces

Maak vanaf de opdrachtprompt de resourcegroep in een [regio die ondersteuning biedt voor Azure Dev Spaces][supported-regions].

```azurecli
az group create --name MyResourceGroup --location <region>
```

Maak een Kubernetes-cluster met de volgende opdracht:

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Het duurt een paar minuten om het cluster te maken.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Uw AKS-cluster configureren voor het gebruik van Azure Dev Spaces

Voer de volgende Azure CLI-opdracht in. Gebruik daarbij de resourcegroep die uw AKS-cluster en de naam van uw AKS-cluster bevat. Met de opdracht wordt uw cluster geconfigureerd met ondersteuning voor Azure Dev Spaces.

   ```azurecli
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
   
> [!IMPORTANT]
> Met het Azure Dev Spaces-configuratieproces wordt de eventueel aanwezige `azds`-naamruimte in het cluster verwijderd.

## <a name="get-the-visual-studio-tools"></a>Visual Studio-hulpprogramma's downloaden
Installeer de nieuwste versie van [Visual Studio 2019](https://www.visualstudio.com/vs/) in Windows met de Azure Development-workload.

## <a name="create-a-web-app-running-in-a-container"></a>Een web-app maken die wordt uitgevoerd in een container

In deze sectie maakt u een ASP.NET Core-web-app en voert u deze uit in een container in Kubernetes.

### <a name="create-an-aspnet-web-app"></a>Een ASP.NET-web-app maken

Maak een nieuw project in Visual Studio. Op dit moment moet het project een **ASP.NET Core-webtoepassing** zijn. Geef het project de naam **webfrontend**.

![In het dialoogvenster ‘Nieuw project’ geeft het maken van een C sharp-webtoepassing met de naam 'webfrontend' op locatie C:\Source\Repos weer. In de vervolgkeuzelijst ‘Oplossing’ vindt u de optie ‘Nieuwe oplossing maken’ en het selectievakje ‘Map maken voor oplossing’ is ingeschakeld.](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Selecteer de sjabloon **Web Application (Model-View-Controller)** en selecteer **.NET Core** en **ASP.NET Core 2.0** in de twee vervolgkeuzelijsten boven in het dialoogvenster. Klik op **OK** om het project aan te maken.

![In het dialoogvenster 'NIEUWE A S P dot NET Core Web Application ', geven twee vervolgkeuzelijsten ' dot NET Core' en 'A S P dot NET Core 2 point 0 ' weer. De sjabloon webtoepassing (Model-Weergave-Controller) is geselecteerd in een matrix met de knoppen van projectsjablonen onder de keuzelijsten. Het selectievakje 'Ondersteuning voor docker inschakelen' is niet ingeschakeld.](media/get-started-netcore-visualstudio/NewProjectDialog2.png)

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Dev Spaces inschakelen voor een AKS-cluster

Met het project dat u zojuist hebt gemaakt, selecteert u **Azure Dev Spaces** in de vervolgkeuzelijst met opstartinstellingen, zoals hieronder wordt weergegeven.

![De vervolgkeuzelijst bevindt zich bovenaan een venster met de naam Microsoft Visual Studio Int Preview. 'Azure Dev Spaces' is geselecteerd.](media/get-started-netcore-visualstudio/LaunchSettings.png)

Controleer in het dialoogvenster dat vervolgens wordt weergegeven, of u bent aangemeld bij het juiste account, en selecteer vervolgens een bestaand Kubernetes-cluster.

![Het dialoogvenster Azure Dev Spaces bevat deze vakken: ‘Abonnement,’ ‘Azure Kubernetes Service-cluster’ en ‘Ruimte’.](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

Laat de waarde in de vervolgkeuzelijst **Space** voorlopig op `default` staan. Later besteden we meer aandacht aan deze optie. Schakel het selectievakje **Publicly Accessible** in zodat de web-app toegankelijk is via een openbaar eindpunt. Deze instelling is niet vereist, maar is wel handig om verderop in dit scenario enkele dingen te demonstreren. Maar u hoeft u geen zorgen te maken, want in beide gevallen kunt u fouten op de website opsporen met Visual Studio.

![Het selectievakje Openbaar toegankelijk is ingeschakeld.](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Klik op **OK** om het cluster te selecteren of een cluster te maken.

Als u een cluster kiest dat niet is ingeschakeld om te werken met Azure Dev Spaces, ziet u een bericht waarin u wordt gevraagd of u dit cluster wilt configureren.

![Het bericht geeft het volgende weer: ‘Azure Dev Spaces-resource toevoegen? Het geselecteerde A K S-cluster moet worden geconfigureerd voor het gebruik van Azure Dev Spaces voordat het kan worden gebruikt. Wilt u dit doen?’ U kunt kiezen uit de knopen ‘O K’ en ‘Annuleren’.](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Kies **OK**.

> [!IMPORTANT]
> Met het Azure Dev Spaces-configuratieproces wordt de eventueel aanwezige `azds`-naamruimte in het cluster verwijderd.

 Er wordt een achtergrondtaak gestart om dit te doen. Het uitvoeren van deze taak duurt enkele minuten. Als u wilt zien of de taak nog steeds actief is, plaatst u de muisaanwijzer op het pictogram **Background tasks** in de linkerhoek onder in de statusbalk, zoals wordt weergegeven in de volgende afbeelding.

![In het pop-upvenster dat wordt weergegeven bij de muisaanwijzer ziet u 'Mijn A K S maken' in de resourcegroep.'](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!NOTE]
> U kunt pas fouten in een toepassing oplossen wanneer de ontwikkelomgeving is gemaakt.

### <a name="look-at-the-files-added-to-project"></a>Bestanden bekijken die zijn toegevoegd aan het project
Terwijl u wacht totdat de ontwikkelomgeving is gemaakt, kunt u bekijken welke bestanden aan het project zijn toegevoegd toen u ervoor koos om een ontwikkelomgeving te gebruiken.

Allereerst ziet u dat een map met de naam `charts` is toegevoegd, en dat er binnen deze map een [Helm-grafiek](https://docs.helm.sh) voor de toepassing is klaargezet. Deze bestanden worden gebruikt om de toepassing in de ontwikkelomgeving te implementeren.

U ziet dat er een bestand met de naam `Dockerfile` is toegevoegd. Dit bestand bevat gegevens die nodig zijn om van de toepassing een pakket te maken met de standaard-Docker-indeling.

Tot slot ziet u een bestand met de naam `azds.yaml`. Dit bestand bevat configuratie over de ontwikkeltijd die de ontwikkelomgeving nodig heeft.

![Het bestand ' z d s dot yaml ' wordt weergegeven in de oplossing webfrontend in het venster Solution Explorer.](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Fouten opsporen in een Kubernetes-container
Zodra de ontwikkelomgeving is gemaakt, kunt u fouten oplossen voor de toepassing. Stel een onderbrekingspunt in de code in, bijvoorbeeld op regel 20 in het bestand `HomeController.cs`, waarbij de variabele `Message` wordt ingesteld. Druk op **F5** om de foutopsporing te starten. 

Visual Studio communiceert met de ontwikkelomgeving om de toepassing te compileren en te implementeren. Vervolgens wordt een browser geopend met de web-app. Het lijkt misschien alsof de container lokaal wordt uitgevoerd, maar dat is niet zo. De container wordt uitgevoerd in de ontwikkelomgeving in Azure. De reden voor het localhost-adres is dat Azure Dev Spaces een tijdelijke SSH-tunnel maakt naar de container die wordt uitgevoerd in AKS.

Klik boven aan de pagina op de koppeling **About** om het onderbrekingspunt te activeren. U hebt volledige toegang tot foutopsporingsgegevens (net als wanneer de code lokaal wordt uitgevoerd), zoals als de aanroep-stack, lokale variabelen, informatie over uitzonderingen, enzovoort.

## <a name="iteratively-develop-code"></a>Code iteratief ontwikkelen

Azure Dev Spaces draait niet alleen om het ophalen van code die wordt uitgevoerd in Kubernetes. Het gaat er om dat u de codewijzigingen snel en iteratief toegepast kunt zien in een Kubernetes-omgeving in de cloud.

### <a name="update-a-content-file"></a>Een inhoudsbestand bijwerken


1. Zoek het bestand `./Views/Home/Index.cshtml` en bewerk de HTML-code. Wijzig bijvoorbeeld [regel 73 waar `<h2>Application uses</h2>`](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Views/Home/Index.cshtml#L73) staat in iets als: 
  
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

Meer informatie over hoe Azure Dev Spaces werkt.

> [!div class="nextstepaction"]
> [Hoe Azure Dev Spaces werkt](how-dev-spaces-works.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
