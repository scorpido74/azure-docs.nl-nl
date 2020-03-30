---
title: Azure Automation-resources in beheeroplossingen | Microsoft Documenten
description: Beheeroplossingen omvatten doorgaans runbooks in Azure Automation om processen zoals het verzamelen en verwerken van bewakingsgegevens te automatiseren.  In dit artikel wordt beschreven hoe u runbooks en bijbehorende bronnen in een oplossing opnemen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ef9f27546e9db95d5a41769e1b5bc7bc0c2f851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663059"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Azure Automation-resources toevoegen aan een beheeroplossing (Voorbeeld)
> [!NOTE]
> Dit is voorlopige documentatie voor het maken van managementoplossingen die momenteel in preview zijn. Elk schema dat hieronder wordt beschreven, kan worden gewijzigd.   


[Beheeroplossingen]( solutions.md) omvatten doorgaans runbooks in Azure Automation om processen zoals het verzamelen en verwerken van bewakingsgegevens te automatiseren.  Naast runbooks bevatten Automatiseringsaccounts activa zoals variabelen en schema's die de runbooks ondersteunen die in de oplossing worden gebruikt.  In dit artikel wordt beschreven hoe u runbooks en bijbehorende bronnen in een oplossing opnemen.

> [!NOTE]
> De voorbeelden in dit artikel gebruiken parameters en variabelen die vereist of gemeenschappelijk zijn voor beheeroplossingen en worden beschreven in [Ontwerp en een beheeroplossing bouwen in Azure]( solutions-creating.md) 


## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u al bekend bent met de volgende informatie.

- Hoe [maak je een management oplossing]( solutions-creating.md).
- De structuur van een [oplossingsbestand]( solutions-solution-file.md).
- [Resourcebeheersjablonen maken](../../azure-resource-manager/templates/template-syntax.md)

