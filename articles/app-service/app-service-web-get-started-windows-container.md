---
title: 'Snelstart: Windows-container (voorbeeld)'
description: Implementeer uw eerste aangepaste Windows-container in Azure App Service. Profiteer van containerisatie, pas de Windows-container aan zoals u dat wilt.
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: cd6b78e5fd824cc013cc946d23677237923f485e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047107"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Een aangepaste Windows-container uitvoeren in Azure (Preview)

[Azure App Service](overview.md) biedt vooraf gedefinieerde toepassingsstacks in Windows, zoals ASP.NET of Node.js, die worden uitgevoerd in IIS. De vooraf geconfigureerde Windows-omgeving vergrendelt het besturingssysteem van administratieve toegang, software-installaties, wijzigingen in de wereldwijde assemblagecache, enzovoort. Zie [Functie van het besturingssysteem op Azure App Service](operating-system-functionality.md)voor meer informatie. Als voor uw toepassing meer toegang is vereist dan is toegestaan in de vooraf geconfigureerde omgeving, kunt u in plaats hiervan een aangepaste Windows-container implementeren.

Met deze quickstart ziet u hoe u een ASP.NET-app in een Windows-afbeelding implementeert naar [Docker Hub](https://hub.docker.com/) vanuit Visual Studio. U voert de app uit in een aangepaste container in Azure App Service.

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

- <a href="https://hub.docker.com/" target="_blank">Registreren voor een Docker Hub-account</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Docker voor Windows installeren</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Docker instellen voor het uitvoeren van Windows-containers</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Installeer Visual Studio 2019</a> met de **ASP.NET- en webontwikkeling-** en **Azure-ontwikkelingsworkloads.** Als u Visual Studio 2019 al hebt geïnstalleerd:

    - Installeer de nieuwste updates in Visual Studio door > **Help-controle op updates te**selecteren. **Help**
    - Voeg de workloads toe in Visual Studio door Hulpmiddelen voor **extra's** > **en functies te**selecteren.

## <a name="create-an-aspnet-web-app"></a>Een ASP.NET-web-app maken

Maak een ASP.NET web-app door de volgende stappen te volgen:

1. Open Visual Studio en selecteer **Vervolgens Een nieuw project maken**.

1. Zoek en kies in **Een nieuw project maken**ASP.NET **Webapplication (.NET Framework)** voor C#en selecteer **Volgende**.

1. Geef in **Uw nieuwe project configureren**de naam van de toepassing _myFirstAzureWebApp_en selecteer **Vervolgens Maken**.

   ![Uw web-app-project configureren](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. U kunt elk type ASP.NET-web-app implementeren in Azure. Kies voor deze quickstart de **MVC-sjabloon.**

1. Selecteer **Docker-ondersteuning**en controleer of verificatie is ingesteld op **Geen verificatie**. Selecteer **Maken**.

   ![ASP.NET webtoepassing maken](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. Als het bestand _Dockerfile_ niet automatisch wordt geopend, opent u het vanuit **Solution Explorer**.

1. U hebt een [ondersteund bovenliggendbeeld](#use-a-different-parent-image)nodig. Wijzig de bovenliggende installatiekopie door de regel `FROM` ​​te vervangen door de volgende code en het bestand op te slaan:

   ```Dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. Selecteer in het menu Visual Studio de optie **Foutopsporing** > **zonder foutopsporing** om de web-app lokaal uit te voeren.

   ![De app lokaal uitvoeren](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publiceren naar Docker Hub

1. Klik in **Solution Explorer**met de rechtermuisknop op het **myFirstAzureWebApp-project** en selecteer **Publiceren**.

1. Kies **App-service** en selecteer **Vervolgens Publiceren**.

1. Selecteer in Een **publicatiedoel**kiezen de optie **Containerregister** en **Docker Hub**en klik op **Publiceren**.

   ![Publiceren vanaf de projectoverzichtspagina](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Lever uw Docker Hub-accountreferenties op en selecteer **Opslaan**.

   Wacht totdat de installatie is voltooid. Op **de pagina Publiceren** wordt nu de naam van de opslagplaats weergegeven die later moet worden gebruikt.

   ![Publiceren vanaf de projectoverzichtspagina](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Kopieer deze naam van de opslagplaats later gebruik.

## <a name="create-a-windows-container-app"></a>Een Windows-container-app maken

1. Meld u aan bij [Azure Portal]( https://portal.azure.com).

1. Kies in de linkerbovenhoek van Azure Portal **Een resource maken**.

1. Zoek in het zoekvak boven de lijst met Azure Marketplace-bronnen naar **Web App voor containers**en selecteer **Maken**.

1. Kies **in Web App Maken**uw abonnement en een **resourcegroep**. U indien nodig een nieuwe resourcegroep maken.

1. Geef een app-naam op, zoals *win-container-demo* en kies **Windows** voor **het besturingssysteem.** Selecteer **Volgende: Docker** om door te gaan.

   ![Een web-app voor containers maken](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. Kies **Docker** **Hub** en voor Afbeelding en tag voor Afbeelding **en tag,** voer de repository-naam in die u hebt gekopieerd in [Publish naar Docker Hub.](#publish-to-docker-hub)

   ![Configureren dat u een web-app voor containers bent](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Als u elders een aangepaste installatiekopie voor de webtoepassing hebt, bijvoorbeeld in [Azure Container Registry](/azure/container-registry/) of in een andere privéopslagplaats, kunt u deze hier configureren.

1. Selecteer **Controleren en maken** en vervolgens **Maken** en wachten tot Azure de vereiste resources maakt.

## <a name="browse-to-the-container-app"></a>Naar de container-app bladeren

Als de bewerking in Azure is voltooid, wordt er een melding weergegeven.

![Implementatie geslaagd](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Klik op **Ga naar resource**.

1. Volg in het overzicht van deze bron de koppeling naast **URL.**

Er wordt een nieuwe browserpagina geopend op de volgende pagina:

![Windows Container App starten](media/app-service-web-get-started-windows-container/app-starting.png)

Wacht enkele minuten en probeer het opnieuw totdat u de startpagina van ASP.NET ziet:

![Windows Container App wordt uitgevoerd](media/app-service-web-get-started-windows-container/app-running-vs.png)

**Gefeliciteerd!** U voert nu uw eerste aangepaste Windows-container uit in Azure App Service.

## <a name="see-container-start-up-logs"></a>Logboeken voor opstarten van containers bekijken

Het kan enige tijd duren voordat de Windows-container is geladen. Als u de voortgang wilt zien, navigeert u naar de volgende URL door * \<app_name>* te vervangen door de naam van uw app.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

De gestreamde logboeken zien er ongeveer als volgt uit:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Lokaal bijwerken en opnieuw implementeren

1. Open in Visual Studio in **Solution Explorer**De **weergave** > **Home** > **homeindex.cshtml**.

1. Zoek ergens bovenaan de HTML-tag `<div class="jumbotron">` en vervang het volledige element door de volgende code:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren in Azure, klikt u met de rechtermuisknop op het **myFirstAzureWebApp-project** in **Solution Explorer** en kiest u **Publiceren**.

1. Selecteer op de pagina Publiceren de knop **Publiceren** en wacht tot het publiceren is voltooit.

1. Als u wilt dat App Service de nieuwe installatiekopie ophaalt uit Docker Hub, moet u de app opnieuw starten. Klik op De app-pagina in de portal op**Ja opnieuw** **starten.** > 

   ![Web-app opnieuw starten in Azure](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

[Blader opnieuw naar de container-app](#browse-to-the-container-app). Nadat de webpagina is vernieuwd, moet de app eerst teruggaan naar de pagina 'Starting up' en dan na een paar minuten opnieuw de bijgewerkte webpagina weergeven.

![Bijgewerkte web-app in Azure](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Een andere bovenliggende installatiekopie gebruiken

U een andere aangepaste Docker-afbeelding gebruiken om uw app uit te voeren. U moet echter de juiste [bovenliggende afbeelding (basisafbeelding)](https://docs.docker.com/develop/develop-images/baseimages/) kiezen voor het gewenste kader:

- Als u .NET Framework-apps wilt implementeren, gebruikt u een bovenliggende afbeelding op basis van de [LTSC-release (Long-Term Servicing Channel) (Windows](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) Server Core 2019). 
- Als u .NET Core-apps wilt implementeren, gebruikt u een bovenliggende afbeelding op basis van de [sac-versie (Semi-Annual Servicing Channel) van](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) Windows Server Nano 1809. 

Het duurt enige tijd om een bovenliggende installatiekopie te downloaden tijdens het opstarten van de app. U kunt deze opstarttijd echter verminderen door een van de volgende bovenliggende installatiekopieën te gebruiken die al in cache zijn opgeslagen in Azure App Service:

- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.7.2-windowsservercore-ltsc2019
- [mcr.microsoft.com/windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/):1809 - deze afbeelding is de basiscontainer die wordt gebruikt in Microsoft [ASP.NET Core](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) Microsoft Windows Nano Server-afbeeldingen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Migreren naar Windows-container in Azure](app-service-web-tutorial-windows-containers-custom-fonts.md)
