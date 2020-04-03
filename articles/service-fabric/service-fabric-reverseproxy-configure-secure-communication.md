---
title: Azure Service Fabric reverse proxy secure communication Azure Service Fabric reverse proxy secure communication Azure Service Fabric reverse proxy secure communication Azure Service
description: Configureer omgekeerde proxy om veilige end-to-end communicatie in een Azure Service Fabric-toepassing mogelijk te maken.
author: kavyako
ms.topic: conceptual
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 61a8d1e766ea576f7d2984add239b0da7e2e8183
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617113"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Verbinding maken met een beveiligde service met de omgekeerde proxy

In dit artikel wordt uitgelegd hoe u een veilige verbinding maken tussen de reverse proxy en services, waardoor een end-to-end secure kanaal mogelijk is. Zie [Proxy omkeren in Azure Service Fabric](service-fabric-reverseproxy.md) voor meer informatie over reverse proxy.

Verbinding maken met beveiligde services wordt alleen ondersteund wanneer reverse proxy is geconfigureerd om te luisteren op HTTPS. Dit artikel gaat ervan uit dat dit het geval is.
Raadpleeg [Reverse Proxy instellen in Azure Service Fabric](service-fabric-reverseproxy-setup.md) om de omgekeerde proxy in Service Fabric te configureren.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Veilige verbindingsinrichting tussen de omgekeerde proxy en services 

