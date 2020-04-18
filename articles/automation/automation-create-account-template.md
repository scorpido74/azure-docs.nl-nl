---
title: Azure Resource Manager-sjablonen gebruiken om automatiseringsaccount te maken | Microsoft Documenten
description: U een Azure Resource Manager-sjabloon gebruiken om een Azure Automation-account te maken.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2020
ms.openlocfilehash: efe51fbada8ac70b24c16a5c7c1e0e91879e5e9f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618683"
---
# <a name="create-automation-account-using-azure-resource-manager-template"></a>Automatiseringsaccount maken met azure resource manager-sjabloon

U [Azure Resource Manager-sjablonen](../azure-resource-manager/templates/template-syntax.md) gebruiken om een Azure Automation-account in uw brongroep te maken. In dit artikel wordt een voorbeeldsjabloon opgenomen waarmee het volgende wordt geautomatist:

* Het maken van een Azure Monitor Log Analytics-werkruimte.
* Het maken van een Azure Automation-account.
* Koppelt het automatiseringsaccount aan de werkruimte Log Analytics.

De sjabloon automatiseert de onboarding van een of meer Azure- of niet-Azure VM's of -oplossingen niet. 

>[!NOTE]
>Het maken van het Automatiseringsrun als-account wordt niet ondersteund bij het gebruik van een Azure Resource Manager-sjabloon. Zie [Uitvoeren als account](manage-runas-account.md)beheren als u handmatig een Run As-account wilt maken.

## <a name="api-versions"></a>API-versies

In de volgende tabel vindt u de API-versie voor de resources die in dit voorbeeld worden gebruikt.

| Resource | Resourcetype | API-versie |
|:---|:---|:---|
| Werkruimte | werkruimten | 2017-03-15-preview |
| Automation-account | automation | 2015-10-31 | 

## <a name="before-using-the-template"></a>Voordat u de sjabloon gebruikt

Als u ervoor kiest PowerShell lokaal te installeren en te gebruiken, vereist dit artikel de Azure PowerShell Az-module. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Zie [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps) als u een upgrade wilt uitvoeren. Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure. Met Azure PowerShell maakt implementatie gebruik [van New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, moet u in dit artikel de Azure CLI-versie 2.1.0 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Met Azure CLI maakt deze implementatie gebruik van [het maken van az-groepsimplementatie.](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) 

De JSON-sjabloon is geconfigureerd om u te vragen om:

* De naam van de werkruimte
* De regio waarin de werkruimte moet worden gemaakt
* De naam van het automatiseringsaccount
* De regio om het account aan te maken in

De JSON-sjabloon geeft een standaardwaarde op voor de andere parameters die waarschijnlijk als standaardconfiguratie in uw omgeving worden gebruikt. U de sjabloon opslaan in een Azure-opslagaccount voor gedeelde toegang in uw organisatie. Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI voor](../azure-resource-manager/templates/deploy-cli.md)meer informatie over het werken met sjablonen.

De volgende parameters in de sjabloon zijn ingesteld met een standaardwaarde voor de werkruimte Log Analytics:

* sku - standaard de nieuwe prijscategorie per GB die is uitgebracht in het prijsmodel van april 2018
* gegevensbewaring - standaardwaarden tot dertig dagen
* capaciteitsreservering - standaard 100 GB

>[!WARNING]
>Als u een Log Analytics-werkruimte maakt of configureert in een abonnement dat is gekozen voor het nieuwe prijsmodel van april 2018, is de enige geldige prijscategorie Log Analytics **PerGB2018**.
>

>[!NOTE]
>Voordat u deze sjabloon gebruikt, raadpleegt u [aanvullende details](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) om volledig inzicht te krijgen in de configuratieopties voor werkruimtes, zoals de toegangscontrolemodus, de prijscategorie, de retentie- en capaciteitsreserveringsniveau. Als u nieuw bent in Azure Monitor-logboeken en nog geen werkruimte hebt geïmplementeerd, moet u de ontwerprichtlijnen voor [werkruimtes](../azure-monitor/platform/design-logs-deployment.md) bekijken om meer te weten te komen over toegangsbeheer en inzicht in de ontwerpimplementatiestrategieën die we voor uw organisatie aanbevelen.

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

2. Bewerk de sjabloon om aan uw vereisten te voldoen. Overweeg een [resourcebeheerbestand te](../azure-resource-manager/templates/parameter-files.md) maken in plaats van parameters door te geven als inlinewaarden.

3. Sla dit bestand op als deployAzAutomationAccttemplate.json in een lokale map.

4. U kunt deze sjabloon nu implementeren. U PowerShell of Azure CLI gebruiken. Wanneer u wordt gevraagd om een werkruimte en de naam van een Automatiseringsaccount, geeft u een naam op die wereldwijd uniek is voor alle Azure-abonnementen.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure-CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    De implementatie kan enkele minuten duren. Wanneer het is voltooid, ziet u een bericht dat vergelijkbaar is met het volgende dat het resultaat bevat:

    ![Voorbeeldresultaat wanneer de implementatie is voltooid](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Volgende stappen

Nu u een Automation-account hebt, u runbooks maken en handmatige processen automatiseren.
