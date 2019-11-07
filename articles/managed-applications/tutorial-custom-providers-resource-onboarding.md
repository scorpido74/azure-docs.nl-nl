---
title: Onboarding van resources met aangepaste Azure-providers
description: Het onboarden van resources via aangepaste providers maakt manipulatie en uitbrei ding van bestaande Azure-resources mogelijk.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: c722b4dc3219f76f8c7c571af3613996fec9e69c
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608678"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Zelf studie: een onboarding van resources met aangepaste Azure-providers

In deze zelf studie wordt een aangepaste resource provider geïmplementeerd naar Azure waarmee de Azure Resource Manager-API wordt uitgebreid met het resource type micro soft. CustomProviders/Associations. In dit voor beeld ziet u hoe u bestaande resources uitbreidt die zich buiten de resource groep bevinden waar het aangepaste provider exemplaar woont. In dit voor beeld wordt de aangepaste resource provider aangedreven door een Azure Logic-app, maar een open bare API-eind punt kan worden gebruikt.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie moet u het volgende weten:

* Mogelijkheden van de [aangepaste Azure-provider](custom-providers-overview.md) .
* [Onboarding van resources met aangepaste providers](concepts-custom-providers-resourceonboarding.md).

## <a name="getting-started-with-resource-onboarding"></a>Aan de slag met het voorbereiden van resources

Voor dit voor beeld zijn er twee onderdelen die moeten worden geïmplementeerd: de aangepaste provider en de koppeling. Ter vereenvoudiging van het voor beeld is er één sjabloon die optioneel kan worden geïmplementeerd.

De sjabloon maakt gebruik van de volgende resources:

* Micro soft. CustomProviders/resourceProviders
* Micro soft. Logic/werk stromen
* Micro soft. CustomProviders/Associations

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>De aangepaste provider infrastructuur implementeren

In het eerste deel van de sjabloon wordt de aangepaste provider infrastructuur geïmplementeerd. Deze infra structuur definieert het effect van de resource ' Association '. Als u niet bekend bent met aangepaste providers, raadpleegt u de [basis principes van aangepaste](custom-providers-overview.md)providers.

We gaan de aangepaste provider infrastructuur implementeren, de bovenstaande sjabloon kopiëren, opslaan en implementeren, of door lopen en implementeren via de Azure Portal.

1. Open de Azure Portal vanuit https://portal.azure.com.

2. Zoek naar ' sjablonen ' in `All Services` of de middelste zoek balk. 

![Sjabloon zoeken](media/custom-providers-resource-onboarding/templates.png)

3. Klik in de linkerbovenhoek van de Blade sjablonen op de knop `+ Add`.

![Nieuwe sjabloon toevoegen](media/custom-providers-resource-onboarding/templatesadd.png)

4. Vul de velden ' naam ' en ' Beschrijving ' onder algemeen in voor de nieuwe sjabloon.

![Naam en beschrijving van de sjabloon](media/custom-providers-resource-onboarding/templatesdescription.png)

5. Vul de Resource Manager-sjabloon in door de JSON-sjabloon te kopiëren van de ' aan de slag met de resource ' voor bereiding '

![Resource Manager-sjabloon invullen](media/custom-providers-resource-onboarding/templatesarmtemplate.png)

6. Klik op de knop `Add` om een nieuwe sjabloon te maken. Als de nieuwe sjabloon niet wordt weer gegeven, klikt u op `Refresh`.

7. Selecteer de sjabloon die u zojuist hebt gemaakt en klik op de knop `Deploy`.

![Selecteer de sjabloon die u zojuist hebt gemaakt en implementeer deze](media/custom-providers-resource-onboarding/templateselectspecific.png)

8. Voer de instellings parameters in voor de vereiste velden en selecteer het abonnement en de resource groep. De aangepaste resource provider-ID mag niet leeg zijn.

