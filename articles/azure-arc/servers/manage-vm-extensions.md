---
title: VM-extensie beheer met Azure Arc voor servers
description: Azure Arc voor servers (preview) kan de implementatie van extensies van virtuele machines beheren die configuratie van de na de implementatie en Automation-taken bieden met niet-Azure Vm's.
ms.date: 06/17/2020
ms.topic: conceptual
ms.openlocfilehash: 0319420fe528d41a23ee8fae90c4ad8c326f35a0
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121303"
---
# <a name="virtual-machine-extension-management-with-azure-arc-for-servers-preview"></a>Extensie beheer voor virtuele machines met Azure Arc voor servers (preview-versie)

Virtuele-machine-uitbrei dingen zijn kleine toepassingen die configuratie-en automatiserings taken na de implementatie leveren op Azure-Vm's. Als op een virtuele machine bijvoorbeeld software of antivirusbeveiliging moet worden geïnstalleerd of een script moet worden uitgevoerd, kan hiervoor een VM-extensie worden gebruikt.

Met Azure Arc voor servers (preview) kunt u Azure VM-extensies implementeren op niet-Azure Windows-en Linux-Vm's, waardoor u het beheer van uw hybride machine on-premises, Edge en andere Cloud omgevingen kunt vereenvoudigen door hun levens cyclus.

## <a name="key-benefits"></a>Belangrijkste voordelen

Ondersteuning voor VM-extensies van Azure Arc voor servers (preview) biedt de volgende belang rijke voor delen:

* Gebruik [Azure Automation status configuratie](../../automation/automation-dsc-overview.md) om configuraties centraal op te slaan en de gewenste status van hybride verbonden computers te onderhouden die zijn ingeschakeld via de DSC-extensie voor vm's.

* Verzamel logboek gegevens voor analyse met [Logboeken in azure monitor](../../azure-monitor/platform/data-platform-logs.md) ingeschakeld via de VM-extensie van de log Analytics agent. Dit is handig voor het uitvoeren van complexe analyses op basis van gegevens uit verschillende bronnen.

