---
title: Azure Resource Manager sjablonen gebruiken voor onboarding Updatebeheer | Microsoft Docs
description: U kunt een Azure Resource Manager sjabloon gebruiken om de Azure Automation Updatebeheer-oplossing uit te staan.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/27/2020
ms.openlocfilehash: a8b382663b56d7481da876979e33194fb0ac533d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925798"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Updatebeheer oplossing voor onboarding met behulp van Azure Resource Manager-sjabloon

U kunt [Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-syntax.md) gebruiken om de Azure Automation updatebeheer oplossing in uw resource groep in te scha kelen. Dit artikel bevat een voorbeeld sjabloon waarmee het volgende kan worden geautomatiseerd:

* Het maken van een Azure Monitor Log Analytics werk ruimte.
* Het maken van een Azure Automation-account.
* Hiermee koppelt u het Automation-account aan de Log Analytics-werk ruimte als deze nog niet is gekoppeld.
* Onboarding van de Azure Automation Updatebeheer oplossing

Met de sjabloon wordt het onboarden van een of meer virtuele machines in azure of niet-Azure geautomatiseerd.

Als u al een Log Analytics-werk ruimte en een Automation-account hebt geïmplementeerd in een ondersteunde regio in uw abonnement, zijn deze niet gekoppeld en is de Updatebeheer oplossing niet al geïnstalleerd op de werk ruimte, met behulp van deze sjabloon. de koppeling en implementeert de Updatebeheer-oplossing. 

## <a name="api-versions"></a>API-versies

De volgende tabel geeft een overzicht van de API-versie voor de resources die in dit voor beeld worden gebruikt.

| Resource | Resourcetype | API-versie |
|:---|:---|:---|
| Werkruimte | werk ruimten | 2017-03-15-preview |
| Automation-account | automation | 2015-10-31 | 
| Oplossing | oplossingen | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Voordat u de sjabloon gebruikt

Als u Power shell lokaal wilt installeren en gebruiken, is voor dit artikel de Azure PowerShell AZ-module vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Zie [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps) als u een upgrade wilt uitvoeren. Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure. Met Azure PowerShell maakt de implementatie gebruik van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel de Azure CLI-versie 2.1.0 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Met Azure CLI maakt deze implementatie gebruik van [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

De JSON-sjabloon is zo geconfigureerd dat u wordt gevraagd om:

* De naam van de werk ruimte
* De regio waarin u de werk ruimte wilt maken
* De naam van het Automation-account
* De regio waarin het account moet worden gemaakt

Met de JSON-sjabloon wordt een standaard waarde opgegeven voor de andere para meters die waarschijnlijk worden gebruikt als een standaard configuratie in uw omgeving. U kunt de sjabloon opslaan in een Azure-opslag account voor gedeelde toegang in uw organisatie. Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../azure-resource-manager/templates/deploy-cli.md)voor meer informatie over het werken met sjablonen.

De volgende para meters in de sjabloon zijn ingesteld met een standaard waarde voor de Log Analytics-werk ruimte:

* SKU - standaard ingesteld op de nieuwe prijzen Per GB-laag met de uitgebracht in het prijsmodel van April 2018
* bewaren van gegevens-standaard ingesteld op dertig dagen

>[!WARNING]
>Als u een Log Analytics-werk ruimte maakt of configureert in een abonnement dat is aangemeld met het nieuwe prijs model van april 2018, is de enige geldige Log Analytics prijs categorie **PerGB2018**.
>

>[!NOTE]
>Voordat u deze sjabloon gaat gebruiken, moet u [aanvullende informatie](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) bekijken om de configuratie opties voor de werk ruimte, zoals de toegangs beheer modus, de prijs categorie, retentie en capaciteits reserverings niveau, volledig te begrijpen. Als u niet bekend bent met Azure Monitor-logboeken en nog geen werk ruimte hebt geïmplementeerd, raadpleegt u de richt lijnen voor het ontwerpen van de [werk ruimte](../azure-monitor/platform/design-logs-deployment.md) voor meer informatie over toegangs beheer en een uitleg van de ontwerp implementatie strategieën die voor uw organisatie worden aanbevolen.

## <a name="deploy-template"></a>Sjabloon implementeren

1. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "pricingTier": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": { 
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
      ]
    }
    ```

2. De sjabloon bijwerken om aan uw eisen voldoen.

3. Sla dit bestand op als deployUMSolutiontemplate. json naar een lokale map.

4. U kunt deze sjabloon nu implementeren. U kunt Power shell of de Azure CLI gebruiken. Wanneer u wordt gevraagd om een naam voor de werk ruimte en het Automation-account, geeft u een naam op die wereld wijd uniek is voor alle Azure-abonnementen.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    De implementatie kan enkele minuten duren. Als deze is voltooid, ziet u een bericht dat lijkt op de volgende mogelijkheden van het resultaat:

    ![Voorbeeld van resultaat wanneer de implementatie is voltooid](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Volgende stappen

Nu u de Updatebeheer oplossing hebt geïmplementeerd, kunt u Vm's inschakelen voor beheer, update-evaluaties bekijken en updates implementeren om ze compatibel te maken.

- Vanuit uw [Azure Automation-account](automation-onboard-solutions-from-automation-account.md) voor een of meer Azure-machines en hand matig voor niet-Azure-machines.

- Voor één Azure-VM vanaf de pagina virtuele machine in het Azure Portal. Dit scenario is beschikbaar voor [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) -en [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) -vm's.

- Voor [meerdere virtuele Azure-machines](manage-update-multi.md) door ze te selecteren op de pagina **virtual machines** in de Azure Portal. 