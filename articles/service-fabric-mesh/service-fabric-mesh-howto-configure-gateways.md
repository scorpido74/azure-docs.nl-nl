---
title: Een gateway configureren voor routeaanvragen
description: Meer informatie over het configureren van de gateway die binnenkomend verkeer voor uw toepassing(en) verwerkt dat wordt uitgevoerd op Service Fabric Mesh.
author: dkkapur
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: ec408403d4baa0f211c6bfe867a15c96513693cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461965"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Een gatewaybron configureren om aanvragen te routeren

Een Gateway-bron wordt gebruikt om binnenkomend verkeer te routeren naar het netwerk waarin uw toepassing is. Configureer het om regels op te geven via welke aanvragen worden gericht op specifieke services of eindpunten op basis van de structuur van de aanvraag. Zie [Inleiding tot netwerken in Service Fabric Mesh](service-fabric-mesh-networks-and-gateways.md) voor meer informatie over netwerken en gateways in Mesh. 

Gatewayresources moeten worden gedeclareerd als onderdeel van uw implementatiesjabloon (JSON of yaml) en zijn afhankelijk van een netwerkbron. In dit document worden de verschillende eigenschappen beschreven die voor uw gateway kunnen worden ingesteld en wordt een voorbeeldgatewayconfig.

## <a name="options-for-configuring-your-gateway-resource"></a>Opties voor het configureren van uw Gateway-bron

Aangezien de Gateway-bron dient als een brug tussen het netwerk van `open` uw toepassing en het netwerk van de onderliggende infrastructuur (het netwerk). U hoeft er slechts één te configureren (in de mesh-preview is er een limiet van één gateway per app). De declaratie voor de resource bestaat uit twee hoofdonderdelen: resourcemetadata en de eigenschappen. 

### <a name="gateway-resource-metadata"></a>Metagegevens gatewaybron

Een gateway wordt gedeclareerd met de volgende metagegevens:
* `apiVersion`- moet worden ingesteld op "2018-09-01-preview" (of later, in de toekomst)
* `name`- een tekenreeksnaam voor deze gateway
* `type`- "Microsoft.ServiceFabricMesh/gateways"
* `location`- moet worden ingesteld op de locatie van uw app / netwerk; meestal zal een verwijzing naar de locatie parameter in uw implementatie
* `dependsOn`- het netwerk waarvoor deze gateway zal dienen als een ingress point voor

Zo ziet het eruit in een JSON-implementatiesjabloon (Azure Resource Manager): 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Gateway-eigenschappen

De sectie Eigenschappen wordt gebruikt om de netwerken te definiëren waartussen de gateway zich bevindt en de regels voor routeringsaanvragen. 

#### <a name="source-and-destination-network"></a>Bron- en bestemmingsnetwerk 

Elke gateway `sourceNetwork` vereist `destinationNetwork`een en . Het bronnetwerk wordt gedefinieerd als het netwerk van waaruit uw app binnenkomende aanvragen ontvangt. De naam eigenschap moet altijd worden ingesteld op "Open". Het doelnetwerk is het netwerk waarop de aanvragen zijn gericht. De naamwaarde hiervoor moet worden ingesteld op de resourcenaam van het lokale netwerk van uw app (moet volledige verwijzing naar de bron bevatten). Zie hieronder voor een voorbeeld config van hoe dit eruit ziet voor een implementatie in een netwerk genaamd "myNetwork".

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Regels 

Een gateway kan meerdere routeringsregels hebben die aangeven hoe binnenkomend verkeer wordt verwerkt. Een routeringsregel definieert de relatie tussen de luisterpoort en het doeleindpunt voor een bepaalde toepassing. Voor TCP-routeringsregels is er een 1:1-toewijzing tussen Poort:Eindpunt. Voor HTTP-routeringsregels u complexere routeringsregels instellen die het pad van de aanvraag en optioneel kopteksten onderzoeken om te bepalen hoe de aanvraag wordt gerouteerd. 

