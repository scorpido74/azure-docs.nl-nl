---
title: Azure Resource Manager sjablonen gebruiken om een Automation-account te maken | Microsoft Docs
description: U kunt een Azure Resource Manager sjabloon gebruiken om een Azure Automation-account te maken.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 431b89df0ce06736a2e76e58797ded65751bb404
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165821"
---
# <a name="create-automation-account-using-azure-resource-manager-template"></a>Een Automation-account maken met Azure Resource Manager sjabloon

U kunt [Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-syntax.md) gebruiken om een Azure Automation-account in uw resource groep te maken. Dit artikel bevat een voorbeeld sjabloon waarmee het volgende kan worden geautomatiseerd:

* Het maken van een Azure Monitor Log Analytics werk ruimte.
* Het maken van een Azure Automation-account.
* Koppelt het Automation-account aan de Log Analytics-werk ruimte.

De sjabloon automatiseert niet de onboarding van een of meer virtuele machines van Azure of niet-Azure, of oplossingen. 

>[!NOTE]
>Het maken van het uitvoeren als-account voor Automation wordt niet ondersteund bij het gebruik van een Azure Resource Manager sjabloon. Zie [Run as-account beheren](manage-runas-account.md)als u een uitvoeren als-account hand matig wilt maken vanuit de portal of met Power shell.

## <a name="api-versions"></a>API-versies

De volgende tabel geeft een overzicht van de API-versie voor de resources die in dit voor beeld worden gebruikt.

| Resource | Resourcetype | API-versie |
|:---|:---|:---|
| Werkruimte | werkruimten | 2017-03-15-preview |
| Automation-account | automation | 2015-10-31 | 

## <a name="before-using-the-template"></a>Voordat u de sjabloon gebruikt

Als u Power shell lokaal wilt installeren en gebruiken, is voor dit artikel de Azure PowerShell AZ-module vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Zie [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps) als u een upgrade wilt uitvoeren. Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure. Met Azure PowerShell maakt de implementatie gebruik van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel de Azure CLI-versie 2.1.0 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Met Azure CLI maakt deze implementatie gebruik van [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

De JSON-sjabloon is zo geconfigureerd dat u wordt gevraagd om:

* De naam van de werk ruimte
* De regio waarin u de werk ruimte wilt maken
* De naam van het Automation-account
* De regio waarin het account moet worden gemaakt

De volgende para meters in de sjabloon zijn ingesteld met een standaard waarde voor de Log Analytics-werk ruimte:

* SKU: wordt standaard ingesteld op de nieuwe prijs categorie per GB die is uitgebracht in het prijs model van april 2018
* bewaren van gegevens-standaard ingesteld op dertig dagen
* capaciteits reservering: de standaard instelling is 100 GB

>[!WARNING]
>Als u een Log Analytics-werk ruimte maakt of configureert in een abonnement dat is aangemeld met het nieuwe prijs model van april 2018, is de enige geldige Log Analytics prijs categorie **PerGB2018**.
>

Met de JSON-sjabloon wordt een standaard waarde opgegeven voor de andere para meters die waarschijnlijk worden gebruikt als een standaard configuratie in uw omgeving. U kunt de sjabloon opslaan in een Azure-opslag account voor gedeelde toegang in uw organisatie. Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../azure-resource-manager/templates/deploy-cli.md)voor meer informatie over het werken met sjablonen.

Het is belang rijk om de volgende configuratie gegevens te begrijpen als u geen ervaring hebt met Azure Automation en Azure Monitor, om fouten te voor komen bij het maken, configureren en gebruiken van een Log Analytics werkruimte die aan uw nieuwe Automation-account is gekoppeld.

* Bekijk [aanvullende informatie](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) om de configuratie opties voor de werk ruimte volledig te begrijpen, zoals de toegangs beheer modus, de prijs categorie en de retentie en het capaciteits reserverings niveau.

* Omdat alleen bepaalde regio's worden ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account in uw abonnement, raadpleegt u [werkruimte toewijzingen](how-to/region-mappings.md) om de ondersteunde regio's inline of in een parameter bestand op te geven.

* Als u geen ervaring hebt met Azure Monitor-logboeken en nog geen werk ruimte hebt geïmplementeerd, raadpleegt u de richt lijnen voor het ontwerpen van de [werk ruimte](../azure-monitor/platform/design-logs-deployment.md) voor meer informatie over toegangs beheer en inzicht in de ontwerp implementatie strategieën die voor uw organisatie worden aanbevolen.

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
        "sku": {
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
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
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
                    "Name": "[parameters('sku')]",
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
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
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
     ]
    }
    ```

2. Bewerk de sjabloon om te voldoen aan uw vereisten. Overweeg om een [Resource Manager-parameter bestand](../azure-resource-manager/templates/parameter-files.md) te maken in plaats van para meters door te geven als inline-waarden.

3. Sla dit bestand op als deployAzAutomationAccttemplate. json naar een lokale map.

4. U kunt deze sjabloon nu implementeren. U kunt Power shell of de Azure CLI gebruiken. Wanneer u wordt gevraagd om een naam voor de werk ruimte en het Automation-account, geeft u een naam op die wereld wijd uniek is voor alle Azure-abonnementen.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure-CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    De implementatie kan enkele minuten duren. Wanneer de bewerking is voltooid, ziet u een bericht dat lijkt op het volgende:

    ![Voor beeld van resultaat wanneer de implementatie is voltooid](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Volgende stappen

Nu u een Automation-account hebt, kunt u runbooks maken en hand matige processen automatiseren.
