---
title: Programmatisch Azure-Dash boards maken | Microsoft Docs
description: U kunt een dash board in de Azure Portal als sjabloon gebruiken om op een programmatische manier Azure-Dash boards te maken. Bevat een JSON-verwijzing.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: mblythe
ms.openlocfilehash: 498e0255cfa289f7d8ccb93040980c362cf510a0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640343"
---
# <a name="programmatically-create-azure-dashboards"></a>Programmatisch Azure-Dash boards maken

In dit document wordt het proces van het programmatisch maken en publiceren van Azure-Dash boards behandeld. In het volgende voor beeld wordt in het document verwezen naar het dash board.

![voorbeelddashboard](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Overzicht

Gedeelde Dash boards in azure zijn [bronnen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) net als virtuele machines en opslag accounts.  Daarom kunnen ze via een programma worden beheerd via de [Azure Resource Manager rest api's](/rest/api/), de [Azure cli](https://docs.microsoft.com/cli/azure), [Azure PowerShell-opdrachten](https://docs.microsoft.com/powershell/azure/get-started-azureps)en veel [Azure Portal](https://portal.azure.com) functies op basis van deze api's bouwen om het bronnen beheer eenvoudiger te maken.  

Elk van deze Api's en hulpprogram ma's biedt manieren om resources te maken, weer te geven, op te halen, te wijzigen en te verwijderen.  Omdat Dash boards resources zijn, kunt u kiezen welke favoriet/welk hulp programma u wilt gebruiken.

Ongeacht welk hulp programma u gebruikt, moet u een JSON-weer gave van uw dashboard object maken voordat u een API voor het maken van resources kunt aanroepen. Dit object bevat informatie over de onderdelen (ook tegels) op het dash board. Dit omvat maten, posities, resources waaraan ze zijn gebonden en eventuele aanpassingen van de gebruiker.

De meest praktische manier om dit JSON-document samen te stellen is door [de portal](https://portal.azure.com/) te gebruiken om uw tegels interactief toe te voegen en te positioneren. Vervolgens exporteert u de JSON. Ten slotte maakt u een sjabloon op basis van het resultaat voor later gebruik in scripts, Program ma's en implementatie hulpprogramma's.

## <a name="create-a-dashboard"></a>Een dashboard maken

Als u een nieuw dash board wilt maken, gebruikt u de nieuwe dashboard opdracht in het hoofd scherm van de portal.

![nieuwe dashboard opdracht](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Vervolgens kunt u de tegel galerie gebruiken om tegels te zoeken en toe te voegen. Tegels worden toegevoegd door ze te slepen en neer te zetten. Sommige tegels bieden ondersteuning voor het verg Roten of verkleinen via een sleep greep, terwijl andere ondersteunen de groottes die kunnen worden weer gegeven in het context menu.

### <a name="drag-handle"></a>Greep slepen
![greep slepen](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Vaste grootte via context menu
![context menu met grootten](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Het dash board delen

Nadat u het dash board naar uw eigen smaak hebt geconfigureerd, moeten de volgende stappen worden uitgevoerd om het dash board te publiceren (met behulp van de share opdracht) en vervolgens de resource Explorer gebruiken om de JSON op te halen.

![opdracht delen](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Als u op de opdracht delen klikt, wordt er een dialoog venster weer gegeven waarin u wordt gevraagd om te kiezen welk abonnement en welke resource groep u wilt publiceren. Denk eraan dat [u schrijf toegang moet hebben](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) tot het abonnement en de resource groep die u kiest.

![delen en toegang](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>De JSON-weer gave van het dash board ophalen

Het publiceren duurt slechts enkele seconden.  Wanneer u klaar bent, gaat u naar de [resource Explorer](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) om de JSON op te halen.

![Bladeren in resource Explorer](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

Ga in resource Explorer naar het abonnement en de resource groep die u hebt gekozen. Klik vervolgens op de zojuist gepubliceerde Dashboard Resource om de JSON zichtbaar te maken.

![resource Explorer-JSON](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Een sjabloon maken op basis van de JSON

De volgende stap bestaat uit het maken van een sjabloon van deze JSON, zodat deze opnieuw kan worden gebruikt via een programma met de juiste resource management-Api's, opdracht regel Programma's of binnen de portal.

Het is niet nodig om volledig inzicht te krijgen in de JSON-structuur van het dash board om een sjabloon te maken. In de meeste gevallen moet u de structuur en configuratie van elke tegel behouden en vervolgens para meters de set met Azure-resources waarnaar wordt verwezen. Bekijk het geëxporteerde JSON-dash board en Zoek alle exemplaren van Azure-resource-Id's. Het dash board voor beeld bevat meerdere tegels die allemaal op één virtuele Azure-machine staan. Dit komt doordat in het dash board alleen naar deze afzonderlijke resource wordt gezocht. Als u de voor beeld-JSON (opgenomen aan het einde van het document) doorzoekt voor "/Subscriptions", kunt u verschillende instanties van deze id vinden.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Als u dit dash board voor elke virtuele machine in de toekomst wilt publiceren, moet u elk exemplaar van deze teken reeks in de JSON para meters. 

Er zijn twee soorten Api's voor het maken van resources in Azure. [Verplichte api's](https://docs.microsoft.com/rest/api/resources/resources) die één resource tegelijk maken en een [op een sjabloon gebaseerd implementatie](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) systeem waarmee het maken van meerdere, afhankelijke resources met één API-aanroep kan worden georganiseerd. De laatste biedt standaard ondersteuning voor parameterisering en sjabloon, dus we gebruiken deze voor het voor beeld.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programmatisch een dash board maken op basis van uw sjabloon met behulp van een sjabloon implementatie

Azure biedt de mogelijkheid om de implementatie van meerdere resources te organiseren. U maakt een implementatie sjabloon die de set met te implementeren resources en de relaties tussen deze bronnen opstelt.  De JSON-indeling van elke resource is hetzelfde als wanneer u ze één voor één maakt. Het verschil is dat in de [sjabloon taal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) enkele concepten worden toegevoegd, zoals variabelen, para meters, basis functies en nog veel meer. Deze uitgebreide syntaxis wordt alleen ondersteund in de context van een sjabloon implementatie en werkt niet als deze wordt gebruikt met de verplichte Api's die eerder zijn besproken.

Als u deze route uitvoert, moeten parameterisering worden uitgevoerd met de parameter syntaxis van de sjabloon.  U vervangt alle exemplaren van de resource-id die u eerder hebt gevonden, zoals hier wordt weer gegeven.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Voor beeld van JSON-eigenschap met in code vastgelegde resource-id
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Voor beeld van JSON-eigenschap geconverteerd naar een geparametriseerde versie op basis van sjabloon parameters

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

U moet ook enkele vereiste meta gegevens van de sjabloon en de para meters boven aan de JSON-sjabloon declareren als volgt:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__Aan het einde van dit document ziet u de volledige werk sjabloon.__

Zodra u uw sjabloon hebt gemaakt, kunt u deze implementeren met behulp van de [rest api's](https://docs.microsoft.com/rest/api/resources/deployments), [Power shell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), de [Azure cli](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create)of de [implementatie pagina voor sjablonen van de portal](https://portal.azure.com/#create/Microsoft.Template).

Hier volgen twee versies van de JSON van het voor beeld-dash board. De eerste is de versie die is geëxporteerd uit de portal die al is gebonden aan een resource. De tweede is de sjabloon versie die programmatisch kan worden gebonden aan een virtuele machine en met Azure Resource Manager kan worden geïmplementeerd.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON-weer gave van het dash board van ons voor beeld (vóór sjabloon)

Dit is wat u kunt verwachten als u de voor gaande instructies volgt om de JSON-weer gave op te halen van een dash board dat al is geïmplementeerd. Noteer de hardcoded resource-id's die laten zien dat dit dash board naar een specifieke virtuele machine van Azure verwijst.

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
        "metadata": { }
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

### <a name="template-representation-of-our-example-dashboard"></a>Sjabloon weergave van het dash board van ons voor beeld

De sjabloon versie van het dash board heeft drie para meters met de naam __virtualMachineName__, __virtualMachineResourceGroup__en __dash board__gedefinieerd.  Met de para meters kunt u dit dash board op een andere virtuele Azure-machine laten wijzen elke keer dat u implementeert. De para meter-id's zijn gemarkeerd om aan te tonen dat dit dash board programmatisch kan worden geconfigureerd en geïmplementeerd om naar elke virtuele machine van Azure te verwijzen. De eenvoudigste manier om deze functie te testen is door de volgende sjabloon te kopiëren en te plakken op de [pagina sjabloon implementatie van Azure Portal](https://portal.azure.com/#create/Microsoft.Template). 

In dit voor beeld wordt een dash board op zichzelf geïmplementeerd, maar met de sjabloon taal kunt u meerdere resources implementeren en een of meer Dash boards naast elkaar bundelen. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```