## <a name="automation-account"></a>Automation-account
Alle resources in Azure Automation bevinden zich in een [Automatiseringsaccount](../../automation/automation-security-overview.md#automation-account-overview).  Zoals beschreven in [log Analytics-werkruimte en automatiseringsaccount,]( solutions.md#log-analytics-workspace-and-automation-account) is het automatiseringsaccount niet opgenomen in de beheeroplossing, maar moet het bestaan voordat de oplossing wordt geïnstalleerd.  Als het niet beschikbaar is, dan is de oplossing te installeren zal mislukken.

De naam van elke automatiseringsbron bevat de naam van het automatiseringsaccount.  Dit gebeurt in de oplossing met de parameter **accountName** zoals in het volgende voorbeeld van een runbook-bron.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
U moet alle runbooks die door de oplossing worden gebruikt, opnemen in het oplossingsbestand, zodat ze worden gemaakt wanneer de oplossing is geïnstalleerd.  U de hoofdtekst van het runbook echter niet in de sjabloon bevatten, dus u moet het runbook publiceren naar een openbare locatie waar het kan worden geopend door elke gebruiker die uw oplossing installeert.

[Azure Automation runbook resources](../../automation/automation-runbook-types.md) hebben een type **Microsoft.Automation/automationAccounts/runbooks** en hebben de volgende structuur. Dit omvat veelvoorkomende variabelen en parameters, zodat u dit codefragment kopiëren en plakken in uw oplossingsbestand en de parameternamen wijzigen. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


De eigenschappen voor runbooks worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| runbookType |Hiermee geeft u de typen van het runbook op. <br><br> Script - PowerShell-script <br>PowerShell - PowerShell-workflow <br> GraphPowerShell - Grafisch PowerShell-scriptrunboek <br> GraphPowerShellWorkflow - Grafische PowerShell-werkstroomrunboek |
| logVoortgang |Hiermee geeft u op of [voortgangsrecords](../../automation/automation-runbook-output-and-messages.md) moeten worden gegenereerd voor het runbook. |
| logVerbose logVerbose |Hiermee geeft u op of [verboserecords](../../automation/automation-runbook-output-and-messages.md) moeten worden gegenereerd voor het runbook. |
| description |Optionele beschrijving voor het runbook. |
| ContentLink publiceren |Hiermee geeft u de inhoud van het runbook op. <br><br>uri - Uri naar de inhoud van het runbook.  Dit is een .ps1-bestand voor PowerShell- en Script-runbooks en een geëxporteerd grafisch runbook-bestand voor een grafiekrunbook.  <br> versie - Versie van het runbook voor uw eigen tracking. |


## <a name="automation-jobs"></a>Automatiseringstaken
Wanneer u een runbook start in Azure Automation, wordt een automatiseringstaak gemaakt.  U een automatiseringstaakbron toevoegen aan uw oplossing om automatisch een runbook te starten wanneer de beheeroplossing is geïnstalleerd.  Deze methode wordt meestal gebruikt om runbooks te starten die worden gebruikt voor de eerste configuratie van de oplossing.  Als u een runbook op regelmatige tijdstippen wilt starten, maakt u een [planning](#schedules) en een [taakschema](#job-schedules)

Taakresources hebben een type **Microsoft.Automation/automationAccounts/jobs** en hebben de volgende structuur.  Dit omvat veelvoorkomende variabelen en parameters, zodat u dit codefragment kopiëren en plakken in uw oplossingsbestand en de parameternamen wijzigen. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

De eigenschappen voor automatiseringstaken worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| runbook |Entiteit met één naam met de naam van het runbook om te beginnen. |
| parameters |Entiteit voor elke parameterwaarde die vereist is voor het runbook. |

De taak bevat de naam van het runbook en eventuele parameterwaarden die naar het runbook moeten worden verzonden.  De taak moet [afhangen van]( solutions-solution-file.md#resources) het runbook dat het begint, omdat het runbook moet worden gemaakt vóór de taak.  Als u meerdere runbooks hebt die moeten worden gestart, u hun volgorde definiëren door een taak te laten afhangen van andere taken die eerst moeten worden uitgevoerd.

De naam van een taakbron moet een GUID bevatten die doorgaans door een parameter is toegewezen.  U meer lezen over GUID-parameters in [Het maken van een beheeroplossingsbestand in Azure.]( solutions-solution-file.md#parameters)  


## <a name="certificates"></a>Certificaten
[Azure Automation-certificaten](../../automation/automation-certificates.md) hebben een type **Microsoft.Automation/automationAccounts/certificaten** en hebben de volgende structuur. Dit omvat veelvoorkomende variabelen en parameters, zodat u dit codefragment kopiëren en plakken in uw oplossingsbestand en de parameternamen wijzigen. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



De eigenschappen voor bronnen certificaten worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| base64Waarde |Basis 64 waarde voor het certificaat. |
| Vingerafdruk |Duimafdruk voor het certificaat. |



## <a name="credentials"></a>Referenties
[Azure Automation-referenties](../../automation/automation-credentials.md) hebben een type **Microsoft.Automation/automationAccounts/credentials** en hebben de volgende structuur.  Dit omvat veelvoorkomende variabelen en parameters, zodat u dit codefragment kopiëren en plakken in uw oplossingsbestand en de parameternamen wijzigen. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

De eigenschappen voor referentiebronnen worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| userName |Gebruikersnaam voor de referentie. |
| wachtwoord |Wachtwoord voor de referentie. |


## <a name="schedules"></a>Planningen
[Azure Automation-schema's](../../automation/automation-schedules.md) hebben een type **Microsoft.Automation/automationAccounts/schema's** en hebben de volgende structuur. Dit omvat veelvoorkomende variabelen en parameters, zodat u dit codefragment kopiëren en plakken in uw oplossingsbestand en de parameternamen wijzigen. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

De eigenschappen voor planningsresources worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| description |Optionele beschrijving voor het schema. |
| startTime |Hiermee geeft u de begintijd van een planning op als een datetime-object. Er kan een tekenreeks worden verstrekt als deze kan worden geconverteerd naar een geldige DateTime. |
| Isenabled |Hiermee geeft u op of de planning is ingeschakeld. |
| interval |Het type interval voor het schema.<br><br>day<br>uur |
| frequency |Frequentie dat het schema moet vuren in aantal dagen of uren. |

Schema's moeten een begintijd hebben met een waarde die groter is dan de huidige tijd.  U deze waarde niet voorzien van een variabele, omdat u geen manier zou hebben om te weten wanneer deze zal worden geïnstalleerd.

Gebruik een van de volgende twee strategieën bij het gebruik van planningsbronnen in een oplossing.

- Gebruik een parameter voor de begintijd van de planning.  Dit zal de gebruiker vragen om een waarde te verstrekken wanneer hij de oplossing installeert.  Als u meerdere schema's hebt, u één parameterwaarde gebruiken voor meer dan één van deze schema's.
- Maak de schema's met behulp van een runbook dat begint wanneer de oplossing is geïnstalleerd.  Hierdoor wordt de vereiste van de gebruiker verwijderd om een tijd op te geven, maar u het schema niet in uw oplossing bevatten, zodat deze wordt verwijderd wanneer de oplossing wordt verwijderd.


### <a name="job-schedules"></a>Jobplanningen
Bronnen voor taakplanning koppelen een runbook aan een planning.  Ze hebben een type **Microsoft.Automation/automationAccounts/jobSchedules** en hebben de volgende structuur.  Dit omvat veelvoorkomende variabelen en parameters, zodat u dit codefragment kopiëren en plakken in uw oplossingsbestand en de parameternamen wijzigen. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


De eigenschappen voor taakroosters worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| naam plannen |Entiteit met één **naam** met de naam van het schema. |
| runbook naam  |Entiteit met één **naam** met de naam van het runbook.  |



## <a name="variables"></a>Variabelen
[Azure Automation-variabelen](../../automation/automation-variables.md) hebben een type **Microsoft.Automation/automationAccounts/variabelen** en hebben de volgende structuur.  Dit omvat veelvoorkomende variabelen en parameters, zodat u dit codefragment kopiëren en plakken in uw oplossingsbestand en de parameternamen wijzigen.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

De eigenschappen voor variabele resources worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| description | Optionele beschrijving voor de variabele. |
| isVersleuteld | Hiermee geeft u op of de variabele moet worden versleuteld. |
| type | Deze eigenschap heeft momenteel geen effect.  Het gegevenstype van de variabele wordt bepaald door de initiële waarde. |
| waarde | Waarde voor de variabele. |

> [!NOTE]
> De **eigenschap type** heeft momenteel geen invloed op de variabele die wordt gemaakt.  Het gegevenstype voor de variabele wordt bepaald door de waarde.  

Als u de initiële waarde voor de variabele instelt, moet deze worden geconfigureerd als het juiste gegevenstype.  In de volgende tabel worden de verschillende toegestane gegevenstypen en de syntaxis ervan weergegeven.  Houd er rekening mee dat waarden in JSON naar verwachting altijd worden ingesloten tussen aanhalingstekens met speciale tekens in de aanhalingstekens.  Een tekenreekswaarde wordt bijvoorbeeld opgegeven door aanhalingstekens rond\\de tekenreeks (met behulp van het escape-teken ( )), terwijl een numerieke waarde wordt opgegeven met één set aanhalingstekens.

| Gegevenstype | Beschrijving | Voorbeeld | Lost op om |
|:--|:--|:--|:--|
| tekenreeks   | Omsluiten waarde in dubbele aanhalingstekens.  | Hallo\"wereld\" | "Hallo wereld" |
| numeriek  | Numerieke waarde met enkele aanhalingstekens.| "64" | 64 |
| booleaans  | **waar** of **onwaar** tussen aanhalingstekens.  Houd er rekening mee dat deze waarde kleine letters moet zijn. | "Waar" | waar |
| datum/tijd | Geserialiseerde datumwaarde.<br>U de cmdlet ConvertTo-Json in PowerShell gebruiken om deze waarde voor een bepaalde datum te genereren.<br>Voorbeeld: get-date "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Datum(1495656897378)\\ | 2017-05-24 13:14:57 |

## <a name="modules"></a>Modules
Uw beheeroplossing hoeft geen [globale modules](../../automation/automation-integration-modules.md) te definiëren die door uw runbooks worden gebruikt, omdat ze altijd beschikbaar zijn in uw Automatiseringsaccount.  U moet wel een bron opnemen voor elke andere module die door uw runbooks wordt gebruikt.

[Integratiemodules](../../automation/automation-integration-modules.md) hebben een type **Microsoft.Automation/automationAccounts/modules** en hebben de volgende structuur.  Dit omvat veelvoorkomende variabelen en parameters, zodat u dit codefragment kopiëren en plakken in uw oplossingsbestand en de parameternamen wijzigen.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


De eigenschappen voor modulebronnen worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| contentLink |Hiermee geeft u de inhoud van de module op. <br><br>uri - Uri naar de inhoud van de module.  Dit is een .ps1-bestand voor PowerShell- en Script-runbooks en een geëxporteerd grafisch runbook-bestand voor een grafiekrunbook.  <br> versie - Versie van de module voor uw eigen tracking. |

Het runbook moet afhankelijk zijn van de modulebron om ervoor te zorgen dat het is gemaakt vóór het runbook.

### <a name="updating-modules"></a>Modules bijwerken
Als u een beheeroplossing bijwerkt die een runbook bevat die een planning gebruikt, en de nieuwe versie van uw oplossing een nieuwe module heeft die door dat runbook wordt gebruikt, kan het runbook de oude versie van de module gebruiken.  U moet de volgende runbooks in uw oplossing opnemen en een taak maken om ze uit te voeren voordat andere runbooks.  Dit zorgt ervoor dat alle modules worden bijgewerkt zoals vereist voordat de runbooks worden geladen.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) zorgt ervoor dat alle modules die door runbooks in uw oplossing worden gebruikt, de nieuwste versie zijn.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) registreert alle planningsbronnen opnieuw om ervoor te zorgen dat de runbooks die eraan zijn gekoppeld met behulp van de nieuwste modules.




## <a name="sample"></a>Voorbeeld
Hieronder volgt een voorbeeld van een oplossing die de volgende bronnen bevat:

- Runbook.  Dit is een voorbeeld van runbook die is opgeslagen in een openbare GitHub-opslagplaats.
- Automatiseringstaak waarmee het runbook wordt gestart wanneer de oplossing is geïnstalleerd.
- Plannen en werkschema om het runbook te starten op regelmatige tijdstippen.
- Certificaat.
- Referentie.
- Variabele.
- Module.  Dit is de [OMSIngestionAPI-module](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) voor het schrijven van gegevens naar Log Analytics. 

Het voorbeeld maakt gebruik van [standaardparametersparametersvariabelen]( solutions-solution-file.md#parameters) die vaak in een oplossing worden gebruikt in tegenstelling tot hardcoderingswaarden in de brondefinities.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for schedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Volgende stappen
* [Voeg een weergave toe aan uw oplossing om]( solutions-resources-views.md) verzamelde gegevens te visualiseren.
