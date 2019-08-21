---
title: Afgeschafte cognitieve vaardig heden-Azure Search
description: Deze pagina bevat een lijst met cognitieve Zoek vaardigheden die als verouderd worden beschouwd en niet in de nabije toekomst wordt ondersteund.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 57dd8e47d44248270920e44268b99c98d399504d
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69635896"
---
# <a name="deprecated-cognitive-search-skills"></a>Afgeschafte vaardig heden voor de cognitieve zoek functie

In dit document worden cognitieve vaardig heden beschreven die als afgeschaft worden beschouwd. Gebruik de volgende hand leiding voor de inhoud:

* Vaardigheids naam: De naam van de vaardigheid die wordt afgeschaft, wordt toegewezen aan het @odata.type kenmerk.
* Laatst beschik bare API-versie: De laatste versie van de open bare API van Azure Search waarmee vaardig heden met de bijbehorende afgeschafte vaardig heden kunnen worden gemaakt/bijgewerkt.
* Einde van de ondersteuning: De laatste dag waarna de bijbehorende vaardigheid wordt beschouwd als niet-ondersteund. Eerder gemaakte vaardig heden moet nog steeds blijven functioneren, maar gebruikers worden aanbevolen een gedeprecieerde vaardigheid te migreren.
* Vereisten Migratie traject vooruit om een ondersteunde vaardigheid te gebruiken. Gebruikers wordt aangeraden de aanbevelingen te volgen om ondersteuning te blijven ontvangen.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Laatst beschik bare API-versie

11-11-2017-preview

### <a name="end-of-support"></a>Einde van ondersteuning

15 februari 2019

### <a name="recommendations"></a>Aanbevelingen 

Gebruik in plaats daarvan [micro soft. skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) . Het biedt de meeste functionaliteit van de NamedEntityRecognitionSkill met een hogere kwaliteit. Het bevat ook uitgebreide informatie over de complexe uitvoer velden.

Als u wilt migreren naar de [kwalificatie voor entiteits herkenning](cognitive-search-skill-entity-recognition.md), moet u een of meer van de volgende wijzigingen door voeren in uw vaardigheids definitie. U kunt de vaardigheids definitie bijwerken met behulp van de update-Vaardigheidset- [API](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> Momenteel wordt de betrouwbaarheids score als concept niet ondersteund. De `minimumPrecision` para meter bevindt zich op de `EntityRecognitionSkill` voor toekomstig gebruik en voor achterwaartse compatibiliteit.

1. *(Vereist)* Wijzig de `@odata.type` `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` vanin.`"#Microsoft.Skills.Text.EntityRecognitionSkill"`

2. *(Optioneel)* Als u de `entities` uitvoer maakt, gebruikt u de `namedEntities` complexe verzamelings uitvoer van de `EntityRecognitionSkill` in plaats daarvan. U kunt de `targetName` in de vaardigheids definitie gebruiken om deze toe te wijzen aan een `entities`aantekening met de naam.

3. *(Optioneel)* Als u de `categories`niet expliciet opgeeft, kan de `EntityRecognitionSkill` retour waarde van een ander type categorieën worden geretourneerd, behalve die van `NamedEntityRecognitionSkill`de. Als dit gedrag niet gewenst is, moet u de `categories` para meter expliciet instellen op. `["Person", "Location", "Organization"]`

    _Voor beelden van migratie definities_

    * Eenvoudige migratie

        _Bij NamedEntityRecognition-vaardigheids definitie_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _After EntityRecognition-vaardigheids definitie_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Enigszins gecompliceerde migratie

        _Bij NamedEntityRecognition-vaardigheids definitie_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _After EntityRecognition-vaardigheids definitie_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Zie ook

+ [Vooraf gedefinieerde vaardig heden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Vaardigheid van entiteits herkenning](cognitive-search-skill-entity-recognition.md)