* Met [Azure monitor voor VM's](../../azure-monitor/insights/vminsights-overview.md)kunt u de prestaties van uw Windows-en Linux-vm's analyseren en de processen en afhankelijkheden van andere bronnen en externe processen controleren. Dit wordt bereikt door zowel de Log Analytics agent als de VM-extensies voor de afhankelijkheids agent in te scha kelen.

* Down load en voer scripts uit op hybride verbonden computers met de extensie voor aangepaste scripts. Deze uitbrei ding is handig voor configuratie na de implementatie, software-installatie of andere configuratie-of beheer taken.

## <a name="availability"></a>Beschikbaarheid

De functionaliteit van de VM-extensie is alleen beschikbaar in de lijst met [ondersteunde regio's](overview.md#supported-regions). Zorg ervoor dat u uw computer in een van deze regio's onboardt.

## <a name="extensions"></a>Extensies

In deze preview worden de volgende VM-extensies ondersteund op Windows-en Linux-computers.

|Extensie |Besturingssysteem |Uitgever |Aanvullende informatie |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Aangepaste script extensie voor Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Micro soft. Power shell|[Windows Power shell DSC-extensie](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics-agent |Windows |Micro soft. EnterpriseCloud. monitoring |[VM-extensie Log Analytics voor Windows](../../virtual-machines/extensions/oms-windows.md)|
|Micro soft-afhankelijkheids agent | Windows |Microsoft.Compute | [Extensie van de virtuele machine van de afhankelijkheids agent voor Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Micro soft. Azure. extension |[Aangepaste script extensie voor Linux versie 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Micro soft. OSTCExtensions |[Power shell DSC-extensie voor Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics-agent |Linux |Micro soft. EnterpriseCloud. monitoring |[VM-extensie Log Analytics voor Linux](../../virtual-machines/extensions/oms-linux.md) |
|Micro soft-afhankelijkheids agent | Linux |Microsoft.Compute | [Extensie van de virtuele machine van de afhankelijkheids agent voor Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

VM-extensies kunnen worden uitgevoerd met Azure Resource Manager sjablonen, van de Azure Portal of Azure PowerShell op hybride servers die worden beheerd door Arc voor servers (preview).

Zie [overzicht van agents](agent-overview.md#agent-component-details)voor meer informatie over het Azure Connected machine agent-pakket en Details over het onderdeel van de extensie agent.

## <a name="prerequisites"></a>Vereisten

Deze functie is afhankelijk van de volgende Azure-resource providers in uw abonnement:

* **Micro soft. HybridCompute**
* **Micro soft. GuestConfiguration**

Als ze nog niet zijn geregistreerd, volgt u de stappen onder [Azure-resource providers registreren](agent-overview.md#register-azure-resource-providers).

Voor de VM-extensie van de Log Analytics-agent voor Linux is Python 2. x geïnstalleerd op de doel computer.

### <a name="connected-machine-agent"></a>Verbonden machine agent

Controleer of uw computer overeenkomt met de [ondersteunde versies](agent-overview.md#supported-operating-systems) van het Windows-en Linux-besturings systeem voor de Azure Connected machine-agent.

De minimale versie van de verbonden machine agent die wordt ondersteund door deze functie is:

* Windows-0,7. x
* Linux-0,8. x

Zie [upgrade agent](manage-agent.md#upgrading-agent)om uw computer te upgraden naar de versie van de vereiste agent.

## <a name="enable-extensions-from-the-portal"></a>Uitbrei dingen van de portal inschakelen

VM-extensies kunnen worden toegepast op uw Arc voor Server (preview) Managed machine via de Azure Portal.

1. Ga in uw browser naar de [Azure Portal](https://aka.ms/arcserver-preview).

2. Blader in de portal naar **machines-Azure-boog** en selecteer uw hybride machine in de lijst.

3. Kies **extensies**en selecteer vervolgens **toevoegen**. Kies de gewenste uitbrei ding in de lijst met beschik bare uitbrei dingen en volg de instructies in de wizard. In dit voor beeld wordt de VM-extensie Log Analytics geïmplementeerd. 

    ![VM-extensie voor geselecteerde machine selecteren](./media/manage-vm-extensions/add-vm-extensions.png)

    In het volgende voor beeld ziet u de installatie van de Log Analytics VM-extensie van de Azure Portal:

    ![Log Analytics VM-extensie installeren](./media/manage-vm-extensions/mma-extension-config.png)

    Als u de installatie wilt volt ooien, moet u de werk ruimte-ID en de primaire sleutel opgeven. Als u niet bekend bent met het vinden van deze informatie, raadpleegt u de [werk ruimte-id en-sleutel ophalen](../../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key).

4. Nadat u de vereiste gegevens hebt bevestigd, selecteert u **maken**. Er wordt een samen vatting van de implementatie weer gegeven en u kunt de status van de implementatie controleren.

>[!NOTE]
>Hoewel meerdere uitbrei dingen tegelijk kunnen worden gebatcheerd en verwerkt, worden ze serieel geïnstalleerd. Zodra de eerste installatie van de extensie is voltooid, wordt de installatie van de volgende uitbrei ding geprobeerd.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

VM-extensies kunnen worden toegevoegd aan een Azure Resource Manager sjabloon en worden uitgevoerd met de implementatie van de sjabloon. Met de VM-extensies die door Arc voor servers worden ondersteund (preview), kunt u de ondersteunde VM-extensie implementeren op Linux-of Windows-computers met behulp van Azure PowerShell. Elk voor beeld hieronder bevat een sjabloon bestand en een parameter bestand met voorbeeld waarden die aan de sjabloon kunnen worden door gegeven.

>[!NOTE]
>Hoewel meerdere uitbrei dingen tegelijk kunnen worden gebatcheerd en verwerkt, worden ze serieel geïnstalleerd. Zodra de eerste installatie van de extensie is voltooid, wordt de installatie van de volgende uitbrei ding geprobeerd.

### <a name="deploy-the-log-analytics-vm-extension"></a>De Log Analytics VM-extensie implementeren

Om de Log Analytics-agent eenvoudig te implementeren, wordt het volgende voor beeld gegeven om de agent te installeren op Windows of Linux.

#### <a name="template-file-for-linux"></a>Sjabloon bestand voor Linux

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Sjabloon bestand voor Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Parameterbestand

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Sla de sjabloon en de parameter bestanden op schijf op en bewerk het parameter bestand met de juiste waarden voor uw implementatie. U kunt vervolgens de uitbrei ding installeren op alle verbonden computers in een resource groep met de volgende opdracht. De opdracht gebruikt de para meter *TemplateFile* om de sjabloon op te geven en de para meter *TemplateParameterFile* om een bestand op te geven dat para meters en parameter waarden bevat.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>De aangepaste script extensie implementeren

Als u de aangepaste script extensie wilt gebruiken, is het volgende voor beeld van toepassing op Windows en Linux. Zie [aangepaste script extensie voor Windows](../../virtual-machines/extensions/custom-script-windows.md) of [aangepaste script extensie voor Linux](../../virtual-machines/extensions/custom-script-linux.md)als u niet bekend bent met de aangepaste script extensie. Er zijn een aantal verschillende kenmerken die u moet begrijpen wanneer u deze uitbrei ding met hybride computers gebruikt:

* De lijst met ondersteunde besturings systemen met de aangepaste script extensie van Azure VM is niet van toepassing op Azure Arc voor servers. De lijst met ondersteunde OSs for Arc voor servers vindt u [hier](agent-overview.md#supported-operating-systems).

* Configuratie gegevens met betrekking tot Azure-Virtual Machine Scale Sets of klassieke Vm's zijn niet van toepassing.

* Als uw machines een script extern moeten downloaden en alleen via een proxy server kunnen communiceren, moet u [de verbonden machine agent configureren](manage-agent.md#update-or-remove-proxy-settings) om de omgevings variabele van de proxy server in te stellen.

De configuratie van de aangepaste script extensie bevat dingen als de script locatie en de opdracht die moet worden uitgevoerd. Deze configuratie is opgegeven in een Azure Resource Manager sjabloon die hieronder wordt weer gegeven voor de hybride machines van Linux en Windows.

#### <a name="template-file-for-linux"></a>Sjabloon bestand voor Linux

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>Sjabloon bestand voor Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Parameterbestand

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>De Power shell DSC-extensie implementeren

Als u de Power shell DSC-uitbrei ding wilt gebruiken, is het volgende voor beeld in Windows en Linux opgenomen. Als u niet bekend bent met de Power shell DSC-uitbrei ding, raadpleegt u [overzicht van DSC-extensie-handler](../../virtual-machines/extensions/dsc-overview.md). Er zijn een aantal verschillende kenmerken die u moet begrijpen wanneer u deze uitbrei ding met hybride computers gebruikt:

* De lijst met ondersteunde besturings systemen met de Azure VM Power shell DSC-extensie is niet van toepassing op Azure Arc voor servers. De lijst met ondersteunde OSs for Arc voor servers vindt u [hier](agent-overview.md#supported-operating-systems).

* Als uw machines een script extern moeten downloaden en alleen via een proxy server kunnen communiceren, moet u [de verbonden machine agent configureren](manage-agent.md#update-or-remove-proxy-settings) om de omgevings variabele van de proxy server in te stellen.

#### <a name="template-file-for-linux"></a>Sjabloon bestand voor Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Sjabloon bestand voor Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Parameterbestand

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>Agent voor afhankelijkheden

Als u de Azure Monitor dependency agent-extensie wilt gebruiken, is het volgende voor beeld in Windows en Linux opgenomen. Als u niet bekend bent met de afhankelijkheids agent, raadpleegt u [overzicht van Azure monitor agents](../../azure-monitor/platform/agents-overview.md#dependency-agent).

#### <a name="template-file-for-linux"></a>Sjabloon bestand voor Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>Sjabloon bestand voor Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="troubleshooting"></a>Problemen oplossen

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit de Azure Portal.

De volgende stappen voor probleem oplossing zijn van toepassing op alle VM-extensies.

1. Als u het logboek van de gast agent wilt controleren, bekijkt u de activiteit wanneer uw extensie is ingericht in `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` voor Windows en voor Linux onder `/var/lib/GuestConfig/ext_mgr_logs` .

2. Raadpleeg de extensie logboeken voor de specifieke extensie voor meer informatie over `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` Windows. Uitvoer van de extensie wordt vastgelegd in een bestand voor elke uitbrei ding die is geïnstalleerd in Linux onder `/var/log/GuestConfig/extension_logs` .

3. Raadpleeg de onderwerpen over probleem oplossing voor specifieke documentatie voor fout codes, bekende problemen, enzovoort. Meer informatie over het oplossen van problemen voor elke uitbrei ding vindt u in de sectie **problemen oplossen en ondersteuning** in het overzicht voor de uitbrei ding. Dit omvat de beschrijving van fout codes die naar het logboek zijn geschreven. De uitbrei ding artikelen worden gekoppeld in de [tabel extensies](#extensions) die eerder in dit artikel zijn gevonden.

4. Bekijk de systeem Logboeken. Controleer op andere bewerkingen die mogelijk zijn verstoord met de extensie, zoals een langlopende installatie van een andere toepassing die exclusieve toegang tot Package Manager vereist.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het beheren van uw machine met [Azure Policy](../../governance/policy/overview.md), voor zaken als VM- [gast configuratie](../../governance/policy/concepts/guest-configuration.md), moet u controleren of de computer rapporteert aan de verwachte log Analytics-werk ruimte, de bewaking inschakelen met [Azure monitor met vm's](../../azure-monitor/insights/vminsights-enable-policy.md)en nog veel meer.

- Meer informatie over de [log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md). De Log Analytics-agent voor Windows en Linux is vereist wanneer u het besturingssysteem en workloads op de machine proactief wilt monitoren, deze wilt beheren met Automation-runbooks of oplossingen zoals Updatebeheer, of andere Azure-services zoals [Azure Security Center](../../security-center/security-center-intro.md) wilt gebruiken.