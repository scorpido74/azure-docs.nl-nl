---
title: Aangepaste artefacten maken voor uw virtuele machine van DevTest Labs | Microsoft Documenten
description: Meer informatie over het maken van uw eigen artefacten die u gebruiken met Azure DevTest Labs.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66399202"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Maak aangepaste artefacten voor uw Virtuele Machine van DevTest Labs

Bekijk de volgende video voor een overzicht van de stappen die in dit artikel worden beschreven:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Overzicht
U *artefacten* gebruiken om uw toepassing te implementeren en in te stellen nadat u een VM hebt ingemaakt. Een artefact bestaat uit een artefact-definitiebestand en andere scriptbestanden die zijn opgeslagen in een map in een Git-opslagplaats. Artefact-definitiebestanden bestaan uit JSON en expressies die u gebruiken om op te geven wat u op een vm wilt installeren. U bijvoorbeeld de naam van een artefact, een opdracht definiëren die moet worden uitgevoerd en parameters die beschikbaar zijn wanneer de opdracht wordt uitgevoerd. U verwijzen naar andere scriptbestanden in het artefact-definitiebestand op naam.

## <a name="artifact-definition-file-format"></a>Bestandsindeling artefact-definitie
In het volgende voorbeeld worden de secties weergegeven die deel uitmaken van de basisstructuur van een definitiebestand:

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

| Elementnaam | Vereist? | Beschrijving |
| --- | --- | --- |
| $schema |Nee |Locatie van het JSON-schemabestand. Het JSON-schemabestand kan u helpen de geldigheid van het definitiebestand te testen. |
| titel |Ja |Naam van het artefact dat in het lab wordt weergegeven. |
| description |Ja |Beschrijving van het artefact in het lab. |
| iconUri |Nee |URI van het pictogram weergegeven in het lab. |
| targetOsType |Ja |Besturingssysteem van de VM waar het artefact is geïnstalleerd. Ondersteunde opties zijn Windows en Linux. |
| parameters |Nee |Waarden die worden opgegeven wanneer de opdracht artefactinstallatie op een machine wordt uitgevoerd. Dit helpt u uw artefact aan te passen. |
| Runcommand |Ja |Artefact-installatieopdracht die wordt uitgevoerd op een vm. |

### <a name="artifact-parameters"></a>Artefact-parameters
Geef in het gedeelte parameters van het definitiebestand op welke waarden een gebruiker kan invoeren wanneer hij een artefact installeert. U deze waarden in de opdracht Artefact-installatie verwijzen.

Gebruik de volgende structuur om parameters te definiëren:

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| Elementnaam | Vereist? | Beschrijving |
| --- | --- | --- |
| type |Ja |Type parameterwaarde. Zie de volgende lijst voor de toegestane typen. |
| displayName |Ja |Naam van de parameter die wordt weergegeven aan een gebruiker in het lab. |
| description |Ja |Beschrijving van de parameter die in het lab wordt weergegeven. |

Toegestane typen zijn:

* tekenreeks (een geldige JSON-tekenreeks)
* int (een geldig JSON-gehele getal)
* bool (een geldige JSON Booleaan)
* array (een geldige JSON-array)

## <a name="secrets-as-secure-strings"></a>Geheimen als veilige snaren
Verklaar geheimen als veilige snaren. Hier vindt u de syntaxis voor `parameters` het declareren van een parameter voor een beveiligde tekenreeks in de sectie van het bestand **artifactfile.json:**

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

Voer voor de opdracht Artefact-installatie het PowerShell-script uit dat de beveiligde tekenreeks neemt die is gemaakt met de opdracht ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Zie [dit voorbeeld op GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)voor het volledige voorbeeld artifactfile.json en het artefact.ps1 (PowerShell-script).

Een ander belangrijk punt om op te merken is niet om geheimen te registreren op de console als output wordt vastgelegd voor het debuggen van de gebruiker. 

## <a name="artifact-expressions-and-functions"></a>Artefact-expressies en -functies
U expressies en functies gebruiken om de opdracht Artefact-installatie te construeren.
Expressies zijn ingesloten met haakjes ([ en ]), en worden geëvalueerd wanneer het artefact is geïnstalleerd. Expressies kunnen overal in een JSON-tekenreekswaarde worden weergegeven. Expressies geven altijd een andere JSON-waarde terug. Als u een letterlijke tekenreeks moet gebruiken die begint met een beugel ([), moet u twee haakjes gebruiken ([[).
Doorgaans gebruikt u expressies met functies om een waarde te construeren. Net als in JavaScript worden functieaanroepen opgemaakt als **functieNaam(arg1, arg2, arg3)**.

In de volgende lijst worden veelvoorkomende functies weergegeven:

* **parameters(parameterName):** geeft als resultaat een parameterwaarde die wordt opgegeven wanneer de artefactopdracht wordt uitgevoerd.
* **concat(arg1, arg2, arg3,.....)**: Combineert meerdere tekenreekswaarden. Deze functie kan verschillende argumenten aannemen.

In het volgende voorbeeld ziet u hoe u expressies en functies gebruikt om een waarde te construeren:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Een aangepast artefact maken

1. Installeer een JSON-editor. U hebt een JSON-editor nodig om te werken met artefact-definitiebestanden. We raden u aan [Visual Studio Code](https://code.visualstudio.com/)te gebruiken, die beschikbaar is voor Windows, Linux en OS X.
2. Download een voorbeeld van artefactfile.json-definitiebestand. Bekijk de artefacten die zijn gemaakt door het DevTest Labs-team in onze [GitHub-repository.](https://github.com/Azure/azure-devtestlab) We hebben een rijke bibliotheek met artefacten gemaakt die je kunnen helpen je eigen artefacten te maken. Download een artefact-definitiebestand en breng er wijzigingen aan aan om uw eigen artefacten te maken.
3. Maak gebruik van IntelliSense. Gebruik IntelliSense om geldige elementen te zien die u gebruiken om een artefact-definitiebestand te construeren. U ook de verschillende opties voor waarden van een element zien. Wanneer u bijvoorbeeld het **doelostype-element** bewerkt, toont IntelliSense u twee keuzes voor Windows of Linux.
4. Bewaar het artefact in de [openbare Git-repository voor DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) of [uw eigen Git-repository.](devtest-lab-add-artifact-repo.md) In de openbare opslagplaats u artefacten bekijken die door anderen worden gedeeld en die u rechtstreeks gebruiken of aanpassen aan uw behoeften.
   
   1. Maak een aparte map voor elk artefact. De naam van de map moet hetzelfde zijn als de artefactnaam.
   2. Sla het artefact-definitiebestand (artifactfile.json) op in de map die u hebt gemaakt.
   3. Sla de scripts op waarnaar wordt verwezen vanuit de opdracht Artefact-installatie.
      
      Hier is een voorbeeld van hoe een artefactmap eruit zou kunnen zien:
      
      ![Voorbeeld van artefactmap](./media/devtest-lab-artifact-author/git-repo.png)
5. Als u uw eigen opslagplaats gebruikt om artefacten op te slaan, voegt u de opslagplaats toe aan het lab door instructies in het artikel te volgen: [Voeg een Git-opslagplaats toe voor artefacten en sjablonen.](devtest-lab-add-artifact-repo.md)

## <a name="related-articles"></a>Verwante artikelen:
* [Artefact-fouten diagnosticeren in DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Een VM deelnemen aan een bestaand Active Directory-domein met behulp van een resourcemanagersjabloon in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [toevoegen van een Git-artefactrepository aan een lab.](devtest-lab-add-artifact-repo.md)
