---
title: Leerbeleid - Personalister
description: Leerinstellingen bepalen de *hyperparameters* van de modeltraining. Twee modellen van dezelfde gegevens die zijn getraind op verschillende leerinstellingen zullen uiteindelijk anders.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219344"
---
# <a name="learning-policy-and-settings"></a>Leerbeleid en -instellingen

Leerinstellingen bepalen de *hyperparameters* van de modeltraining. Twee modellen van dezelfde gegevens die zijn getraind op verschillende leerinstellingen zullen uiteindelijk anders.

[Het leerbeleid en de instellingen](how-to-settings.md#configure-rewards-for-the-feedback-loop) zijn ingesteld op uw personalizerbron in de Azure-portal.

## <a name="import-and-export-learning-policies"></a>Leerbeleid voor importeren en exporteren

U leerbeleidsbestanden importeren en exporteren vanuit de Azure-portal. Gebruik deze methode om bestaand beleid op te slaan, te testen, te vervangen en ze te archiveren in uw broncodebesturingselement als artefacten voor toekomstige referentie en audit.

Meer informatie over [het](how-to-manage-model.md#import-a-new-learning-policy) importeren en exporteren van een leerbeleid in de Azure-portal voor uw personalizerbron.

## <a name="understand-learning-policy-settings"></a>Inzicht in leerbeleidsinstellingen

De instellingen in het leerbeleid zijn niet bedoeld om te worden gewijzigd. Wijzig instellingen alleen als u begrijpt hoe deze van invloed zijn op Personalizer. Zonder deze kennis u problemen veroorzaken, waaronder het ongeldig maken van personalizermodellen.

Personalizer gebruikt [vowpalwabbit](https://github.com/VowpalWabbit) om de gebeurtenissen te trainen en te scoren. Raadpleeg de [vowpalwabbit documentatie](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) over het bewerken van de leerinstellingen met behulp van vowpalwabbit. Zodra u de juiste opdrachtregelargumenten hebt, slaat u de opdracht op in een bestand met de volgende indeling (vervang de eigenschapswaarde arguments door de gewenste opdracht) en uploadt u het bestand naar leerinstellingen importeren in het deelvenster **Model- en Leerinstellingen** in de Azure-portal voor uw personalizerbron.

Het `.json` volgende is een voorbeeld van een leerbeleid.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Leerbeleid vergelijken

U vergelijken hoe verschillende leerbeleid presteren ten opzichte van gegevens uit het verleden in personalizerlogboeken door [offline evaluaties](concepts-offline-evaluation.md)uit te voeren.

[Upload je eigen leerbeleid](how-to-manage-model.md) om ze te vergelijken met het huidige leerbeleid.

## <a name="optimize-learning-policies"></a>Leerbeleid optimaliseren

Personalizer kan een geoptimaliseerd leerbeleid maken in een [offline evaluatie.](how-to-offline-evaluation.md) Een geoptimaliseerd leerbeleid dat betere beloningen heeft in een offline evaluatie, levert betere resultaten op wanneer het online wordt gebruikt in Personalizer.

Nadat u een leerbeleid hebt geoptimaliseerd, u het rechtstreeks toepassen op Personalizer, zodat het onmiddellijk het huidige beleid vervangt. U het geoptimaliseerde beleid opslaan voor verdere evaluatie en later beslissen of u het wilt verwijderen, opslaan of toepassen.

## <a name="next-steps"></a>Volgende stappen

* Leer [actieve en inactieve gebeurtenissen](concept-active-inactive-events.md).
