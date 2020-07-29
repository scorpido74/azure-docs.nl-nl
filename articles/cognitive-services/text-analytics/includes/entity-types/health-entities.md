---
title: Benoemde entiteiten voor gezondheids zorg
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 4ac65e85e05f408b8d2f37a1d6845dc9e28e2bab
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373064"
---
## <a name="health-entity-categories"></a>Categorie status entiteit:

De volgende entiteits categorieën worden geretourneerd door [Text Analytics voor de status](../../how-tos/text-analytics-for-health.md).  Alleen Engelse tekst wordt ondersteund in deze container preview en er wordt slechts één model versie in elke container installatie kopie gegeven.

### <a name="named-entity-recognition"></a>Herkenning van benoemde entiteiten

|Categorie  |Beschrijving   |
|----------|--------------|
| LEEFTIJD | Leeftijd. |
| BODY_STRUCTURE | Delen van de menselijke instantie, waaronder organen en andere structuren. | 
| CONDITION_QUALIFIER | Voorwaarde niveaus zoals *mild*, *uitgebreid*of *diffuus*. | 
| IDENTIFICEREN | Medische voor waarden. Bijvoorbeeld *hypertension* . | 
| DRAAIEN | Instructies zoals *Left* of *voorste*. | 
| GELEVERD | Omvang of hoeveelheid van een medicijn.  | 
| EXAMINATION_NAME | Een methode of onderzoek procedure. | 
| EXAMINATION_RELATION | een koppeling tussen een maat eenheid en een onderzoek.  | 
| EXAMINATION_UNIT | Een maat eenheid voor een onderzoek. | 
| EXAMINATION_VALUE | De waarde van de meting eenheid voor het onderzoek. | 
| FAMILY_RELATION | Een familial-relatie, zoals *zuster*.  | 
| INGANG | Meet.   | 
| GESLACHT | Geslachten. | 
| GENE | Een gen-entiteit, zoals *TP53*.   | 
| MEDICATION_CLASS | Medicijnen klassen. Bijvoorbeeld *antibiotica*.  | 
| MEDICATION_NAME  | Algemeen en merk met de naam medicijnen.| 
| ROUTE_OR_MODE  | Methode voor het beheren van medicijnen. | 
| SYMPTOM_OR_SIGN  | Ziekte symptomen. | 
| TIME  | Aantal. Bijvoorbeeld ' 8 jaar ' of ' 2: om 9:30 uur deze ochtend ' |
| TREATMENT_NAME  | Namen van behandeling. | 
| KLEUROVERGANGSVARIANT  | Een genetische variant van de gen-entiteit | 

### <a name="relation-extraction"></a>Relatie-extractie

Met relatie-extractie worden betekenis volle verbindingen van concepten aangegeven die worden vermeld in de tekst. Zo wordt een relatie ' tijd van voor waarde ' gevonden door een voorwaarde naam te koppelen aan een tijd. Text Analytics status kan de volgende relaties identificeren:

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* VEELGEBRUIKT 
