---
title: Gewenste statusconfiguratie-extensie met Azure Resource Manager-sjablonen
description: Meer informatie over de resourcemanager-sjabloondefinitie voor de DSC-extensie (Desired State Configuration) in Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: Dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: ef781653332984a7fb6d71ef91d53cbf77e6c91c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72437951"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Gewenste statusconfiguratie-extensie met Azure Resource Manager-sjablonen

In dit artikel wordt de azure resource manager-sjabloon voor de [DSC-extensiehandler (Desired State Configuration) beschreven.](dsc-overview.md) Veel van de voorbeelden gebruiken **RegistrationURL** (verstrekt als string) en **RegistrationKey** (verstrekt als [PSCredential)](/dotnet/api/system.management.automation.pscredential)om aan boord te gaan met Azure Automation. Zie [Onboarding-machines voor beheer door Azure Automation State Configuration - Secure registration voor](/azure/automation/automation-dsc-onboarding#secure-registration)meer informatie over het verkrijgen van deze waarden.

> [!NOTE]
> U iets andere schemavoorbeelden tegenkomen. De wijziging in het schema vond plaats in de release van oktober 2016. Zie Bijwerken [vanaf een vorige indeling](#update-from-a-previous-format)voor meer informatie.

## <a name="template-example-for-a-windows-vm"></a>Voorbeeld van sjabloon voor een Windows-vm

Het volgende fragment gaat in de sectie **Resource** van de sjabloon.
De DSC-extensie erft standaardextensie-eigenschappen.
Zie [virtualmachineextension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet)klasse voor meer informatie.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-06-30",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Sjabloonvoorbeeld voor windows-schaalsets voor virtuele machines

Een knooppunt voor een virtuele machineschaalset heeft een **eigenschappensectie** met een **virtualmachineprofiel, extensieProfiel.**
Voeg **onder extensies**de details voor DSC Extension toe.

De DSC-extensie erft standaardextensie-eigenschappen.
Zie [VirtualMachineScaleSetExtension, klasse VirtualMachineScaleSetExtension, voor](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet)meer informatie.

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Gedetailleerde instellingeninformatie

Gebruik het volgende schema in de **sectie Instellingen** van de Azure DSC-extensie in een resourcemanagersjabloon.

Zie [Standaardconfiguratiescript](#default-configuration-script)voor een lijst met argumenten die beschikbaar zijn voor het standaardconfiguratiescript.

```json
"settings": {
    "wmfVersion": "latest",
    "configuration": {
        "url": "http://validURLToConfigLocation",
        "script": "ConfigurationScript.ps1",
        "function": "ConfigurationFunction"
    },
    "configurationArguments": {
        "argument1": "Value1",
        "argument2": "Value2"
    },
    "configurationData": {
        "url": "https://foo.psd1"
    },
    "privacy": {
        "dataCollection": "enable"
    },
    "advancedOptions": {
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
        }
    }
},
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        },
        "parameterOfTypePSCredential2": {
            "userName": "UsernameValue2",
            "password": "PasswordValue2"
        }
    },
    "configurationUrlSasToken": "?g!bber1sht0k3n",
    "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}
```

## <a name="details"></a>Details

| Naam van eigenschap | Type | Beschrijving |
| --- | --- | --- |
| settings.wmfVersion |tekenreeks |Hiermee geeft u de versie op van Windows Management Framework (WMF) die op uw vm moet worden geïnstalleerd. Als u deze eigenschap instelt **op de meest** recente installatie van de meest recente versie van WMF. Momenteel zijn de enige mogelijke waarden voor deze eigenschap **4.0**, **5.0**, **5.1**, en **laatste**. Deze mogelijke waarden zijn onderhevig aan updates. De standaardwaarde is **de laatste**. |
| settings.configuration.url |tekenreeks |Hiermee geeft u de URL-locatie op van waaruit uw DSC-configuratie .zip-bestand moet worden gedownload. Als de opgegeven URL een SAS-token voor toegang vereist, stelt u de eigenschap **protectedSettings.configurationUrlSasToken** in op de waarde van uw SAS-token. Deze eigenschap is vereist als **instellingen.configuration.script** of **settings.configuration.function** zijn gedefinieerd. Als er geen waarde wordt opgegeven voor deze eigenschappen, wordt het standaardconfiguratiescript aanroept om lcm-metagegevens (Location Configuration Manager) in te stellen en moeten argumenten worden opgegeven. |
| settings.configuration.script |tekenreeks |Hiermee geeft u de bestandsnaam op van het script dat de definitie van uw DSC-configuratie bevat. Dit script moet zich in de hoofdmap bevinden van het .zip-bestand dat is gedownload van de URL die is opgegeven door de eigenschap **settings.configuration.url.** Deze eigenschap is vereist als **settings.configuration.url** of **settings.configuration.script** zijn gedefinieerd. Als er geen waarde wordt opgegeven voor deze eigenschappen, roept de extensie het standaardconfiguratiescript aan om LCM-metagegevens in te stellen en moeten argumenten worden opgegeven. |
| settings.configuration.function |tekenreeks |Hiermee geeft u de naam van uw DSC-configuratie op. De benoemde configuratie moet worden opgenomen in het script dat **instellingen.configuration.script** definieert. Deze eigenschap is vereist als **settings.configuration.url** of **settings.configuration.function** zijn gedefinieerd. Als er geen waarde wordt opgegeven voor deze eigenschappen, roept de extensie het standaardconfiguratiescript aan om LCM-metagegevens in te stellen en moeten argumenten worden opgegeven. |
| instellingen.configurationArguments |Verzameling |Hiermee definieert u alle parameters die u wilt doorgeven aan uw DSC-configuratie. Deze eigenschap is niet versleuteld. |
| settings.configurationData.url |tekenreeks |Hiermee geeft u de URL op van waaruit u uw configuratiegegevensbestand (.psd1) wilt downloaden om te gebruiken als invoer voor uw DSC-configuratie. Als de opgegeven URL een SAS-token voor toegang vereist, stelt u de eigenschap **protectedSettings.configurationDataUrlSasToken** in op de waarde van uw SAS-token. |
| settings.privacy.dataCollection |tekenreeks |Hiermee schakelt u telemetrieverzameling in of uit. De enige mogelijke waarden voor deze eigenschap zijn **Inschakelen,** **Uitschakelen,** **''** of **$null**. Als u deze eigenschap leeg of null laat, u telemetrie. De standaardwaarde is **''**. Zie Azure [DSC-extensiegegevensverzameling](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/)voor meer informatie. |
| settings.advancedOptions.downloadMappings |Verzameling |Hiermee definieert u alternatieve locaties van waaruit WMF moet worden gedownload. Zie [Azure DSC-extensie 2.8 voor meer informatie en hoe u downloads van de extensieafhankelijkheden naar uw eigen locatie toewijzen.](https://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments |Verzameling |Hiermee definieert u alle parameters die u wilt doorgeven aan uw DSC-configuratie. Deze eigenschap is versleuteld. |
| protectedSettings.configurationUrlSasToken |tekenreeks |Hiermee geeft u het SAS-token op dat moet worden gebruikt om toegang te krijgen tot de URL die **instellingen.configuration.url** definieert. Deze eigenschap is versleuteld. |
| protectedSettings.configurationDataUrlSasToken |tekenreeks |Hiermee geeft u het SAS-token op dat moet worden gebruikt om toegang te krijgen tot de URL die **instellingen.configurationData.url** definieert. Deze eigenschap is versleuteld. |

## <a name="default-configuration-script"></a>Standaardconfiguratiescript

Zie [Basisinstellingen Lokaal Configuratiebeheer](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings)voor meer informatie over de volgende waarden.
U het standaardconfiguratiescript van de DSC-extensie gebruiken om alleen de LCM-eigenschappen te configureren die in de volgende tabel worden weergegeven.

| Naam van eigenschap | Type | Beschrijving |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |PSCredential |Vereiste eigenschap. Hiermee geeft u de sleutel op die wordt gebruikt voor een knooppunt dat moet worden geregistreerd bij de Azure Automation-service als het wachtwoord van een PowerShell-referentieobject. Deze waarde kan automatisch worden gedetecteerd met behulp van de **methode listkeys** ten opzichte van het automatiseringsaccount.  Zie het [voorbeeld](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments.RegistrationUrl |tekenreeks |Vereiste eigenschap. Hiermee geeft u de URL op van het eindpunt Automatisering waar het knooppunt probeert te registreren. Deze waarde kan automatisch worden gedetecteerd met behulp van de **referentiemethode** ten opzichte van het automatiseringsaccount. |
| settings.configurationArguments.NodeConfigurationName |tekenreeks |Vereiste eigenschap. Hiermee geeft u de knooppuntconfiguratie op in het automatiseringsaccount dat aan het knooppunt moet worden toegewezen. |
| settings.configurationArguments.ConfigurationMode |tekenreeks |Hiermee geeft u de modus voor LCM op. Geldige opties zijn **ApplyOnly,** **ApplyandMonitor**en **ApplyandAutoCorrect**.  De standaardwaarde is **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Hiermee geeft u op hoe vaak LCM probeert te controleren met het automatiseringsaccount voor updates.  Standaardwaarde is **30**.  Minimumwaarde is **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Hiermee geeft u op hoe vaak LCM de huidige configuratie valideert. Standaardwaarde is **15**. Minimumwaarde is **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | booleaans | Hiermee geeft u op of een knooppunt automatisch opnieuw kan worden opgestart als een DSC-bewerking daarom vraagt. Standaardwaarde is **onwaar**. |
| settings.configurationArguments.ActionAfterReboot | tekenreeks | Hiermee geeft u op wat er gebeurt na een herstart bij het toepassen van een configuratie. Geldige opties zijn **ContinueConfiguratie** en **StopConfiguratie**. Standaardwaarde is **ContinueConfiguratie**. |
| settings.configurationArguments.AllowModuleOverwrite | booleaans | Hiermee geeft u op of LCM bestaande modules op het knooppunt overschrijft. Standaardwaarde is **onwaar**. |

## <a name="settings-vs-protectedsettings"></a>instellingen vs. protectedInstellingen

Alle instellingen worden opgeslagen in een tekstbestand met instellingen op de vm.
Eigenschappen die onder **instellingen** worden vermeld, zijn openbare eigenschappen.
Openbare eigenschappen worden niet versleuteld in het tekstbestand instellingen.
Eigenschappen die onder **protectedWorden vermeldInstellingen** worden versleuteld met een certificaat en worden niet in platte tekst weergegeven in het instellingenbestand op de VM.

Als de configuratie referenties nodig heeft, u de referenties opnemen in **protectedSettings:**

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Voorbeeldconfiguratiescript

In het volgende voorbeeld wordt het standaardgedrag voor de DSC-extensie weergegeven, namelijk het verstrekken van metagegevensinstellingen voor LCM en het registreren bij de AutomatiseringsdSC-service.
Configuratieargumenten zijn vereist.
Configuratieargumenten worden doorgegeven aan het standaardconfiguratiescript om LCM-metagegevens in te stellen.

```json
"settings": {
    "configurationArguments": {
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Voorbeeld van het configuratiescript in Azure Storage

Het volgende voorbeeld is in het overzicht van de [DSC-extensiehandler](dsc-overview.md).
In dit voorbeeld worden resourcebeheersjablonen gebruikt in plaats van cmdlets om de extensie te implementeren.
Sla de iisInstall.ps1-configuratie op, plaats deze `iisinstall.zip`in een .zip-bestand (voorbeeld: ) en upload het bestand vervolgens in een toegankelijke URL.
In dit voorbeeld wordt Azure Blob-opslag gebruikt, maar u .zip-bestanden downloaden vanaf elke willekeurige locatie.

In de sjabloon Resourcebeheer geeft de volgende code de VM de opdracht om het juiste bestand te downloaden en voert u vervolgens de juiste PowerShell-functie uit:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Voorbeeld van het gebruik van de registratiewaarden van Azure Automation

In het volgende voorbeeld wordt de **registrationurl-** en **registratiesleutel opgehaald** door te verwijzen naar de eigenschappen van het Azure Automation-account en de **methode listsleutels** te gebruiken om de primaire sleutel (0) op te halen.  In dit voorbeeld werden de parameters **automationAccountName** en **NodeConfigName** aan de sjabloon verstrekt.

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Bijwerken vanaf een vorige indeling

Alle instellingen in een vorige indeling van de extensie (en die de openbare eigenschappen **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken**of **Eigenschappen)** hebben, passen zich automatisch aan de huidige indeling van de extensie aan.
Ze rennen net als voorheen.

In het volgende schema ziet u hoe het vorige instellingenschema eruit zag:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Zo past de vorige indeling zich aan de huidige indeling aan:

| Huidige eigenschapsnaam | Vorig schema-equivalent |
| --- | --- |
| settings.wmfVersion |Instellingen. WMFVersie |
| settings.configuration.url |Instellingen. ModulesUrl |
| settings.configuration.script |Eerste deel van de instellingen. Configuratiefunctie (vóór \\ \\) |
| settings.configuration.function |Tweede deel van de instellingen. Configuratiefunctie (na \\ \\) |
| settings.configuration.module.name | Instellingen. ModuleBron |
| settings.configuration.module.version | Instellingen. ModuleVersie |
| instellingen.configurationArguments |Instellingen. Eigenschappen |
| settings.configurationData.url |protectedSettings.DataBlobUri (zonder SAS-token) |
| settings.privacy.dataCollection |Instellingen. Privacy.dataVerzameling |
| settings.advancedOptions.downloadMappings |Instellingen. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Eigenschappen |
| protectedSettings.configurationUrlSasToken |Instellingen. SasToken (SasToken) |
| protectedSettings.configurationDataUrlSasToken |SAS-token van protectedSettings.DataBlobUri |

## <a name="troubleshooting"></a>Problemen oplossen

Hier zijn enkele van de fouten die u zou kunnen tegenkomen en hoe u ze oplossen.

### <a name="invalid-values"></a>Ongeldige waarden

"Privacy.dataCollection is{0}' '.
De enige mogelijke waarden zijn '', 'Inschakelen' en 'Uitschakelen'".
"WmfVersion is{0}' '.
Alleen mogelijke waarden zijn ... en 'laatste'".

**Probleem**: Een opgegeven waarde is niet toegestaan.

**Oplossing:** Wijzig de ongeldige waarde in een geldige waarde.
Zie de tabel in [Details](#details)voor meer informatie.

### <a name="invalid-url"></a>Ongeldige URL

"ConfigurationData.url is{0}' '. Dit is geen geldige URL" "DataBlobUri is '{0}'. Dit is geen geldige URL" "Configuration.url is '{0}'. Dit is geen geldige URL"

**Probleem:** een opgegeven URL is niet geldig.

**Oplossing:** Controleer alle opgegeven URL's.
Zorg ervoor dat alle URL's worden opgelost op geldige locaties waartoe de extensie toegang heeft op de externe machine.

### <a name="invalid-registrationkey-type"></a>Ongeldig type registratiesleutel

"Ongeldig type voor parameter RegistrationKey van type PSCredential."

**Probleem:** De *waarde RegistrationKey* in protectedSettings.configurationArguments kunnen niet worden opgegeven als een ander type dan een PSCredential.

**Oplossing:** Wijzig uw wizardSettings.configurationArguments entry for RegistrationKey to a PSCredential type using the following format:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Ongeldig configuratieargumenttype

"Ongeldig configuratieargumentstype {0}"

**Probleem:** de eigenschap *ConfigurationArguments* kan niet worden opgelost in een **hashtabelobject.**

**Oplossing:** Maak van de eigenschap *ConfigurationArguments* een **hashtabel**.
Volg de notatie in de voorgaande voorbeelden. Kijk uit voor citaten, komma's en beugels.

### <a name="duplicate-configurationarguments"></a>Configuratieargumenten dupliceren

"Gevonden dubbele{0}argumenten ' ' in zowel openbare als beveiligde configuratieArgumenten"

**Probleem:** de *configuratieargumenten* in openbare instellingen en de *configuratieargumenten* in beveiligde instellingen hebben eigenschappen met dezelfde naam.

**Oplossing:** Verwijder een van de dubbele eigenschappen.

### <a name="missing-properties"></a>Ontbrekende eigenschappen

"instellingen. Configuration.function vereist dat settings.configuration.url of settings.configuration.module is opgegeven"

"instellingen. Configuration.url vereist dat settings.configuration.script is opgegeven"

"instellingen. Configuration.script vereist dat settings.configuration.url is opgegeven"

"instellingen. Configuration.url vereist dat settings.configuration.function is opgegeven"

"protectedSettings.ConfigurationUrlSasToken vereist dat settings.configuration.url is opgegeven"

"protectedSettings.ConfigurationDataUrlSasToken vereist dat settings.configurationData.url is opgegeven"

**Probleem:** een gedefinieerde eigenschap heeft een andere eigenschap nodig, die ontbreekt.

**Oplossingen**:

- Zorg voor de ontbrekende eigenschap.
- Verwijder de eigenschap die de ontbrekende eigenschap nodig heeft.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het gebruik van virtuele machineschaalsets met de Azure DSC-extensie](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Meer informatie over [het beveiligde referentiebeheer van DSC](dsc-credentials.md).
- Krijg een [inleiding tot de Azure DSC-extensiehandler](dsc-overview.md).
- Ga voor meer informatie over PowerShell DSC naar het [PowerShell-documentatiecentrum.](/powershell/scripting/dsc/overview/overview)
