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
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122439"
---
## <a name="health-entity-categories"></a>Categorie status entiteit:

De volgende entiteits categorieën worden geretourneerd door [Text Analytics voor de status](../../how-tos/text-analytics-for-health.md).  Alleen Engelse tekst wordt ondersteund in deze container preview en er wordt slechts één model versie in elke container installatie kopie gegeven.

### <a name="named-entity-recognition"></a>Herkenning van benoemde entiteiten

|Categorie  |Beschrijving   |
|----------|--------------|
| Ouderdom | Leeftijd. Bijvoorbeeld *30 jaar oud*. |
| AdministrativeEvent | Een beheer gebeurtenis. |
| BodyStructure | Delen van de menselijke instantie, waaronder organen en andere structuren. Bijvoorbeeld *arm*of *hart*. | 
| CareEnvironment | De omgeving waar aandacht of behandeling wordt beheerd. Bijvoorbeeld *nood kamer* | 
| ConditionQualifier | Voorwaarde niveaus. Bijvoorbeeld *mild*, *uitgebreid*of *diffuus*. | 
| Identificeren | Medische voor waarden. Bijvoorbeeld *hypertension*. | 
| Richting | Aanwijzing. Bijvoorbeeld *Left* of *voorste*. | 
| Geleverd | Omvang of hoeveelheid van een medicijn. Bijvoorbeeld *25mg*.  | 
| Onderzoeksbureau | Een methode of onderzoek procedure. Bijvoorbeeld *X-Ray*. | 
| RelationalOperator | Een operator die een relatie tussen twee entiteiten definieert. Bijvoorbeeld *kleiner dan*, of `>=` .  | 
| MeasurementUnit | Een maat eenheid (zoals een percentage). | 
| MeasurementValue | De numerieke waarde van een maat eenheid. | 
| FamilyRelation | Een familial-relatie. Bijvoorbeeld *zuster*.  | 
| Frequency | Meet.   | 
| Geslacht | Geslachten. | 
| Gene | Een gen-entiteit, zoals *TP53*.   | 
| HealthcareProfession | Methode voor het beheren van medicijnen. Bijvoorbeeld *orale toediening*. | 
| MedicationClass | Medicijnen klassen. Bijvoorbeeld *antibiotica*.  | 
| MedicationForm | Een vorm van medicijnen. Bijvoorbeeld *capsule*. | 
| Medicijnen  | Algemeen en merk met de naam medicijnen. Bijvoorbeeld *ibuprofen*. | 
| MedicationRoute | Methode voor het beheren van medicijnen. Bijvoorbeeld *orale toediening*. | 
| SymptomOrSign  | Ziekte symptomen. Bijvoorbeeld *Sore Throat*. | 
| Tijd | Aantal. Bijvoorbeeld *8 jaar* of *2: om 9:30 uur deze ochtend* |
| Melkbehandelings  | Namen van behandeling. Voor beeld van *therapie*. | 
| Variant | Een genetische variant van de entiteit gen. | 

### <a name="relation-extraction"></a>Relatie-extractie

Met relatie-extractie worden betekenis volle verbindingen van concepten aangegeven die worden vermeld in de tekst. Zo wordt een relatie ' tijd van voor waarde ' gevonden door een voorwaarde naam te koppelen aan een tijd. Text Analytics status kan de volgende relaties identificeren:

|Categorie  |Beschrijving   |
|----------|--------------|
| DirectionOfBodyStructure | Richting van de structuur van een hoofd tekst. |
| DirectionOfCondition | Richting van een voor waarde. |
| DirectionOfExamination | Richting van een onderzoek. |
| DirectionOfTreatment | Richting van een behandeling. |
| TimeOfCondition | De tijd die is gekoppeld aan het begin van een voor waarde. |
| QualifierOfCondition | De bijbehorende kwalificatie voor een voor waarde. |
| DosageOfMedication | Een dosering van medicijnen. |
| FormOfMedication | Een vorm van medicijnen. |
| RouteOfMedication | Een route of modus voor het gebruik van een medicijn. Bijvoorbeeld *oraal*. |
| FrequencyOfMedication | De frequentie waarmee een medicijnen wordt verbruikt. | 
| ValueOfCondition | Een numerieke waarde die is gekoppeld aan een voor waarde. |
| UnitOfCondition | Een eenheid (zoals een tijd) die is gekoppeld aan een voor waarde. |
| TimeOfMedication | Het tijdstip waarop een medicijnen is verbruikt. |
| TimeOfTreatment | Het tijdstip waarop een behandeling is beheerd. | 
| FrequencyOfTreatment | De frequentie waarmee een behandeling wordt beheerd. |
| ValueOfExamination | Een numerieke waarde die is gekoppeld aan een onderzoek. | 
| UnitOfExamination | Een eenheid (zoals een percentage) dat is gekoppeld aan een onderzoek. |
| RelationOfExamination | Een relatie tussen een entiteit en een onderzoek. | 
| TimeOfExamination | De tijd die is gekoppeld aan een onderzoek. |
| Afkorting | Een afkorting.  | 
