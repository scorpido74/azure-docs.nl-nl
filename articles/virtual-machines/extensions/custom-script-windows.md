---
title: Azure Custom Script-extensie voor Windows
description: Windows VM-configuratietaken automatiseren met de extensie Aangepast script
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 698fab470cdc8b8d04fa4319fd71c31b58d1c5a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066879"
---
# <a name="custom-script-extension-for-windows"></a>Aangepaste scriptextensie voor Windows

De Aangepaste Script-extensie downloadt en voert scripts uit op virtuele Azure-machines. Deze extensie is handig voor configuratie na implementatie, software-installatie of andere configuratie- of beheertaken. Scripts kunnen worden gedownload uit Azure Storage of GitHub, of worden geleverd in Azure Portal tijdens de uitvoering van extensies. De Aangepaste Script-extensie integreert met Azure Resource Manager-sjablonen en kan worden uitgevoerd met de Azure CLI,PowerShell, Azure-portal of de Azure Virtual Machine REST API.

In dit document wordt beschreven hoe u de aangepaste scriptextensie gebruikt met de Azure PowerShell-module, Azure Resource Manager-sjablonen en stappen voor het oplossen van problemen met Windows-systemen.

## <a name="prerequisites"></a>Vereisten

> [!NOTE]  
> Gebruik geen Aangepaste scriptextensie om Update-AzVM uit te voeren met dezelfde VM als de parameter, omdat deze op zichzelf wacht.  

### <a name="operating-system"></a>Besturingssysteem

De aangepaste scriptextensie voor Windows wordt uitgevoerd op de extensieondersteunde extensie-besturingssystemen, zie voor meer informatie deze door [Azure Extension ondersteunde besturingssystemen.](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)

### <a name="script-location"></a>Scriptlocatie

U de extensie configureren om uw Azure Blob-opslagreferenties te gebruiken om toegang te krijgen tot Azure Blob-opslag. De scriptlocatie kan overal zijn, zolang de VM naar dat eindpunt kan worden doorgestuurd, zoals GitHub of een interne bestandsserver.

### <a name="internet-connectivity"></a>Internetverbinding

