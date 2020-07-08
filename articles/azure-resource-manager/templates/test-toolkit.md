---
title: ARM-sjabloon test Toolkit
description: Hierin wordt beschreven hoe u de ARM-sjabloon test Toolkit uitvoert op uw sjabloon. Met de Toolkit kunt u zien of u aanbevolen procedures hebt geïmplementeerd.
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7b88096dfdd1c7fb3e2671a369132e75a8885b8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255930"
---
# <a name="use-arm-template-test-toolkit"></a>ARM-sjabloon test Toolkit gebruiken

Met de [arm-sjabloon test Toolkit](https://aka.ms/arm-ttk) wordt gecontroleerd of de sjabloon aanbevolen procedures gebruikt. Als uw sjabloon niet voldoet aan de aanbevolen procedures, wordt een lijst met waarschuwingen met de voorgestelde wijzigingen geretourneerd. Met behulp van de test Toolkit kunt u leren hoe u veelvoorkomende problemen bij het ontwikkelen van sjablonen voor komt.

De test Toolkit bevat een aantal [standaard testen](test-cases.md). Deze tests zijn aanbevelingen, maar geen vereisten. U kunt bepalen welke tests relevant zijn voor uw doelen en aanpassen welke tests worden uitgevoerd.

In dit artikel wordt beschreven hoe u de test Toolkit uitvoert en hoe u tests toevoegt of verwijdert. Zie [test cases voor kits](test-cases.md)voor beschrijvingen van de standaard tests.

De Toolkit is een set Power shell-scripts die kunnen worden uitgevoerd met een opdracht in Power shell of CLI.

## <a name="download-test-toolkit"></a>Test Toolkit downloaden

Als u de test Toolkit wilt gebruiken, kunt u de [opslag plaats](https://aka.ms/arm-ttk) met de scripts splitsen en klonen of [het meest recente zip-bestand downloaden](https://aka.ms/arm-ttk-latest).

Afhankelijk van het uitvoerings beleid van de computer waarop u het script uitvoert, wordt mogelijk een fout bericht weer geven over het uitvoeren van scripts van het internet. U moet ofwel het [uitvoerings beleid](/powershell/module/microsoft.powershell.core/about/about_execution_policies) wijzigen of [de blok kering van de script bestanden opheffen](/powershell/module/microsoft.powershell.utility/unblock-file).

## <a name="run-on-powershell"></a>Uitvoeren op Power shell

Importeer de module voordat u de tests uitvoert.

```powershell
Import-Module .\arm-ttk.psd1 # from the same directory as .\arm-ttk.psd1
```

Als u de tests in **Power shell**wilt uitvoeren, gebruikt u de volgende opdracht:

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

## <a name="run-on-linux"></a>Uitvoeren op Linux

Installeer [Power shell core](/powershell/scripting/install/installing-powershell-core-on-linux)voordat u de tests uitvoert.

Als u de tests op **Linux** in bash wilt uitvoeren, gebruikt u de volgende opdracht:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

U kunt ook de test uitvoeren op pwsh.exe.

## <a name="run-on-macos"></a>Uitvoeren op macOS

Installeer [Power shell core](/powershell/scripting/install/installing-powershell-core-on-macos)voordat u de tests uitvoert. 

`coreutils` installeren:

```bash
brew install coreutils
```

Als u de tests op **macOS**wilt uitvoeren, gebruikt u de volgende opdracht:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

## <a name="result-format"></a>Resultaat indeling

Tests die worden uitgevoerd in de **groen** worden weer gegeven en zijn voorafgegaan door **[+]**.

Niet-uitgevoerde tests worden **rood** weer gegeven en worden voorafgegaan door **[-]**.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="test resultaten weer geven":::

De tekst resultaten zijn:

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>Test parameters

Wanneer u de para meter **-TemplatePath** opgeeft, zoekt de toolkit in die map naar een sjabloon met de naam azuredeploy.jsop of maintemplate.jsop. Deze sjabloon wordt eerst getest en vervolgens worden alle andere sjablonen in de map en de bijbehorende submappen getest. De andere sjablonen worden getest als gekoppelde sjablonen. Als uw pad een bestand bevat met de naam [CreateUiDefinition.jsop](../managed-applications/create-uidefinition-overview.md), worden er tests uitgevoerd die relevant zijn voor de UI-definitie.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Als u een bestand in die map wilt testen, voegt u de para meter **-File** toe. De map moet echter nog steeds een hoofd sjabloon hebben met de naam azuredeploy.jsop of maintemplate.jsop.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Standaard worden alle tests uitgevoerd. Als u wilt opgeven welke afzonderlijke testen moeten worden uitgevoerd, gebruikt u de para meter **-test** . Geef de naam van de test op. Zie [test cases voor Toolkit](test-cases.md)voor de namen.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Tests aanpassen

Voor ARM-sjablonen voert de Toolkit alle tests uit in de map **\arm-ttk\testcases\deploymentTemplate**. Als u een test permanent wilt verwijderen, verwijdert u het bestand uit de map.

Voor [CreateUiDefinition](../managed-applications/create-uidefinition-overview.md) -bestanden worden alle tests in de map **\arm-ttk\testcases\CreateUiDefinition**uitgevoerd.

Als u uw eigen test wilt toevoegen, maakt u een bestand met de naam Conventie: **Your-Custom-Test-Name.test.ps1**.

De test kan de sjabloon ophalen als een object parameter of een teken reeks parameter. Normaal gesp roken gebruikt u de ene of de andere, maar u kunt beide gebruiken.

Gebruik de object parameter wanneer u een sectie van de sjabloon wilt ophalen en de eigenschappen wilt door lopen. Een test die gebruikmaakt van de object parameter heeft de volgende indeling:

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

Het object Temp late heeft de volgende eigenschappen:

* $schema
* contentVersion
* parameters
* variabelen
* resources
* uitvoer

U kunt bijvoorbeeld de verzameling para meters ophalen met `$TemplateObject.parameters` .

Gebruik de teken reeks parameter wanneer u een teken reeks bewerking op de hele sjabloon moet uitvoeren. Een test die gebruikmaakt van de teken reeks parameter heeft de volgende indeling:

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

U kunt bijvoorbeeld een reguliere expressie van de teken reeks parameter uitvoeren om te zien of er een specifieke syntaxis wordt gebruikt.

Bekijk de andere tests in die map voor meer informatie over het implementeren van de test.

## <a name="integrate-with-azure-pipelines"></a>Integreren met Azure-pijp lijnen

U kunt de test Toolkit toevoegen aan uw Azure-pijp lijn. Met een pijp lijn kunt u de test uitvoeren elke keer dat de sjabloon wordt bijgewerkt, of deze uitvoeren als onderdeel van het implementatie proces.

De eenvoudigste manier om de test Toolkit toe te voegen aan uw pijp lijn is met uitbrei dingen van derden. De volgende twee uitbrei dingen zijn beschikbaar:

* [ARM TTK-tests uitvoeren](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [Test programma ARM-sjabloon](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

U kunt ook uw eigen taken implementeren. In het volgende voor beeld ziet u hoe u de test Toolkit downloadt.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

In het volgende voor beeld ziet u hoe u de tests uitvoert.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie [test cases voor Toolkit voor](test-cases.md)meer informatie over de standaard tests.
