---
title: 'Zelf studie: omgevings Machine Learning instellen op Azure IoT Edge'
description: 'Zelf studie: uw omgeving voorbereiden voor het ontwikkelen en implementeren van modules voor machine learning aan de rand.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2ea4248ebaedd318e4112e41169f72bc80b1120f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114068"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Zelf studie: een omgeving instellen voor machine learning op IoT Edge

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelf studie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks in dit artikel hebt gearriveerd, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks voor de beste resultaten.

Dit artikel van de end-to-end-Azure Machine Learning op IoT Edge zelf studie helpt u bij het voorbereiden van uw omgeving voor ontwikkeling en implementatie. Stel eerst een ontwikkel machine in met alle hulpprogram ma's die u nodig hebt. Maak vervolgens de benodigde cloud resources in Azure.

## <a name="set-up-a-development-machine"></a>Een ontwikkel machine instellen

Deze stap wordt doorgaans uitgevoerd door een Cloud ontwikkelaar. Een deel van de software kan ook nuttig zijn voor een gegevens wetenschapper.

In het kader van dit artikel voeren we verschillende ontwikkel taken uit, waaronder het coderen, compileren, configureren en implementeren van IoT Edge-modules en IoT-apparaten. Voor gebruiks gemak hebt u een Power shell-script gemaakt waarmee een virtuele Azure-machine wordt gemaakt waarbij veel van de vereiste onderdelen al zijn geconfigureerd. De virtuele machine die wordt gemaakt, moet [geneste virtualisatie](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)kunnen verwerken. Daarom kiest u een [Standard_D8s_v3](../virtual-machines/windows/sizes-general.md#dsv3-series-1) machine grootte.

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
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

De ontwikkel-VM is niet strikt nood zakelijk. alle ontwikkel hulpprogramma's kunnen worden uitgevoerd op een lokale computer. We raden u echter ten zeerste aan gebruik te maken van de virtuele machine om een veld op niveau af te spelen.

Het duurt ongeveer 30 minuten om de virtuele machine te maken en te configureren.

### <a name="get-the-script"></a>Het script ophalen

Kloon of down load het Power shell-script uit het [machine learning en IOT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) voor beeld van een opslag plaats.

### <a name="create-an-azure-virtual-machine"></a>Een virtuele machine van Azure maken

De map DevVM bevat de bestanden die nodig zijn om een virtuele machine van Azure te maken die geschikt is voor het volt ooien van deze zelf studie.

1. Open Power shell als beheerder en navigeer naar de map waar u de code hebt gedownload. We verwijzen naar de hoofdmap voor uw bron als `<srcdir>`.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. Voer de volgende opdracht uit om scripts toe te staan. Kies **Ja** als u hierom wordt gevraagd.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Voer Create-AzureDevVM. ps1 uit vanuit deze map.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * Wanneer u hierom wordt gevraagd, geeft u de volgende informatie op:
      * **Azure-abonnements-id**: uw abonnements-id, die u kunt vinden in de Azure Portal
      * **Naam van resource groep**: de naam van een nieuwe of bestaande resource groep in azure
      * **Locatie**: Kies een Azure-locatie waar de virtuele machine wordt gemaakt. Bijvoorbeeld westus2 of northeurope. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor meer informatie.
      * **AdminUsername**: Geef een naam op voor het beheerders account dat u wilt maken en gebruiken op de virtuele machine.
      * **AdminPassword**: Stel een wacht woord in voor het beheerders account op de virtuele machine.

    * Als Azure PowerShell niet is geïnstalleerd, wordt het script [Azure PowerShell AZ-module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0) geïnstalleerd

    * U wordt gevraagd zich aan te melden bij Azure.

    * Het script bevestigt de gegevens voor het maken van de virtuele machine. Druk op `y` of `Enter` om door te gaan.

Het script wordt gedurende enkele minuten uitgevoerd, omdat de volgende stappen worden uitgevoerd:

* De resource groep maken als deze niet bestaat
* De virtuele machine implementeren
* Hyper-V op de virtuele machine inschakelen
* Software nodig hebben om de voorbeeld opslagplaats te ontwikkelen en te klonen
* De virtuele machine opnieuw opstarten
* Een RDP-bestand op uw bureau blad maken voor verbinding met de virtuele machine

### <a name="set-auto-shutdown-schedule"></a>Schema voor automatisch afsluiten instellen

Om u te helpen kosten te verlagen, is de virtuele machine gemaakt met een schema voor automatische afsluiting dat is ingesteld op 1900 PST. Mogelijk moet u deze timing bijwerken, afhankelijk van uw locatie en planning. De afsluit planning bijwerken:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Ga naar de virtuele machine in de resource groep die u in de vorige sectie hebt gegeven.

3. Selecteer **automatisch afsluiten** in de kantlijn Navigator.

4. Voer een nieuwe afsluit tijd in **gepland afsluiten** in of wijzig de **tijd zone** en klik vervolgens op **Opslaan**.

### <a name="connect-and-configure-development-machine"></a>Een ontwikkel machine verbinden en configureren

Nu u een virtuele machine hebt gemaakt, moet u de installatie van de benodigde software volt ooien om de zelf studie te volt ooien.

#### <a name="start-a-remote-desktop-session"></a>Een extern-bureaublad sessie starten

1. Het script voor het maken van een virtuele machine heeft een RDP-bestand op uw bureau blad gemaakt.

2. Dubbel klik op het bestand met de naam\<naam van de **Azure-VM\>. RDP**.

3. Er wordt een dialoog venster weer gegeven met de melding dat de uitgever van de externe verbinding onbekend is. Klik op het selectie vakje **niet opnieuw vragen voor verbindingen met deze computer** en selecteer vervolgens **verbinding maken**.

4. Wanneer u hierom wordt gevraagd, geeft u het AdminPassword op dat u hebt gebruikt bij het uitvoeren van het script om de virtuele machine in te stellen en klikt u op **OK**.

5. U wordt gevraagd om het certificaat voor de virtuele machine te accepteren. Selecteer **mij niet opnieuw vragen voor verbindingen met deze computer** en kies **Ja**.

#### <a name="install-visual-studio-code-extensions"></a>Visual Studio code-extensies installeren

Nu u verbinding hebt gemaakt met de ontwikkel machine, voegt u een aantal nuttige uitbrei dingen toe aan Visual Studio code om de ontwikkelings ervaring eenvoudiger te maken.

1. Navigeer in een Power shell-venster naar **C:\\source\\IoTEdgeAndMlSample\\DevVM**.

2. Toestaan dat scripts worden uitgevoerd op de virtuele machine door te typen.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Voer het script uit.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. Het script wordt uitgevoerd met een paar minuten installatie van VS code-extensies:

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>IoT Hub en opslag instellen

Deze stappen worden doorgaans uitgevoerd door een Cloud ontwikkelaar.

Azure IoT Hub is het hart van elke IoT-toepassing. Het zorgt voor een veilige communicatie tussen IoT-apparaten en de Cloud. Het is het belangrijkste coördinatie punt voor de werking van de IoT Edge machine learning oplossing.

* IoT Hub gebruikt routes voor het omleiden van binnenkomende gegevens van IoT-apparaten naar andere downstream-Services. We zullen gebruikmaken van IoT Hub routes voor het verzenden van apparaatgegevens naar Azure Storage waar deze kunnen worden gebruikt door Azure Machine Learning om onze resterende resterende levens duur-classificatie (bruikbare levens duur) te trainen.

* Verderop in de zelf studie gebruiken we IoT Hub om het Azure IoT Edge-apparaat te configureren en te beheren.

In deze sectie gebruikt u een script om een Azure IoT hub en een Azure Storage-account te maken. Vervolgens configureert u een route waarmee door de hub ontvangen gegevens worden doorgestuurd naar een Azure Storage Blob container met behulp van de Azure Portal. Deze stappen duren ongeveer tien minuten om te volt ooien.

### <a name="create-cloud-resources"></a>Cloud resources maken

1. Open een Power shell-venster op de ontwikkel computer.

1. Ga naar de map IoTHub.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Voer het aanmaak script uit. Gebruik dezelfde waarden voor abonnements-ID, locatie en resource groep als bij het maken van de ontwikkelings-VM.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * U wordt gevraagd zich aan te melden bij Azure.
    * Het script bevestigt de gegevens voor het maken van uw hub en opslag account. Druk op `y` of `Enter` om door te gaan.

Het uitvoeren van het script duurt ongeveer twee minuten. Zodra het script is voltooid, wordt de naam van de hub en het opslag account uitgevoerd.

### <a name="review-route-to-storage-in-iot-hub"></a>Route naar opslag in IoT Hub bekijken

Als onderdeel van het maken van de IoT-hub, heeft het script dat we in de vorige sectie hebben uitgevoerd ook een aangepast eind punt en een route gemaakt. IoT Hub routes bestaan uit een query-expressie en een eind punt. Als een bericht overeenkomt met de expressie, worden de gegevens via de route naar het bijbehorende eind punt verzonden. Eind punten kunnen worden Event Hubs, Service Bus wacht rijen en onderwerpen. In dit geval is het eind punt een BLOB-container in een opslag account. We gaan de Azure Portal gebruiken om de route te bekijken die door ons script is gemaakt.

1. Open de [Azure Portal](https://portal.azure.com).

1. Kies alle services in de linkernavigatiebalk, typ IoT in het zoekvak en selecteer **IOT hub**.

1. Kies het IoT Hub dat u in de vorige stap hebt gemaakt.

1. Kies **bericht routering**In de IOT hub-navigatie.

1. De pagina bericht routering bevat twee tabbladen, **routes** en **aangepaste eind punten**. Selecteer het tabblad **aangepaste eind punten** .

1. Onder **Blob Storage**selecteert u **turbofanDeviceStorage**.

1. Houd er rekening mee dat dit eind punt verwijst naar een BLOB-container met de naam **devicedata** in het opslag account dat in de laatste stap is gemaakt, met de naam **iotedgeandml\<unieke achtervoegsel\>** .

1. Houd er ook rekening mee dat de indeling van de **BLOB-bestands naam** is gewijzigd van de standaard indeling naar in plaats daarvan de partitie als laatste element in de naam te plaatsen. Deze indeling is handiger voor de bestands bewerkingen die we gaan uitvoeren met Azure Notebooks verderop in de zelf studie.

1. Sluit de Blade Details van eind punt om terug te keren naar de pagina **bericht routering** .

1. Selecteer het tabblad **routes** .

1. Selecteer de route met de naam **turbofanDeviceDataToStorage**.

1. Houd er rekening mee dat het eind punt van de route het aangepaste **turbofanDeviceStorage** -eind punt is.

1. Bekijk de routerings query, die is ingesteld op **True**. Dit betekent dat alle telemetrie-berichten van het apparaat overeenkomen met deze route en daarom worden alle berichten verzonden naar het **turbofanDeviceStorage** -eind punt.

1. Sluit de details van de route.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een IoT Hub gemaakt en een route naar een Azure Storage-account geconfigureerd. In het volgende artikel worden gegevens verzonden vanuit een set gesimuleerde apparaten via de IoT Hub naar het opslag account. Verderop in de zelf studie, nadat we onze IoT Edge apparaat en modules hebben geconfigureerd, gaan we de routes opnieuw bezoeken en kijken we nog iets meer op de routerings query.

Zie voor meer informatie over de stappen in dit gedeelte van de Machine Learning op IoT Edge zelf studie:

* [Basisprincipes van Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Bericht routering configureren met IoT Hub](../iot-hub/tutorial-routing.md)
* [Een IoT-hub maken met behulp van de Azure Portal](../iot-hub/iot-hub-create-through-portal.md)

Ga door naar het volgende artikel om een gesimuleerd apparaat te maken dat u wilt bewaken.

> [!div class="nextstepaction"]
> [Apparaatgegevens genereren](tutorial-machine-learning-edge-03-generate-data.md)
