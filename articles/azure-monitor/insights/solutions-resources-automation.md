---
title: Azure Automation bronnen in beheer oplossingen | Microsoft Docs
description: Beheer oplossingen bevatten doorgaans runbooks in Azure Automation voor het automatiseren van processen, zoals het verzamelen en verwerken van bewakings gegevens.  In dit artikel wordt beschreven hoe u runbooks en hun gerelateerde resources opneemt in een oplossing.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a3b1b134afbc4a13d7888281a82609d444cee377
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682866"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Azure Automation-resources toevoegen aan een beheer oplossing (preview)
> [!NOTE]
> Dit is voorlopige documentatie voor het maken van beheer oplossingen die momenteel als preview-versie beschikbaar zijn. Elk schema dat hieronder wordt beschreven, kan worden gewijzigd.   


[Beheer oplossingen]( solutions.md) bevatten doorgaans runbooks in azure Automation voor het automatiseren van processen, zoals het verzamelen en verwerken van bewakings gegevens.  Automation-accounts bevatten naast runbooks ook activa zoals variabelen en schema's die ondersteuning bieden voor de runbooks die in de oplossing worden gebruikt.  In dit artikel wordt beschreven hoe u runbooks en hun gerelateerde resources opneemt in een oplossing.

> [!NOTE]
> In de voor beelden in dit artikel worden para meters en variabelen gebruikt die vereist zijn of gemeen schappelijk zijn voor beheer oplossingen en die worden beschreven in [ontwerp en bouw een beheer oplossing in azure]( solutions-creating.md) 


## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u al bekend bent met de volgende informatie.

- [Een beheer oplossing maken]( solutions-creating.md).
- De structuur van een [oplossings bestand]( solutions-solution-file.md).
- [Resource Manager-sjablonen ontwerpen](../../azure-resource-manager/templates/template-syntax.md)