| Naam van instelling | Vereist | Beschrijving |
| ------------ | -------- | ----------- |
| Locatie | *klikt* | De locatie van de resources in de sjabloon. |
| Naam van logische app | *geen* | De naam van de logische app. |
| Naam van de aangepaste resource provider | *geen* | De naam van de aangepaste resource provider. |
| Aangepaste resource provider-ID | *geen* | Een bestaande aangepaste resource provider die de koppelings bron ondersteunt. Als u dit opgeeft, worden de logische app en de implementatie van de aangepaste provider overs Laan. |
| Naam van koppeling | *geen* | De naam van de koppelings bron. |

Voorbeeld parameters:

![Invoer sjabloon parameters](media/custom-providers-resource-onboarding/templatescustomprovider.png)

9. Ga naar de implementatie en wacht totdat deze is voltooid. De nieuwe ' koppelings bron moet worden weer gegeven en uitgevoerd.

Geslaagde implementaties:

![Implementatie van aangepaste provider](media/custom-providers-resource-onboarding/customproviderdeployment.png)

Resource groep met ' verborgen typen weer geven ':

![Implementatie van aangepaste provider](media/custom-providers-resource-onboarding/showhidden.png)

10. Verken het tabblad ' uitvoerings geschiedenis van de logische app ' om de aanroepen voor het maken van de koppeling weer te geven.

![Uitvoerings geschiedenis van de logische app](media/custom-providers-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Aanvullende koppelingen implementeren

Zodra de aangepaste provider infrastructuur is ingesteld, kunnen we eenvoudig extra ' Associations ' implementeren. De resource groep voor extra "Associations" hoeft niet hetzelfde te zijn als de resource groep waarin de aangepaste provider infrastructuur is geïmplementeerd. Als u een koppeling wilt maken, zijn de machtigingen ' micro soft. CustomProviders/resourceproviders/write ' nodig voor de opgegeven ' aangepaste resource provider-ID '.

1. Ga naar de resource groep ' micro soft. CustomProviders/resourceProviders ' van de aangepaste provider van de vorige implementatie. Het selectie vakje verborgen typen weer geven moet zijn ingeschakeld.

![Implementatie van aangepaste provider](media/custom-providers-resource-onboarding/showhidden.png)

2. Kopieer de eigenschap Resource ID van de aangepaste provider.

3. Zoek naar ' sjablonen ' in `All Services` of de middelste zoek balk. 

![Sjabloon zoeken](media/custom-providers-resource-onboarding/templates.png)

4. Selecteer de sjabloon die u eerder hebt gemaakt en klik op de knop `Deploy`.

![Selecteer de eerder gemaakte sjabloon en implementeer deze](media/custom-providers-resource-onboarding/templateselectspecific.png)

5. Voer de instellings parameters in voor de vereiste velden en selecteer het abonnement en een andere resource groep. Voer voor de instelling ' aangepaste resource provider-ID ' de gekopieerde ' Resource-ID ' van de eerder geïmplementeerde aangepaste provider in.

6. Ga naar de implementatie en wacht totdat deze is voltooid. De nieuwe resource ' Association ' moet nu alleen worden geïmplementeerd.

![Gemaakte koppeling](media/custom-providers-resource-onboarding/createdassociationresource.png)

Desgewenst kunt u teruggaan naar de logische app ' uitvoerings geschiedenis ' en zien dat er een andere aanroep is uitgevoerd naar de logische app. De logische app kan worden bijgewerkt om extra functionaliteit voor elke gemaakte koppeling uit te breiden.

## <a name="looking-for-help"></a>Zoeken naar Help

Als u vragen hebt over aangepaste Azure-providers, kunt u een vraag stellen over [stack overflow](http://stackoverflow.com/questions/tagged/azure-custom-providers). Er is mogelijk al een vergelijk bare vraag gesteld en beantwoord, dus controleer eerst vóór het boeken. Voeg de tag `azure-custom-providers` toe om snel antwoord te krijgen.

