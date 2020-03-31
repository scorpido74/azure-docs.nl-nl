---
title: Op programmatische wijze Azure-dashboards maken
description: Gebruik een dashboard in de Azure-portal als sjabloon om programmatisch Azure Dashboards te maken. Inclusief JSON-referentie.
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
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 5329a7b21aff7ecffc7153c7aa74ddb93bce75cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132003"
---
# <a name="programmatically-create-azure-dashboards"></a>Op programmatische wijze Azure-dashboards maken

In dit artikel wordt u door het proces van het programmatisch maken en publiceren van Azure-dashboards heen gezwalking. Het onderstaande dashboard wordt in het hele document vermeld.

![voorbeelddashboard](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Overzicht

Gedeelde dashboards in de [Azure-portal](https://portal.azure.com) zijn [bronnen,](../azure-resource-manager/management/overview.md) net als virtuele machines en opslagaccounts. U resources programmatisch beheren met behulp van de [AZURE Resource Manager REST API's,](/rest/api/)de Azure [CLI-](/cli/azure)en [Azure PowerShell-opdrachten](/powershell/azure/get-started-azureps).

Veel functies bouwen voort op deze API's om resourcebeheer gemakkelijker te maken. Elk van deze API's en hulpprogramma's biedt manieren om resources te maken, op te sommen, op te halen, te wijzigen en te verwijderen. Aangezien dashboards bronnen zijn, u uw favoriete API of tool kiezen om te gebruiken.

Welke tools u ook gebruikt om een dashboard programmatisch te maken, u bouwt een JSON-weergave van uw dashboardobject. Dit object bevat informatie over de tegels op het dashboard. Het bevat maten, posities, resources waaraan ze gebonden zijn en eventuele aanpassingen van de gebruiker.

De meest praktische manier om dit JSON-document op te bouwen, is door de Azure-portal te gebruiken. U uw tegels interactief toevoegen en positioneren. Exporteer vervolgens de JSON en maak een sjabloon van het resultaat voor later gebruik in scripts, programma's en implementatietools.

## <a name="create-a-dashboard"></a>Een dashboard maken

Als u een dashboard wilt maken, selecteert u **Dashboard** in het [azure-portalmenu](https://portal.azure.com) en selecteert u **Vervolgens Nieuw dashboard**.

![nieuwe dashboardopdracht](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Gebruik de tegelgalerie om tegels te zoeken en toe te voegen. Tegels worden toegevoegd door ze te slepen en te laten vallen. Sommige tegels ondersteunen het formaat met behulp van een sleepgreep.

![sleepgreep om de grootte te wijzigen](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Anderen hebben vaste maten om uit te kiezen in hun contextmenu.

![contextmenu van grootte om de grootte te wijzigen](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Het dashboard delen

Nadat u het dashboard hebt geconfigureerd, is de volgende stap het publiceren van het dashboard met de opdracht **Delen.**

![een dashboard delen](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Als u **Delen** selecteert, moet u kiezen naar welke abonnementsgroep en resourcegroep u wilt publiceren. U moet schrijftoegang hebben tot de abonnements- en resourcegroep die u kiest. Zie [Roltoewijzingen toevoegen of verwijderen met Azure RBAC en de Azure-portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie.

![wijzigingen aanbrengen in delen en openen](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>De JSON-weergave van het dashboard ophalen

Publiceren duurt slechts een paar seconden. Wanneer dit is gedaan, is de volgende stap het ophalen van de JSON met de opdracht **Downloaden.**

![JSON-vertegenwoordiging downloaden](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Een sjabloon maken vanuit het JSON

De volgende stap is het maken van een sjabloon van deze JSON. Gebruik die sjabloon programmatisch met de juiste API's voor resourcebeheer, opdrachtregelgereedschappen of binnen de portal.

U hoeft de JSON-structuur van het dashboard niet volledig te begrijpen om een sjabloon te maken. In de meeste gevallen wilt u de structuur en configuratie van elke tegel behouden. Parameteriseer vervolgens de set Azure-resources waarnaar de tegels wijzen. Bekijk het geëxporteerde JSON-dashboard en vind alle exemplaren van Azure-bron-id's. Ons voorbeelddashboard heeft meerdere tegels die allemaal naar één azure virtuele machine wijzen. Dat komt omdat ons dashboard alleen kijkt naar deze ene bron. Als u in het voorbeeld JSON zoekt, dat aan het einde van het document is opgenomen, voor "/abonnementen", vindt u verschillende exemplaren van deze ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Als u dit dashboard in de toekomst voor elke virtuele machine wilt publiceren, u elke gebeurtenis van deze tekenreeks in de JSON parameteriseren.

Er zijn twee benaderingen voor API's die resources in Azure maken:

* Dwingende API's maken één resource tegelijk. Zie [Bronnen voor](/rest/api/resources/resources)meer informatie.
* Een op sjablonen gebaseerd implementatiesysteem dat meerdere, afhankelijke bronnen maakt met één API-aanroep. Zie [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)voor meer informatie.

Op sjablonen gebaseerde implementatie ondersteunt parameterisering en templating. We gebruiken deze aanpak in dit artikel.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programmatisch een dashboard maken op basis van uw sjabloon met behulp van een sjabloonimplementatie

Azure biedt de mogelijkheid om de implementatie van meerdere resources te orkestreren. U maakt een implementatiesjabloon die de set resources uitdrukt die moet worden geïmplementeerd en de relaties tussen deze bronnen.  De JSON-indeling van elke resource is hetzelfde als wanneer u ze één voor één maakt. Het verschil is dat de sjabloontaal een aantal concepten toevoegt, zoals variabelen, parameters, basisfuncties en meer. Deze uitgebreide syntaxis wordt alleen ondersteund in de context van een sjabloonimplementatie. Het werkt niet als gebruikt met de dwingende API's eerder besproken. Zie [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen](../azure-resource-manager/resource-group-authoring-templates.md)voor meer informatie.

Parameterisatie moet worden gedaan met behulp van de parametersyntaxis van de sjabloon.  U vervangt alle exemplaren van de resource-id die we eerder hebben gevonden, zoals hier wordt weergegeven.

Voorbeeld van JSON-eigenschap met een hard-coded resource-id:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Voorbeeld van JSON-eigenschap die is geconverteerd naar een geparameteriseerde versie op basis van sjabloonparameters

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Declareer de vereiste sjabloonmetagegevens en de parameters boven aan de JSON-sjabloon als volgt:

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
Zodra u de sjabloon hebt geconfigureerd, implementeert u deze met een van de volgende methoden:

* [REST API's](/rest/api/resources/deployments)
* [Powershell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure-CLI](/cli/azure/group/deployment#az-group-deployment-create)
* [De pagina met de implementatievan Azure-portalsjablonen](https://portal.azure.com/#create/Microsoft.Template)

Vervolgens ziet u twee versies van ons voorbeelddashboard JSON. De eerste is de versie die we exporteerden vanuit de portal die al was gebonden aan een resource. De tweede is de sjabloonversie die programmatisch kan worden gekoppeld aan elke virtuele machine en kan worden geïmplementeerd met Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON-weergave van ons voorbeelddashboard voor templating

In dit voorbeeld ziet u wat u verwachten om te zien of u dit artikel hebt gevolgd. De instructies exporteerden de JSON-weergave van een dashboard dat al is geïmplementeerd. De hard-coded resource-id's laten zien dat dit dashboard wijst naar een specifieke Azure virtuele machine.

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

### <a name="template-representation-of-our-example-dashboard"></a>Sjabloonweergave van ons voorbeelddashboard

In de sjabloonversie van het dashboard `virtualMachineName` `virtualMachineResourceGroup`zijn `dashboardName`drie parameters gedefinieerd, die worden aangeroepen , en .  Met de parameters u dit dashboard elke keer dat u implementeert op een andere Azure-virtuele machine plaatsen. Dit dashboard kan programmatisch worden geconfigureerd en geïmplementeerd om naar elke virtuele Azure-machine te wijzen. Als u deze functie wilt testen, kopieert u de volgende sjabloon en plakt u deze in de [pagina azure-portalsjabloon.](https://portal.azure.com/#create/Microsoft.Template)

In dit voorbeeld wordt een dashboard zelf geïmplementeerd, maar met de sjabloontaal u meerdere bronnen implementeren en een of meer dashboards met elkaar bundelen.

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

Nu u een voorbeeld hebt gezien van het gebruik van een geparameteriseerde sjabloon om een dashboard te implementeren, u proberen de sjabloon te implementeren met behulp van de [AZURE Resource Manager REST API's,](/rest/api/)de Azure [CLI-](/cli/azure)of [Azure PowerShell-opdrachten](/powershell/azure/get-started-azureps).
