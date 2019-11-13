---
title: Snel een .NET-app maken op Service Fabric in azure
description: In deze snelstartgids maakt u een .NET-toepassing voor Azure met behulp van de voorbeeldtoepassing van de betrouwbare Service Fabric-services.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: azure-vs
ms.date: 06/26/2019
ms.author: mikhegn
ms.custom: mvc, devcenter, vs-azure
ms.openlocfilehash: dfc8890d58763bd193f6a0b4137fc972d26beb9f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013221"
---
# <a name="quickstart-deploy-a-net-reliable-services-application-to-service-fabric"></a>Snelstart: Een .NET Reliable Services-toepassing maken voor Service Fabric

Azure Service Fabric is een platform voor gedistribueerde systemen waarmee u schaalbare en betrouwbare microservices en containers implementeert en beheert.

Deze snelstartgids laat zien hoe u uw eerste .NET-toepassing in Service Fabric implementeert. Wanneer u klaar bent, hebt u een stemtoepassing met een ASP.NET Core-web-front-end die stemresultaten opslaat in een stateful back-endservice in het cluster.

![Schermafbeelding van de toepassing](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

Met behulp van deze toepassing leert u hoe u:

* een toepassing kunt maken met .Net en Service Fabric
* ASP.NET core gebruiken als een Webfront-end
* Toepassingsgegevens in een stateful service opslaan
* Lokaal problemen met uw toepassing oplossen
* De toepassing uitschalen over meerdere knooppunten
* Een rolling upgrade op een toepassingen uitvoeren

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstartgids:

1. [Installeer Visual Studio 2019](https://www.visualstudio.com/) met de **Azure development** -en **ASP.net-en Web Development** -workloads.
2. [Git installeren](https://git-scm.com/)
3. [Microsoft Azure Service Fabric SDK installeren](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. Voer de volgende opdracht uit zodat Visual Studio in staat is om implementaties naar het lokale Service Fabric-cluster uit te voeren:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
   ```
    
## <a name="build-a-cluster"></a>Een cluster bouwen

Als u de runtime, SDK's en hulpprogramma's van Visual Studio Docker hebt geïnstalleerd en Docker wordt uitgevoerd, maakt u een lokaal ontwikkelcluster met vijf knooppunten.

> [!Note]
> De reden voor het uitvoeren van Docker bij het maken van het cluster is dat het cluster zo wordt gemaakt met de containerfuncties ingeschakeld. Als Docker niet actief is, moet u het cluster opnieuw maken om containerfuncties in te schakelen.
> Hoewel het niet nodig is voor deze quickstart, zijn de instructies voor het uitvoeren van Docker bij het maken van het cluster opgenomen als best practice.
> Test of Docker wordt uitgevoerd door een terminalvenster te openen en `docker ps` uit te voeren om te zien of er een fout optreedt. Als er geen fout wordt aangegeven, wordt Docker uitgevoerd en kunt u een cluster gaan bouwen.

1. Open als beheerder een nieuw PowerShell-venster met verhoogde bevoegdheid.
2. Voer de volgende PowerShell-opdracht uit om een ontwikkelcluster te maken:

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
   ```
3. Voer de volgende opdracht uit om het lokale hulpprogramma voor clusterbeheer te starten:

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
   ```

>[!NOTE]
> De voorbeeldtoepassing in deze snelstartgids maakt gebruik van functies die niet beschikbaar zijn in Windows 7.
>

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voer in een opdrachtvenster de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen op de lokale computer.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>De toepassing lokaal uitvoeren

Klik met de rechtermuisknop op het pictogram van Visual Studio in het menu Start en kies **Als administrator uitvoeren**. Als u het fout opsporingsprogramma aan uw services wilt koppelen, moet u Visual Studio als beheerder uitvoeren.

Open de oplossing **Voting.sln** van Visual Studio vanuit de opslagplaats die u hebt gekloond.

De toepassing Voting luistert standaard op poort 8080.  De poort van de toepassing is ingesteld in het bestand */VotingWeb/PackageRoot/ServiceManifest.xml*.  U kunt de poort van de toepassing wijzigen door het kenmerk**Port** van het element **Endpoint** bij te werken.  Als u de toepassing lokaal wilt implementeren en uitvoeren, moet de poort van de toepassing open zijn en beschikbaar zijn op uw computer.  Als u de toepassings poort wijzigt, vervangt u de nieuwe toepassings poort waarde voor "8080" in dit artikel.

Druk op **F5** om de toepassing te implementeren.

> [!NOTE]
> In het uitvoervenster van Visual Studio ziet u het bericht 'De URL van de toepassing is niet ingesteld of is geen HTTP/HTTPS-URL zodat de browser niet door de toepassing kan worden geopend'.  Dit bericht betekent niet dat er een fout is opgetreden, maar dat een browser niet automatisch kan worden gestart.

Wanneer de implementatie is voltooid, start u een browser en opent u `http://localhost:8080` om het webfront-end van de toepassing weer te geven.

![Front-end van toepassing](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

U kunt nu een reeks stemmingsopties toevoegen en beginnen met het verzamelen van stemmen. De toepassing wordt uitgevoerd en alle gegevens worden opgeslagen in het Service Fabric-cluster, zonder dat hiervoor een aparte database nodig is.

## <a name="walk-through-the-voting-sample-application"></a>Stapsgewijs door de voorbeeldstemtoepassing gaan

De stemtoepassing bestaat uit twee services:

* Web-front-endservice (VotingWeb): een ASP.NET Core web-front-endservice die de webpagina ondersteunt en web-API's beschikbaar stelt om te communiceren met de back-endservice.
* Back-endservice (VotingData): een ASP.NET Core-webservice, die een API beschikbaar stelt om de stemresultaten in een betrouwbare woordenlijst op schijf op te slaan.

![Diagram van de toepassing](./media/service-fabric-quickstart-dotnet/application-diagram.png)

Wanneer u in de toepassing stemt, vinden de volgende gebeurtenissen plaats:

1. Een JavaScript verzendt de stemaanvraag als een HTTP PUT-aanvraag naar de web-API in de web-front-endservice.

2. De web-front-endservice gebruikt een proxyserver om een HTTP PUT-aanvraag te vinden en door te sturen naar de back-endservice.

3. De back-endservice neemt de binnenkomende aanvraag aan en slaat het bijgewerkte resultaat op in een betrouwbare woordenlijst die wordt gerepliceerd naar meerdere knooppunten binnen het cluster en wordt opgeslagen op schijf. Gegevens van de toepassing worden opgeslagen in het cluster, zodat er geen database nodig is.

## <a name="debug-in-visual-studio"></a>Fouten opsporen met Visual Studio

De toepassing zou zonder problemen moeten worden uitgevoerd, maar u kunt het foutopsporingsprogramma gebruiken om te zien hoe belangrijke onderdelen van de toepassing werken. Wanneer u fouten opspoort in de toepassing in Visual Studio, gebruikt u een lokaal Service Fabric-ontwikkelings cluster. U kunt de fout opsporingsgegevens aanpassen aan uw scenario. In deze toepassing worden gegevens met behulp van een betrouwbare woordenlijst opgeslagen in de back-endservice. Visual Studio verwijdert standaard de toepassing wanneer u het foutopsporingsprogramma stopt. Door de toepassing te verwijderen, worden de gegevens in de back-endservice ook verwijderd. Als u de gegevens tussen de foutopsporingssessies wilt kunnen behouden, kunt u de **foutopsporingsmodus van de toepassing** als eigenschap op het **Voting**-project in Visual Studio wijzigen.

Als u wilt zien wat er in de code gebeurt, moet u de volgende stappen uitvoeren:

1. Open het bestand **/VotingWeb/Controllers/VotesController.cs** en stel een onderbrekingspunt in de methode **Put** van de web-API (regel 69) in. U kunt in Solution Explorer in Visual Studio naar het bestand zoeken.

2. Open het bestand **/VotingData/Controllers/VoteDataController.cs** en stel een onderbrekingspunt in de methode **Put** van deze web-API (regel 54) in.

3. Ga terug naar de browser en klik op een stemoptie of voeg een nieuwe stemoptie toe. U komt uit bij het eerste onderbrekingspunt in de API-controller van de web-front-end.
   * In deze stap wordt de Java script in de browser een aanvraag naar de Web-API-controller in de front-end-service verzonden.

     ![Front-endservice van Vote toevoegen](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

   * Stel eerst de URL van de ReverseProxy samen voor onze back-endservice **(1)** .
   * Verzend vervolgens de HTTP PUT-aanvraag naar de ReverseProxy **(2)** .
   * Tot slot wordt het antwoord van de back-endservice naar de client geretourneerd **(3)** .

4. Druk op **F5** om door te gaan
   - Als u hierom wordt gevraagd door de browser, verleent u ServiceFabricAllowedUsers rechten voor het lezen en uitvoeren van groepen voor de foutopsporingsmodus.
   - U bevindt zich nu op het onderbreek punt in de back-end-service.

     ![Back-endservice Vote toevoegen](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

   - In de eerste regel in de methode **(1)** wordt door de `StateManager` een betrouwbare woordenlijst met de naam `counts` aangeroepen of toegevoegd.
   - Alle interacties met waarden in een betrouwbare woordenlijst vereisen een transactie, met deze instructie **(2)** wordt die transactie gemaakt.
   - Werk in de transactie de waarde bij van de relevante sleutel voor de stemoptie en voer de bewerking **(3)** door. Zodra de doorvoermethode resultaten retourneert, worden de gegevens bijgewerkt in de dictionary en gerepliceerd naar andere knooppunten in het cluster. De gegevens worden nu veilig opgeslagen in het cluster en de back-endservice kan een failover-overschakeling uitvoeren naar andere knooppunten, waarbij de gegevens beschikbaar blijven.
5. Druk op **F5** om door te gaan

Als u de foutopsporingssessie wilt stoppen, drukt u op **Shift+F5**.

## <a name="perform-a-rolling-application-upgrade"></a>Een rolling upgrade op een toepassingen uitvoeren

Service Fabric voert de implementatie van nieuwe updates voor uw toepassing op een veilige manier uit. Rolling upgrades zorgen dat er geen downtime is als de upgrades worden bijgewerkt of als deze worden teruggedraaid omdat er zich fouten hebben voorgedaan.

Als u de toepassing wilt upgraden, gaat u als volgt te werk:

1. Open in Visual Studio het bestand **/VotingWeb/Views/Home/Index.cshtml**.
2. Wijzig de kop van de pagina door tekst toe te voegen of bij te werken. Wijzig de kop bijvoorbeeld in 'Voorbeeld van Service Fabric Voting v2'.
3. Sla het bestand op.
4. Klik met de rechtermuisknop op **Voting** in Solution Explorer en kies **Publiceren**. Het dialoogvenster Publiceren wordt weergegeven.
5. Klik op de knop **Manifestversie** om de versie van de service en de toepassing te wijzigen.
6. Wijzig de versie van het **code-element** onder **VotingWebPkg** in bijvoorbeeld '2.0.0' en klik op **Opslaan**.

    ![Het dialoogvenster Versie wijzigen](./media/service-fabric-quickstart-dotnet/change-version.png)
7. Schakel in het dialoogvenster **Service Fabric-toepassing publiceren** het selectievakje **De toepassing bijwerken** in.
8.  Wijzig **Doelprofiel** in **PublishProfiles\Local.5Node.xml** en zorg ervoor dat **Verbindingseindpunt** is ingesteld op **Lokaal cluster**. 
9. Selecteer **De toepassing bijwerken**.

    ![Upgrade-instelling in het dialoogvenster Publiceren](./media/service-fabric-quickstart-dotnet/upgrade-app.png)

10. Klik op **Publish**.

    U kunt de toepassing blijven gebruiken terwijl de upgrade wordt uitgevoerd. Omdat er twee exemplaren van de service in het cluster worden uitgevoerd, wordt als reactie op een aantal van uw aanvragen mogelijk een bijgewerkte versie van de toepassing verstrekt, terwijl op basis van andere aanvragen mogelijk nog steeds de oude versie wordt verstrekt.

11. Open uw browser en blader naar het adres van het cluster op poort 19080. Bijvoorbeeld `http://localhost:19080/`.
12. Klik in de structuurweergave op het knooppunt **Toepassingen** en vervolgens op **Upgrades worden uitgevoerd** in het rechterdeelvenster. U ziet nu hoe de upgrade wordt uitgevoerd op de upgradedomeinen in uw cluster en dat elk domein wordt bijgewerkt voordat het volgende wordt bijgewerkt. Een upgradedomein wordt in de voortgangsbalk groen weergegeven als de status van het domein is geverifieerd.
    ![Weergave van de upgrade in Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/upgrading.png)

    Service Fabric voert upgrades veilig uit door na het bijwerken van de service op elk knooppunt in het cluster twee minuten te wachten. Houd er rekening mee dat de volledige update ongeveer acht minuten kan duren.

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u de volgende zaken geleerd:

* een toepassing kunt maken met .Net en Service Fabric
* ASP.NET core gebruiken als een Webfront-end
* Toepassingsgegevens in een stateful service opslaan
* Lokaal problemen met uw toepassing oplossen
* De toepassing uitschalen over meerdere knooppunten
* Een rolling upgrade op een toepassingen uitvoeren

Raadpleeg de volgende zelfstudie voor meer informatie over Service Fabric en .NET:
> [!div class="nextstepaction"]
> [.NET application on Service Fabric](service-fabric-tutorial-create-dotnet-app.md) (.NET-toepassing op Service Fabric)
