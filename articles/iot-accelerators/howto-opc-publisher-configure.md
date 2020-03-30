---
title: OPC Publisher configureren - Azure | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u OPC Publisher configureert om wijzigingen in OPC UA-knooppuntgegevens, OPC UA-gebeurtenissen op te geven die moeten worden gepubliceerd en ook de telemetrie-indeling.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0ebbf0d41c05f71c571d9665903ba4ba44f71bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198800"
---
# <a name="configure-opc-publisher"></a>OPC Publisher configureren

U OPC Publisher configureren om op te geven:

- De gegevens van het OPC UA-knooppunt worden gewijzigd om te publiceren.
- De OPC UA-evenementen die moeten worden gepubliceerd.
- De telemetrie-indeling.

U OPC Publisher configureren met configuratiebestanden of met behulp van methodeaanroepen.

## <a name="use-configuration-files"></a>Configuratiebestanden gebruiken

In deze sectie worden opties beschreven voor het configureren van opc UA-knooppuntpublicatie met configuratiebestanden.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Een configuratiebestand gebruiken om wijzigingen in publicatiegegevens te configureren

De eenvoudigste manier om de OPC UA-knooppunten te configureren om te publiceren, is met een configuratiebestand. De bestandsindeling van de configuratie is gedocumenteerd in [publishednodes.json](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) in de repository.

De syntaxis van configuratiebestanden is in de loop van de tijd gewijzigd. OPC Publisher leest nog steeds oude indelingen, maar zet ze om in de nieuwste indeling wanneer de configuratie blijft bestaan.

In het volgende voorbeeld wordt de indeling van het configuratiebestand weergegeven:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Een configuratiebestand gebruiken om publicatiegebeurtenissen te configureren

Als u OPC UA-gebeurtenissen wilt publiceren, gebruikt u hetzelfde configuratiebestand als voor gegevenswijzigingen.

In het volgende voorbeeld ziet u hoe u publiceren configureert voor gebeurtenissen die worden gegenereerd door de [SimpleEvents-server.](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/SimpleEvents/Server) De SimpleEvents server is te vinden in de [OPC Foundation repository](https://github.com/OPCFoundation/UA-.NETStandard) is:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>Methodeaanroepen gebruiken

In deze sectie wordt beschreven welke methode aanroepen u gebruiken om OPC Publisher te configureren.

### <a name="configure-using-opc-ua-method-calls"></a>Configureren met OPC UA-methodeaanroepen

OPC Publisher bevat een OPC UA Server, die toegankelijk is op poort 62222. Als de hostnaam **uitgever**is, is `opc.tcp://publisher:62222/UA/Publisher`het eindpunt URI: .

Dit eindpunt legt de volgende vier methoden bloot:

- PublishNode
- Ongedaan maken Node
- GetPublishedNodes
- IoT-hubdirectmethode

### <a name="configure-using-iot-hub-direct-method-calls"></a>Configureren met behulp van directe aanroepen van iot-hub

OPC Publisher implementeert de volgende IoT Hub directe methode aanroepen:

- PublishNodes
- Publicatieknooppunten ongedaan maken
- Alle publicatiealle knooppunten ongedaan maken
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo GetInfo

Het formaat van de JSON-payload van de methodeaanvraag en -antwoorden wordt gedefinieerd in [opcpublisher/HubMethodModel.cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs).

Als u een onbekende methode op de module aanroept, reageert deze met een tekenreeks die zegt dat de methode niet is geïmplementeerd. U een onbekende methode aanroepen als een manier om de module te pingen.

### <a name="configure-username-and-password-for-authentication"></a>Gebruikersnaam en wachtwoord configureren voor verificatie

De verificatiemodus kan worden ingesteld via een directe iot-hub-aanroepen van de methode. De payload moet de eigenschap **OpcAuthenticationMode** en de gebruikersnaam en het wachtwoord bevatten:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

Het wachtwoord wordt versleuteld door de IoT Hub Workload Client en opgeslagen in de configuratie van de uitgever. Als u de verificatie terug wilt wijzigen in anoniem, gebruikt u de methode met de volgende payload:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Als de eigenschap **OpcAuthenticationMode** niet is ingesteld in de payload, blijven de verificatie-instellingen ongewijzigd in de configuratie.

## <a name="configure-telemetry-publishing"></a>Publicatie van telemetrie configureren

Wanneer OPC Publisher een melding ontvangt van een waardewijziging in een gepubliceerd knooppunt, genereert dit een JSON-opgemaakt bericht dat naar IoT Hub wordt verzonden.

U de inhoud van dit opgemaakte JSON-opmaakbericht configureren met behulp van een configuratiebestand. Als er geen configuratiebestand `--tc` is opgegeven met de optie, wordt een standaardconfiguratie gebruikt die compatibel is met de versneller van de [verbonden fabrieksoplossing.](https://github.com/Azure/azure-iot-connected-factory)

Als OPC Publisher is geconfigureerd voor batchberichten, worden ze verzonden als een geldige JSON-array.

De telemetrie is afgeleid van de volgende bronnen:

- De knooppuntconfiguratie van OPC Publisher voor het knooppunt
- Het **object MonitoredItem** van de OPC UA-stack waarvoor OPC Publisher een melding heeft ontvangen.
- Het argument is doorgegeven aan deze melding, die details geeft over de wijziging van de gegevenswaarde.

De telemetrie die in het opgemaakte JSON-geformatteerd bericht wordt geplaatst, is een selectie van belangrijke eigenschappen van deze objecten. Als u meer eigenschappen nodig hebt, moet u de codebasis van OPC Publisher wijzigen.

De syntaxis van het configuratiebestand is als volgt:

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is omitted (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC Publisher configureren, is de voorgestelde volgende stap om te leren hoe [opc Publisher wordt uitgevoerd.](howto-opc-publisher-run.md)