Routeringsregels worden per poort opgegeven. Elke inkomende poort heeft zijn eigen reeks regels binnen het eigenschappengedeelte van uw gatewayconfig. 

#### <a name="tcp-routing-rules"></a>TCP-routeringsregels 

Een TCP-routeringsregel bestaat uit de volgende eigenschappen: 
* `name`- verwijzing naar de regel die elke tekenreeks van uw keuze kan zijn 
* `port`- poort om te luisteren naar binnenkomende aanvragen 
* `destination`- eindpuntspecificatie die `applicationName` `serviceName`omvat `endpointName`, en , voor wanneer de aanvragen moeten worden doorgestuurd naar

Hier volgt een voorbeeld van TCP-routeringsregel:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>HTTP-routeringsregels 

Een HTTP-routeringsregel bestaat uit de volgende eigenschappen: 
* `name`- verwijzing naar de regel die elke tekenreeks van uw keuze kan zijn 
* `port`- poort om te luisteren naar binnenkomende aanvragen 
* `hosts`- een reeks beleidsregels die van toepassing zijn op aanvragen die naar de verschillende "hosts" op de hierboven vermelde poort komen. Hosts zijn de set toepassingen en services die mogelijk in het netwerk worden uitgevoerd en kunnen binnenkomende aanvragen, d.w.z. een web-app, weergeven. Hostbeleid wordt in volgorde geïnterpreteerd, dus u moet het volgende maken in aflopende specificiteitsniveaus
    * `name`- de DNS-naam van de host waarvoor de volgende routeringsregels zijn opgegeven. Met behulp van "*" hier zou leiden regels voor alle hosts.
    * `routes`- een scala aan beleidsregels voor deze specifieke host
        * `match`- specificatie van de inkomende aanvraagstructuur voor de toepassing van deze regel op basis van een`path`
            * `path`- bevat `value` een (inkomende URI), `rewrite` (hoe u wilt dat `type` het verzoek wordt doorgestuurd), en een (kan momenteel alleen worden "Prefix")
            * `header`- is een optionele array met koptekstwaarden die overeenkomen met de koptekst van het verzoek, zodat als de aanvraag overeenkomt met de padspecificatie (hierboven).
              * elk item `name` bevat (tekenreeksnaam van `value` de koptekst die overeenkomt), (tekenreekswaarde van de koptekst in de aanvraag) en een `type` (kan momenteel alleen 'Exact' zijn)
        * `destination`- indien het verzoek overeenkomt, wordt het naar deze bestemming `applicationName` `serviceName`gerouteerd, die is aangegeven met behulp van een , en`endpointName`

Hier volgt een voorbeeld http-routeringsregel die van toepassing is op aanvragen die op poort 80 worden weergegeven, op alle hosts die worden bediend door apps in dit netwerk. Als de URL van het verzoek een structuur heeft `<IPAddress>:80/pickme/<requestContent>`die overeenkomt met de `myListener` padspecificatie, d.w.z. , wordt deze naar het eindpunt geleid.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Voorbeeldconfig voor een Gateway-bron 

Hier is wat een volledige Gateway resource config eruit ziet (dit is aangepast van de inbinnendringen monster beschikbaar in de [Mesh monsters repo):](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Deze gateway is geconfigureerd voor een Linux applicatie, "meshAppLinux", die bestaat uit ten minste twee diensten, "helloWorldService" en "counterService", die luistert op poort 80. Afhankelijk van de URL-structuur van de binnenkomende aanvraag wordt het verzoek doorgestuurd naar een van deze services. 
* "\<IPAddress>:80/helloWorld/\<request\>" zou resulteren in een verzoek dat wordt gericht aan de "helloWorldListener" in de helloWorldService. 
* "\<IPAddress>:80/counter/\<request\>" zou resulteren in een verzoek dat wordt gericht aan de "counterListener" in de counterService. 

## <a name="next-steps"></a>Volgende stappen
* Het [voorbeeld Van Ingress implementeren](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) om gateways in actie te zien