Als u een extern script moet downloaden, bijvoorbeeld van GitHub of Azure Storage, moeten extra firewall- en netwerkbeveiligingsgroeppoorten worden geopend. Als uw script zich bijvoorbeeld in Azure Storage bevindt, u toegang toestaan via Azure NSG-servicetags voor [opslag.](../../virtual-network/security-overview.md#service-tags)

Als uw script zich op een lokale server bevindt, hebt u mogelijk nog steeds extra firewall nodig en moeten de poorten van de netwerkbeveiligingsgroep worden geopend.

### <a name="tips-and-tricks"></a>Tips en trucs

* Het hoogste percentage fouten voor deze extensie is vanwege syntaxisfouten in het script, test het script zonder fouten en plaats ook extra inlogin het script om het gemakkelijker te maken te vinden waar het is mislukt.
* Schrijf scripts die idempotent zijn. Dit zorgt ervoor dat als ze per ongeluk opnieuw worden uitgevoerd, dit geen systeemwijzigingen zal veroorzaken.
* Zorg ervoor dat de scripts geen gebruikersinvoer nodig hebben wanneer ze worden uitgevoerd.
* De uitvoering van het script mag 90 minuten duren. Als het langer duurt, mislukt de inrichting van de extensie.
* Neem opnieuw opstarten niet in het script op. Deze actie veroorzaakt problemen met andere extensies die worden geïnstalleerd. Na het opnieuw opstarten wordt de extensie niet voortgezet.
* Als u een script hebt dat een herstart veroorzaakt, installeert u toepassingen en voert u scripts uit, u de herstart plannen met een Geplande Windows-taak of hulpprogramma's gebruiken, zoals DSC-, Chef- of Puppet-extensies.
* De extensie voert een script slechts eenmaal uit. Wilt u dat een script bij iedere start wordt uitgevoerd, dan moet u de extensie gebruiken om een geplande Windows-taak te maken.
* Als u wilt plannen wanneer een script wordt uitgevoerd, dan moet u de extensie gebruiken om een geplande Windows-taak te maken.
* Wanneer het script wordt uitgevoerd, ziet u alleen de extensiestatus 'overgang maken' van de Azure-portal of CLI. Als u meer statusupdates van een actief script wilt, dan moet u uw eigen oplossing maken.
* Aangepaste Script-extensie biedt geen ondersteuning voor proxyservers, maar u een hulpmiddel voor bestandsoverdracht gebruiken dat proxyservers in uw script ondersteunt, zoals *Curl*
* Houd rekening met niet-standaard maplocaties waar uw scripts of opdrachten eventueel gebruik van maken. Pak deze situatie logisch aan.
* Aangepaste scriptextensie wordt uitgevoerd onder het LocalSystem-account

## <a name="extension-schema"></a>Extensieschema

De configuratie van de aangepaste scriptextensie geeft zaken aan zoals de locatie van het script en de opdracht die moet worden uitgevoerd. U deze configuratie opslaan in configuratiebestanden, deze opgeven op de opdrachtregel of opgeven in een Azure Resource Manager-sjabloon.

U gevoelige gegevens opslaan in een beveiligde configuratie, die is versleuteld en alleen wordt gedecodeerd in de virtuele machine. De beveiligde configuratie is handig wanneer de uitvoeringsopdracht geheimen zoals een wachtwoord bevat.

Deze items moeten worden behandeld als gevoelige gegevens en worden opgegeven in de configuratie van de beveiligde instelling voor extensies. Azure VM-extensiebeveiligde instellingsgegevens worden versleuteld en alleen gedecodeerd op de virtuele doelmachine.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> managedIdentity-eigenschap **mag niet** worden gebruikt in combinatie met storageAccountName- of storageAccountKey-eigenschappen

> [!NOTE]
> Slechts één versie van een extensie kan op een bepaald moment op een VM worden geïnstalleerd, waarbij aangepaste script tweekeer wordt opgegeven in dezelfde Resource Manager-sjabloon voor dezelfde VM, mislukt.

> [!NOTE]
> We kunnen dit schema gebruiken in de VirtualMachine-bron of als een zelfstandige bron. De naam van de resource moet in deze indeling "virtualMachineName/extensionName" zijn, als deze extensie wordt gebruikt als een zelfstandige bron in de ARM-sjabloon. 

### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde / Voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| uitgever | Microsoft.Compute | tekenreeks |
| type | AangepasteScriptExtensie | tekenreeks |
| typeHandlerVersie | 1,10 | int |
| fileUris (bijv. | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matrix |
| tijdstempel (bijv. | 123456789 | 32-bits geheel getal |
| commandToExecute (bijv. | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | tekenreeks |
| storageAccountName (bijv. | voorbeeldstorageacct | tekenreeks |
| storageAccountKey (bijv. | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | tekenreeks |
| managedIdentity (bijv. | { } of { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } of { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json-object |

>[!NOTE]
>Deze eigenschapsnamen zijn geval-gevoelig. Gebruik de namen zoals hier weergegeven om implementatieproblemen te voorkomen.

#### <a name="property-value-details"></a>Details van de waarde van onroerend goed

* `commandToExecute`: (**vereist**, tekenreeks) het item script uit te voeren. Gebruik dit veld in plaats daarvan als uw opdracht geheimen bevat, zoals wachtwoorden, of uw fileUris gevoelig is.
* `fileUris`: (optioneel, tekenreeksarray) de URL's voor bestanden die moeten worden gedownload.
* `timestamp`(optioneel, 32-bits geheel getal) gebruikt dit veld alleen om een rerun van het script te activeren door de waarde van dit veld te wijzigen.  Elke gehele waarde is aanvaardbaar; het mag alleen anders zijn dan de vorige waarde.
* `storageAccountName`: (optioneel, tekenreeks) de naam van het opslagaccount. Als u opslagreferenties opgeeft, moeten alle `fileUris` URL's voor Azure Blobs zijn.
* `storageAccountKey`: (optioneel, tekenreeks) de toegangssleutel van het opslagaccount
* `managedIdentity`: (optioneel, json-object) de [beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor het downloaden van bestand(en)
  * `clientId`: (optioneel, tekenreeks) de client-id van de beheerde identiteit
  * `objectId`: (optioneel, tekenreeks) de object-id van de beheerde identiteit

De volgende waarden kunnen worden ingesteld in openbare of beveiligde instellingen, de extensie weigert elke configuratie waarin de onderstaande waarden zijn ingesteld in zowel openbare als beveiligde instellingen.

* `commandToExecute`

Het gebruik van openbare instellingen is misschien handig voor het opsporen van fouten, maar het wordt aanbevolen om beveiligde instellingen te gebruiken.

Openbare instellingen worden in duidelijke tekst naar de VM gestuurd waar het script wordt uitgevoerd.  Beveiligde instellingen worden versleuteld met een sleutel die alleen bekend is bij azure en de VM. De instellingen worden opgeslagen in de VM als ze werden verzonden, dat wil zeggen, als de instellingen zijn versleuteld ze zijn opgeslagen versleuteld op de VM. Het certificaat dat wordt gebruikt om de versleutelde waarden te decoderen, wordt opgeslagen op de VM en wordt gebruikt om instellingen (indien nodig) te decoderen tijdens runtime.

####  <a name="property-managedidentity"></a>Eigenschap: managedIdentity

CustomScript (versie 1.10 vanaf daarentegen) ondersteunt [beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor het downloaden van bestanden(en) van URL's die in de instelling 'fileUris' worden gegeven. Hiermee heeft CustomScript toegang tot privéblobs of containers van Azure Storage zonder dat de gebruiker geheimen zoals SAS-tokens of opslagaccountsleutels hoeft door te geven.

Als u deze functie wilt gebruiken, moet de gebruiker een door het systeem toegewezen of [door de gebruiker toegewezen](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) identiteit toevoegen aan de VM of VMSS waar CustomScript naar verwachting wordt uitgevoerd, en de [beheerde identiteit toegang verlenen tot de Azure Storage-container of blob.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access) [system-assigned](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity)

Als u de door het systeem toegewezen identiteit wilt gebruiken op de doel-VM/VMSS, stelt u het veld 'beheerde identiteit' in op een leeg json-object. 

> Voorbeeld:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Als u de door de gebruiker toegewezen identiteit wilt gebruiken op de doel-VM/VMSS, configureert u het veld 'beheerde identiteit' met de client-id of de object-id van de beheerde identiteit.

> Voorbeelden:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> managedIdentity-eigenschap **mag niet** worden gebruikt in combinatie met storageAccountName- of storageAccountKey-eigenschappen

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Het JSON-schema, dat is beschreven in de vorige sectie, kan worden gebruikt in een Azure Resource Manager-sjabloon om de aangepaste scriptextensie tijdens de implementatie uit te voeren. In de volgende voorbeelden wordt uitgelegd hoe u de extensie Aangepast script gebruiken:

* [Zelfstudie: Extensies voor virtuele machines implementeren met Azure Resource Manager-sjablonen](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Tweelaagse toepassing implementeren op Windows en Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell-implementatie

De `Set-AzVMCustomScriptExtension` opdracht kan worden gebruikt om de extensie Aangepast script toe te voegen aan een bestaande virtuele machine. Zie [Set-AzVMCustomScriptExtension voor](/powershell/module/az.compute/set-azvmcustomscriptextension)meer informatie.

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Aanvullende voorbeelden

### <a name="using-multiple-scripts"></a>Meerdere scripts gebruiken

In dit voorbeeld hebt u drie scripts die worden gebruikt om uw server te bouwen. De **commandToExecute** roept het eerste script aan en vervolgens hebt u opties voor hoe de anderen worden aangeroepen. U bijvoorbeeld een hoofdscript hebben dat de uitvoering regelt, met de juiste foutafhandeling, logboekregistratie en staatsbeheer. De scripts worden gedownload naar de lokale machine voor het uitvoeren. Bijvoorbeeld in `1_Add_Tools.ps1` u `2_Add_Features.ps1` zou `.\2_Add_Features.ps1` bellen door toe te voegen aan het script, en herhaal dit proces voor de andere scripts die u definieert in `$settings`.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Scripts uitvoeren vanuit een lokaal aandeel

In dit voorbeeld u een lokale SMB-server gebruiken voor uw scriptlocatie. Door dit te doen, hoeft u geen andere instellingen te bieden, behalve **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Aangepast script meerdere keer uitvoeren met CLI

Als u de aangepaste scriptextensie meerdere tijd wilt uitvoeren, u deze actie alleen onder de volgende voorwaarden uitvoeren:

* De parameter **extensienaam** is hetzelfde als de vorige implementatie van de extensie.
* Werk de configuratie bij, anders wordt de opdracht niet opnieuw uitgevoerd. U een dynamische eigenschap toevoegen aan de opdracht, zoals een tijdstempel.

U ook de eigenschap [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) op **true**instellen.

### <a name="using-invoke-webrequest"></a>Invoke-WebRequest gebruiken

Als u [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) in uw script gebruikt, `-UseBasicParsing` moet u de parameter opgeven, anders ontvangt u de volgende fout bij het controleren van de gedetailleerde status:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Schaalsets voor virtuele machines

Zie [Add-AzVmssExtension](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0) voor het implementeren van de aangepaste scriptextensie op een schaalset

## <a name="classic-vms"></a>Klassieke VM's

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Als u de Aangepaste Script-extensie wilt implementeren op klassieke VM's, u de Azure-portal of de klassieke Azure PowerShell-cmdlets gebruiken.

### <a name="azure-portal"></a>Azure Portal

Navigeer naar uw klassieke VM-bron. Selecteer **Extensies** onder **Instellingen**.

Klik **op + Toevoegen** en kies in de lijst met bronnen aangepaste **scriptextensie**.

Selecteer **op** de pagina Extensie installeren het lokale PowerShell-bestand en vul eventuele argumenten in en klik op **Ok**.

### <a name="powershell"></a>PowerShell

Gebruik de cmdlet [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) kan worden gebruikt om de extensie Aangepast script toe te voegen aan een bestaande virtuele machine.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure PowerShell-module. Voer de volgende opdracht uit om de implementatiestatus van extensies voor een bepaalde vm te bekijken:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Extensie-uitvoer wordt geregistreerd op bestanden die worden gevonden onder de volgende map op de virtuele doelmachine.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

De opgegeven bestanden worden gedownload naar de volgende map op de virtuele doelmachine.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

waar `<n>` een geheel getal achter de komma is, dat kan veranderen tussen de uitvoeringen van de extensie.  De `1.*` waarde komt overeen met de werkelijke, huidige `typeHandlerVersion` waarde van de extensie.  De werkelijke map kan `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`bijvoorbeeld .  

Bij het `commandToExecute` uitvoeren van de opdracht stelt de `...\Downloads\2`extensie deze map (bijvoorbeeld) in als de huidige werkmap. Dit proces maakt het gebruik van relatieve paden `fileURIs` om de bestanden gedownload via de eigenschap te lokaliseren. Zie de onderstaande tabel voor voorbeelden.

Aangezien het absolute downloadpad in de loop van de tijd kan variëren, `commandToExecute` is het beter om te kiezen voor relatieve script/ bestandspaden in de tekenreeks, waar mogelijk. Bijvoorbeeld:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Padinformatie nadat het eerste URI-segment wordt `fileUris` bewaard voor bestanden die via de eigenschappenlijst worden gedownload.  Zoals in de onderstaande tabel wordt weergegeven, worden gedownloade bestanden toegewezen `fileUris` aan downloadsubmappen om de structuur van de waarden weer te geven.  

#### <a name="examples-of-downloaded-files"></a>Voorbeelden van gedownloade bestanden

| URI in fileUris | Relatief gedownloade locatie | Absoluut gedownloade locatie <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> De absolute mappaden veranderen gedurende de levensduur van de VM, maar niet binnen één uitvoering van de CustomScript-extensie.

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.
