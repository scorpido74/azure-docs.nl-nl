---
title: Azure Service Fabric reverse proxy diagnostics Azure Service Fabric reverse proxy diagnostics Azure Service Fabric reverse proxy diagnostics Azure Service
description: Meer informatie over het bewaken en diagnosticeren van aanvraagverwerking bij de omgekeerde proxy voor een Azure Service Fabric-toepassing.
author: kavyako
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: bbc1fe5a76ecb5720bc49e0a082d5e9151b403d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645460"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Verwerking van aanvragen controleren en diagnosticeren bij de omgekeerde proxy

Vanaf de 5.7 release van Service Fabric zijn reverse proxy-gebeurtenissen beschikbaar voor verzameling. De gebeurtenissen zijn beschikbaar in twee kanalen, één met alleen foutgebeurtenissen met betrekking tot foutloos reageren op de reverse proxy en het tweede kanaal met verbose-gebeurtenissen met vermeldingen voor zowel geslaagde als mislukte aanvragen.

Raadpleeg [Omgekeerde proxygebeurtenissen verzamelen](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) om het verzamelen van gebeurtenissen uit deze kanalen in lokale en Azure Service Fabric-clusters mogelijk te maken.

## <a name="troubleshoot-using-diagnostics-logs"></a>Problemen oplossen met diagnostische logboeken
Hier volgen enkele voorbeelden over het interpreteren van de algemene foutlogboeken die men kan tegenkomen:

1. Reverse proxy returns response status code 504 (Timeout).

    Een reden kan zijn te wijten aan de dienst niet te antwoorden binnen de aanvraag time-out periode.
   De eerste gebeurtenis hieronder registreert de details van het verzoek ontvangen op de reverse proxy. 
   De tweede gebeurtenis geeft aan dat de aanvraag is mislukt tijdens het doorsturen naar de service, als gevolg van "interne fout = ERROR_WINHTTP_TIMEOUT" 

    Het laadvermogen omvat:

   * **traceid**: Deze GUID kan worden gebruikt om alle gebeurtenissen die overeenkomen met één aanvraag te correleren. In de onderstaande twee gebeurtenissen, de traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, wat impliceert dat ze behoren tot hetzelfde verzoek.
   * **requestUrl**: De URL (Reverse proxy URL) waarnaar de aanvraag is verzonden.
   * **werkwoord**: HTTP-werkwoord.
   * **remoteAddress**: Adres van de klant die de aanvraag verzendt.
   * **resolvedServiceUrl:** URL van serviceeindpunt waarop de binnenkomende aanvraag is opgelost. 
   * **errorDetails**: Aanvullende informatie over de fout.

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. Reverse proxy returns response status code 404 (Not Found). 
    
    Hier is een voorbeeldgebeurtenis waarbij omgekeerde proxy 404 retourneert omdat het overeenkomende serviceeindpunt niet is gevonden.
    De payload inzendingen van belang hier zijn:
   * **processRequestPhase**: geeft de fase aan tijdens de verwerking van aanvragen wanneer de fout is opgetreden, ***TryGetEndpoint*** d.w.z. terwijl u het serviceeindpunt probeert te ophalen om naar door te sturen. 
   * **errorDetails:** geeft de zoekcriteria voor eindpunten weer. Hier u zien dat de listenerName opgegeven = **FrontEndListener,** terwijl de replica eindpunt lijst bevat alleen een luisteraar met de naam **OldListener**.
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     Een ander voorbeeld waar reverse proxy 404 Not Found kan retourneren is: ApplicationGateway\Http-configuratieparameter **SecureOnlyMode** is ingesteld op true met de omgekeerde proxy luisteren op **HTTPS,** maar alle replica eindpunten zijn onveilig (luisteren op HTTP).
     Reverse proxy retourneert 404 omdat het geen eindpunt kan vinden om te luisteren op HTTPS om de aanvraag door te sturen. Het analyseren van de parameters in de gebeurtenis payload helpt om het probleem te beperken:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. Verzoek om de omgekeerde proxy mislukt met een time-out fout. 
    De gebeurtenislogboeken bevatten een gebeurtenis met de gegevens van de ontvangen aanvraag (hier niet weergegeven).
    De volgende gebeurtenis laat zien dat de service heeft gereageerd met een statuscode van 404 en dat reverse proxy een nieuwe oplossing initieert. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Bij het verzamelen van alle gebeurtenissen, zie je een trein van gebeurtenissen met elke vastberaden en voorwaartse poging.
    De laatste gebeurtenis in de reeks toont de verwerking van de aanvraag is mislukt met een time-out, samen met het aantal geslaagde resolve-pogingen.
    
    > [!NOTE]
    > Het wordt aanbevolen om de verbose-kanaalgebeurtenisverzameling standaard uitgeschakeld te houden en deze in te schakelen voor het oplossen van problemen op basis van problemen.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Als verzameling alleen is ingeschakeld voor kritieke/foutgebeurtenissen, ziet u één gebeurtenis met details over de time-out en het aantal resolve-pogingen. 
    
    Services die van plan zijn een statuscode van 404 terug te sturen naar de gebruiker, moeten in het antwoord een "X-ServiceFabric"-header toevoegen. Nadat de koptekst aan het antwoord is toegevoegd, stuurt de omgekeerde proxy de statuscode terug naar de client.  

4. Gevallen waarin de client de aanvraag heeft losgekoppeld.

    De volgende gebeurtenis wordt geregistreerd wanneer reverse proxy het antwoord op de client doorstuurt, maar de client de verbinding verbreekt:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. Reverse Proxy retourneert 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    FABRIC_E_SERVICE_DOES_NOT_EXIST fout wordt geretourneerd als het URI-schema niet is opgegeven voor het serviceeindpunt in het servicemanifest.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Als u het probleem wilt oplossen, geeft u het URI-schema in het manifest op.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Gebeurtenissen met betrekking tot verwerking van websocketaanvragen worden momenteel niet geregistreerd. Dit zal worden toegevoegd in de volgende release.

## <a name="next-steps"></a>Volgende stappen
* [Aggregatie en verzameling van gebeurtenissen met Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) voor het inschakelen van logboekverzameling in Azure-clusters.
* Zie [Lokaal controleren en diagnosticeren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)om servicefabric-gebeurtenissen in Visual Studio weer te geven.
* Raadpleeg [Reverse proxy configureren om verbinding te maken met beveiligde services](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) voor sjabloonvoorbeelden van Azure Resource Manager om veilige reverse proxy te configureren met de verschillende validatieopties voor servicecertificaten.
* Lees [Service Fabric reverse proxy](service-fabric-reverseproxy.md) voor meer informatie.
