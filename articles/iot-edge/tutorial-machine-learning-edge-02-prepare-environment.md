---
title: 'Zelf studie: omgevings Machine Learning instellen op Azure IoT Edge'
description: 'Zelf studie: uw omgeving voorbereiden voor het ontwikkelen en implementeren van modules voor machine learning aan de rand.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/23/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9809a9b4d644a1fa11fe9cce1cbd35b037206b29
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944283"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Zelf studie: een omgeving instellen voor machine learning op IoT Edge

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelf studie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks in dit artikel hebt gearriveerd, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks voor de beste resultaten.

Dit artikel helpt u bij het voorbereiden van uw omgeving voor ontwikkeling en implementatie. Stel eerst een ontwikkel machine in met alle hulpprogram ma's die u nodig hebt. Maak vervolgens de benodigde cloud resources in Azure.

## <a name="set-up-the-development-vm"></a>De ontwikkelings-VM instellen

Deze stap wordt doorgaans uitgevoerd door een Cloud ontwikkelaar. Een deel van de software kan ook nuttig zijn voor een gegevens wetenschapper.

We hebben een Power shell-script gemaakt waarmee een virtuele Azure-machine wordt gemaakt waarbij veel van de vereiste onderdelen al zijn geconfigureerd. De virtuele machine die wordt gemaakt, moet [geneste virtualisatie](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)kunnen verwerken. Daarom kiest u een [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) machine grootte.

