---
title: De structuur van Azure Dashboards | Microsoft Documenten
description: Loop door de JSON-structuur van een Azure-dashboard met behulp van een voorbeelddashboard. Inclusief verwijzing naar resourceeigenschappen.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/20/2019
ms.author: mblythe
ms.openlocfilehash: ad0d3a1bf2c293039df3bba3aa18da7d6e7dd0a5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459216"
---
# <a name="the-structure-of-azure-dashboards"></a>Structuur van Azure-dashboards
Dit document loopt door de structuur van een Azure-dashboard en gebruikt het volgende dashboard als voorbeeld:

![voorbeelddashboard](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Aangezien gedeelde [Azure-dashboards resources zijn,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)kan dit dashboard worden weergegeven als JSON.  De volgende JSON vertegenwoordigt het dashboard hierboven gevisualiseerd.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

## <a name="common-resource-properties"></a>Algemene resourceeigenschappen

Laten we de relevante delen van de JSON opsplitsen.  De eigenschappen op het hoogste niveau, __id,__ __naam,__ __type,__ __locatie__en __tags__ worden gedeeld in alle Azure-brontypen. Dat wil zeggen, ze hebben niet veel te maken met de inhoud van het dashboard.

### <a name="the-id-property"></a>De eigenschap ID

De Azure-bron-id, onder voorbehoud van de [naamgevingsconventies van Azure-resources](/azure/architecture/best-practices/resource-naming). Wanneer de portal een dashboard maakt, kiest het over het algemeen een ID in de vorm van een guid, maar u bent vrij om elke geldige naam te gebruiken wanneer u ze programmatisch maakt. 

### <a name="the-name-property"></a>De eigenschap naam
De naam is het segment van de resource-id dat geen gegevens over het abonnement, het brontype of de brongroep bevat. In wezen is het het laatste segment van de resource-ID.

### <a name="the-type-property"></a>De eigenschap type
Alle dashboards zijn van het type __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>De locatieeigenschap
In tegenstelling tot andere bronnen hebben dashboards geen runtime-component.  Voor dashboards geeft de locatie de primaire geografische locatie aan die de JSON-weergave van het dashboard opslaat. De waarde moet een van de locatiecodes zijn die kunnen worden opgehaald met behulp van de [locatie-API op de bron van abonnementen](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>De eigenschap tags
Tags zijn een veelvoorkomend kenmerk van Azure-resources waarmee u uw resource ordenen op willekeurige naamwaardenparen. Voor dashboards is er een speciale tag genaamd __hidden-title__. Als deze eigenschap op uw dashboard is gevuld, wordt deze gebruikt als weergavenaam voor uw dashboard in de portal. Azure-bron-id's kunnen niet worden hernoemd, maar tags wel. Deze tag geeft u een manier om een renamable weergavenaam voor uw dashboard te hebben.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Het object Eigenschappen
Het eigenschappenobject bevat twee eigenschappen, __lenzen__ en __metagegevens.__ De eigenschap __lenzen__ bevat informatie over de tegels op het dashboard.  De __eigenschap metagegevens__ is er voor mogelijke toekomstige functies.

### <a name="the-lenses-property"></a>De lenzen eigenschap
De eigenschap __lenzen__ bevat het dashboard. Houd er rekening mee dat het object lenzen in dit voorbeeld één eigenschap bevat die "0" wordt genoemd. Lenzen zijn een groeperingsconcept dat momenteel niet in dashboards wordt geïmplementeerd. Voor nu, al uw dashboards hebben deze ene eigenschap op de lens object, nogmaals, genaamd "0".

### <a name="the-lens-object"></a>Het lensobject
Het object onder de "0" bevat twee eigenschappen, __volgorde__ en __onderdelen.__  In de huidige versie van dashboards is __de volgorde__ altijd 0. De eigenschap __onderdelen__ bevat een object dat de afzonderlijke onderdelen (ook wel tegels genoemd) op het dashboard definieert.

Het __object onderdelen__ bevat een eigenschap voor elk onderdeel, waarbij de naam van de eigenschap een getal is. Dit aantal is niet significant. 

### <a name="the-part-object"></a>Het onderdeelobject
Elk afzonderlijk onderdeelobject heeft een __positie__en __metagegevens__.

### <a name="the-position-object"></a>Het positieobject
De __eigenschap positie__ bevat de grootte- en locatiegegevens voor het deel, uitgedrukt in __x,__ __y,__ __rowSpan__en __colSpan__. De waarden zijn in termen van rastereenheden. Deze rastereenheden zijn zichtbaar wanneer het dashboard zich in de pasmodus bevindt, zoals hier wordt weergegeven. Als u wilt dat een tegel een breedte heeft van twee rastereenheden, een hoogte van één rastereenheid en een locatie in de linkerbovenhoek van het dashboard, ziet het positieobject er als volgt uit:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![rastereenheden](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Het metagegevensobject
Elk onderdeel heeft een eigenschap metagegevens, een object heeft slechts één vereiste eigenschap die __type wordt genoemd.__ Deze tekenreeks vertelt de portal welke tegel moet worden weergegeven. Ons voorbeelddashboard gebruikt dit soort tegels:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart`– Wordt gebruikt om monitoringstatistieken weer te geven
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart`- Wordt gebruikt om te worden weergegeven met tekst of afbeeldingen met basisopmaak voor lijsten, links, enz.
1. `Extension[azure]/HubsExtension/PartType/VideoPart`– Wordt gebruikt om video's van YouTube, Channel9 en elk ander type video weer te geven dat werkt in een HTML-videotag.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart`– Wordt gebruikt om de naam en status van een virtuele Azure-machine weer te geven.

Elk type onderdeel heeft zijn eigen configuratie. De mogelijke configuratie-eigenschappen worden __inputs__, __instellingen__en __asset__genoemd . 

### <a name="the-inputs-object"></a>Het invoerobject
Het object inputs bevat over het algemeen informatie die een tegel aan een broninstantie bindt.  Het onderdeel virtuele machine in ons voorbeelddashboard bevat één invoer die de Azure-bron-id gebruikt om de binding uit te drukken.  Deze resource-id-indeling is consistent voor alle Azure-bronnen.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Het gedeelte met de grafiek heeft één invoer die de resource uitdrukt waaraan moet worden gekoppeld, evenals informatie over de metrische gegevens die worden weergegeven. Hier is de invoer voor de tegel waarop de statistieken Netwerk in en netwerk uit worden weergegeven.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
                {
                    "name": "Network In",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                },
                {
                    "name": "Network Out",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                }
            ]
        }
    }
]

```

### <a name="the-settings-object"></a>Het instellingenobject
Het instellingenobject bevat de configureerbare elementen van een onderdeel.  In ons voorbeelddashboard gebruikt het markdown-gedeelte instellingen om de aangepaste markeringsinhoud op te slaan, evenals een configureerbare titel en ondertitel.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

Op dezelfde manier heeft de videotegel zijn eigen instellingen die een aanwijzer bevatten naar de video die moet worden afgespeeld, een instelling voor automatisch afspelen en optionele titelgegevens.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>Het assetobject
Tegels die gebonden zijn aan eersteklas beheerbare portalobjecten (assets genoemd) hebben deze relatie uitgedrukt via het assetobject.  In ons voorbeelddashboard bevat de tegel virtuele machine deze assetbeschrijving.  De eigenschap __idInputName__ vertelt de portal dat de ID-invoer de unieke id voor het item bevat, in dit geval de resource-id. De meeste Azure-brontypen hebben elementen die zijn gedefinieerd in de portal.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`
