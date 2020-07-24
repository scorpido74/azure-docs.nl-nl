---
title: Problemen met live video Analytics op IoT Edge-Azure oplossen
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen met live video Analytics op IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: 62163217862f586be7ed5c0a6000693f8e7fcdd6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043183"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Problemen met live video Analytics op IoT Edge oplossen

In dit artikel worden de stappen beschreven voor het oplossen van problemen met live video Analytics (LVA) op Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Oplossen van implementatieproblemen

### <a name="diagnostics"></a>Diagnostiek

Als onderdeel van uw live video Analytics-implementatie stelt u Azure-resources in zoals IoT Hub en IoT Edge apparaten. Als eerste stap bij het vaststellen van problemen moet u altijd controleren of het apparaat aan de rand juist is ingesteld door de volgende instructies te volgen:

1. [Voer de `check` opdracht uit](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Controleer uw IOT Edge versie](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Controleer de status van de IOT Edge Security Manager en de bijbehorende logboeken](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Bekijk de berichten die door de IOT Edge hub worden verzonden](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Containers opnieuw opstarten](../../iot-edge/troubleshoot.md#restart-containers).
1. [Controleer uw firewall en poort configuratie regels](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Problemen vóór de implementatie

Als de rand infrastructuur nauw keurig is, kunt u problemen met het manifest bestand van de implementatie zoeken. Als u de live video Analytics op IoT Edge module op het IoT Edge-apparaat naast andere IoT-modules wilt implementeren, gebruikt u een implementatie manifest dat de IoT Edge hub, IoT Edge agent en andere modules en de bijbehorende eigenschappen bevat. Als de JSON-code niet de juiste indeling heeft, wordt mogelijk de volgende fout weer gegeven: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Kan JSON niet parseren vanuit bestand: ' <deployment manifest.json> ' voor het argument ' content ' met uitzonde ring: ' extra gegevens: regel 101 kolom 1 (char 5325) '

Als deze fout optreedt, raden we u aan om de JSON te controleren op ontbrekende vier Kante haken of andere problemen met de structuur van het bestand. Als u de bestands structuur wilt valideren, kunt u een client gebruiken zoals de [invoeg toepassing Notepad + + met JSON Viewer](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) of een online hulp programma, zoals de [json-formatter & validator](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Tijdens de implementatie: problemen vaststellen met media Graph direct-methoden 

Nadat de module live video-analyse op IoT Edge op de juiste wijze is geïmplementeerd op het IoT Edge apparaat, kunt u de media grafiek maken en uitvoeren door [directe methoden](direct-methods.md)aan te roepen. U kunt de Azure Portal gebruiken om een diagnose uit te voeren van de media grafiek via directe methoden:

1. Ga in het Azure Portal naar de IoT-hub die is verbonden met uw IoT Edge apparaat.

1. Zoek naar **automatisch Apparaatbeheer**en selecteer vervolgens **IOT Edge**.  

1. Selecteer in de lijst met rand apparaten het apparaat dat u wilt diagnosticeren.  
         
    ![Scherm afbeelding van de Azure Portal een lijst met Edge-apparaten weer geven](./media/troubleshoot-how-to/lva-sample-device.png)

1. Controleer of de antwoord code *200-OK*is. Andere antwoord codes voor de [IOT Edge runtime](../../iot-edge/iot-edge-runtime.md) zijn onder andere:
    * 400-de implementatie configuratie is onjuist of ongeldig.
    * 417-er is geen implementatie configuratie ingesteld op het apparaat.
    * 412-de schema versie in de implementatie configuratie is ongeldig.
    * 406-het IoT Edge apparaat is offline of stuurt geen status rapporten.
    * 500-er is een fout opgetreden in de IoT Edge-runtime.

1. Als u de status 501-code krijgt, controleert u of de naam van de directe methode nauw keurig is. Als de methode naam en de aanvraag lading nauw keurig zijn, moet u de resultaten ophalen en de succes code = 200. Als de aanvraag lading onjuist is, krijgt u de status = 400 en een reactie lading die de fout code en het bericht aangeeft die het probleem met uw directe methode aanroepen moeten helpen diagnosticeren.
    * Door de gerapporteerde en gewenste eigenschappen te controleren, kunt u begrijpen of de module-eigenschappen zijn gesynchroniseerd met de implementatie. Als dat niet het geval is, kunt u uw IoT Edge apparaat opnieuw opstarten. 
    * Gebruik de hand leiding [directe methoden](direct-methods.md) voor het aanroepen van een paar methoden, met name eenvoudige items zoals GraphTopologyList. De hand leiding bevat ook de verwachte nettoladingen voor aanvragen en antwoorden en fout codes. Nadat de eenvoudige directe methoden zijn geslaagd, weet u zeker dat de module live video Analytics IoT Edge functioneel is.
        
       ![Scherm afbeelding van het deel venster directe methode voor de module IoT Edge.](./media/troubleshoot-how-to/direct-method.png) 

1. Als het **opgegeven in** de kolommen implementatie en **gerapporteerd door** de kolom *Ja*aangeeft, kunt u directe methoden aanroepen in de module live video-analyses op IOT Edge. Selecteer de module om naar een pagina te gaan waar u de gewenste en gerapporteerde eigenschappen kunt controleren en direct-methoden aanroept. Houd rekening met het volgende: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Post-implementatie: diagnose logboeken voor problemen tijdens de uitvoering 

De container logboeken voor uw IoT Edge-module moeten diagnostische gegevens bevatten die u helpen bij het opsporen van fouten in uw problemen tijdens module runtime. U kunt de [container logboeken voor problemen controleren](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) en het probleem zelf vaststellen. 

Als u alle voor gaande controles hebt uitgevoerd en nog steeds problemen ondervindt, kunt u logboeken van het IoT Edge-apparaat verzamelen [met de `support bundle` opdracht](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) voor verdere analyse van het Azure-team. U kunt [contact met ons](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) opnemen voor ondersteuning en de verzamelde logboeken indienen.

## <a name="common-error-resolutions"></a>Oplossingen voor algemene problemen

Live video Analytics wordt geïmplementeerd als een IoT Edge module op het IoT Edge apparaat en werkt samen met de IoT Edge agent-en hub-modules. Enkele veelvoorkomende fouten die u tegen komt bij het implementeren van live video Analytics worden veroorzaakt door problemen met de onderliggende IoT-infra structuur. De fouten zijn onder andere:

* [De IOT Edge-agent stopt na ongeveer een minuut](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [De IOT Edge-agent heeft geen toegang tot de installatie kopie van een module (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [De module IOT Edge agent rapporteert "empty configuratie bestand" en er worden geen modules gestart op het apparaat](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Het starten van de IOT Edge hub mislukt](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [De IOT Edge Security daemon mislukt met een ongeldige hostnaam](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [De live video analyse of een andere aangepaste IOT Edge module kan geen bericht verzenden naar de Edge hub met een 404-fout](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [De module IOT Edge is geïmplementeerd en wordt vervolgens van het apparaat verwijderd](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-setup-script-issues"></a>Problemen met Edge-installatie script

Als onderdeel van onze documentatie bieden we een [installatie script](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) voor het implementeren van Edge-en cloud-resources en aan de slag met live video Analytics Edge. Deze sectie bevat enkele script fouten die kunnen optreden, samen met oplossingen voor het opsporen van fouten.

Probleem: het script wordt uitgevoerd, heeft gedeeltelijk weinig resources gemaakt, maar mislukt met het volgende bericht:

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
Dit probleem oplossen:

1. Voer de volgende opdracht uit:

    ```
    az --version
    ```
1. Zorg ervoor dat de volgende uitbrei dingen zijn geïnstalleerd. Vanaf de publicatie van dit artikel zijn de uitbrei dingen en hun versies:

    | Extensie | Versie |
    |---|---|
    |azure-cli   |      2.5.1|
    |opdracht-modules-nspkg         |   2.0.3|
    |baan  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetrie| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Als u een geïnstalleerde extensie hebt waarvan de versie lager is dan het release nummer dat hier wordt vermeld, moet u de extensie bijwerken met de volgende opdracht:

    ```
    az extension update --name <Extension name>
    ```

    U kunt bijvoorbeeld uitvoeren `az extension update --name azure-iot` .

### <a name="sample-app-issues"></a>Voor beelden van app-problemen

Als onderdeel van onze release hebben we een voor beeld van een .NET-voorbeeld code gegeven om onze ontwikkel aars-Community Boots trapd te krijgen. In deze sectie vindt u enkele fouten die kunnen optreden wanneer u de voorbeeld code uitvoert, samen met oplossingen voor het opsporen van fouten.

Probleem: Program.cs mislukt met de volgende fout op de directe methode aanroep:

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. Zorg ervoor dat er [Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) zijn geïnstalleerd in uw Visual Studio-code omgeving en dat u de verbinding met uw IOT-hub hebt ingesteld. U doet dit door CTRL + SHIFT + P te selecteren en vervolgens **IOT hub methode selecteren**te kiezen.

1. Controleer of u een directe methode kunt aanroepen in de module IoT Edge via Visual Studio code. Roep bijvoorbeeld GraphTopologyList aan met de volgende Payload { &nbsp; " @apiVersion ": "1,0"}. Het volgende antwoord wordt weer gegeven: 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Scherm opname van het antwoord in Visual Studio code.](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Als de voor gaande oplossing mislukt, kunt u het volgende proberen:

    a. Ga naar de opdracht prompt op uw IoT Edge apparaat en voer de volgende opdracht uit:
    
      ```
      sudo systemctl restart iotedge
      ```

      Met deze opdracht worden de IoT Edge apparaat en alle modules opnieuw gestart. Wacht een paar minuten en controleer, voordat u de directe methode opnieuw probeert te gebruiken, of de modules worden uitgevoerd door de volgende opdracht uit te voeren:

      ```
      sudo iotedge list
      ```

    b. Als de voor gaande aanpak ook mislukt, probeert u de virtuele machine of computer opnieuw op te starten.

    c. Als alle benaderingen mislukken, voert u de volgende opdracht uit om een zip-bestand met alle [relevante logboeken](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)te verkrijgen en koppelt u dit aan een [ondersteunings ticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. Als er een fout bericht met de code *400* wordt weer geven, moet u ervoor zorgen dat de nettolading van de methode aanroep goed gevormd is, conform de hand leiding voor [directe methoden](direct-methods.md) .
1. Als u de status *200* -code krijgt, betekent dit dat uw hub goed werkt en dat de module-implementatie correct en responsief is. 

1. Controleer of de app-configuratie juist is. Uw app-configuratie bestaat uit de volgende velden in het bestand *appsettings.js* . Controleer of deviceId en moduleId correct zijn. Een eenvoudige manier om te controleren, is door naar de Azure IoT Hub-extensie sectie te gaan in Visual Studio code. De waarden in het *appsettings.js* bestand en de sectie IOT hub moeten overeenkomen.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. Controleer in de *appsettings.jsop* bestand of u de IOT hub Connection String hebt ingevoerd en *niet* het IOT hub apparaat connection string, omdat de [Connection String indelingen verschillen](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/).

### <a name="live-video-analytics-working-with-external-modules"></a>Live video Analytics met externe modules

Met live video analyses via de HTTP-extensie processor kan de media grafiek worden uitgebreid om gegevens van andere IoT Edge modules via HTTP te verzenden en te ontvangen met behulp van REST. Net als een [specifiek voor beeld](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension): de media grafiek kan video frames als installatie kopieën verzenden naar een externe Afleidings module, zoals Yolo v3 en resultaten van op JSON gebaseerde analyses ontvangen. In een dergelijke topologie is het doel voor de gebeurtenissen voornamelijk de IoT-hub. In situaties waarin de gebeurtenissen voor afnemen op de hub niet worden weer geven, controleert u het volgende:

* Controleer of de hub waarnaar de media grafiek wordt gepubliceerd en de hub die u bekijkt hetzelfde zijn. Wanneer u meerdere implementaties maakt, kan het zijn dat u met meerdere hubs kunt eindigen en per ongeluk de verkeerde hub kunt controleren op gebeurtenissen.
* Controleer in Visual Studio code of de externe module is geïmplementeerd en wordt uitgevoerd. In de voorbeeld afbeelding hier worden rtspsim en CV IoT Edge modules die extern worden uitgevoerd op de lvaEdge-module.

    ![Scherm opname van de actieve status van modules in azure IoT Hub.](./media/troubleshoot-how-to/iot-hub.png)

* Controleer of u gebeurtenissen naar het juiste URL-eind punt verzendt. De externe AI-container maakt een URL en een poort beschikbaar die deze ontvangt en retourneert de gegevens van POST-aanvragen. Deze URL wordt opgegeven als een `endpoint: url` eigenschap voor de HTTP-extensie processor. Zoals in de [topologie-URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)wordt weer gegeven, wordt het eind punt ingesteld op de para meter voor de deactieve URL. Zorg ervoor dat de standaard waarde voor de [para meter](http://yolov3/score) of de door gegeven waarde nauw keurig is. U kunt controleren of het werkt met behulp van de client-URL (krul).  

    Hier volgt een voor beeld van een Yolo v3-container die wordt uitgevoerd op een lokale computer met het IP-adres 172.17.0.3. Gebruik docker inspecte om het IP-adres te vinden.

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Geretourneerd resultaat:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* Als u een of meer exemplaren van een grafiek gebruikt die gebruikmaken van de HTTP-uitbreidings processor, moet u een frame frequentie filter hebben voor elke HTTP-extensie processor om het aantal frames per seconde (fps) van de videofeed te beheren. 

   In bepaalde situaties, waarbij de CPU of het geheugen van de Edge-computer zeer intensief wordt gebruikt, kunt u bepaalde gebeurtenissen voor de afleiding verliezen. Om dit probleem op te lossen, stelt u een lage waarde in voor de eigenschap maximumFps op het frame frequentie filter. U kunt dit instellen op 0,5 ("maximumFps": 0,5) voor elk exemplaar van de grafiek en het exemplaar vervolgens opnieuw uitvoeren om te controleren of er gebeurtenissen voor inschakeling op de hub zijn.

   U kunt ook een krachtigere Edge-machine verkrijgen met hogere CPU en geheugen.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Meerdere directe methoden parallel: time-out-fout 

Live video Analytics op IoT Edge biedt een directe, op methode gebaseerd programmeer model waarmee u meerdere topologieën en meerdere grafiek exemplaren kunt instellen. Als onderdeel van de topologie en de grafiek instellingen roept u meerdere directe-methode aanroepen aan in de module IoT Edge. Als u deze meerdere methode-aanroepen parallel aanroept, met name de verbindingen die de grafieken starten en stoppen, kan er een time-outfout optreden, zoals de volgende: 

De initialisatie methode voor assembly Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync heeft een uitzonde ring gegenereerd. Micro soft. Azure. devices. common. exceptions. IotHubException: micro soft. Azure. devices. common. exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

U wordt aangeraden directe methoden parallel *niet* te bellen. U kunt ze sequentieel aanroepen (dat wil zeggen, een directe methode aanroepen nadat de vorige is voltooid).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Logboeken verzamelen voor het indienen van een ondersteunings ticket

Wanneer u het probleem niet kunt oplossen met de zelf-begeleide stappen voor probleem oplossing, gaat u naar Azure Portal en [opent u een ondersteunings ticket](../../azure-portal/supportability/how-to-create-azure-support-request.md).

Volg de instructies in de volgende secties om de relevante logboeken te verzamelen die aan het ticket moeten worden toegevoegd. U kunt de logboek bestanden uploaden in het **detail** venster van de ondersteunings aanvraag.

### <a name="use-the-support-bundle-command"></a>De ondersteunings bundel opdracht gebruiken

Wanneer u logboeken van een IoT Edge apparaat wilt verzamelen, is het de eenvoudigste manier om de `support-bundle` opdracht te gebruiken. Met deze opdracht wordt verzameld:

- Module Logboeken
- IoT Edge Security Manager en container engine-logboeken
- JSON-uitvoer Iotedge controleren
- Nuttige informatie over fout opsporing

1. Voer de `support-bundle` opdracht uit met de vlag *--sindsdien* om op te geven hoeveel tijd de logboeken moeten omvatten. Zo ontvangt 2H logboeken voor de afgelopen twee uur. U kunt de waarde van deze vlag wijzigen zodat logboeken voor verschillende Peri Oden worden toegevoegd.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Met deze opdracht maakt u een bestand met de naam *support_bundle.zip* in de map waarin u de opdracht hebt uitgevoerd. 
   
1. Koppel het *support_bundle.zip* -bestand aan het ondersteunings ticket.

### <a name="live-video-analytics-debug-logs"></a>Logboeken voor fout opsporing in live video Analytics

Ga als volgt te werk om de module live video-analyse op IoT Edge te configureren voor het genereren van Logboeken voor fout opsporing:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en ga naar uw IOT-hub.
1. Selecteer **IOT Edge**in het linkerdeel venster.
1. Selecteer de ID van het doel apparaat in de lijst met apparaten.
1. Selecteer boven in het deel venster de optie **modules instellen**.

   ![Scherm opname van de knop ' modules instellen ' in de Azure Portal.](media/troubleshoot-how-to/set-modules.png)

1. Zoek in de sectie **IOT Edge modules** naar en selecteer **lvaEdge**.
1. Selecteer de optie voor het **maken van containers**.
1. Voeg in de sectie **bindingen** de volgende opdracht toe:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Met deze opdracht worden de logboeken-mappen tussen het rand apparaat en de container gebonden. Als u de logboeken op een andere locatie wilt verzamelen, gebruikt u de volgende opdracht, waarbij u **$LOG _LOCATION_ON_EDGE_DEVICE** vervangt door de locatie die u wilt gebruiken:`/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Selecteer **Update**.
1. Selecteer **Controleren + maken**. Een geslaagd validatie bericht wordt geplaatst onder een groene banner.
1. Selecteer **Maken**.
1. Update **module-identiteit** , te verwijzen naar de para meter DebugLogsDirectory, die verwijst naar de map waarin de logboeken worden verzameld:

    a. Selecteer in de tabel **modules** de optie **lvaEdge**.  
    b. Selecteer boven in het deel venster **module identiteit, twee**. Een bewerkbaar deel venster wordt geopend.  
    c. Voeg onder **gewenste sleutel**de volgende sleutel/waarde-paar toe:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Met deze opdracht worden de logboeken-mappen tussen het rand apparaat en de container gebonden. Als u de logboeken op een andere locatie wilt verzamelen, gebruikt u de volgende opdracht, waarbij u **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** vervangt door de locatie die u wilt gebruiken:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE"`  

    d. Selecteer **Opslaan**.

1. Reproduceer het probleem.
1. Maak verbinding met de virtuele machine via de **IOT hub** pagina in de portal.
1. Zip alle bestanden in de map *debugLogs* .

   > [!NOTE]
   > Deze logboek bestanden zijn niet bedoeld voor zelf-diagnose. Ze zijn bedoeld voor het technische team van Azure om uw problemen te analyseren.

   a. In de volgende opdracht moet u **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** vervangen door de locatie van de logboeken voor fout opsporing op het apparaat dat u eerder hebt ingesteld.  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. Koppel het *debugLogs.zip* -bestand aan het ondersteunings ticket.

1. U kunt de logboek verzameling stoppen door de waarde in **module-identiteit** in te stellen op *Null*. Ga terug naar de **dubbele pagina module Identity** en werk de volgende para meter bij als:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: Video-opname op basis van gebeurtenissen in de cloud en afspelen vanuit de Cloud](event-based-video-recording-tutorial.md)