### <a name="reverse-proxy-authenticating-to-services"></a>Reverse proxy authenticeren naar services:
De omgekeerde proxy identificeert zich met services met behulp van het certificaat. Voor Azure-clusters is het certificaat opgegeven met de eigenschap ***reverseProxyCertificate*** in de sectie [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [Resourcetype](../azure-resource-manager/templates/template-syntax.md) van de resourcemanagersjabloon. Voor zelfstandige clusters wordt het certificaat opgegeven met het ***ReverseProxyCertificate*** of de eigenschap ***ReverseProxyCertificateCommonNames*** in de sectie **Beveiliging** van ClusterConfig.json. Zie [Omgekeerde proxy inschakelen op zelfstandige clusters](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters)voor meer informatie. 

Services kunnen de logica implementeren om het certificaat te verifiëren dat wordt gepresenteerd door de omgekeerde proxy. De services kunnen de geaccepteerde clientcertificaatgegevens opgeven als configuratie-instellingen in het configuratiepakket. Dit kan worden gelezen tijdens runtime en worden gebruikt om het certificaat te valideren dat wordt gepresenteerd door de omgekeerde proxy. Raadpleeg [Toepassingsparameters beheren](service-fabric-manage-multiple-environment-app-configuration.md) om de configuratie-instellingen toe te voegen. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Reverse proxy verifying the service's identity via the certificate presented by the service:
Reverse proxy ondersteunt het volgende beleid voor het valideren van servercertificaten van de certificaten die worden gepresenteerd door services: Geen, ServiceCommonNameAndIssuer en ServiceCertificateThumbprints.
Als u het beleid wilt selecteren voor omgekeerde proxy die u wilt gebruiken, geeft u het **Toepassingscertificaatvalidatiebeleid** op in de sectie **ApplicationGateway/Http** onder [fabricSettings](service-fabric-cluster-fabric-settings.md).

In de volgende sectie worden configuratiedetails voor elk van deze opties weergegeven.

### <a name="service-certificate-validation-options"></a>Validatieopties voor servicecertificaten 

- **Geen:** Reverse proxy slaat verificatie van het proxied servicecertificaat over en stelt de beveiligde verbinding in. Dit is de standaardinstelling.
Geef het **ApplicationCertificateValidationPolicy** op met waarde **Geen** in de sectie [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**: Reverse proxy verifieert het certificaat dat door de service wordt gepresenteerd op basis van de algemene naam van het certificaat en de duimafdruk van de directe uitgever: Geef het **ApplicationCertificateValidationPolicy** op met waarde **ServiceCommonNameAndIssuer** in de sectie [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Als u de lijst met algemene naam en duimafdrukken van de uitgever wilt opgeven, voegt u een sectie [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) toe onder **fabricSettings**, zoals hieronder wordt weergegeven. Meerdere gemeenschappelijke naam- en emittentduimafdrukparen kunnen worden toegevoegd in de **parametersarray.** 

   Als de reverse proxy van het eindpunt verbinding maakt met een certificaat met de algemene naam en de duimafdruk van de uitgever komt overeen met een van de waarden die hier zijn opgegeven, wordt een TLS-kanaal ingesteld.
   Als de certificaatgegevens niet overeenkomen, mislukt de reverse proxy het verzoek van de client met een statuscode van 502 (Bad Gateway). De HTTP-statusregel bevat ook de zinsnede 'Ongeldig SSL-certificaat'. 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints:** Reverse proxy verifieert het proxied service certificaat op basis van de duimafdruk. U ervoor kiezen om deze route te gaan wanneer de services zijn geconfigureerd met zelfondertekende certificaten: Geef het **ApplicationCertificateValidationPolicy** op met value **ServiceCertificateThumbprints** in de sectie [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Geef ook de duimafdrukken op met een **ServiceCertificateThumbprints-vermelding** in de sectie **ApplicationGateway/Http.** Meerdere duimafdrukken kunnen worden opgegeven als een door komma's gescheiden lijst in het waardeveld, zoals hieronder wordt weergegeven:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Als de duimafdruk van het servercertificaat in deze config-vermelding wordt vermeld, volgt de reverse proxy de TLS-verbinding op. Anders wordt de verbinding beëindigd en wordt het verzoek van de client met een 502 (Bad Gateway) mislukt. De HTTP-statusregel bevat ook de zinsnede 'Ongeldig SSL-certificaat'.

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Logica voor de selectie van eindpunten wanneer services zowel beveiligde als onbeveiligde eindpunten blootleggen
Servicefabric ondersteunt het configureren van meerdere eindpunten voor een service. Zie [Resources opgeven in een servicemanifest voor](service-fabric-service-manifest-resources.md)meer informatie .

Reverse proxy selecteert een van de eindpunten om de aanvraag door te sturen op basis van de parameter **ListenerName** query in de [service URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Als de parameter **ListenerName** niet is opgegeven, kan de omgekeerde proxy elk eindpunt uit de lijst met eindpunten kiezen. Afhankelijk van de eindpunten die voor de service zijn geconfigureerd, kan het geselecteerde eindpunt een HTTP- of HTTPS-eindpunt zijn. Er kunnen scenario's of vereisten zijn waarin u wilt dat de omgekeerde proxy in een "secure-only-modus" werkt. dat wil zeggen dat u niet wilt dat de beveiligde omgekeerde proxy aanvragen doorstuurt naar onbeveiligde eindpunten. Als u de omgekeerde proxy wilt instellen op de modus Alleen beveiligd, geeft u de **secureOnlyMode-configuratievermelding** op met **waarde true** in de sectie [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> Wanneer een **SecureOnlyMode**client een **ListenerName** heeft opgegeven die overeenkomt met een HTTP-eindpunt(onbeveiligd) kanaal, mislukt de omgekeerde proxy de aanvraag met een HTTP-statuscode (Niet gevonden) 404 (Niet gevonden).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Clientcertificaatverificatie instellen via de omgekeerde proxy
TLS-beëindiging gebeurt bij de omgekeerde proxy en alle clientcertificaatgegevens gaan verloren. Geef de instelling **ForwardClientCertificate** op in de sectie [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) voor de services die clientcertificaatverificatie uitvoeren.

1. Wanneer **ForwardClientCertificate** is ingesteld op **false,** zal reverse proxy het clientcertificaat niet aanvragen tijdens de TLS-handshake met de client.
Dit is de standaardinstelling.

2. Wanneer **ForwardClientCertificate** is ingesteld op **true,** vraagt reverse proxy het certificaat van de client aan tijdens de TLS-handshake met de client.
Vervolgens worden de clientcertificaatgegevens doorgestuurd in een aangepaste HTTP-header met de naam **X-Client-Certificate.** De kopwaarde is de basis64 gecodeerde PEM-formaatreeks van het certificaat van de client. De service kan de aanvraag na het inspecteren van de certificaatgegevens met de juiste statuscode opvolgen/mislukken.
Als de client geen certificaat presenteert, stuurt de proxy een lege koptekst door en laat de service de aanvraag afhandelen.

> [!NOTE]
> Reverse proxy is slechts een expeditere. Het zal geen validatie van het certificaat van de klant uitvoeren.


## <a name="next-steps"></a>Volgende stappen
* [Omgekeerde proxy instellen en configureren op een cluster](service-fabric-reverseproxy-setup.md).
* Verwijzen naar [Omgekeerde proxy configureren om verbinding te maken met beveiligde services](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Zie een voorbeeld van HTTP-communicatie tussen services in een [voorbeeldproject op GitHub.](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Externe proceduregesprekken met Betrouwbare Services remoting](service-fabric-reliable-services-communication-remoting.md)
* [Web API die OWIN gebruikt in Betrouwbare Services](service-fabric-reliable-services-communication-webapi.md)
* [Clustercertificaten beheren](service-fabric-cluster-security-update-certs-azure.md)
