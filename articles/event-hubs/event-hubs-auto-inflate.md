---
title: Doorvoereenheden automatisch opschalen - Azure Event Hubs | Microsoft Documenten
description: Schakel Automatisch opblazen in op een naamruimte om de doorvoereenheden automatisch op te schalen.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: dc6edaebebe89b6d4a35ada58d40795f86a935d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264467"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Doorvoereenheden voor Azure Event Hubs automatisch opschalen
Azure Event Hubs is een zeer schaalbaar datastreamingplatform. Als zodanig neemt het gebruik van Gebeurtenishubs vaak toe nadat de service is begonnen met het gebruik van de service. Een dergelijk gebruik vereist het verhogen van de vooraf bepaalde [doorvoereenheden](event-hubs-scalability.md#throughput-units) om gebeurtenishubs te schalen en grotere overdrachtssnelheden te verwerken. De **functie Automatisch opblazen** van gebeurtenishubs wordt automatisch opschalen door het aantal doorvoereenheden te verhogen om aan de gebruiksbehoeften te voldoen. Het verhogen van doorvoereenheden voorkomt beperkingsscenario's, waarbij:

* Gegevensinvallen overschrijden de ingestelde doorvoereenheden.
* Gegevensuitgangsaanvraagsnelheden overschrijden de ingestelde doorvoereenheden.

De service Gebeurtenishubs verhoogt de doorvoer wanneer de belasting toeneemt boven de minimumdrempel, zonder dat aanvragen mislukken met serverdrukkefouten.

## <a name="how-auto-inflate-works"></a>Hoe auto-inflate werkt

Het verkeer van gebeurtenishubs wordt beheerd door [doorvoereenheden](event-hubs-scalability.md#throughput-units). Een enkele doorvoereenheid maakt 1 MB per seconde binnendringen en twee maal die hoeveelheid uitgang mogelijk. Standaard gebeurtenishubs kunnen worden geconfigureerd met 1-20 doorvoereenheden. Met automatisch opblazen u klein beginnen met de minimaal vereiste doorvoereenheden die u kiest. De functie schaalt vervolgens automatisch naar de maximale limiet van doorvoereenheden die u nodig hebt, afhankelijk van de toename van uw verkeer. Automatisch opblazen biedt de volgende voordelen:

- Een efficiënt schaalmechanisme om klein te starten en op te schalen naarmate je groeit.
- Schaal automatisch naar de opgegeven bovengrens zonder beperking.
- Meer controle over schalen, omdat u bepaalt wanneer en hoeveel u moet schalen.

## <a name="enable-auto-inflate-on-a-namespace"></a>Automatisch opblazen inschakelen op een naamruimte

U de naamruimte voor gebeurtenishubs in een standaardlaag in- of uitschakelen met behulp van een van de volgende methoden:

- De [Azure-portal](https://portal.azure.com).
- Een [Azure Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Naamruimten voor gebeurtenishubs op basisniveau bieden geen ondersteuning voor Automatisch opblazen.

### <a name="enable-auto-inflate-through-the-portal"></a>Automatisch opblazen inschakelen via de portal


#### <a name="enable-at-the-time-of-creation"></a>Inschakelen op het moment van maken 
U de functie Automatisch opblazen inschakelen **bij het maken van een naamruimte voor gebeurtenishubs:**
 
![Automatisch opblazen inschakelen op het moment dat gebeurtenishub wordt gemaakt](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Als deze optie is ingeschakeld, u klein beginnen met uw doorvoereenheden en opschalen naarmate uw gebruiksbehoeften toenemen. De bovengrens voor inflatie heeft niet onmiddellijk invloed op de prijsstelling, wat afhankelijk is van het aantal doorvoereenheden dat per uur wordt gebruikt.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Automatisch opblazen inschakelen voor een bestaande gebeurtenishub
U ook de functie Automatisch opblazen inschakelen en de instellingen wijzigen met behulp van de volgende instructies: 
 
1. Selecteer op de pagina **Naamruimte van gebeurtenishubs** de optie **Uitgeschakeld** onder **doorvoereenheden automatisch opblazen**.  

    ![Doorvoereenheden selecteren op de pagina Naamruimte van gebeurtenishubs](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Schakel op de pagina **Schaalinstellingen** het selectievakje **inschakelen** in (als de functie automatisch schalen niet is ingeschakeld).

    ![Selecteer Inschakelen](./media/event-hubs-auto-inflate/scale-settings.png)
3. Voer het **maximum** aantal doorvoereenheden in of gebruik de schuifbalk om de waarde in te stellen. 
4. (facultatief) Werk het **minimumaantal** doorvoereenheden boven aan deze pagina bij. 


> [!NOTE]
> Wanneer u de configuratie automatisch opblazen toepast om doorvoereenheden te verhogen, zendt de service Gebeurtenishubs diagnostische logboeken uit die u informatie geven over waarom en wanneer de doorvoer is toegenomen. Als u diagnostische logboekregistratie voor een gebeurtenishub wilt inschakelen, selecteert u **Diagnostische instellingen** in het linkermenu op de pagina Gebeurtenishub in de Azure-portal. Zie [Diagnostische logboeken instellen voor een Azure-gebeurtenishub voor](event-hubs-diagnostic-logs.md)meer informatie. 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Automatisch opblazen inschakelen met een sjabloon Azure Resource Manager

U Automatisch opblazen inschakelen tijdens een azure resource manager-sjabloonimplementatie. Stel de `isAutoInflateEnabled` eigenschap bijvoorbeeld in op **true** en stel in op `maximumThroughputUnits` 10. Bijvoorbeeld:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Zie voor de volledige sjabloon de [naamruimte Gebeurtenishubs maken en inflatetemplate inschakelen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) op GitHub.


## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Overzicht van gebeurtenishubs](event-hubs-what-is-event-hubs.md)

