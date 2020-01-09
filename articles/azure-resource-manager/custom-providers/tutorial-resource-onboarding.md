---
title: Zelf studie-onboarding van resources
description: Met onboarding van resources via aangepaste providers kunt u bestaande Azure-resources bewerken en uitbreiden.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75649937"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Zelf studie: een onboarding van resources met aangepaste Azure-providers

In deze zelf studie implementeert u naar Azure een aangepaste resource provider die de Azure Resource Manager-API uitbreidt met het resource type micro soft. CustomProviders/Associations. De zelf studie laat zien hoe u bestaande resources uitbreidt die zich buiten de resource groep bevinden waar het exemplaar van de aangepaste provider zich bevindt. In deze zelf studie wordt de aangepaste resource provider gevoed door een Azure Logic-app, maar u kunt een open bare API-eind punt gebruiken.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie moet u het volgende weten:

* De mogelijkheden van [aangepaste Azure-providers](overview.md).
* Basis informatie over het [onboarden van resources met aangepaste providers](concepts-resource-onboarding.md).

## <a name="get-started-with-resource-onboarding"></a>Aan de slag met het voorbereiden van resources

In deze zelf studie zijn er twee onderdelen die moeten worden geïmplementeerd: de aangepaste provider en de koppeling. Als u het proces eenvoudiger wilt maken, kunt u eventueel één sjabloon gebruiken waarmee beide worden geïmplementeerd.

Deze resources worden gebruikt voor de sjabloon:

* Micro soft. CustomProviders/resourceProviders
* Microsoft.Logic/workflows
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

In het eerste deel van de sjabloon wordt de infra structuur van de aangepaste provider geïmplementeerd. Deze infra structuur definieert het effect van de bron van de koppelingen. Als u niet bekend bent met aangepaste providers, raadpleegt u de [basis beginselen van aangepaste](overview.md)providers.

We gaan de infra structuur van de aangepaste provider implementeren. U kunt de voor gaande sjabloon kopiëren, opslaan en implementeren, of de infra structuur volgen en implementeren met behulp van de Azure Portal.

1. Ga naar de [Azure Portal](https://portal.azure.com).

2. Zoek naar **sjablonen** in **alle services** of via het hoofd zoekvak:

   ![Sjablonen zoeken](media/tutorial-resource-onboarding/templates.png)

3. Selecteer **toevoegen** in het deel venster **sjablonen** :

   ![Selecteer E-mail](media/tutorial-resource-onboarding/templatesadd.png)

4. Voer onder **Algemeen**een **naam** en **Beschrijving** in voor de nieuwe sjabloon:

   ![Naam en beschrijving van de sjabloon](media/tutorial-resource-onboarding/templatesdescription.png)

5. Maak de Resource Manager-sjabloon door te kopiëren in de JSON-sjabloon vanuit de sectie aan de slag met het voorbereiden van resources in dit artikel:

   ![Een Resource Manager-sjabloon maken](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. Selecteer **toevoegen** om de sjabloon te maken. Als de nieuwe sjabloon niet wordt weer gegeven, selecteert u **vernieuwen**.

7. Selecteer de sjabloon die u zojuist hebt gemaakt en selecteer vervolgens **implementeren**:

   ![Selecteer de nieuwe sjabloon en selecteer vervolgens implementeren](media/tutorial-resource-onboarding/templateselectspecific.png)

8. Voer de instellingen voor de vereiste velden in en selecteer vervolgens het abonnement en de resource groep. U kunt het vak **aangepaste resource provider-id** leeg laten.

   | Naam van instelling | Vereist? | Beschrijving |
   | ------------ | -------- | ----------- |
   | Locatie | Ja | De locatie van de resources in de sjabloon. |
   | Naam van logische app | Nee | De naam van de logische app. |
   | Naam van de aangepaste resource provider | Nee | De naam van de aangepaste resource provider. |
   | Aangepaste resource provider-id | Nee | Een bestaande aangepaste resource provider die ondersteuning biedt voor de koppelings bron. Als u hier een waarde opgeeft, wordt de logische app en de implementatie van de aangepaste provider overgeslagen. |
   | Naam van koppeling | Nee | De naam van de koppelings bron. |

   Voorbeeld parameters:

   ![Sjabloon parameters invoeren](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. Ga naar de implementatie en wacht totdat deze is voltooid. U ziet een item zoals in de volgende scherm afbeelding. U ziet de nieuwe koppelings resource als een uitvoer:

   ![Geslaagde implementatie](media/tutorial-resource-onboarding/customproviderdeployment.png)

   Hier ziet u de resource groep, met **verborgen typen weer geven** geselecteerd:

   ![Implementatie van aangepaste provider](media/tutorial-resource-onboarding/showhidden.png)

10. Bekijk het tabblad **geschiedenis uitvoeringen** van logische apps om de aanroepen voor de koppeling te maken:

    ![Geschiedenis van uitvoeringen van logische app](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Aanvullende koppelingen implementeren

Nadat u de aangepaste provider infrastructuur hebt ingesteld, kunt u eenvoudig meer koppelingen implementeren. De resource groep voor aanvullende koppelingen hoeft niet hetzelfde te zijn als de resource groep waar u de infra structuur van de aangepaste provider hebt geïmplementeerd. Als u een koppeling wilt maken, moet u de machtigingen micro soft. CustomProviders/resourceproviders/write hebben voor de opgegeven aangepaste resource provider-ID.

1. Ga naar de resource groep **micro soft. CustomProviders/resourceProviders** van de aangepaste provider in de resource groepen van de vorige implementatie. U moet het selectie vakje **verborgen typen weer geven** inschakelen:

   ![Ga naar de resource](media/tutorial-resource-onboarding/showhidden.png)

2. Kopieer de eigenschap Resource-ID van de aangepaste provider.

3. Zoek naar **sjablonen** in **alle services** of via het hoofd zoekvak:

   ![Sjablonen zoeken](media/tutorial-resource-onboarding/templates.png)

4. Selecteer de eerder gemaakte sjabloon en selecteer vervolgens **implementeren**:

   ![Selecteer de eerder gemaakte sjabloon en selecteer vervolgens implementeren](media/tutorial-resource-onboarding/templateselectspecific.png)

5. Voer de instellingen voor de vereiste velden in en selecteer vervolgens het abonnement en een andere resource groep. Voer voor de instelling van de **aangepaste resource provider-id** de resource-id in die u hebt gekopieerd uit de aangepaste provider die u eerder hebt geïmplementeerd.

6. Ga naar de implementatie en wacht totdat deze is voltooid. Nu moet alleen de resource nieuwe koppelingen worden geïmplementeerd:

   ![Resource voor nieuwe koppelingen](media/tutorial-resource-onboarding/createdassociationresource.png)

Als u wilt, kunt u teruggaan naar de **uitvoerings geschiedenis** van de logische app en zien dat er een andere aanroep is uitgevoerd naar de logische app. U kunt de logische app bijwerken om extra functionaliteit voor elke gemaakte koppeling uit te breiden.

## <a name="getting-help"></a>Ondersteuning vragen

Als u vragen hebt over aangepaste Azure-providers, kunt u ze vragen op [stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Er is mogelijk al een vergelijk bare vraag beantwoord, dus controleer eerst vóór het boeken. Voeg de tag `azure-custom-providers` toe om snel antwoord te krijgen.

