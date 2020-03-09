---
title: 'Snelstartgids: Windows-container (preview-versie)'
description: Implementeer uw eerste aangepaste Windows-container naar Azure App Service. Profiteer van container opslag en pas de Windows-container aan zoals u dat wilt.
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: seodec18
ms.openlocfilehash: 7901498772b8e746fb2c87a5237f06ab279e3b64
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374012"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Een aangepaste Windows-container uitvoeren in Azure (Preview)

[Azure App Service](overview.md) biedt vooraf gedefinieerde toepassingsstacks in Windows, zoals ASP.NET of Node.js, die worden uitgevoerd in IIS. De vooraf geconfigureerde Windows-omgeving blokkeert het besturings systeem van beheerders toegang, software-installaties, wijzigingen in de Global Assembly Cache, enzovoort. Zie de [functionaliteit van het besturings systeem op Azure app service](operating-system-functionality.md)voor meer informatie. Als voor uw toepassing meer toegang is vereist dan is toegestaan in de vooraf geconfigureerde omgeving, kunt u in plaats hiervan een aangepaste Windows-container implementeren.

In deze Quick start ziet u hoe u een ASP.NET-app, in een Windows-installatie kopie, implementeert op [docker hub](https://hub.docker.com/) vanuit Visual Studio. U voert de app uit in een aangepaste container in Azure App Service.

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- <a href="https://hub.docker.com/" target="_blank">Registreren voor een Docker Hub-account</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Installeer Docker voor Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Docker instellen voor het uitvoeren van Windows-containers</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Installeer Visual Studio 2019</a> met de **ASP.net-en Web Development** -en **Azure-ontwikkel** werkbelastingen. Als u Visual Studio 2019 al hebt geïnstalleerd:

    - Installeer de meest recente updates in Visual Studio door **Help** te selecteren > **op updates te controleren**.
    - Voeg de werk belastingen in Visual Studio toe door **extra** > **Hulpprogram Ma's en functies ophalen**te selecteren.

## <a name="create-an-aspnet-web-app"></a>Een ASP.NET-web-app maken

Maak een ASP.NET-Web-app door de volgende stappen uit te voeren:

1. Open Visual Studio en selecteer vervolgens **een nieuw project maken**.

1. In **een nieuw project maken**, zoek en kies **ASP.net Web Application (.NET Framework)** voor C#en selecteer vervolgens **volgende**.

1. Geef in **uw nieuwe project een**naam voor de toepassing _myFirstAzureWebApp_en selecteer vervolgens **maken**.

   ![Uw web-app-project configureren](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. U kunt elk type ASP.NET-web-app implementeren in Azure. Kies voor deze Quick Start de **MVC** -sjabloon.

1. Selecteer **docker-ondersteuning**en zorg ervoor dat verificatie is ingesteld op **geen verificatie**. Selecteer **Maken**.

   ![ASP.NET-webtoepassing maken](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. Als het bestand _Dockerfile_ niet automatisch wordt geopend, opent u het vanuit **Solution Explorer**.

1. U hebt een [ondersteunde bovenliggende installatie kopie](#use-a-different-parent-image)nodig. Wijzig de bovenliggende installatiekopie door de regel `FROM` ​​te vervangen door de volgende code en het bestand op te slaan:

   ```Dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. Selecteer in het menu van Visual Studio **fout opsporing** > **zonder fout opsporing starten** om de web-app lokaal uit te voeren.

   ![De app lokaal uitvoeren](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publiceren naar Docker Hub

1. Klik in **Solution Explorer**met de rechter muisknop op het project **MyFirstAzureWebApp** en selecteer **publiceren**.

1. Kies **app service** en selecteer vervolgens **publiceren**.

1. Selecteer in een **publicatie doel**kiezen de optie **container Registry** en **docker hub**en klik vervolgens op **publiceren**.

   ![Publiceren vanaf de projectoverzichtspagina](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Geef de referenties van uw docker hub-account op en selecteer **Opslaan**.

   Wacht totdat de installatie is voltooid. Op de pagina **publiceren** wordt nu de naam van de opslag plaats weer gegeven die u later kunt gebruiken.

   ![Publiceren vanaf de projectoverzichtspagina](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Kopieer deze naam van de opslagplaats later gebruik.

## <a name="create-a-windows-container-app"></a>Een Windows-container-app maken

1. Meld u aan bij de [Azure-portal]( https://portal.azure.com).

1. Kies in de linkerbovenhoek van Azure Portal **Een resource maken**.

1. Zoek in het zoekvak boven de lijst met resources van Azure Marketplace naar **Web App for containers**en selecteer **maken**.

1. Kies uw abonnement en een **resource groep**in **Web-app maken**. U kunt zo nodig een nieuwe resource groep maken.

1. Geef een naam op voor de app, zoals *Win-container-demo* , en kies **Windows** voor het **besturings systeem**. Selecteer **volgende: docker** om door te gaan.

   ![Een Web App for Containers maken](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. Voor **installatie kopie bron**kiest u **docker hub** en voor **afbeelding en label**voert u de naam in van de opslag plaats die u hebt gekopieerd in [publiceren naar docker hub](#publish-to-docker-hub).

   ![Configureer een Web App for Containers](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Als u elders een aangepaste installatiekopie voor de webtoepassing hebt, bijvoorbeeld in [Azure Container Registry](/azure/container-registry/) of in een andere privéopslagplaats, kunt u deze hier configureren.

1. Selecteer **controleren en maken** **en wacht** tot Azure de vereiste resources heeft gemaakt.

## <a name="browse-to-the-container-app"></a>Naar de container-app bladeren

Als de bewerking in Azure is voltooid, wordt er een melding weergegeven.

![Implementatie is voltooid](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Klik op **Ga naar resource**.

1. In het overzicht van deze resource volgt u de koppeling naast **URL**.

Er wordt een nieuwe browser pagina geopend op de volgende pagina:

![Windows-container-app wordt gestart](media/app-service-web-get-started-windows-container/app-starting.png)

Wacht enkele minuten en probeer het opnieuw totdat u de startpagina van ASP.NET ziet:

![Windows-container-app wordt uitgevoerd](media/app-service-web-get-started-windows-container/app-running-vs.png)

**Gefeliciteerd!** U voert nu uw eerste aangepaste Windows-container uit in Azure App Service.

## <a name="see-container-start-up-logs"></a>Logboeken voor opstarten van containers bekijken

Het kan enige tijd duren voordat de Windows-container is geladen. Als u de voortgang wilt bekijken, gaat u naar de volgende URL en vervangt u *\<app_name>* door de naam van de app.
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

1. Open in Visual Studio in **Solution Explorer** **weer gaven** > **Start** > **index. cshtml**.

1. Zoek ergens bovenaan de HTML-tag `<div class="jumbotron">` en vervang het volledige element door de volgende code:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren naar Azure, klikt u met de rechter muisknop op het **myFirstAzureWebApp** -project in **Solution Explorer** en kiest u **publiceren**.

1. Selecteer op de pagina Publiceren de knop **Publiceren** en wacht tot het publiceren is voltooit.

1. Als u wilt dat App Service de nieuwe installatiekopie ophaalt uit Docker Hub, moet u de app opnieuw starten. Klik als u terugbent in de app-pagina in de portal op **Opnieuw starten** > **Ja**.

   ![Web-app opnieuw starten in Azure](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

[Blader opnieuw naar de container-app](#browse-to-the-container-app). Nadat de webpagina is vernieuwd, moet de app eerst teruggaan naar de pagina 'Starting up' en dan na een paar minuten opnieuw de bijgewerkte webpagina weergeven.

![Bijgewerkte web-app in Azure](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Een andere bovenliggende installatiekopie gebruiken

U kunt een andere aangepaste docker-installatie kopie gebruiken om uw app uit te voeren. U moet echter de juiste [bovenliggende installatie kopie (basis installatie kopie)](https://docs.docker.com/develop/develop-images/baseimages/) kiezen voor het gewenste Framework:

- Als u .NET Framework-Apps wilt implementeren, gebruikt u een bovenliggende installatie kopie op basis van de versie van het Windows Server Core 2019 [-LTSC (Long-term Servicing Channel)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) . 
- Als u .NET Core-Apps wilt implementeren, gebruikt u een bovenliggende installatie kopie op basis van de versie van het Windows Server nano 1809 [Semi-Annual-onderhouds kanaal (SAC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) . 

Het duurt enige tijd om een bovenliggende installatiekopie te downloaden tijdens het opstarten van de app. U kunt deze opstarttijd echter verminderen door een van de volgende bovenliggende installatiekopieën te gebruiken die al in cache zijn opgeslagen in Azure App Service:

- [MCR.Microsoft.com/DOTNET/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4.7.2-windowsservercore-ltsc2019
- [MCR.Microsoft.com/Windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/): 1809-deze installatie kopie is de basis container die wordt gebruikt in micro soft [ASP.net core](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) micro soft Windows nano server-installatie kopieën.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Migreren naar Windows-container in Azure](app-service-web-tutorial-windows-containers-custom-fonts.md)
