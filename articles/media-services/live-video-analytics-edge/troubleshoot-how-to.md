---
title: Problemen met live video Analytics op IoT Edge-Azure oplossen
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen met live video Analytics op IoT Edge.
ms.topic: how-to
ms.date: 05/24/2020
ms.openlocfilehash: c235dd27da1d370531c1668c40586d4ae479aec7
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261119"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Problemen met live video Analytics op IoT Edge oplossen

In dit artikel worden de stappen beschreven voor het oplossen van problemen met live video Analytics op IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Oplossen van implementatieproblemen

### <a name="diagnostics"></a>Diagnostiek

Als onderdeel van de implementatie van live video-analyses stelt u Azure-resources in, zoals de IoT Hub en het IoT Edge-apparaat. Als eerste stap voor het oplossen van problemen zorgt u er altijd voor dat de rand juist is ingesteld door de volgende instructies te volgen:

1. [Voer de opdracht ' check ' uit](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [Controleer uw IoT Edge versie](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [Controleer de status van de IoT Edge Security Manager en de bijbehorende logboeken](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [De berichten weer geven die via de IoT Edge hub worden verzonden](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [Containers opnieuw opstarten](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [Controleer uw firewall en poort configuratie regels](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>Problemen vóór de implementatie

Als de rand infrastructuur nauw keurig is, kunt u problemen met het manifest bestand van de implementatie zoeken. Als u live video Analytics wilt implementeren op IoT Edge module op het edge-apparaat naast andere IoT-modules, gebruikt u een implementatie manifest dat de Edge hub, de Edge-agent en andere modules met hun eigenschappen bevat. Als de JSON niet de juiste indeling heeft, kunt u de volgende fout melding krijgen: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Kan JSON niet parseren vanuit bestand: ' <deployment manifest.json> ' voor het argument ' content ' met uitzonde ring: ' extra gegevens: regel 101 kolom 1 (char 5325) '

Als deze fout optreedt, is het raadzaam om het JSON-bestand te controleren op ontbrekende vier Kante haken of andere problemen met de structuur van het bestand. U kunt een-client zoals [Notepad + + met de JSON Viewer-invoeg toepassing](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) of een online hulp programma gebruiken https://jsonformatter.curiousconcept.com/ om de bestands structuur te valideren.

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>Implementatie: problemen vaststellen met media Graph direct-methoden 

Zodra de module live video-analyse op IoT Edge correct op het apparaat Edge is geïmplementeerd, kunt u de media grafiek maken en uitvoeren door [directe methoden](direct-methods.md)aan te roepen. U kunt de portal gebruiken om de diagnose van media Graph via directe methoden uit te voeren:

1. Ga in de portal naar de IoT Hub die is verbonden met uw edge-apparaat.
    1. Zoek in de Blade IoT Hub naar het >voor automatische Apparaatbeheer: IoT Edge.
    1. Als u op IoT Edge klikt, wordt een lijst met rand apparaten weer geven. Kies het apparaat dat u wilt diagnosticeren.
         
        ![Edge-apparaten](./media/troubleshoot-how-to/lva-sample-device.png)
    1. Controleer of de antwoord code 200-OK is. Er zijn verschillende andere antwoord codes voor de [IOT Edge runtime](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime) , zoals:
        1. 400-de implementatie configuratie is onjuist of ongeldig.
        1. 417-er is geen implementatie configuratie ingesteld op het apparaat.
        1. 412-de schema versie in de implementatie configuratie is ongeldig.
        1. 406-het IoT Edge apparaat is offline of stuurt geen status rapporten.
        1. 500-er is een fout opgetreden in de IoT Edge-runtime.
    1. Als u op het apparaat klikt, moet u ook de lijst met verwachte IoT Edge modules weer geven die zijn geïmplementeerd en hun status hebben
    1. Als in de kolommen ' opgegeven in implementatie ' en ' gerapporteerd door apparaat ' ja ' wordt aangegeven, kunt u directe methoden aanroepen in de module live video-analyses op IoT Edge. Klik op de module en u gaat naar een scherm waarin u de gewenste en gerapporteerde eigenschappen kunt controleren en direct methoden kunt aanroepen. 
        1. Als u de gerapporteerde en gewenste eigenschappen controleert, kunt u zien of de module-eigenschappen zijn gesynchroniseerd met de implementatie. Als dat niet het geval is, kunt u de rand opnieuw opstarten 
        1. Gebruik de hand leiding [directe methoden](direct-methods.md) voor het aanroepen van een paar methoden, met name eenvoudige items zoals GraphTopologyList. De hand leiding bevat ook de verwachte nettoladingen voor aanvragen en antwoorden en fout codes. Zodra de eenvoudige directe methoden zijn geslaagd, kunt u ervoor zorgen dat de module live video Analytics Edge functioneel is.
        
        ![Directe methode](./media/troubleshoot-how-to/direct-method.png) 
1. Als u de status 501-code krijgt, controleert u of de naam van de directe methode nauw keurig is. Als de methode naam en de aanvraag lading nauw keurig zijn, moet u resultaten weer geven met succes code = 200. Als de aanvraag lading onjuist is, krijgt u de status = 400 en een reactie lading die de fout code en het bericht aangeeft die het probleem met uw directe methode aanroepen moeten helpen diagnosticeren. 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>Post-implementatie: diagnose logboeken voor problemen tijdens de uitvoering 

De container logboeken voor onze Edge-module moeten diagnostische gegevens hebben<!--<todo:add link to diagnostics doc>--> informatie die u kan helpen bij het opsporen van fouten in uw problemen tijdens module runtime. U kunt de [container logboeken voor problemen](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) en zelf diagnoses controleren, maar als alle bovenstaande controles zijn uitgevoerd en u nog steeds problemen ondervindt, moet u logboeken van het IOT edge apparaat verzamelen [met de opdracht ondersteuning bundel](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) , die verder kan worden geanalyseerd door het team van Azure. U kunt [contact](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) opnemen met ons voor ondersteuning en de verzamelde logboeken indienen.

## <a name="common-error-resolutions"></a>Oplossingen voor algemene problemen

Live video Analytics wordt geïmplementeerd als een IoT Edge module op het edge-apparaat en werkt samen met de IoT Edge agent-en hub-modules. Enkele van de veelvoorkomende fouten die u met de implementatie van live video Analytics krijgt, worden veroorzaakt door de problemen met de onderliggende IoT-infra structuur. Enkele veelvoorkomende fouten die IoT Edge agent en hub kunnen hebben:

1. [IOT Edge agent stopt na ongeveer een minuut](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute).
1. [IOT Edge agent heeft geen toegang tot de installatie kopie van een module (403)](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403).
1. [De module Edge agent rapporteert een leeg configuratie bestand en er worden geen modules gestart op het apparaat](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
1. [IOT Edge hub niet starten](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start).
1. [IOT Edge Security daemon mislukt met een ongeldige hostnaam](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname).
1. [Live video Analytics of een andere aangepaste IOT Edge module kan geen bericht verzenden naar Edge hub met 404-fout](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
1. [IOT Edge module implementeert, verdwijnt vervolgens van apparaat](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-set-up-script-issues"></a>Rand script problemen instellen

Als onderdeel van onze documentatie hebben we een [script ingesteld](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) voor het implementeren van Edge-en cloud-resources om aan de slag te gaan met live video Analytics Edge. In deze sectie zijn fouten vastgelegd die u mogelijk aanmeldt met het script en wordt uitgelegd hoe u fouten kunt opsporen.

Script wordt deels het maken van weinig resources, maar mislukt met het volgende bericht:

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
1. Zorg ervoor dat de volgende uitbrei dingen zijn geïnstalleerd. Vanaf het schrijven van deze hand leiding zijn de versie voor uitbrei dingen als volgt:

    |||
    |---|---|
    |azure-cli   |      2.5.1|
    |opdracht-modules-nspkg         |   2.0.3|
    |baan  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetrie| 1.0.4|
    |Extensions    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Als een van de uitbrei dingen ouder is dan de bovenstaande release nummers, werkt u de extensie bij naar de meest recente versie met behulp van de opdracht:

    ```
    az extension update --name <Extension name>
    ```

    Bijvoorbeeld: `az extension update --name azure-iot`

### <a name="sample-app-issues"></a>Voor beelden van app-problemen

Als onderdeel van onze release hebben we een voor beeld van een .NET-voorbeeld code gegeven om onze ontwikkel aars in de community te laten opbootsden. In deze sectie zijn fouten vastgelegd waarmee u mogelijk te kampen krijgt tijdens het uitvoeren van de voorbeeld code en het opsporen van fouten.

1. Program.cs mislukt met de volgende fout bij het aanroepen van de directe methode:

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. Zorg ervoor dat de [Azure IOT-hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) zijn geïnstalleerd in uw VS-code omgeving en de verbinding met uw IOT hub-installatie. (CTRL + SHIFT + P en kies vervolgens IoT Hub methode selecteren om verbinding te maken met uw abonnement en IoT Hub)
1. Controleer of u een directe methode kunt aanroepen voor de module Edge via VS code (bijvoorbeeld GraphToplogyList aanroepen met de volgende payload {" @apiVersion ": "1,0"}) en de volgende reactie moet worden teruggestuurd. 

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

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Als dit niet het geval is, probeert u het volgende:
    1. Ga naar de opdracht prompt op uw rand apparaat en typ.
    
    ```
    sudo systemctl restart iotedge
    ```

    Hiermee wordt het apparaat met de rand en alle modules opnieuw opgestart. Wacht enkele minuten en voer de volgende stappen uit om te bevestigen dat de modules worden uitgevoerd voordat u de DirectMethod opnieuw probeert te gebruiken.

    ```
    sudo iotedge list
    ```
    1. Als dat niet het geval is, kunt u de virtuele machine of computer opnieuw opstarten.
    1. Als dat niet het geval is, voert u de volgende opdracht uit om een ZIP-bestand op te halen met alle [relevante logboeken](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) die aan die [ondersteunings ticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)moeten worden gekoppeld.

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. Als er een fout bericht met de code 400 wordt weer geven, zorgt u ervoor dat de nettolading van de methode aanroep goed gevormd is volgens de [directe methode](direct-methods.md) gids.
1. Als u de status 200-code krijgt, geeft deze aan dat uw hub goed werkt en dat de module-implementatie juist is en reageert. De volgende stap is om te controleren of de app-configuraties nauw keurig zijn. Uw app-configuratie bestaat uit de volgende velden in het bestand appSettings. json. Controleer of deviceId en moduleId nauw keurig zijn. Een eenvoudige manier om dit te controleren, is via de Azure IoT Hub extension-sectie in de VSCode. De waarden in het bestand appSettings. json en de sectie IoT Hub moeten overeenkomen.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![IOT-HUB](./media/troubleshoot-how-to/iot-hub.png)

1. Ten slotte moet u ervoor zorgen [dat de verbindings](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/) reeks van het IOT hub apparaat in appSettings. json is ingesteld IOT hub verbindings reeks.

### <a name="live-video-analytics-working-with-external-modules"></a>Live video Analytics met externe modules

Met live video analyses via de HTTP-extensie processor kan de media grafiek worden uitgebreid om gegevens van andere IoT Edge modules te verzenden en ontvangen via HTTP met REST.  Net als een [specifiek voor beeld](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension) kan de media grafiek video frames als installatie kopieën verzenden naar een externe Afleidings module, zoals Yolo v3 en terug te ontvangen analyse resultaten op basis van JSON. In een dergelijke topologie is de eind bestemming voor de gebeurtenissen voornamelijk het IoT Hub. In situaties waarin de gebeurtenissen voor afnemen op de hub niet worden weer geven, moet u het volgende controleren:

1. Controleer of de hub waarnaar de media grafiek wordt gepubliceerd, overeenkomt met het item dat u wilt onderzoeken. Als u meerdere implementaties maakt, wordt er een fout opgetreden bij het maken van meerdere hubs en kan het per ongeluk de verkeerde hub controleren op gebeurtenissen.
1. Controleer via VSCode of de externe module is geïmplementeerd en wordt uitgevoerd. In het onderstaande voor beeld zijn rtspsim en CV IoT Edge modules die extern worden uitgevoerd op de lvaEdge-module.

    ![IOT-HUB](./media/troubleshoot-how-to/iot-hub.png)
1. Controleer of u gebeurtenissen naar het juiste URL-eind punt verzendt. De externe AI-container geeft een URL en een poort weer die deze ontvangt en retourneert de gegevens van POST-aanvragen. Deze URL is opgegeven als een eind punt: URL-eigenschap voor de http-extensie processor. Zoals in de [topologie-URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) wordt weer gegeven, is ingesteld op de para meter voor de URL voor dezoneren. Zorg ervoor dat de standaard waarde voor de para meter ( http://yolov3/score) of de door gegeven waarde is nauw keurig is en u kunt testen of deze met krul werkt.  
    1. Een voor beeld: een Yolo v3-container die wordt uitgevoerd op de lokale computer en het IP-adres voor de container is 172.17.0.3 (gebruik docker inspecte om IP-adres te zoeken).

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Geretourneerd resultaat:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. Als u een of meer exemplaren van een grafiek gebruikt die gebruikmaken van de http-extensie processor, moet u een frame frequentie filter hebben voor elke http-extensie processor om de frames per seconde (fps) van de video-feed te beheren. In bepaalde situaties waarbij de CPU/het geheugen van de Edge-computer zeer intensief wordt gebruikt, kunt u bepaalde gebeurtenissen voor de afleiding verliezen. Als u dit wilt verhelpen, stelt u een lage waarde in voor de eigenschap maximumFps op het frame frequentie filter. U kunt dit instellen op 0,5 ("maximumFps": 0,5) voor elk exemplaar van de grafiek en opnieuw uitvoeren om te controleren op gebeurtenissen voor inschakeling op de hub.
    1. U kunt ook een krachtigere Edge-machine verkrijgen met hogere CPU en geheugen.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Meerdere directe methoden parallel: time-out-fout 

Live video Analytics op IoT Edge biedt een directe methode op basis van een programmeer model waarmee u meerdere topologieën en meerdere grafiek exemplaren kunt instellen. Als onderdeel van de topologie en de grafiek instellingen roept u meerdere directe-methode aanroepen aan in de module Edge. Als u deze methode aanroepen met meerdere methoden aanroept, met name de bewerkingen die de grafieken parallel starten en stoppen, kunnen er enkele time-outstoringen optreden, zoals hieronder. 

Assembly-initialisatie methode micro soft. media. LiveVideoAnalytics. test. feature. Edge. AssemblyInitializer. InitializeAssemblyAsync heeft een uitzonde ring gegenereerd. Micro soft. Azure. devices. common. exceptions. IotHubException: micro soft. Azure. devices. common. exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

We raden u aan om directe methoden niet op parallelle wijze aan te roepen, maar dit op een sequentiële manier te doen, d.w.z.  een directe aanroep van de methode is pas nadat het vorige is voltooid. 

## <a name="next-steps"></a>Volgende stappen

[Zelf studie: video-opname op basis van gebeurtenissen in de Cloud en afspelen vanuit de Cloud](event-based-video-recording-tutorial.md)