## <a name="automation-account"></a>Automation-account
Alle resources in Azure Automation zijn opgenomen in een [Automation-account](../../automation/automation-security-overview.md).  Zoals beschreven in [log Analytics werk ruimte en het Automation-account]( solutions.md#log-analytics-workspace-and-automation-account) is het Automation-account niet opgenomen in de beheer oplossing, maar moet het bestaan voordat de oplossing wordt geïnstalleerd.  Als deze niet beschikbaar is, mislukt de installatie van de oplossing.

De naam van elke Automation-bron bevat de naam van het Automation-account.  Dit gebeurt in de oplossing met de para meter **AccountName** , zoals in het volgende voor beeld van een runbook-resource.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
U moet alle runbooks die door de oplossing in het oplossings bestand worden gebruikt, gebruiken om ze te maken wanneer de oplossing is geïnstalleerd.  U kunt de hoofd tekst van het runbook niet in de sjabloon opnemen. Daarom moet u het runbook publiceren naar een open bare locatie waar het kan worden geopend door elke gebruiker die uw oplossing installeert.

[Azure Automation runbook](../../automation/automation-runbook-types.md) -resources hebben het type **micro soft. Automation/automationAccounts/runbooks** en hebben de volgende structuur. Dit omvat algemene variabelen en para meters, zodat u dit code fragment kunt kopiëren en plakken in het oplossings bestand en de parameter namen wijzigt. 

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
| runbookType |Hiermee geeft u de typen van het runbook op. <br><br> Script-Power shell-script <br>Power shell-Power shell-werk stroom <br> GraphPowerShell-grafisch Power shell-script runbook <br> GraphPowerShellWorkflow-grafisch Power shell-werk stroom-runbook |
| logProgress |Hiermee geeft u op of [voortgangs records](../../automation/automation-runbook-output-and-messages.md) moeten worden gegenereerd voor het runbook. |
| logVerbose |Hiermee wordt aangegeven of [uitgebreide records](../../automation/automation-runbook-output-and-messages.md) moeten worden gegenereerd voor het runbook. |
| beschrijving |Optionele beschrijving voor het runbook. |
| publishContentLink |Hiermee geeft u de inhoud van het runbook op. <br><br>URI-URI naar de inhoud van het runbook.  Dit is een. ps1-bestand voor Power shell-en script-runbooks en een geëxporteerd grafisch runbook-bestand voor een grafiek runbook.  <br> versie-versie van het runbook voor uw eigen tracking. |


## <a name="automation-jobs"></a>Automation-taken
Wanneer u een runbook start in Azure Automation, wordt er een Automation-taak gemaakt.  U kunt een Automation-taak resource toevoegen aan uw oplossing om automatisch een runbook te starten wanneer de beheer oplossing is geïnstalleerd.  Deze methode wordt doorgaans gebruikt om runbooks te starten die worden gebruikt voor de initiële configuratie van de oplossing.  Een [planning](#schedules) en een [taak schema](#job-schedules) maken om een runbook met regel matige tussen pozen te starten

Taak resources hebben het type **micro soft. Automation/automationAccounts/Jobs** en hebben de volgende structuur.  Dit omvat algemene variabelen en para meters, zodat u dit code fragment kunt kopiëren en plakken in het oplossings bestand en de parameter namen wijzigt. 

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

De eigenschappen voor Automation-taken worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| runbook |Entiteit met één naam en de naam van het runbook dat moet worden gestart. |
| parameters |Entiteit voor elke parameter waarde die wordt vereist door het runbook. |

De taak bevat de naam van het runbook en de parameter waarden die naar het runbook moeten worden verzonden.  De taak moet [afhankelijk]( solutions-solution-file.md#resources) zijn van het runbook dat wordt gestart, omdat het runbook moet worden gemaakt voor de taak.  Als u meerdere runbooks hebt die moeten worden gestart, kunt u de volg orde definiëren door een taak te laten afhangen van andere taken die eerst moeten worden uitgevoerd.

De naam van een taak resource moet een GUID bevatten die meestal wordt toegewezen door een para meter.  U kunt meer lezen over de GUID-para meters in het [maken van een beheer oplossings bestand in azure]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certificaten
[Azure Automation certificaten](../../automation/automation-certificates.md) zijn van het type **micro soft. Automation/automationAccounts/certificates** en hebben de volgende structuur. Dit omvat algemene variabelen en para meters, zodat u dit code fragment kunt kopiëren en plakken in het oplossings bestand en de parameter namen wijzigt. 

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



De eigenschappen voor certificaten bronnen worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| base64Value |Basis 64 waarde voor het certificaat. |
| vingerafdruk |Vinger afdruk voor het certificaat. |



## <a name="credentials"></a>Referenties
[Azure Automation referenties](../../automation/automation-credentials.md) hebben het type **micro soft. Automation/automationAccounts/credentials** en hebben de volgende structuur.  Dit omvat algemene variabelen en para meters, zodat u dit code fragment kunt kopiëren en plakken in het oplossings bestand en de parameter namen wijzigt. 


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

De eigenschappen voor referentie bronnen worden in de volgende tabel beschreven.

| Eigenschap | Beschrijving |
|:--- |:--- |
| userName |De gebruikers naam voor de referentie. |
| wachtwoord |Wacht woord voor de referentie. |


## <a name="schedules"></a>Planningen
[Azure Automation planningen](../../automation/automation-schedules.md) hebben het type **micro soft. Automation/automationAccounts/schedules** en hebben de volgende structuur. Dit omvat algemene variabelen en para meters, zodat u dit code fragment kunt kopiëren en plakken in het oplossings bestand en de parameter namen wijzigt. 

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

De eigenschappen voor plannings resources worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| beschrijving |Optionele beschrijving voor het schema. |
| startTime |Hiermee geeft u de begin tijd van een schema op als een DateTime-object. Er kan een teken reeks worden gegeven als deze kan worden geconverteerd naar een geldige datum/tijd. |
| isEnabled |Hiermee geeft u op of de planning is ingeschakeld. |
| interval |Het type interval voor het schema.<br><br>day<br>uur |
| frequency |De frequentie waarmee het schema binnen dagen of uren moet worden geactiveerd. |

Schema's moeten een begin tijd hebben met een waarde die hoger is dan de huidige tijd.  U kunt deze waarde niet opgeven met een variabele omdat u er geen manier van hoeft te weten wanneer deze wordt geïnstalleerd.

Gebruik een van de volgende twee strategieën bij het gebruik van resources plannen in een oplossing.

- Gebruik een para meter voor de begin tijd van de planning.  Hiermee wordt de gebruiker gevraagd een waarde op te geven wanneer de oplossing wordt geïnstalleerd.  Als u meerdere schema's hebt, kunt u één parameter waarde gebruiken voor meer dan een van deze.
- Maak de planningen met behulp van een runbook dat wordt gestart wanneer de oplossing wordt geïnstalleerd.  Hiermee verwijdert u de vereiste van de gebruiker om een tijd op te geven, maar u kunt de planning niet in uw oplossing opnemen zodat deze wordt verwijderd wanneer de oplossing wordt verwijderd.


### <a name="job-schedules"></a>Jobplanningen
Taak planning resources koppelen een runbook met een schema.  Ze hebben het type **micro soft. Automation/automationAccounts/jobSchedules** en hebben de volgende structuur.  Dit omvat algemene variabelen en para meters, zodat u dit code fragment kunt kopiëren en plakken in het oplossings bestand en de parameter namen wijzigt. 

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


De eigenschappen voor taak schema's worden in de volgende tabel beschreven.

| Eigenschap | Beschrijving |
|:--- |:--- |
| schema naam |Entiteit met één **naam** en de naam van het schema. |
| runbooknaam  |Entiteit met één **naam** en de naam van het runbook.  |



## <a name="variables"></a>Variabelen
[Azure Automation variabelen](../../automation/automation-variables.md) hebben het type **micro soft. Automation/automationAccounts/Varia bles** en hebben de volgende structuur.  Dit omvat algemene variabelen en para meters, zodat u dit code fragment kunt kopiëren en plakken in het oplossings bestand en de parameter namen wijzigt.

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

De eigenschappen voor variabelen bronnen worden in de volgende tabel beschreven.

| Eigenschap | Beschrijving |
|:--- |:--- |
| beschrijving | Optionele beschrijving voor de variabele. |
| isEncrypted | Hiermee geeft u op of de variabele moet worden versleuteld. |
| type | Deze eigenschap heeft momenteel geen effect.  Het gegevens type van de variabele wordt bepaald door de oorspronkelijke waarde. |
| waarde | Waarde voor de variabele. |

> [!NOTE]
> De eigenschap **type** heeft momenteel geen effect op de variabele die wordt gemaakt.  Het gegevens type voor de variabele wordt bepaald door de waarde.  

Als u de aanvankelijke waarde voor de variabele instelt, moet deze worden geconfigureerd als het juiste gegevens type.  De volgende tabel bevat de verschillende typen gegevens die kunnen worden en de syntaxis ervan.  Houd er rekening mee dat waarden die in JSON worden verwacht, altijd tussen aanhalings tekens worden geplaatst.  Zo zou een teken reeks waarde worden opgegeven door aanhalings tekens rond de teken reeks (met het escape-teken ( \\ )) terwijl een numerieke waarde wordt opgegeven met één set aanhalings tekens.

| Gegevenstype | Beschrijving | Voorbeeld | Wordt omgezet in |
|:--|:--|:--|:--|
| tekenreeks   | Plaats de waarde tussen dubbele aanhalings tekens.  | " \" Hallo wereld \" " | "Hallo wereld" |
| numeriek  | Numerieke waarde met enkele aanhalings tekens.| "64" | 64 |
| booleaans  | **waar** of **Onwaar** in aanhalings tekens.  Houd er rekening mee dat deze waarde moet worden omgezet in kleine letters. | echte | waar |
| datum/tijd | Geserialiseerde datum waarde.<br>U kunt de ConvertTo-JSON-cmdlet in Power shell gebruiken om deze waarde te genereren voor een bepaalde datum.<br>Voor beeld: Get-date "5/24/2017 13:14:57" \| ConvertTo-JSON | " \\ /Date (1495656897378) \\ /" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Modules
Uw beheer oplossing hoeft geen [algemene modules](../../automation/automation-integration-modules.md) te definiëren die worden gebruikt door uw runbooks, omdat deze altijd beschikbaar zijn in uw Automation-account.  U moet een resource toevoegen voor een andere module die wordt gebruikt door uw runbooks.

[Integratie modules](../../automation/automation-integration-modules.md) hebben het type **micro soft. Automation/automationAccounts/modules** en hebben de volgende structuur.  Dit omvat algemene variabelen en para meters, zodat u dit code fragment kunt kopiëren en plakken in het oplossings bestand en de parameter namen wijzigt.

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


De eigenschappen voor module resources worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Content link |Hiermee geeft u de inhoud van de module. <br><br>URI-URI naar de inhoud van de module.  Dit is een. ps1-bestand voor Power shell-en script-runbooks en een geëxporteerd grafisch runbook-bestand voor een grafiek runbook.  <br> versie-versie van de module voor uw eigen tracking. |

Het runbook moet afhankelijk zijn van de resource module om ervoor te zorgen dat het wordt gemaakt vóór het runbook.

### <a name="updating-modules"></a>Modules bijwerken
Als u een beheer oplossing bijwerkt die een runbook bevat dat een planning gebruikt, en de nieuwe versie van uw oplossing een nieuwe module heeft die door het runbook wordt gebruikt, kan het runbook gebruikmaken van de oude versie van de module.  U moet de volgende runbooks in uw oplossing toevoegen en een taak maken om deze voor andere runbooks uit te voeren.  Dit zorgt ervoor dat alle modules zo nodig worden bijgewerkt voordat de runbooks worden geladen.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) zorgt ervoor dat alle modules die worden gebruikt door runbooks in uw oplossing de nieuwste versie zijn.  
* [ReRegisterAutomationSchedule-MS-](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) rewaak registreert alle plannings resources opnieuw om ervoor te zorgen dat de runbooks die aan hen zijn gekoppeld de nieuwste modules gebruiken.




## <a name="sample"></a>Voorbeeld
Hieronder ziet u een voor beeld van een oplossing met daarin de volgende resources:

- Runbook.  Dit is een voor beeld van een runbook dat is opgeslagen in een open bare GitHub-opslag plaats.
- Een Automation-taak waarmee het runbook wordt gestart wanneer de oplossing is geïnstalleerd.
- Plannings-en taak planning om het runbook regel matig te starten.
- Certificaat.
- Referentie.
- Variabeletype.
- Programma.  Dit is de [OMSIngestionAPI-module](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) voor het schrijven van gegevens naar log Analytics. 

In het voor beeld worden de variabelen voor [standaard oplossings parameters]( solutions-solution-file.md#parameters) gebruikt die doorgaans in een oplossing zouden worden toegepast, in tegens telling tot hardcoding-waarden in de resource definities.


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
* [Voeg een weer gave toe aan uw oplossing]( solutions-resources-views.md) om verzamelde gegevens te visualiseren.
