---
title: Realtime datavisualisatie van uw IoT-hubgegevens in een web-app
description: Gebruik een webapplicatie om temperatuur- en vochtigheidsgegevens te visualiseren die van een sensor worden verzameld en naar uw iot-hub worden verzonden.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 138e077f7b47fa9f38a4710db95eb7208cef78e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675321"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Real-time sensorgegevens van uw Azure IoT-hub visualiseren in een webtoepassing

![End-to-end diagram](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Wat u leert

In deze zelfstudie leert u hoe u realtime sensorgegevens visualiseert die uw IoT-hub ontvangt met een node.js-web-app die op uw lokale computer wordt uitgevoerd. Nadat u de web-app lokaal hebt uitgevoerd, u optioneel stappen uitvoeren om de web-app te hosten in Azure App Service. Zie Power BI gebruiken om [realtime sensorgegevens van Azure IoT Hub te visualiseren](iot-hub-live-data-visualization-in-power-bi.md)als u wilt proberen de gegevens in uw IoT-hub te visualiseren.

## <a name="what-you-do"></a>Wat je doet

* Een consumentengroep toevoegen aan uw IoT-hub die de webtoepassing gebruikt om sensorgegevens te lezen
* Download de web-app code van GitHub
* De code van de web-app onderzoeken
* Omgevingsvariabelen configureren om de IoT Hub-artefacten te behouden die uw web-app nodig heeft
* De web-app uitvoeren op uw ontwikkelingsmachine
* Open een webpagina om realtime temperatuur- en vochtigheidsgegevens van uw IoT-hub te bekijken
* (Optioneel) Azure CLI gebruiken om uw web-app te hosten in Azure App Service

## <a name="what-you-need"></a>Wat u nodig hebt

* Voltooi de [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial of een van de apparaat tutorials; bijvoorbeeld [Raspberry Pi met node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Deze hebben betrekking op de volgende eisen:

  * Een actief Azure-abonnement
  * Een Iot-hub onder uw abonnement
  * Een clienttoepassing die berichten verzendt naar uw Iot-hub

* [Download Git](https://www.git-scm.com/downloads)

* De stappen in dit artikel gaan uit van een Windows-ontwikkelingsmachine; u deze stappen echter eenvoudig uitvoeren op een Linux-systeem in uw voorkeursshell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IOT-extensie voegt specifieke opdrachten voor IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS) toe aan Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Een consumentengroep toevoegen aan uw IoT-hub

[Consumentengroepen](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) bieden onafhankelijke weergaven in de gebeurtenisstream waarmee apps en Azure-services onafhankelijk gegevens van hetzelfde eindpunt van Event Hub kunnen gebruiken. In deze sectie voegt u een consumentengroep toe aan het ingebouwde eindpunt van uw IoT-hub waarmee de web-app gegevens kan lezen.

Voer de volgende opdracht uit om een consumentengroep toe te voegen aan het ingebouwde eindpunt van uw IoT-hub:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Noteer de naam die je kiest, je hebt het later nodig in deze tutorial.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Een tekenreeks voor serviceverbindingen voor uw IoT-hub

IoT-hubs worden gemaakt met verschillende standaardtoegangsbeleidsregels. Een dergelijk beleid is het **servicebeleid,** dat voldoende machtigingen biedt voor een service om de eindpunten van de IoT-hub te lezen en te schrijven. Voer de volgende opdracht uit om een verbindingstekenreeks voor uw IoT-hub te krijgen die voldoet aan het servicebeleid:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

De verbindingstekenreeks moet er als volgt uitzien:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Noteer de tekenreeks serviceverbinding, je hebt het later in deze zelfstudie nodig.

## <a name="download-the-web-app-from-github"></a>Download de web-app van GitHub

Open een opdrachtvenster en voer de volgende opdrachten in om het voorbeeld van GitHub te downloaden en de voorbeeldmap te wijzigen:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>De code van de web-app onderzoeken

Open de web-app in de map web-apps-node-iot-hub-data-visualisatie in uw favoriete editor. Als volgt wordt de bestandsstructuur weergegeven in VS-code:

![Bestandsstructuur van web-apps](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Neem even de tijd om de volgende bestanden te onderzoeken:

* **Server.js** is een script aan de servicezijde dat de websocket en de wrapperklasse Van de Gebeurtenishub initialiseert. Het biedt een callback naar de wrapperklasse Van de Gebeurtenishub die de klasse gebruikt om binnenkomende berichten naar de websocket uit te zenden.

* **Event-hub-reader.js** is een script aan de servicezijde dat verbinding maakt met het ingebouwde eindpunt van de IoT-hub met behulp van de opgegeven verbindingstekenreeks en consumentengroep. Het haalt de DeviceId en EnqueuedTimeUtc uit metagegevens op binnenkomende berichten en stuurt het bericht vervolgens door met behulp van de callback-methode die is geregistreerd door server.js.

* **Chart-device-data.js** is een client-side script dat luistert op de websocket, houdt bij van elke DeviceId, en slaat de laatste 50 punten van inkomende gegevens voor elk apparaat. Vervolgens worden de geselecteerde apparaatgegevens aan het grafiekobject gekoppeld.

* **Index.html** verwerkt de ui-indeling voor de webpagina en verwijst naar de benodigde scripts voor logica aan de clientzijde.

## <a name="configure-environment-variables-for-the-web-app"></a>Omgevingsvariabelen configureren voor de web-app

Om gegevens van uw IoT-hub te lezen, heeft de web-app de verbindingstekenreeks van uw IoT-hub en de naam van de consumentengroep nodig die deze moet doorlezen. Deze tekenreeksen worden opgehaald uit de procesomgeving in de volgende regels in server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Stel de omgevingsvariabelen in het opdrachtvenster in met de volgende opdrachten. Vervang de tijdelijke aanduidingswaarden door de tekenreeks serviceverbinding voor uw IoT-hub en de naam van de consumentengroep die u eerder hebt gemaakt. Citeer de touwtjes niet.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>De web-app uitvoeren

1. Controleer of uw apparaat gegevens gebruikt en verzendt.

2. Voer in het opdrachtvenster de volgende regels uit om gerefereerde pakketten te downloaden en te installeren en start de website:

   ```cmd
   npm install
   npm start
   ```

3. U ziet uitvoer in de console die aangeeft dat de web-app met succes is verbonden met uw IoT-hub en luistert op poort 3000:

   ![Web-app gestart op console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Een webpagina openen om gegevens van uw IoT-hub te bekijken

Open een `http://localhost:3000`browser naar .

Selecteer **in** de lijst Een apparaat selecteren de optie uw apparaat om een lopend plot te zien van de laatste 50 temperatuur- en vochtigheidsgegevenspunten die door het apparaat naar uw IoT-hub worden verzonden.

![Webpagina met real-time temperatuur en vochtigheid](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

U moet ook uitvoer in de console zien die de berichten weergeeft die uw web-app uitzond naar de browserclient:  

![Uitvoer van web-apps op console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>De web-app hosten in App-service

De [Web Apps-functie van Azure App Service](https://docs.microsoft.com/azure/app-service/overview) biedt een platform als een service (PAAS) voor het hosten van webtoepassingen. Webtoepassingen die worden gehost in Azure App Service kunnen profiteren van krachtige Azure-functies, zoals extra beveiliging, taakverdeling en schaalbaarheid, evenals Azure- en partner DevOps-oplossingen zoals continue implementatie, pakketbeheer, enzovoort. Azure App Service ondersteunt webapplicaties die in veel populaire talen zijn ontwikkeld en worden geïmplementeerd op de Windows- of Linux-infrastructuur.

In deze sectie indient u een web-app in App Service en implementeert u uw code ernaar met behulp van Azure CLI-opdrachten. Details van de commando's die worden gebruikt in de documentatie van de [AZ Webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) vindt u hier. Voordat u begint, moet u de stappen uitvoeren om [een brongroep toe te voegen aan uw IoT-hub,](#add-a-consumer-group-to-your-iot-hub) [een tekenreeks voor de serviceverbinding voor uw IoT-hub te krijgen](#get-a-service-connection-string-for-your-iot-hub)en de [web-app downloaden van GitHub](#download-the-web-app-from-github).

1. Een [App-serviceplan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) definieert een set rekenbronnen voor een app die wordt gehost in App-service om uit te voeren. In deze zelfstudie gebruiken we de laag Ontwikkelaars/Vrij om de web-app te hosten. Met de laag Gratis wordt uw web-app uitgevoerd op gedeelde Windows-bronnen met andere App Service-apps, waaronder apps van andere klanten. Azure biedt ook App Service-abonnementen om web-apps te implementeren op Linux-compute resources. U deze stap overslaan als u al een App Service-abonnement hebt dat u wilt gebruiken.

   Voer de volgende opdracht uit om een App Service-abonnement te maken met de gratis windows-laag. Gebruik dezelfde resourcegroep waarin uw IoT-hub zich bevindt. De naam van uw serviceplan kan hoofdletters, cijfers en koppeltekens bevatten.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Instel nu een web-app in uw App Service-abonnement. Met `--deployment-local-git` de parameter kan de web-app-code worden geüpload en geïmplementeerd vanuit een Git-opslagplaats op uw lokale machine. De naam van uw web-app moet wereldwijd uniek zijn en hoofdletters, cijfers en koppeltekens bevatten. Geef Node versie 10.6 of hoger `--runtime` op voor de parameter, afhankelijk van de versie van de runtime van Node.js die u gebruikt. U `az webapp list-runtimes` de opdracht gebruiken om een lijst met ondersteunde runtimes te krijgen.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Voeg nu toepassingsinstellingen toe voor de omgevingsvariabelen die de iot-hubverbindingstekenreeks en de consumentengroep Event hub opgeven. Individuele instellingen zijn ruimte `-settings` die is afgebakend in de parameter. Gebruik de tekenreeks serviceverbinding voor uw IoT-hub en de consumentengroep die u eerder in deze zelfstudie hebt gemaakt. Citeer de waarden niet.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Schakel het Web Sockets-protocol voor de web-app in en stel de web-app in om alleen HTTPS-verzoeken te ontvangen (HTTP-verzoeken worden doorgestuurd naar HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Als u de code wilt implementeren in De App-service, gebruikt u de [implementatiereferenties op gebruikersniveau.](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials) Uw implementatiereferenties op gebruikersniveau verschillen van uw Azure-referenties en worden gebruikt voor git-lokale en FTP-implementaties naar een web-app. Eenmaal ingesteld, zijn ze geldig voor al uw App Service-apps in alle abonnementen in uw Azure-account. Als u eerder implementatiereferenties op gebruikersniveau hebt ingesteld, u deze gebruiken.

   Als u nog niet eerder implementatiereferenties op gebruikersniveau hebt ingesteld of als u uw wachtwoord niet meer onthouden, voert u de volgende opdracht uit. Uw gebruikersnaam voor implementatie moet uniek zijn binnen Azure en mag niet het '@'-symbool voor lokale Git-pushes bevatten. Wanneer u wordt gevraagd, voert u uw nieuwe wachtwoord in en bevestigt u deze. Het wachtwoord moet ten minste acht tekens lang zijn, met twee van de volgende drie elementen: letters, cijfers en symbolen.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Laat de Git-URL gebruiken om uw code naar App Service te pushen.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Voeg een afstandsbediening toe aan uw kloon die verwijst naar de Git-opslagplaats voor de web-app in App-service. Voor \<Git-kloon-URL\>gebruikt u de URL die in de vorige stap is geretourneerd. Voer de volgende opdracht uit in het opdrachtvenster.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Als u de code wilt implementeren in App-service, voert u de volgende opdracht in het opdrachtvenster in. Als u om referenties wordt gevraagd, voert u de implementatiereferenties op gebruikersniveau in die u in stap 5 hebt gemaakt. Zorg ervoor dat u naar de hoofdvestiging van de app-service-afstandsbediening duwt.

    ```cmd
    git push webapp master:master
    ```

9. De voortgang van de implementatie wordt bijgewerkt in het opdrachtvenster. Een succesvolle implementatie eindigt met regels die vergelijkbaar zijn met de volgende uitvoer:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Voer de volgende opdracht uit om de status van uw web-app op te vragen en ervoor te zorgen dat deze wordt uitgevoerd:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Ga naar `https://<your web app name>.azurewebsites.net` in een browser. Een webpagina die vergelijkbaar is met die waarop u de web-app lokaal hebt uitgevoerd, wordt weergegeven. Ervan uitgaande dat uw apparaat gegevens uitvoert en verzendt, ziet u een lopend plot van de 50 meest recente temperatuur- en vochtigheidsmetingen die door het apparaat worden verzonden.

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen tegenkomt met dit voorbeeld, probeert u de stappen in de volgende secties. Als je nog steeds problemen hebt, stuur ons dan feedback onderaan dit onderwerp.

### <a name="client-issues"></a>Problemen met klanten

* Als een apparaat niet in de lijst wordt weergegeven of als er geen grafiek wordt getekend, controleert u of de apparaatcode op uw apparaat wordt uitgevoerd.

* Open in de browser de hulpprogramma's voor ontwikkelaars (in veel browsers wordt de F12-toets geopend) en vind je de console. Kijk voor eventuele waarschuwingen of fouten gedrukt daar.

* U client-side script debuggen in /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemen met lokale websites

* Bekijk de uitvoer in het venster waar u knooppunt hebt gestart voor console-uitvoer.

* Debug de servercode, met name server.js en /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Azure App Service-problemen

* Ga in azure-portal naar uw web-app. Selecteer **App-servicelogboeken**onder **Controle** in het linkerdeelvenster . **Schakel Toepassingslogboekregistratie (bestandssysteem)** in, stel **Niveau** in op Fout en selecteer **Opslaan.** Open vervolgens **Logstream** (onder **Controleren).**

* Selecteer in uw web-app in Azure-portal onder **Hulpprogramma's** voor `npm -v`ontwikkeling de optie **Console-** en npm-versies met `node -v` en .

* Als u een fout ziet over het niet vinden van een pakket, hebt u de stappen mogelijk buiten de orde uitgevoerd. Wanneer de site wordt `git push`geïmplementeerd (met `npm install`) wordt de app-service uitgevoerd, die wordt uitgevoerd op basis van de huidige versie van het knooppunt dat is geconfigureerd. Als dat later in configuratie wordt gewijzigd, moet u een zinloze wijziging in de code aanbrengen en opnieuw duwen.

## <a name="next-steps"></a>Volgende stappen

U hebt uw web-app met succes gebruikt om realtime sensorgegevens van uw IoT-hub te visualiseren.

Zie [Power BI gebruiken om realtime sensorgegevens van uw IoT-hub te visualiseren](iot-hub-live-data-visualization-in-power-bi.md)voor een andere manier om gegevens van Azure IoT Hub te visualiseren.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
