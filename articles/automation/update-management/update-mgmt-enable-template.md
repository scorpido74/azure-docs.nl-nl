---
title: Updatebeheer met Azure Resource Manager sjabloon inschakelen | Microsoft Docs
description: In dit artikel leest u hoe u een Azure Resource Manager sjabloon kunt gebruiken om Updatebeheer in te scha kelen.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/10/2020
ms.openlocfilehash: c4b29db8bbcb741116fcd425b4489973228066e6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021592"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Updatebeheer inschakelen met behulp van Azure Resource Manager-sjabloon

U kunt een [Azure Resource Manager sjabloon](../../azure-resource-manager/templates/template-syntax.md) gebruiken om de functie Azure Automation updatebeheer in uw resource groep in te scha kelen. Dit artikel bevat een voorbeeld sjabloon waarmee het volgende kan worden geautomatiseerd:

* Het maken van een Azure Monitor Log Analytics werk ruimte.
* Het maken van een Azure Automation-account.
* Koppel het Automation-account aan de Log Analytics-werk ruimte, als dat nog niet is gekoppeld.
* Updatebeheer inschakelen.

Met de sjabloon wordt het inschakelen van Updatebeheer op een of meer virtuele machines in azure of niet-Azure niet geautomatiseerd.

Als u al een Log Analytics werk ruimte en een Automation-account hebt geïmplementeerd in een ondersteunde regio in uw abonnement, zijn deze niet gekoppeld. Met deze sjabloon maakt u de koppeling en implementeert u Updatebeheer.

## <a name="api-versions"></a>API-versies

De volgende tabel geeft een lijst van de API-versies voor de resources die in deze sjabloon worden gebruikt.

| Resource | Resourcetype | API-versie |
|:---|:---|:---|
| Werkruimte | workspaces | 2020-03-01-preview |
| Automation-account | automation | 2018-06-30 |
| Oplossing | oplossingen | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Voordat u de sjabloon gebruikt

Als u Power shell lokaal wilt installeren en gebruiken, is voor dit artikel de Azure PowerShell AZ-module vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Zie [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps) als u een upgrade wilt uitvoeren. Als u Power shell lokaal uitvoert, moet u ook [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) uitvoeren om een verbinding te maken met Azure. Met Azure PowerShell maakt de implementatie gebruik van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel de Azure CLI-versie 2.1.0 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Met Azure CLI maakt deze implementatie gebruik van [AZ Group Deployment Create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

De JSON-sjabloon is zo geconfigureerd dat u wordt gevraagd om:

* De naam van de werkruimte.
* De regio waarin u de werk ruimte wilt maken.
* Om resource-of werkruimte machtigingen in te scha kelen.
* De naam van het Automation-account.
* De regio waarin het account moet worden gemaakt.

Met de JSON-sjabloon wordt een standaard waarde opgegeven voor de andere para meters die waarschijnlijk zullen worden gebruikt voor een standaard configuratie in uw omgeving. U kunt de sjabloon opslaan in een Azure-opslagaccount voor gedeelde toegang in uw organisatie. Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../../azure-resource-manager/templates/deploy-cli.md)voor meer informatie over het werken met sjablonen.

De volgende parameters in de sjabloon hebben een standaardwaarde voor de Log Analytics-werkruimte:

* SKU: wordt standaard ingesteld op de nieuwe prijs categorie per GB die is uitgebracht in het prijs model van april 2018
* bewaren van gegevens-standaard ingesteld op dertig dagen

>[!WARNING]
>Als u een Log Analytics-werk ruimte maakt of configureert in een abonnement dat is aangemeld met het nieuwe prijs model van april 2018, is de enige geldige Log Analytics prijs categorie **PerGB2018**.
>

De JSON-sjabloon bevat standaardwaarden voor de andere parameters die waarschijnlijk worden gebruikt in een standaardconfiguratie in uw omgeving. U kunt de sjabloon opslaan in een Azure-opslagaccount voor gedeelde toegang in uw organisatie. Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../../azure-resource-manager/templates/deploy-cli.md)voor meer informatie over het werken met sjablonen.

Het is belang rijk om de volgende configuratie gegevens te begrijpen als u geen ervaring hebt met Azure Automation en Azure Monitor, om fouten te voor komen bij het maken, configureren en gebruiken van een Log Analytics werkruimte die aan uw nieuwe Automation-account is gekoppeld.

* Bekijk [aanvullende informatie](../../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) om de configuratie opties voor de werk ruimte volledig te begrijpen, zoals de toegangs beheer modus, de prijs categorie en de retentie en het capaciteits reserverings niveau.

* Omdat alleen bepaalde regio's worden ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account in uw abonnement, raadpleegt u [werkruimte toewijzingen](../how-to/region-mappings.md) om de ondersteunde regio's inline of in een parameter bestand op te geven.

* Als u geen ervaring hebt met Azure Monitor-logboeken en nog geen werk ruimte hebt geïmplementeerd, raadpleegt u de richt lijnen voor het ontwerpen van de [werk ruimte](../../azure-monitor/platform/design-logs-deployment.md) voor meer informatie over toegangs beheer en inzicht in de ontwerp implementatie strategieën die voor uw organisatie worden aanbevolen.

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
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "resourcePermissions": {
              "type": "bool",
              "metadata": {
                "description": "true to use resource or workspace permissions. false to require workspace permissions."
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
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]"
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
                    "location": "[parameters('location')]",
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
            "apiVersion": "2018-06-30",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            }
        },
        {
            "apiVersion": "2020-03-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[parameters('location')]",
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

2. Bewerk de sjabloon om te voldoen aan uw vereisten. Overweeg om een [Resource Manager-parameter bestand](../../azure-resource-manager/templates/parameter-files.md) te maken in plaats van para meters door te geven als inline-waarden.

3. Sla dit bestand op in een lokale map als **deployUMSolutiontemplate.jsop**.

4. U kunt deze sjabloon nu implementeren. U kunt Power shell of de Azure CLI gebruiken. Wanneer u wordt gevraagd om een naam voor de werk ruimte en het Automation-account, geeft u een naam op die wereld wijd uniek is voor alle Azure-abonnementen.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure-CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    De implementatie kan enkele minuten duren. Wanneer de bewerking is voltooid, ziet u een bericht dat lijkt op het volgende:

    ![Voorbeeld van uitvoer als de implementatie is voltooid](media/update-mgmt-enable-template/template-output.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [updates en patches voor uw virtuele machines beheren](update-mgmt-manage-updates-for-vm.md)voor meer informatie over het gebruik van updatebeheer voor vm's.

* Als u Updatebeheer niet meer wilt gebruiken en wilt verwijderen, raadpleegt u de instructies in de [functie updatebeheer verwijderen](update-mgmt-remove-feature.md).

* Als u VM's uit Updatebeheer wilt verwijderen, raadpleegt u [VM's uit Updatebeheer verwijderen](update-mgmt-remove-vms.md).
