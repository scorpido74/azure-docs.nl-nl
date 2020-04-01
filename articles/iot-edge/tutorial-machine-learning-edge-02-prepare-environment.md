---
title: 'Zelfstudie: Omgeving instellen - Machine Learning op Azure IoT Edge'
description: 'Zelfstudie: Bereid uw omgeving voor op de ontwikkeling en implementatie van modules voor machine learning aan de rand.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c776d4d6c3973e7c222c9c9adf3e5105f6c84f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79296796"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Zelfstudie: Een omgeving instellen voor machine learning op IoT Edge

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks tot dit artikel bent gekomen, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de serie voor de beste resultaten.

In dit artikel u uw omgeving voorbereiden op ontwikkeling en implementatie. Stel eerst een ontwikkelmachine op met alle gereedschappen die je nodig hebt. Maak vervolgens de benodigde cloudbronnen in Azure.

## <a name="set-up-the-development-vm"></a>De ontwikkel-VM instellen

Deze stap wordt meestal uitgevoerd door een cloudontwikkelaar. Sommige van de software kan ook nuttig zijn voor een data scientist.

We hebben een PowerShell-script gemaakt dat een Azure-virtuele machine maakt met veel van de vereisten die al zijn geconfigureerd. De VM die we maken moet [geneste virtualisatie](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)kunnen verwerken, daarom hebben we gekozen voor een [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) machinegrootte.

