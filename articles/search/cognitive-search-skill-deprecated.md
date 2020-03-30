---
title: Afgeschafte cognitieve vaardigheden
titleSuffix: Azure Cognitive Search
description: Deze pagina bevat een lijst met cognitieve vaardigheden die als afgeschaft worden beschouwd en in de nabije toekomst niet worden ondersteund in Azure Cognitive Search-skillsets.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f3587e4398be28cbaa2372be720258196bb48ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792023"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Afgeschafte cognitieve vaardigheden in Azure Cognitive Search

Dit document beschrijft cognitieve vaardigheden die als afgeschaft worden beschouwd. Gebruik de volgende handleiding voor de inhoud:

* Skill Name: De naam van de vaardigheid die zal worden @odata.type afgeschaft, het kaarten aan het attribuut.
* Laatst beschikbare api-versie: De laatste versie van de azure cognitive search public API waarmee skillsets met de bijbehorende afgeschafte vaardigheid kunnen worden gemaakt/bijgewerkt.
* Einde van de ondersteuning: de laatste dag waarna de bijbehorende vaardigheid wordt beschouwd als niet-ondersteund. Eerder gemaakte skillsets moeten nog steeds blijven functioneren, maar gebruikers wordt aangeraden om weg te migreren van een afgeschafte vaardigheid.
* Aanbevelingen: Migratiepad naar voren om een ondersteunde vaardigheid te gebruiken. Gebruikers wordt geadviseerd om de aanbevelingen op te volgen om ondersteuning te blijven ontvangen.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Laatst beschikbare api-versie

11-11-2017-preview

### <a name="end-of-support"></a>Einde van de ondersteuning

15 februari 2019

### <a name="recommendations"></a>Aanbevelingen 

Gebruik in plaats daarvan [Microsoft.Skills.Text.EntityRecognitionSkill.](cognitive-search-skill-entity-recognition.md) Het biedt het grootste deel van de functionaliteit van de NamedEntityRecognitionSkill op een hogere kwaliteit. Het heeft ook rijkere informatie in zijn complexe uitvoervelden.

Als u wilt migreren naar de [entiteitserkenningsvaardigheid,](cognitive-search-skill-entity-recognition.md)moet u een of meer van de volgende wijzigingen in uw vaardigheidsdefinitie uitvoeren. U de vaardigheidsdefinitie bijwerken met de [API Voor het bijwerken van skillset.](https://docs.microsoft.com/rest/api/searchservice/update-skillset)

> [!NOTE]
> Op dit moment wordt de vertrouwensscore als concept niet ondersteund. De `minimumPrecision` parameter bestaat `EntityRecognitionSkill` op het voor toekomstig gebruik en voor achterwaartse verenigbaarheid.

1. *(Vereist)* Verander `@odata.type` de `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` `"#Microsoft.Skills.Text.EntityRecognitionSkill"`van naar .

2. *(Optioneel)* Als u gebruik maakt `entities` van de `namedEntities` uitvoer, gebruikt `EntityRecognitionSkill` u de complexe verzamelingsuitvoer van de in plaats daarvan. U `targetName` de in de vaardigheidsdefinitie gebruiken om `entities`deze in kaart te brengen naar een annotatie genaamd.

3. *(Optioneel)* Als u niet expliciet `categories`de `EntityRecognitionSkill` , het kan verschillende soorten categorieën terug `NamedEntityRecognitionSkill`te keren dan die welke werden ondersteund door de . Als dit gedrag ongewenst is, moet `categories` u `["Person", "Location", "Organization"]`de parameter expliciet instellen op .

    _Definities voor voorbeeldmigratie_

    * Eenvoudige migratie

        _(Vóór) NamedEntityRecognition skill definition NamedEntityRecognition skill definition NamedEntityRecognition skill definition NamedEntity_
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
        _(Na) EntiteitHerkenningse vaardigheidsdefinitie_
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

        _(Vóór) NamedEntityRecognition skill definition NamedEntityRecognition skill definition NamedEntityRecognition skill definition NamedEntity_
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
        _(Na) EntiteitHerkenningse vaardigheidsdefinitie_
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

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Entiteitserkenningsvaardigheid](cognitive-search-skill-entity-recognition.md)
