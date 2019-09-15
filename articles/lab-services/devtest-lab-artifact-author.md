---
title: Maken van aangepaste artefacten voor uw virtuele machine van DevTest Labs | Microsoft Docs
description: Informatie over het ontwerpen van uw eigen artefacten voor gebruik met Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 69b83590fb9b25c68d231b732b985ba633bb6884
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399202"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Maken van aangepaste artefacten voor uw virtuele machine van DevTest Labs

Bekijk de volgende video voor een overzicht van de stappen in dit artikel beschreven:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Overzicht
U kunt *artefacten* om te implementeren en uw toepassing instellen nadat u een virtuele machine inricht. Een artefact bestaat uit een definitiebestand artefact en andere bestanden die zijn opgeslagen in een map in een Git-opslagplaats. Artefact definitiebestanden bestaan uit JSON en expressies die u gebruiken kunt om op te geven wat u wilt installeren op een virtuele machine. Bijvoorbeeld, kunt u de naam van een artefact, een opdracht uit te voeren en parameters die beschikbaar zijn wanneer de opdracht wordt uitgevoerd. U kunt verwijzen naar andere scriptbestanden in de definitie van het artefact bestand met de naam.

## <a name="artifact-definition-file-format"></a>Bestandsindeling van pakketdefinities artefact
Het volgende voorbeeld ziet u de secties die gezamenlijk de basisstructuur van een definitiebestand:

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| De naam van element | Vereist? | Description |
| --- | --- | --- |
| $schema |Nee |Locatie van het bestand met JSON-schema. Het JSON-schema-bestand kunt u de geldigheid van de definitie van het bestand te testen. |
| title |Ja |De naam van het artefact weergegeven in het lab. |
| description |Ja |Beschrijving van het artefact weergegeven in het lab. |
| iconUri |Nee |De URI van het pictogram weergegeven in het lab. |
| targetOsType |Ja |Besturingssysteem van de virtuele machine waarop het artefact is geïnstalleerd. Ondersteunde opties zijn Windows en Linux. |
| parameters |Nee |De waarden die zijn opgegeven wanneer de installatieopdracht artefact op een virtuele machine wordt uitgevoerd. Zo kunt u uw artefact aanpassen. |
| runCommand |Ja |Artefacten installeren de opdracht die wordt uitgevoerd op een virtuele machine. |

### <a name="artifact-parameters"></a>Artefact parameters
In de parametersectie van de definitie van het bestand opgeven welke waarden van een gebruiker kan invoeren wanneer ze een artefact installeert. U kunt verwijzen naar deze waarden in de installatieopdracht artefact.

Gebruik de volgende structuur voor het definiëren van parameters:

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| De naam van element | Vereist? | Description |
| --- | --- | --- |
| type |Ja |Het type van de waarde van parameter. Zie de volgende lijst voor de toegestane typen. |
| displayName |Ja |De naam van de parameter die wordt weergegeven aan een gebruiker in het lab. |
| description |Ja |Beschrijving van de parameter die wordt weergegeven in het lab. |

Toegestane typen zijn:

* tekenreeks (een willekeurige geldige JSON-tekenreeks)
* Int (een geldige JSON geheel getal)
* BOOL (geldige JSON Boolean)
* matrix (geldige JSON-matrix)

## <a name="secrets-as-secure-strings"></a>Geheimen als beveiligde tekenreeksen
Declareer geheimen als beveiligde tekenreeksen. Hier volgt de syntaxis voor het declareren van een beveiligde tekenreeks-parameter in de `parameters` sectie van de **artifactfile.json** bestand:

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

Voor het artefact installatieopdracht, voert u de PowerShell-script waarmee de beveiligde tekenreeks die is gemaakt met behulp van de opdracht ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Zie voor het volledige voorbeeld artifactfile.json en de artifact.ps1 (PowerShell-script) [in dit voorbeeld op GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

Een ander belangrijk punt om te onthouden is niet om aan te melden geheimen in de console als uitvoer wordt vastgelegd voor foutopsporing van de gebruiker. 

## <a name="artifact-expressions-and-functions"></a>Artefact expressies en functies
Kunt u expressies en functies te maken van het artefact installatieopdracht.
Expressies zijn tussen vierkante haken ([en]), en worden geëvalueerd wanneer het artefact is geïnstalleerd. Expressies kunnen overal worden weergegeven in een JSON-tekenreeks-waarde. Expressies retourneert altijd een ander JSON-waarde. Als u een letterlijke tekenreeks begint met een haakje sluiten ([]) gebruiken moet, moet u twee vierkante haken ([[) gebruiken.
Meestal gebruikt u expressies met functions te maken van een waarde. Net als in JavaScript-functieaanroepen die zijn opgemaakt als **functionName (arg1, arg2, arg3)** .

De volgende lijst bevat algemene functies:

* **parameters(parameterName)** : Retourneert een parameterwaarde die is opgegeven tijdens het artefact-opdracht wordt uitgevoerd.
* **concat (arg1, arg2, arg3,...)** : Combineert meerdere tekenreekswaarden. Deze functie kan duren voordat een aantal argumenten.

Het volgende voorbeeld laat zien hoe u expressies en functies te maken van een waarde:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Een aangepast artefact maken

1. Installeer een JSON-editor. U moet een JSON-editor om te werken met artefact definitiebestanden. Wordt u aangeraden [Visual Studio Code](https://code.visualstudio.com/), die beschikbaar is voor Windows, Linux en OS X.
2. Een voorbeeldbestand artifactfile.json in het definitie ophalen. Bekijk de artefacten die zijn gemaakt door de DevTest Labs-team in onze [GitHub-opslagplaats](https://github.com/Azure/azure-devtestlab). Er is een uitgebreide bibliotheek met artefacten die kan helpen u bij het maken van uw eigen artefacten gemaakt. Download een definitiebestand artefact en wijzigingen aanbrengen in het maken van uw eigen artefacten.
3. Maakt gebruik van IntelliSense. IntelliSense gebruiken om te zien van geldige elementen die u gebruiken kunt om een definitiebestand artefact samen te stellen. U kunt ook de verschillende opties voor waarden van een element zien. Bijvoorbeeld, wanneer u bewerkt de **targetOsType** -element, IntelliSense ziet u twee opties voor Windows of Linux.
4. Het artefact in Store de [openbare Git-opslagplaats voor DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) of [uw eigen Git-opslagplaats](devtest-lab-add-artifact-repo.md). In de openbare opslagplaats, kunt u de artefacten die gedeeld met anderen kunt u rechtstreeks gebruiken of pas ze aan uw eigen wensen aan weergeven.
   
   1. Maak een afzonderlijke map voor elke artefact. Naam van de map moet hetzelfde zijn als de naam van het assemblyartefact.
   2. Het artefact-definitiebestand (artifactfile.json) Store in de map die u hebt gemaakt.
   3. De scripts die zijn waarnaar wordt verwezen in de installatieopdracht artefact Store.
      
      Hier volgt een voorbeeld van een map artefact kan als volgt uitzien:
      
      ![Voorbeeld van de map artefact](./media/devtest-lab-artifact-author/git-repo.png)
5. Als u uw eigen opslagplaats voor het opslaan van artefacten, voegt u de opslagplaats naar het lab met de instructies in het artikel: [Een Git-opslagplaats voor artefacten en sjablonen toevoegen](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Verwante artikelen:
* [Hoe u met artefacten vaststellen in DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Een VM toevoegen aan een bestaand Active Directory-domein met behulp van Resource Manager-sjabloon in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [een Git-opslagplaats voor artefacten toevoegen aan een lab](devtest-lab-add-artifact-repo.md).
