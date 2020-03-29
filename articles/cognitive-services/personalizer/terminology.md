---
title: Terminologie - Personalizer
description: Personalizer gebruikt terminologie uit reinforcement learning. Deze termen worden gebruikt in de Azure-portal en de API's.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624273"
---
# <a name="terminology"></a>Terminologie

Personalizer gebruikt terminologie uit reinforcement learning. Deze termen worden gebruikt in de Azure-portal en de API's.

## <a name="conceptual-terminology"></a>Conceptuele terminologie

* **Learning Loop:** U maakt een Personalizer resource, een _zogenaamde learning loop,_ voor elk deel van uw toepassing dat kan profiteren van personalisatie. Als je meer dan één ervaring hebt om te personaliseren, maak je een lus voor elk.

* **Model:** Een personalizermodel legt alle gegevens vast die over gebruikersgedrag zijn geleerd, het verkrijgen van trainingsgegevens uit de combinatie van de argumenten die u naar Rang- en Beloningsgesprekken verzendt en met een trainingsgedrag dat wordt bepaald door het leerbeleid.

## <a name="personalizer-configuration"></a>Gepersonaliseerde configuratie

Personalizer is geconfigureerd vanuit de [Azure-portal.](https://portal.azure.com)

* **Beloningen:** configureer de standaardwaarden voor de wachttijd voor beloning, standaardbeloning en beloningsaggregatiebeleid.

* **Verkenning:** configureer het percentage rankcalls dat moet worden gebruikt voor verkenning

* **Model update frequentie**: Hoe vaak het model wordt omgeschoold.

* **Gegevensbewaring**: Hoeveel dagen aan gegevens moeten worden opgeslagen. Dit kan gevolgen hebben voor offline evaluaties, die worden gebruikt om uw leerlus te verbeteren.

## <a name="use-rank-and-reward-apis"></a>Api's voor rang en beloning gebruiken

* **Rang:** Gebruik, gezien de acties met functies en de contextfuncties, verkennen of exploiteren om de hoogste actie (inhoudsitem) terug te sturen.

    * **Acties:** Acties zijn de inhouditems, zoals producten of promoties, om uit te kiezen. Personalizer kiest de hoogste actie (geretourneerde reward action ID) om uw gebruikers te laten zien via de Rank API.

    * **Context:** Om een nauwkeurigere rang te bieden, moet u informatie verstrekken over uw context, bijvoorbeeld:
        * Uw gebruiker.
        * Het apparaat waarop ze zitten.
        * De huidige tijd.
        * Andere gegevens over de huidige situatie.
        * Historische gegevens over de gebruiker of context.

        Uw specifieke toepassing kan verschillende contextinformatie bevatten.

    * **[Kenmerken:](concepts-features.md)** een verzameling informatie over een inhoudsitem of een gebruikerscontext. Zorg ervoor dat u alleen functies gebruikt die zijn samengevoegd. Gebruik geen specifieke tijden, gebruikersnamen of andere niet-geaggregeerde gegevens als functies.

        * Een _actiefunctie_ is metagegevens over de inhoud.
        * Een _contextfunctie_ is metagegevens over de context waarin de inhoud wordt gepresenteerd.

* **Verkenning**: De Personalizer-service onderzoekt wanneer deze, in plaats van de beste actie terug te sturen, een andere actie voor de gebruiker kiest. De Personalizer-service voorkomt drift, stagnatie en kan zich aanpassen aan het voortdurende gedrag van gebruikers door te verkennen.

* **Exploitatie**: De Personalizer-service gebruikt het huidige model om de beste actie te bepalen op basis van gegevens uit het verleden.

* **Experimentduur:** de hoeveelheid tijd die de personalizerservice wacht op een beloning, vanaf het moment dat de rangoproep voor die gebeurtenis is uitgevoerd.

* **Inactieve gebeurtenissen**: Een inactieve gebeurtenis is er een waarbij u Rank hebt genoemd, maar u weet niet zeker of de gebruiker het resultaat ooit zal zien, als gevolg van beslissingen over clienttoepassingen. Met inactieve gebeurtenissen u personalisatieresultaten maken en opslaan en vervolgens besluiten deze later te verwijderen zonder dat dit gevolgen heeft voor het machine learning-model.


* **Beloning:** Een maat voor hoe de gebruiker reageerde op de geretourneerde reward action ID van de Rank API, als een score tussen 0 tot 1. De 0-1 waarde wordt bepaald door uw bedrijfslogica, gebaseerd op hoe de keuze heeft bijgedragen aan het bereiken van uw zakelijke doelstellingen van personalisatie. De leerlus slaat deze beloning niet op als individuele gebruikersgeschiedenis.

## <a name="offline-evaluations"></a>Offline evaluaties

* **Evaluatie:** Een offline evaluatie bepaalt het beste leerbeleid voor uw lus op basis van de gegevens van uw lus.

* **Leerbeleid:** Hoe Personalizer een model traint op elke gebeurtenis, wordt bepaald door een aantal parameters die van invloed zijn op de manier waarop het machine learning-algoritme werkt. Een nieuwe leerlus begint met een standaard **leerbeleid**, dat matige prestaties kan opleveren. Bij het uitvoeren [van evaluaties](concepts-offline-evaluation.md)maakt Personalizer nieuw leerbeleid dat specifiek is geoptimaliseerd voor de use cases van uw lus. Personalizer zal aanzienlijk beter presteren met beleid geoptimaliseerd voor elke specifieke lus, gegenereerd tijdens de evaluatie. Het leerbeleid wordt _leerinstellingen_ genoemd in de **model- en leerinstellingen** voor de personalizerbron in de Azure-portal.