---
title: Probleemoplossing - Personalizer
description: Dit artikel bevat antwoorden op veelgestelde vragen over het oplossen van problemen over Personalizer.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 904953f028eb31afe42cf477ac05be43e8b72a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336017"
---
# <a name="personalizer-troubleshooting"></a>Probleemoplossing voor gepersonaliseerde problemen

Dit artikel bevat antwoorden op veelgestelde vragen over het oplossen van problemen over Personalizer.

## <a name="transaction-errors"></a>Transactiefouten

<details>
<summary><b>Ik krijg een HTTP 429 (Te veel verzoeken) antwoord van de dienst. Wat kan ik doen?</b></summary>

**Antwoord:** Als u een gratis prijslaag hebt gekozen toen u de instantie Personalizer maakte, is er een quotumlimiet voor het aantal rangaanvragen dat is toegestaan. Controleer uw API-aanroeptarief voor de Rank-API (in het deelvenster Statistieken in de Azure-portal voor uw personalizerbron) en pas de prijscategorie aan (in het deelvenster Prijslagen) als het aantal oproepen naar verwachting zal toenemen boven de drempel voor gekozen prijscategorie.

</details>

<details>
<summary><b>Ik krijg een 5xx fout op Rank of Reward API's. Wat moet ik doen?</b></summary>

**Antwoord**: Deze kwesties moeten transparant zijn. Als ze doorgaan, neemt u contact op met ondersteuning door **Nieuw ondersteuningsverzoek te** selecteren in de sectie **Ondersteuning + probleemoplossing** in de Azure-portal voor uw personalizerbron.

</details>

## <a name="learning-loop"></a>Leerlus

<details>
<summary>
<b>De leerlus bereikt geen 100% match met het systeem zonder Personalizer. Hoe los ik dit op?</b></summary>

**Antwoord:** De redenen waarom je je doel niet bereikt met de leerlus:
* Niet genoeg functies verzonden met Rank API-aanroep
* Bugs in de verzonden functies, zoals het verzenden van niet-geaggregeerde functiegegevens, zoals tijdstempels naar Rank API
* Bugs met lusverwerking - zoals het niet verzenden van beloningsgegevens naar Reward API voor gebeurtenissen

Om het probleem op te lossen, moet u de verwerking wijzigen door de functies die naar de lus worden verzonden te wijzigen, of ervoor zorgen dat de beloning een juiste evaluatie is van de kwaliteit van de reactie van de rang.

</details>

<details>
<summary>
<b>De leerlus lijkt niet te leren. Hoe los ik dit op?</b></summary>

**Antwoord**: De leerlus heeft een paar duizend Reward-oproepen nodig voordat Rank-oproepen effectief prioriteit geven.

Als u niet zeker weet hoe uw leerlus zich momenteel gedraagt, voert u een [offline evaluatie](concepts-offline-evaluation.md)uit en past u het gecorrigeerde leerbeleid toe.

</details>

<details>
<summary><b>Ik krijg steeds rang resultaten met alle dezelfde waarschijnlijkheden voor alle items. Hoe weet ik dat Personalizer aan het leren is?</b></summary>

**Antwoord:** Personalizer retourneert dezelfde waarschijnlijkheden in een Rank API-resultaat wanneer het net is gestart en een _leeg_ model heeft, of wanneer u de Personalizer Loop opnieuw instelt en uw model zich nog steeds binnen de **frequentieperiode van** de modelupdate bevindt.

Wanneer de nieuwe updateperiode begint, wordt het bijgewerkte model gebruikt en ziet u de waarschijnlijkheidsverandering.

</details>

<details>
<summary><b>De leerlus was leren, maar lijkt niet meer te leren, en de kwaliteit van de Rank resultaten is niet zo goed. Wat moet ik doen?</b></summary>

**Antwoord**:
* Zorg ervoor dat u één evaluatie hebt voltooid en toegepast in de Azure-portal voor die personalizerbron (leerlus).
* Zorg ervoor dat alle beloningen worden verzonden, via de Reward API, en verwerkt.

</details>


<details>
<summary><b>Hoe weet ik dat de leerlus regelmatig wordt bijgewerkt en wordt gebruikt om mijn gegevens te scoren?</b></summary>

**Antwoord:** U het tijdstip vinden waarop het model voor het laatst is bijgewerkt op de pagina **Model- en Leerinstellingen** van de Azure-portal. Als u een oude tijdstempel ziet, is dit waarschijnlijk omdat u de rang- en beloningsoproepen niet verzendt. Als de service geen binnenkomende gegevens heeft, wordt het leren niet bijgewerkt. Als u ziet dat de leerlus niet vaak genoeg wordt bijgewerkt, u de **modelupdatefrequentie**van de lus bewerken.

</details>

## <a name="offline-evaluations"></a>Offline evaluaties

<details>
<summary><b>Het functiebelang van een offlineevaluatie geeft een lange lijst met honderden of duizenden items. Wat is er gebeurd?</b></summary>

**Antwoord:** Dit is meestal te wijten aan timestamps, gebruikers-id's of een andere fijnkorrelige functies verzonden.

</details>

<details>
<summary><b>Ik heb een offline evaluatie gemaakt en dat lukte vrijwel direct. Waarom is dat? Zie ik geen resultaten?</b></summary>

**Antwoord**: De offline evaluatie maakt gebruik van de getrainde modelgegevens van de gebeurtenissen in die periode. Als u geen gegevens hebt verzonden in de periode tussen begin- en eindtijd van de evaluatie, wordt deze zonder resultaat voltooid. Verzend een nieuwe offline evaluatie door een tijdsbereik te selecteren met gebeurtenissen die naar Personalizer zijn verzonden.

</details>


## <a name="learning-policy"></a>Leerbeleid

<details>
<summary><b>Hoe importeer ik een leerbeleid?</b></summary>

**Antwoord:** Meer informatie over [leerbeleidsconcepten](concept-active-learning.md#understand-learning-policy-settings) en hoe u een nieuw leerbeleid [toepast.](how-to-manage-model.md) Als u geen leerbeleid wilt selecteren, u de [offlineevaluatie](how-to-offline-evaluation.md) gebruiken om een leerbeleid voor te stellen op basis van uw huidige gebeurtenissen.

</details>

## <a name="security"></a>Beveiliging

<details>
<summary><b>De API-sleutel voor mijn lus is gecompromitteerd. Wat kan ik doen?</b></summary>

**Antwoord:** U de ene sleutel regenereren nadat u uw klanten hebt verwisseld om de andere sleutel te gebruiken. Met twee sleutels u de sleutel op een luie manier propageren zonder downtime te hoeven hebben. We raden aan om dit op een regelmatige cyclus te doen als een veiligheidsmaatregel.

</details>

## <a name="next-steps"></a>Volgende stappen

[De frequentie van de modelupdate configureren](how-to-settings.md#model-update-frequency)