De ontwikkeling VM zal worden opgezet met:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker-bureaublad voor Windows](https://www.docker.com/products/docker-desktop)
* [Git voor Windows](https://gitforwindows.org/)
* [Git-referentiebeheer voor Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core-SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS-code-extensies](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

De ontwikkelaar VM is niet strikt noodzakelijk - alle ontwikkeltools kunnen worden uitgevoerd op een lokale machine. We raden echter ten zeerste aan om de VM te gebruiken om een gelijk speelveld te garanderen.

Het duurt ongeveer 30 minuten om de virtuele machine te maken en te configureren.

1. Kloon of download de [Machine Learning en IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) sample repository naar uw lokale computer.

1. Open PowerShell als beheerder en navigeer naar de **\IoTEdgeAndMlSample\DevVM-map** onder de hoofdmap waar u de code hebt gedownload. We verwijzen naar de hoofdmap `srcdir`voor uw bron als .

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   De Map DevVM bevat de bestanden die nodig zijn om een virtuele Azure-machine te maken die geschikt is voor het voltooien van deze zelfstudie.

1. Voer de volgende opdracht uit om de uitvoering van scripts toe te staan. Kies **Ja tot alles** wanneer daarom wordt gevraagd.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Uitvoeren Create-AzureDevVM.ps1.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Geef de volgende informatie op wanneer u daarom wordt gevraagd:

    * **Azure Subscription ID**: Uw abonnements-ID, die kan worden gevonden in [Azure-abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in portal.
    * **Naam resourcegroep:** de naam van een nieuwe of bestaande resourcegroep in Azure.
    * **Locatie:** kies een Azure-locatie waar de virtuele machine wordt gemaakt. Bijvoorbeeld 'West US 2' of 'Noord-Europa'. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor meer informatie.
    * **Gebruikersnaam:** Geef een gedenkwaardige naam op voor het beheerdersaccount voor de VM.
    * **Wachtwoord:** stel een wachtwoord in voor het beheerdersaccount voor de vm.

   Het script wordt enkele minuten uitgevoerd terwijl de volgende stappen worden uitgevoerd:

    1. Installeert de [Azure PowerShell Az-module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0).
    1. Vraagt u zich aan te melden bij Azure.
    1. Bevestigt de informatie voor het maken van uw VM. Druk op **y** of **Enter** om door te gaan.
    1. Hiermee maakt u de resourcegroep als deze niet bestaat.
    1. Implementeert de virtuele machine.
    1. Hiermee schakelt u Hyper-V in op de VM.
    1. Installeert software nodig voor ontwikkeling en kloon de steekproef repository.
    1. Start de VM opnieuw.
    1. Hiermee maakt u een RDP-bestand op uw bureaublad om verbinding te maken met de virtuele machine.

   Als u wordt gevraagd om de naam van de virtuele machine opnieuw op te starten, u de naam uit de scriptuitvoer kopiëren. De uitvoer toont ook het pad naar het RDP-bestand voor verbinding maken met de VM.

### <a name="set-auto-shutdown-schedule"></a>Schema voor automatisch afsluiten instellen

Om u te helpen de kosten te verlagen, is de ontwikkelings-VM gemaakt met een automatisch uitschakelschema dat is ingesteld op 1900 PST. Mogelijk moet u deze instelling bijwerken, afhankelijk van uw locatie en planning. Ga als u het afsluitschema bijwerken:

1. Navigeer in de Azure-portal naar de VM die het script heeft gemaakt.

1. Selecteer In het menu van het linkerdeelvenster onder **Bewerkingen**de optie **Automatisch afsluiten**.

1. Pas de **geplande afsluiting** en **tijdzone** naar wens aan en selecteer **Opslaan**.

## <a name="connect-to-the-development-vm"></a>Verbinding maken met de ontwikkel-VM

Nu dat we een VM hebben gemaakt moeten we klaar zijn met het installeren van de software die nodig is om de tutorial te voltooien.

1. Dubbelklik op het RDP-bestand dat het script op uw bureaublad heeft gemaakt.

1. U krijgt een dialoogvenster te zien waarin staat dat de uitgever van de externe verbinding onbekend is. Dit is acceptabel, dus selecteer **Connect**.

1. Geef het beheerderswachtwoord op dat u hebt opgegeven om de vm te maken en klik op **OK**.

1. U wordt gevraagd het certificaat voor de VM te accepteren. Selecteer **Ja**.

## <a name="install-visual-studio-code-extensions"></a>Visual Studio Code-extensies installeren

Nu u verbinding hebt gemaakt met de ontwikkelingsmachine, voegt u enkele nuttige extensies toe aan Visual Studio Code om de ontwikkelingservaring gemakkelijker te maken.

1. Maak verbinding met de ontwikkel-VM, open een PowerShell-venster en navigeer naar de map **C:\source\IoTEdgeAndMlSample\DevVM.** Deze map is gemaakt door het script dat de VM heeft gemaakt.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Voer de volgende opdracht uit om de uitvoering van scripts toe te staan. Kies **Ja tot alles** wanneer daarom wordt gevraagd.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Voer het script voor Visual Studio Code-extensies uit.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. Het script wordt enkele minuten uitgevoerd bij het installeren van VS-code-extensies:

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>IoT-hub en -opslag instellen

Deze stappen worden meestal uitgevoerd door een cloudontwikkelaar.

Azure IoT Hub is het hart van elke IoT-toepassing omdat het veilige communicatie tussen IoT-apparaten en de cloud verwerkt. Het is het belangrijkste coördinatiepunt voor de werking van de IoT Edge machine learning-oplossing.

* IoT Hub gebruikt routes om binnenkomende gegevens van IoT-apparaten naar andere downstreamservices te leiden. We maken gebruik van IoT Hub-routes om apparaatgegevens naar Azure Storage te verzenden. In Azure Storage worden de apparaatgegevens verbruikt door Azure Machine Learning om onze resterende gebruiksduur (RUL) te trainen.

* Later in de zelfstudie gebruiken we IoT Hub om ons Azure IoT Edge-apparaat te configureren en te beheren.

In deze sectie gebruikt u een script om een Azure IoT-hub en een Azure Storage-account te maken. Vervolgens configureert u in de Azure-portal een route die gegevens die door de hub zijn ontvangen doorstuurt naar een Azure Storage-container. Deze stappen duren ongeveer 10 minuten.

1. Maak verbinding met de ontwikkel-VM, open een PowerShell-venster en navigeer naar de **IoTHub-map.**

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Voer het creatiescript uit. Gebruik dezelfde waarden voor abonnements-ID, locatie en resourcegroep als bij het maken van de ontwikkel-VM.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * U wordt gevraagd zich aan te melden bij Azure.
    * Het script bevestigt de informatie voor het aanmaken van uw Hub- en Opslagaccount. Druk op **y** of **Enter** om door te gaan.

Het uitvoeren van het script duurt ongeveer twee minuten. Zodra het script is voltooid, wordt de naam van de IoT-hub en het opslagaccount uitgevoerd.

## <a name="review-route-to-storage-in-iot-hub"></a>Route naar opslag in IoT Hub bekijken

Als onderdeel van het maken van de IoT-hub heeft het script dat we in de vorige sectie hebben uitgevoerd, ook een aangepast eindpunt en een route gemaakt. IoT Hub-routes bestaan uit een queryexpressie en een eindpunt. Als een bericht overeenkomt met de expressie, worden de gegevens langs de route naar het bijbehorende eindpunt verzonden. Eindpunten kunnen gebeurtenishubs, wachtrijen voor servicebussen en onderwerpen zijn. In dit geval is het eindpunt een blobcontainer in een opslagaccount. Laten we de Azure-portal gebruiken om de route te bekijken die door ons script is gemaakt.

1. Open de [Azure-portal](https://portal.azure.com) en ga naar de resourcegroep die u voor deze zelfstudie gebruikt.

1. Selecteer in de lijst met bronnen de IoT-hub die het script heeft gemaakt. Het heeft een naam die eindigt `IotEdgeAndMlHub-jrujej6de6i7w`met willekeurige tekens zoals .

1. Selecteer **berichtroutering**in het menu van het linkerdeelvenster onder **Berichten**.

1. Selecteer op de pagina **Berichtroutering** het tabblad **Aangepaste eindpunten.**

1. De sectie **Opslag** uitvouwen:

   ![Controleren of turbofanDeviceStorage zich in de lijst met aangepaste eindpunten bevindt](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   We zien **dat turbofanDeviceStorage** in de lijst met aangepaste eindpunten staat. Let op de volgende kenmerken van dit eindpunt:

   * Het wijst naar de blob-opslagcontainer die u hebt gemaakt, zoals `devicedata` aangegeven door **containernaam**.
   * De **bestandsnaamnotatie** heeft partitie als laatste element in de naam. We vinden deze indeling handiger voor de bestandsbewerkingen die we later in de zelfstudie met Azure-notitieblokken zullen uitvoeren.
   * De **status** moet gezond zijn.

1. Selecteer het tabblad **Routes**.

1. Selecteer de route met de naam **turbofanDeviceDataToStorage**.

1. Houd er op de pagina **Routes details** rekening mee dat het eindpunt van de route het eindpunt **van turbofanDeviceStorage** is.

   ![Bekijk de details over de turbofanDeviceDataToStorage-route](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Kijk naar de **routeringsquery**, die is ingesteld op **true**. Deze instelling betekent dat alle telemetrieberichten van het apparaat overeenkomen met deze route; en daarom worden alle berichten naar het **turbofanDeviceStorage** eindpunt gestuurd.

1. Omdat er geen bewerkingen zijn aangebracht, sluit u deze pagina.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een IoT-hub gemaakt en een route naar een Azure Storage-account geconfigureerd. Vervolgens sturen we gegevens van een set gesimuleerde apparaten via de IoT-hub naar het opslagaccount. Later in de zelfstudie, nadat we ons IoT Edge-apparaat en -modules hebben geconfigureerd, zullen we routes opnieuw bekijken en iets meer kijken naar de routeringsquery.

Zie voor meer informatie over de stappen in dit gedeelte van de machine learning op IoT Edge-zelfstudie:

* [Basisprincipes van Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Berichtroutering configureren met IoT Hub](../iot-hub/tutorial-routing.md)
* [Een IoT-hub maken met de Azure-portal](../iot-hub/iot-hub-create-through-portal.md)

Ga verder naar het volgende artikel om een gesimuleerd apparaat te maken om te controleren.

> [!div class="nextstepaction"]
> [Apparaatgegevens genereren](tutorial-machine-learning-edge-03-generate-data.md)