De ontwikkelings-VM wordt ingesteld met:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop voor Windows](https://www.docker.com/products/docker-desktop)
* [Git voor Windows](https://gitforwindows.org/)
* [Git Credential Manager voor Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core-SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS code-extensies](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT-Hulpprogram Ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

De ontwikkel-VM is niet strikt nood zakelijk. alle ontwikkel hulpprogramma's kunnen worden uitgevoerd op een lokale computer. We raden u echter ten zeerste aan gebruik te maken van de virtuele machine om een veld op niveau af te spelen.

Het duurt ongeveer 30 minuten om de virtuele machine te maken en te configureren.

1. Kloon of down load de voorbeeld opslagplaats voor [machine learning en IOT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) naar de lokale computer.

1. Open Power shell als beheerder en ga naar de map **\IoTEdgeAndMlSample\DevVM** die zich bevindt in de hoofdmap waarin u de code hebt gedownload. We verwijzen naar de hoofdmap voor uw bron als `srcdir`.

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   De map DevVM bevat de bestanden die nodig zijn om een virtuele machine van Azure te maken die geschikt is voor het volt ooien van deze zelf studie.

1. Voer de volgende opdracht uit om scripts toe te staan. Kies **Ja** als u hierom wordt gevraagd.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Voer Create-AzureDevVM. ps1 uit.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Wanneer u hierom wordt gevraagd, geeft u de volgende informatie op:

    * **Azure-abonnements-id**: uw abonnements-id, die u kunt vinden in [Azure-abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de portal.
    * **Naam van resource groep**: de naam van een nieuwe of bestaande resource groep in Azure.
    * **Locatie**: Kies een Azure-locatie waar de virtuele machine wordt gemaakt. Bijvoorbeeld ' vs West 2 ' of ' Europa-noord '. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor meer informatie.
    * **Gebruikers naam**: Geef een naam op voor het beheerders account voor de virtuele machine.
    * **Wacht woord**: Stel een wacht woord in voor het beheerders account voor de virtuele machine.

   Het script wordt gedurende enkele minuten uitgevoerd, omdat de volgende stappen worden uitgevoerd:

    1. Installeert de [Azure PowerShell AZ-module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0).
    1. Hiermee wordt u gevraagd om u aan te melden bij Azure.
    1. Hiermee wordt de informatie voor het maken van uw VM bevestigd. Druk op **j** of **Enter** om door te gaan.
    1. Maakt de resource groep als deze niet bestaat.
    1. Hiermee implementeert u de virtuele machine.
    1. Hiermee schakelt u Hyper-V in op de VM.
    1. Hiermee installeert u de benodigde software voor het ontwikkelen en klonen van de voorbeeld opslagplaats.
    1. Hiermee wordt de virtuele machine opnieuw opgestart.
    1. Hiermee maakt u een RDP-bestand op uw bureau blad om verbinding te maken met de virtuele machine.

   Als u wordt gevraagd de naam van de virtuele machine opnieuw op te starten, kunt u de naam van de VM kopiëren uit de script uitvoer. De uitvoer toont ook het pad naar het RDP-bestand om verbinding te maken met de virtuele machine.

### <a name="set-auto-shutdown-schedule"></a>Schema voor automatisch afsluiten instellen

Om u te helpen kosten te verlagen, is de ontwikkelings-VM gemaakt met een schema voor automatische afsluiting dat is ingesteld op 1900 PST. U moet deze instelling mogelijk bijwerken, afhankelijk van uw locatie en planning. De afsluit planning bijwerken:

1. Navigeer in het Azure Portal naar de virtuele machine die het script heeft gemaakt.

1. Selecteer in het menu van het linkerdeel venster onder **bewerkingen**de optie **automatisch afsluiten**.

1. Pas de **geplande afsluiting** en de gewenste **tijd zone** aan en selecteer **Opslaan**.

## <a name="connect-to-the-development-vm"></a>Verbinding maken met de ontwikkelings-VM

Nu u een virtuele machine hebt gemaakt, moet u de installatie van de benodigde software volt ooien om de zelf studie te volt ooien.

1. Dubbel klik op het RDP-bestand dat door het script op uw bureau blad is gemaakt.

1. Er wordt een dialoog venster weer gegeven met de melding dat de uitgever van de externe verbinding onbekend is. Dit is acceptabel, dus Selecteer **verbinding maken**.

1. Geef het beheerders wachtwoord op dat u hebt opgegeven om de virtuele machine te maken en klik op **OK**.

1. U wordt gevraagd om het certificaat voor de virtuele machine te accepteren. Selecteer **Ja**.

## <a name="install-visual-studio-code-extensions"></a>Visual Studio code-extensies installeren

Nu u verbinding hebt gemaakt met de ontwikkel machine, voegt u een aantal nuttige uitbrei dingen toe aan Visual Studio code om de ontwikkelings ervaring eenvoudiger te maken.

1. Maak verbinding met de ontwikkel-VM, open een Power shell-venster en navigeer naar de **C:\source\IoTEdgeAndMlSample\DevVM** -map. Deze map is gemaakt door het script waarmee de virtuele machine is gemaakt.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Voer de volgende opdracht uit om scripts toe te staan. Kies **Ja** als u hierom wordt gevraagd.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Voer het script voor Visual Studio code Extensions uit.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. Het script wordt uitgevoerd met een paar minuten installatie van VS code-extensies:

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>IoT Hub en opslag instellen

Deze stappen worden doorgaans uitgevoerd door een Cloud ontwikkelaar.

Azure IoT Hub is het hart van een IoT-toepassing, omdat deze beveiligde communicatie afhandelt tussen IoT-apparaten en de Cloud. Het is het belangrijkste coördinatie punt voor de werking van de IoT Edge machine learning oplossing.

* IoT Hub gebruikt routes voor het omleiden van binnenkomende gegevens van IoT-apparaten naar andere downstream-Services. We zullen gebruikmaken van IoT Hub routes voor het verzenden van apparaatgegevens naar Azure Storage. In Azure Storage worden de gegevens van het apparaat gebruikt door Azure Machine Learning om onze resterende resterende levens duur-classificatie (bruikbare levens duur) te trainen.

* Verderop in de zelf studie gebruiken we IoT Hub om het Azure IoT Edge-apparaat te configureren en te beheren.

In deze sectie gebruikt u een script om een Azure IoT hub en een Azure Storage-account te maken. Vervolgens configureert u in de Azure Portal een route waarmee gegevens die door de hub worden ontvangen, worden doorgestuurd naar een Azure Storage-container. Deze stappen duren ongeveer tien minuten om te volt ooien.

1. Maak verbinding met de ontwikkel-VM, open een Power shell-venster en navigeer naar de **IoTHub** -map.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Voer het aanmaak script uit. Gebruik dezelfde waarden voor abonnements-ID, locatie en resource groep als bij het maken van de ontwikkelings-VM.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * U wordt gevraagd zich aan te melden bij Azure.
    * Het script bevestigt de gegevens voor het maken van uw hub en opslag account. Druk op **j** of **Enter** om door te gaan.

Het uitvoeren van het script duurt ongeveer twee minuten. Zodra het script is voltooid, wordt de naam van de IoT-hub en het opslag account uitgevoerd.

## <a name="review-route-to-storage-in-iot-hub"></a>Route naar opslag in IoT Hub bekijken

Als onderdeel van het maken van de IoT-hub, heeft het script dat we in de vorige sectie hebben uitgevoerd ook een aangepast eind punt en een route gemaakt. IoT Hub routes bestaan uit een query-expressie en een eind punt. Als een bericht overeenkomt met de expressie, worden de gegevens via de route naar het bijbehorende eind punt verzonden. Eind punten kunnen worden Event Hubs, Service Bus wacht rijen en onderwerpen. In dit geval is het eind punt een BLOB-container in een opslag account. We gaan de Azure Portal gebruiken om de route te bekijken die door ons script is gemaakt.

1. Open de [Azure Portal](https://portal.azure.com) en ga naar de resource groep die u voor deze zelf studie gebruikt.

1. Selecteer in de lijst met resources het IoT Hub dat het script heeft gemaakt. Er is een naam die eindigt op wille keurige tekens zoals `IotEdgeAndMlHub-jrujej6de6i7w`.

1. Selecteer in het menu van het linkerdeel venster onder **berichten**de optie **bericht routering**.

1. Op de pagina **bericht routering** selecteert u het tabblad **aangepaste eind punten** .

1. Vouw de sectie **opslag** uit:

   ![Controleren of de turbofandevicestorage is in de lijst met aangepaste eind punten](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   We zien **turbofandevicestorage** in de lijst met aangepaste eind punten. Houd rekening met de volgende kenmerken over dit eind punt:

   * Deze verwijst naar de BLOB storage-container die u hebt gemaakt met de naam `devicedata`, zoals aangegeven door de **container naam**.
   * De **filename-indeling** heeft partitie als laatste element in de naam. Deze indeling is handiger voor de bestands bewerkingen die we gaan uitvoeren met Azure Notebooks verderop in de zelf studie.

1. Selecteer het tabblad **routes** .

1. Selecteer de route met de naam **turbofanDeviceDataToStorage**.

1. Op de pagina **routes Details** ziet u dat het eind punt van de route het **turbofanDeviceStorage** -eind punt is.

   ![Bekijk de details van de turbofanDeviceDataToStorage-route](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Bekijk de **routerings query**, die is ingesteld op **True**. Deze instelling betekent dat alle telemetrie-berichten van het apparaat overeenkomen met deze route. en daarom worden alle berichten naar het **turbofanDeviceStorage** -eind punt verzonden.

1. Omdat er geen bewerkingen zijn uitgevoerd, sluit u deze pagina.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een IoT Hub gemaakt en een route naar een Azure Storage-account geconfigureerd. Vervolgens verzenden we gegevens van een set gesimuleerde apparaten via de IoT Hub naar het opslag account. Verderop in de zelf studie, nadat we onze IoT Edge apparaat en modules hebben geconfigureerd, gaan we de routes opnieuw bezoeken en kijken we nog iets meer op de routerings query.

Zie voor meer informatie over de stappen in dit gedeelte van de Machine Learning op IoT Edge zelf studie:

* [Basisprincipes van Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Bericht routering configureren met IoT Hub](../iot-hub/tutorial-routing.md)
* [Een IoT-hub maken met behulp van de Azure Portal](../iot-hub/iot-hub-create-through-portal.md)

Ga door naar het volgende artikel om een gesimuleerd apparaat te maken dat u wilt bewaken.

> [!div class="nextstepaction"]
> [Apparaatgegevens genereren](tutorial-machine-learning-edge-03-generate-data.md)
