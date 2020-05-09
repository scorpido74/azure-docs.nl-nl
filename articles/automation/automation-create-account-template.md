---
title: Azure Resource Manager sjablonen gebruiken om een Automation-account te maken | Microsoft Docs
description: U kunt een Azure Resource Manager sjabloon gebruiken om een Azure Automation-account te maken.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 19aee9d5fdf3f4a3d74484bb7cb2e609bc2807b4
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927857"
---
# <a name="create-an-automation-account-by-using-an-azure-resource-manager-template"></a>Een Automation-account maken met behulp van een Azure Resource Manager sjabloon

U kunt [Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-syntax.md) gebruiken om een Azure Automation-account in uw resource groep te maken. Dit artikel bevat een voorbeeld sjabloon die:

* Automatiseert het maken van een Azure Monitor Log Analytics-werk ruimte.
* Automatiseert het maken van een Azure Automation-account.
* Koppelt het Automation-account aan de Log Analytics-werk ruimte.

Met de sjabloon wordt het onboarding van virtuele machines of oplossingen van Azure of niet-Azure geautomatiseerd. 

>[!NOTE]
>Het maken van het uitvoeren als-account voor Automation wordt niet ondersteund wanneer u een Azure Resource Manager sjabloon gebruikt. Zie [Run as-accounts beheren](manage-runas-account.md)als u een uitvoeren als-account hand matig wilt maken vanuit de portal of met Power shell.

## <a name="api-versions"></a>API-versies

De volgende tabel geeft een overzicht van de API-versie voor de resources die in dit voor beeld worden gebruikt.

| Resource | Resourcetype | API-versie |
|:---|:---|:---|
| Werkruimte | werkruimten | 2017-03-15-preview |
| Automation-account | automation | 2015-10-31 | 

## <a name="before-you-use-the-template"></a>Voordat u de sjabloon gebruikt

Als u Power shell lokaal wilt installeren en gebruiken, is voor dit artikel de Azure PowerShell AZ-module vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Zie [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps) als u een upgrade wilt uitvoeren. Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure. Met Power Shell maakt de implementatie gebruik van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Als u ervoor kiest om de Azure CLI lokaal te installeren en te gebruiken, moet u voor dit artikel versie 2.1.0 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Met de Azure CLI maakt deze implementatie gebruik van [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

De JSON-sjabloon is zo geconfigureerd dat u wordt gevraagd om:

* De naam van de werk ruimte.
* De regio waarin u de werk ruimte wilt maken.
* De naam van het Automation-account.
* De regio waarin het account moet worden gemaakt.

De volgende para meters in de sjabloon zijn ingesteld met een standaard waarde voor de Log Analytics-werk ruimte:

* *SKU* wordt standaard ingesteld op de prijs categorie per GB die is uitgebracht in het prijs model van april 2018.
* *dataRetention* standaard ingesteld op 30 dagen.
* *capacityReservationLevel* wordt standaard ingesteld op 100 GB.

>[!WARNING]
>Als u een Log Analytics-werk ruimte wilt maken of configureren in een abonnement dat is aangemeld bij het prijs model van april 2018, is de enige geldige Log Analytics prijs categorie *PerGB2018*.
>

Met de JSON-sjabloon wordt een standaard waarde opgegeven voor de andere para meters die waarschijnlijk worden gebruikt als een standaard configuratie in uw omgeving. U kunt de sjabloon opslaan in een Azure-opslag account voor gedeelde toegang in uw organisatie. Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../azure-resource-manager/templates/deploy-cli.md)voor meer informatie over het werken met sjablonen.

Als u geen ervaring hebt met Azure Automation en Azure Monitor, is het belang rijk dat u de volgende configuratie details kent. Ze kunnen u helpen bij het maken, configureren en gebruiken van een Log Analytics werkruimte die aan uw nieuwe Automation-account is gekoppeld. 

* Bekijk [aanvullende informatie](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) om de configuratie opties voor de werk ruimte volledig te begrijpen, zoals de toegangs beheer modus, de prijs categorie en de retentie en het capaciteits reserverings niveau.

* Bekijk [werkruimte toewijzingen](how-to/region-mappings.md) om de ondersteunde regio's inline of in een parameter bestand op te geven. Alleen bepaalde regio's worden ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account in uw abonnement.

* Als u geen ervaring hebt met Azure Monitor-logboeken en nog geen werk ruimte hebt geïmplementeerd, moet u de [richt lijnen voor werkruimte ontwerp](../azure-monitor/platform/design-logs-deployment.md)door nemen. Het helpt u meer te weten te komen over toegangs beheer en inzicht te krijgen in de ontwerp implementatie strategieën die worden aanbevolen voor uw organisatie.

## <a name="deploy-the-template"></a>De sjabloon implementeren

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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can have only 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This applies only when retention is being set to 30 days."
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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

4. U kunt deze sjabloon nu implementeren. U kunt Power shell of de Azure CLI gebruiken. Wanneer u wordt gevraagd om een naam voor de werk ruimte en het Automation-account, geeft u een naam op die wereld wijd uniek is voor al uw Azure-abonnementen.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure-CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    Het kan een paar minuten duren voordat de implementatie is voltooid. Als dit het geval is, ziet u een bericht zoals het volgende, inclusief het resultaat.

    ![Voor beeld van resultaat wanneer de implementatie is voltooid](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Volgende stappen

Nu u een Automation-account hebt, kunt u runbooks maken en hand matige processen automatiseren.

* Zie [een Power shell-Runbook maken](automation-first-runbook-textual-powershell.md)om aan de slag te gaan met Power shell-runbooks.
* Zie [een Power shell workflow-Runbook maken](automation-first-runbook-textual.md)om aan de slag te gaan met Power shell workflow-runbooks.
* Zie [een python-Runbook maken](automation-first-runbook-textual-python2.md)om aan de slag te gaan met python 2-runbooks.