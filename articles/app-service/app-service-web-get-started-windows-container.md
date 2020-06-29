---
title: 'Snelstartgids: Windows-container (preview)'
description: Voer uw eerste aangepaste Windows-container uit in Azure App Service. Profiteer van containervorming en pas de Windows-container aan uw wensen aan.
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: 44ade8b0b218f028cf6f211e37824ffc43a191ac
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84764074"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Een aangepaste Windows-container uitvoeren in Azure (Preview)

[Azure App Service](overview.md) biedt vooraf gedefinieerde toepassingsstacks in Windows, zoals ASP.NET of Node.js, die worden uitgevoerd in IIS. Met de vooraf geconfigureerde Windows-omgeving wordt het besturingssysteem vergrendeld voor beheerderstoegang, software-installaties, wijzigingen aan de Global Assembly Cache, enzovoort. Zie [Functionaliteit van besturingssystemen in Azure App Service](operating-system-functionality.md) voor meer informatie. Als voor uw toepassing meer toegang is vereist dan is toegestaan in de vooraf geconfigureerde omgeving, kunt u in plaats hiervan een aangepaste Windows-container implementeren.

Deze quickstart laat zien hoe u een ASP.NET-app in een Windows-installatiekopie implementeert in [Docker Hub](https://hub.docker.com/) vanuit Visual Studio. U voert de app uit in een aangepaste container in Azure App Service.

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- <a href="https://hub.docker.com/" target="_blank">Registreren voor een Docker Hub-account</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Docker voor Windows installeren</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Docker instellen voor het uitvoeren van Windows-containers</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 installeren</a> met de workloads **ASP.NET- en webontwikkeling** en **Azure-ontwikkeling**. Als u Visual Studio 2019 al hebt geïnstalleerd:

    - Installeer de nieuwste updates in Visual Studio door **Help** > **Controleren op updates** te selecteren.
    - Voeg de workloads toe in Visual Studio door **Extra** > **Hulpprogramma’s en functies ophalen** te selecteren.

## <a name="create-an-aspnet-web-app"></a>Een ASP.NET-web-app maken

Maak een ASP.NET-web-app door de volgende stappen uit te voeren:

1. Open Visual Studio en selecteer **Een nieuw project maken**.

1. Kies in **Een nieuw project maken** de optie **ASP.NET-webtoepassing (.NET Framework)** voor #C. Selecteer vervolgens **Volgende**.

1. Geef in **Uw nieuwe project configureren** de toepassing de naam _myfirstazurewebapp_ en selecteer **Maken**.

   ![Uw web-app-project configureren](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. U kunt elk type ASP.NET-web-app implementeren in Azure. Kies voor deze quickstart de sjabloon **MVC**.

1. Selecteer **Docker-ondersteuning** en zorg dat verificatie is ingesteld op **Geen verificatie**. Selecteer **Maken**.

   ![ASP.NET-webtoepassing maken](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. Als het bestand _Dockerfile_ niet automatisch wordt geopend, opent u het vanuit **Solution Explorer**.

1. U hebt een [ondersteunde bovenliggende installatiekopie](#use-a-different-parent-image) nodig. Wijzig de bovenliggende installatiekopie door de regel `FROM` ​​te vervangen door de volgende code en het bestand op te slaan:

   ```Dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. Selecteer in het menu van Visual Studio de optie **Foutopsporing** > **Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

   ![De app lokaal uitvoeren](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publiceren naar Docker Hub

1. Klik in **Solution Explorer** met de rechtermuisknop op het project **myfirstazurewebapp** en selecteer **Publiceren**.

1. Kies **App Service** en selecteer vervolgens **Publiceren**.

1. Selecteer in **Een publicatiedoel kiezen** de opties **Container Registry** en **Docker Hub**. Selecteer vervolgens **Publiceren**.

   ![Publiceren vanaf de projectoverzichtspagina](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Geef de referenties voor uw Docker Hub-account op en selecteer **Opslaan**.

   Wacht totdat de installatie is voltooid. Op de pagina **Publiceren** ziet u nu de naam van de opslagplaats die u later gaat gebruiken.

   ![Publiceren vanaf de projectoverzichtspagina](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Kopieer deze naam van de opslagplaats later gebruik.

## <a name="create-a-windows-container-app"></a>Een Windows-container-app maken

1. Meld u aan bij de [Azure-portal]( https://portal.azure.com).

1. Kies in de linkerbovenhoek van Azure Portal **Een resource maken**.

1. Ga in het zoekvak boven de lijst met Azure Marketplace-resources naar **Web App for Containers** en selecteer **Maken**.

1. Kies in **Web-app maken** uw abonnement en een **Resourcegroep**. U kunt een nieuwe resourcegroep maken, indien nodig.

1. Geef een naam op voor de app, zoals *win-container-demo* en kies **Windows** als **Besturingssysteem**. Selecteer **Volgende: Docker** om door te gaan.

   ![Een web-app voor containers maken](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. Kies **Docker Hub** als **Broninstallatiekopie**, en voer voor **Installatiekopie en tag**, de opslagplaatsnaam in die u hebt gekopieerd in [Publiceren in Docker Hub](#publish-to-docker-hub).

   ![Een web-app voor containers configureren](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Als u elders een aangepaste installatiekopie voor de webtoepassing hebt, bijvoorbeeld in [Azure Container Registry](/azure/container-registry/) of in een andere privéopslagplaats, kunt u deze hier configureren.

1. Selecteer **Controleren en maken** en vervolgens **Maken**, en wacht tot de vereiste resources zijn gemaakt in Azure.

## <a name="browse-to-the-container-app"></a>Naar de container-app bladeren

Als de bewerking in Azure is voltooid, wordt er een melding weergegeven.

![Implementatie geslaagd](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Klik op **Ga naar resource**.

1. Klik in het overzicht van deze resource op de koppeling naast **URL**.

Er wordt een nieuwe browserpagina geopend met de volgende pagina:

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

1. Open in Visual Studio, in **Solution Explorer**, achtereenvolgens **Weergaven** > **Start** > **Index.cshtml**.

1. Zoek ergens bovenaan de HTML-tag `<div class="jumbotron">` en vervang het volledige element door de volgende code:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren in Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myfirstazurewebapp**, en kiest u **Publiceren**.

1. Selecteer op de pagina Publiceren de knop **Publiceren** en wacht tot het publiceren is voltooit.

1. Als u wilt dat App Service de nieuwe installatiekopie ophaalt uit Docker Hub, moet u de app opnieuw starten. Klik als u terugbent in de app-pagina in de portal op **Opnieuw starten** > **Ja**.

   ![Web-app opnieuw starten in Azure](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

[Blader opnieuw naar de container-app](#browse-to-the-container-app). Nadat de webpagina is vernieuwd, moet de app eerst teruggaan naar de pagina 'Starting up' en dan na een paar minuten opnieuw de bijgewerkte webpagina weergeven.

![Bijgewerkte web-app in Azure](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Een andere bovenliggende installatiekopie gebruiken

U mag ook een andere aangepaste Docker-installatiekopie gebruiken om de app uit te voeren. Kies echter wel de juiste [bovenliggende installatiekopie (basisinstallatiekopie)](https://docs.docker.com/develop/develop-images/baseimages/) voor het gewenste framework:

- Als u .NET Framework-apps wilt implementeren, gebruikt u een bovenliggende installatiekopie op basis van de release Windows Server Core 2019 [LTSC (Long-Term Servicing Channel)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc). 
- Als u .NET Core-apps wilt implementeren, gebruikt u een bovenliggende installatiekopie op basis van de release Windows Server Nano 1809 [SAC (Semi-Annual Servicing Channel)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel). 

Het duurt enige tijd om een bovenliggende installatiekopie te downloaden tijdens het opstarten van de app. U kunt deze opstarttijd echter verminderen door een van de volgende bovenliggende installatiekopieën te gebruiken die al in cache zijn opgeslagen in Azure App Service:

- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.7.2-windowsservercore-ltsc2019
- [mcr.microsoft.com/windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/):1809 - deze installatiekopie is de basiscontainer die wordt gebruikt in verschillende Microsoft [ASP.NET Core](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) Microsoft Windows Nano Server-installatiekopieën.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Migreren naar Windows-container in Azure](app-service-web-tutorial-windows-containers-custom-fonts.md)
