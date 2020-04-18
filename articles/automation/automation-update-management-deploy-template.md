---
title: Azure Resource Manager-sjablonen gebruiken om updatebeheer in te gaan | Microsoft Documenten
description: U een Azure Resource Manager-sjabloon gebruiken om aan boord te gaan van de Azure Automation Update Management-oplossing.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/30/2020
ms.openlocfilehash: 81f9d242d93ffe513c0c3733ceb9d38ca9cadc1c
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617449"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Onboard Update Management-oplossing met Azure Resource Manager-sjabloon

U [Azure Resource Manager-sjablonen](../azure-resource-manager/templates/template-syntax.md) gebruiken om de Azure Automation Update Management-oplossing in te schakelen in uw brongroep. In dit artikel wordt een voorbeeldsjabloon opgenomen waarmee het volgende wordt geautomatist:

* Het maken van een Azure Monitor Log Analytics-werkruimte.
* Het maken van een Azure Automation-account.
* Het automatiseringsaccount koppelen aan de werkruimte Log Analytics, als deze nog niet is gekoppeld.
* Onboarding van de Azure Automation Update Management-oplossing.

De sjabloon automatiseert de onboarding van een of meer Azure- of niet-Azure VM's niet.

Als u al een Log Analytics-werkruimte en een Automatiseringsaccount hebt geïmplementeerd in een ondersteund gebied in uw abonnement, zijn deze niet gekoppeld. De werkruimte heeft nog niet de oplossing Updatebeheer geïmplementeerd. Als u deze sjabloon gebruikt, wordt de koppeling gemaakt en wordt de oplossing Updatebeheer geïmplementeerd. 

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="api-versions"></a>API-versies

In de volgende tabel worden de API-versies weergegeven voor de bronnen die in deze sjabloon worden gebruikt.

| Resource | Resourcetype | API-versie |
|:---|:---|:---|
| Werkruimte | werkruimten | 2017-03-15-preview |
| Automation-account | automation | 2015-10-31 | 
| Oplossing | oplossingen | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Voordat u de sjabloon gebruikt

Als u ervoor kiest PowerShell lokaal te installeren en te gebruiken, vereist dit artikel de Azure PowerShell Az-module. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Zie [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps) als u een upgrade wilt uitvoeren. Als u PowerShell lokaal uitvoert, moet u ook [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) uitvoeren om een verbinding met Azure te maken. Met Azure PowerShell maakt implementatie gebruik [van New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, moet u in dit artikel de Azure CLI-versie 2.1.0 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Met Azure CLI maakt deze implementatie gebruik van [het maken van az-groepsimplementatie.](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) 

De JSON-sjabloon is geconfigureerd om u te vragen om:

* De naam van de werkruimte
* De regio waarin de werkruimte moet worden gemaakt
* De naam van het automatiseringsaccount
* De regio waarin u het account aanmaken

De JSON-sjabloon geeft een standaardwaarde op voor de andere parameters die waarschijnlijk worden gebruikt voor een standaardconfiguratie in uw omgeving. U de sjabloon opslaan in een Azure-opslagaccount voor gedeelde toegang in uw organisatie. Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI voor](../azure-resource-manager/templates/deploy-cli.md)meer informatie over het werken met sjablonen.

De volgende parameters in de sjabloon zijn ingesteld met een standaardwaarde voor de werkruimte Log Analytics:

* sku - standaard de nieuwe prijscategorie per GB die is uitgebracht in het prijsmodel van april 2018
* gegevensbewaring - standaardwaarden tot dertig dagen
* capaciteitsreservering - standaard 100 GB

>[!WARNING]
>Als u een Log Analytics-werkruimte maakt of configureert in een abonnement dat is gekozen voor het prijsmodel van april 2018, is de enige geldige prijscategorie Log Analytics **PerGB2018**.
>

>[!NOTE]
>Voordat u deze sjabloon gebruikt, raadpleegt u [aanvullende details](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) om volledig inzicht te krijgen in de configuratieopties voor werkruimtes, zoals de toegangscontrolemodus, de prijscategorie, de retentie en het capaciteitsreserveringsniveau. Als u nieuw bent in Azure Monitor-logboeken en nog geen werkruimte hebt geïmplementeerd, moet u de ontwerprichtlijnen voor [werkruimtes](../azure-monitor/platform/design-logs-deployment.md) bekijken om meer te weten te komen over toegangsbeheer en inzicht te krijgen in de ontwerpimplementatiestrategieën die we voor uw organisatie aanbevelen.

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

2. Bewerk de sjabloon om aan uw vereisten te voldoen. Overweeg een [resourcebeheerbestand te](../azure-resource-manager/templates/parameter-files.md) maken in plaats van parameters door te geven als inlinewaarden.

3. Sla dit bestand op in een lokale map als **deployUMSolutiontemplate.json**.

4. U kunt deze sjabloon nu implementeren. U PowerShell of Azure CLI gebruiken. Wanneer u wordt gevraagd om een werkruimte en de naam van een Automatiseringsaccount, geeft u een naam op die wereldwijd uniek is voor alle Azure-abonnementen.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure-CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    De implementatie kan enkele minuten duren. Wanneer het is voltooid, ziet u een bericht dat vergelijkbaar is met het volgende dat het resultaat bevat:

    ![Voorbeeldresultaat wanneer de implementatie is voltooid](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Volgende stappen

Nu u de updatebeheeroplossing hebt geïmplementeerd, u VM's inschakelen voor beheer, updatebeoordelingen bekijken en updates implementeren om deze in overeenstemming te brengen.

- Vanuit uw [Azure Automation-account](automation-onboard-solutions-from-automation-account.md) voor een of meer Azure-machines en handmatig voor niet-Azure-machines.

- Voor één Azure VM vanaf de virtuele machinepagina in de Azure-portal. Dit scenario is beschikbaar voor [Linux-](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) en Windows-VM's. [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)

- Voor [meerdere Azure VM's](manage-update-multi.md) door ze te selecteren op de pagina **Virtuele machines** in de Azure-portal. 