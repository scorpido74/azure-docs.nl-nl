---
title: Zelfstudie - onboarding voor resources
description: Met resource onboarding via aangepaste providers u bestaande Azure-bronnen manipuleren en uitbreiden.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649937"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Zelfstudie: Resource onboarding met Azure Custom Providers

In deze zelfstudie implementeert u naar Azure een aangepaste resourceprovider die de Azure Resource Manager API uitbreidt met het resourcetype Microsoft.CustomProviders/associations. In de zelfstudie wordt uitgelegd hoe u bestaande resources uitbreiden die zich buiten de resourcegroep bevinden waarin de instantie van de aangepaste provider zich bevindt. In deze zelfstudie wordt de aangepaste resourceprovider aangedreven door een Azure-logica-app, maar u elk openbaar API-eindpunt gebruiken.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, moet je het weten:

* De mogelijkheden van [Azure Custom Providers](overview.md).
* Basisinformatie over [resource onboarding bij aangepaste providers.](concepts-resource-onboarding.md)

## <a name="get-started-with-resource-onboarding"></a>Aan de slag met resource onboarding

In deze zelfstudie zijn er twee stukken die moeten worden geïmplementeerd: de aangepaste provider en de koppeling. Om het proces eenvoudiger te maken, u optioneel één sjabloon gebruiken die beide implementeert.

De sjabloon gebruikt de volgende bronnen:

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/werkstromen
* Microsoft.CustomProviders/koppelingen

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

### <a name="deploy-the-custom-provider-infrastructure"></a>De aangepaste providerinfrastructuur implementeren

Het eerste deel van de sjabloon implementeert de aangepaste providerinfrastructuur. Deze infrastructuur definieert het effect van de associatiebron. Zie [Basisbeginselen](overview.md)van aangepaste providers als u niet bekend bent met aangepaste providers.

Laten we de infrastructuur van de aangepaste provider implementeren. Kopieer, sla en implementeer de voorgaande sjabloon of volg de infrastructuur met behulp van de Azure-portal.

1. Ga naar de [Azure-portal.](https://portal.azure.com)

2. Zoek naar **sjablonen** in **Alle services** of met het hoofdzoekvak:

   ![Sjablonen zoeken](media/tutorial-resource-onboarding/templates.png)

3. Selecteer **Toevoegen** in het deelvenster **Sjablonen:**

   ![Selecteer Toevoegen](media/tutorial-resource-onboarding/templatesadd.png)

4. Voer **onder Algemeen**een **naam** en **beschrijving** in voor de nieuwe sjabloon:

   ![Naam en beschrijving van sjabloon](media/tutorial-resource-onboarding/templatesdescription.png)

5. Maak de sjabloon Resourcemanager door te kopiëren in de JSON-sjabloon in het gedeelte 'Aan de slag met resource onboarding' van dit artikel:

   ![Een Resource Manager-sjabloon maken](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. Selecteer **Toevoegen** om de sjabloon te maken. Als de nieuwe sjabloon niet wordt weergegeven, selecteert u **Vernieuwen**.

7. Selecteer de nieuw gemaakte sjabloon en selecteer **Vervolgens Implementeren:**

   ![Selecteer de nieuwe sjabloon en selecteer Vervolgens Implementeren](media/tutorial-resource-onboarding/templateselectspecific.png)

8. Voer de instellingen voor de vereiste velden in en selecteer vervolgens de abonnements- en brongroep. U het vak **Custom Resource Provider Id** leeg laten.

   | Naam van instelling | Vereist? | Beschrijving |
   | ------------ | -------- | ----------- |
   | Locatie | Ja | De locatie voor de resources in de sjabloon. |
   | Logische app-naam | Nee | De naam van de logische app. |
   | Aangepaste naam resourceprovider | Nee | De naam van de aangepaste resourceprovider. |
   | Aangepaste resourceprovider-id | Nee | Een bestaande aangepaste resourceprovider die de koppelingsbron ondersteunt. Als u hier een waarde opgeeft, worden de logische app en de aangepaste providerimplementatie overgeslagen. |
   | Naam van de vereniging | Nee | De naam van de associatiebron. |

   Voorbeeldparameters:

   ![Sjabloonparameters invoeren](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. Ga naar de implementatie en wacht tot het is voltooid. Je zou iets als de volgende screenshot moeten zien. U moet de nieuwe koppelingsbron als een uitvoer zien:

   ![Geslaagde implementatie](media/tutorial-resource-onboarding/customproviderdeployment.png)

   Hier is de resourcegroep, waarbij **Verborgen typen weergeven** zijn geselecteerd:

   ![Implementatie van aangepaste provider](media/tutorial-resource-onboarding/showhidden.png)

10. De logica-app **Voert geschiedenis** tabblad om de oproepen voor de koppeling te maken te zien:

    ![Logica-app Voert geschiedenis uit](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Extra koppelingen implementeren

Nadat u de aangepaste providerinfrastructuur hebt ingesteld, u eenvoudig meer koppelingen implementeren. De resourcegroep voor extra koppelingen hoeft niet dezelfde te zijn als de resourcegroep waarin u de aangepaste providerinfrastructuur hebt geïmplementeerd. Als u een koppeling wilt maken, moet u microsoft.customproviders/resourceproviders/schrijfmachtigingen hebben op de opgegeven aangepaste resourceprovider-id.

1. Ga naar de aangepaste provider **Microsoft.CustomProviders/resourceProviders-bron** in de brongroep van de vorige implementatie. U moet het selectievakje **Verborgen typen weergeven** inschakelen:

   ![Ga naar de resource](media/tutorial-resource-onboarding/showhidden.png)

2. Kopieer de eigenschap Resource-id van de aangepaste provider.

3. Zoek naar **sjablonen** in **Alle services** of met het hoofdzoekvak:

   ![Sjablonen zoeken](media/tutorial-resource-onboarding/templates.png)

4. Selecteer de eerder gemaakte sjabloon en selecteer **Vervolgens Implementeren:**

   ![Selecteer de eerder gemaakte sjabloon en selecteer Vervolgens Implementeren](media/tutorial-resource-onboarding/templateselectspecific.png)

5. Voer de instellingen voor de vereiste velden in en selecteer vervolgens het abonnement en een andere brongroep. Voer voor de instelling **Aangepaste resourceprovider-id** de resource-id in die u hebt gekopieerd van de aangepaste provider die u eerder hebt geïmplementeerd.

6. Ga naar de implementatie en wacht tot het is voltooid. Het moet nu alleen de nieuwe associaties resource implementeren:

   ![Nieuwe associaties bron](media/tutorial-resource-onboarding/createdassociationresource.png)

Als u wilt, u teruggaan naar de geschiedenis van de logica-app **Uitvoeren** en zien dat er een ander gesprek is gevoerd naar de logische app. U de logische app bijwerken om extra functionaliteit voor elke gemaakte koppeling uit te breiden.

## <a name="getting-help"></a>Ondersteuning vragen

Als u vragen hebt over Azure Custom Providers, probeert u deze te vragen op [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Een soortgelijke vraag kan al zijn beantwoord, dus controleer eerst voor het posten. Voeg de `azure-custom-providers` tag toe om snel te reageren!

