---
title: Probleem oplossing-persoonlijker
description: In dit artikel vindt u antwoorden op veelgestelde vragen over het oplossen van problemen met persoonlijke instellingen.
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: diberry
ms.openlocfilehash: fec403da7f54098dbf197d14f3b16afd30bf5efc
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77469541"
---
# <a name="personalizer-troubleshooting"></a>Problemen met persoonlijker oplossen

In dit artikel vindt u antwoorden op veelgestelde vragen over het oplossen van problemen met persoonlijke instellingen.

## <a name="transaction-errors"></a>Transactie fouten

<details>
<summary><b>Er wordt een reactie van HTTP 429 (te veel aanvragen) ontvangen van de service. Wat kan ik doen?</b></summary>

**Antwoord**: als u een gratis prijs categorie hebt gekozen tijdens het maken van het persoonlijk exemplaar, is er een quotum limiet voor het aantal toegestane rang aanvragen. Controleer de API-aanroep frequentie voor de positie-API (in het deel venster metrische gegevens in de Azure Portal voor uw persoonlijke resource) en pas de prijs categorie aan (in het deel venster prijs categorie) als uw oproep volume naar verwachting groter is dan de drempel waarde voor gekozen prijs categorie.

</details>

<details>
<summary><b>Er wordt een 5xx-fout weer op de positie-of belonings-Api's. Wat moet ik doen?</b></summary>

**Antwoord**: deze problemen moeten transparant zijn. Als ze door gaan, neemt u contact op met de ondersteuning door **nieuwe ondersteunings aanvraag** te selecteren in het gedeelte **ondersteuning en probleem oplossing** in de Azure portal voor uw persoonlijke resource.

</details>

## <a name="learning-loop"></a>Learning-lus

<details>
<summary>
<b>De learning-lus lijkt niet te leren. Hoe kan ik dit probleem oplossen?</b></summary>

**Antwoord**: voor de learning-lus zijn enkele duizenden belonings aanvragen vereist voordat de rang schikkingen een efficiënte prioriteit krijgt.

Als u niet zeker weet hoe uw leer proces momenteel zich gedraagt, voert u een [offline-evaluatie](concepts-offline-evaluation.md)uit en past u het gecorrigeerde leer beleid toe.

</details>

<details>
<summary><b>Ik blijf bij het ophalen van rang resultaten met dezelfde kansen voor alle items. Hoe kan ik weet je dat Personaler leren?</b></summary>

**Antwoord**: personaler retourneert dezelfde waarschijnlijkheid in een rangorde API-resultaat wanneer het zojuist is gestart en een _leeg_ model heeft, of wanneer u de personaler-lus opnieuw instelt en uw model zich nog steeds in de periode van uw **model update** bevindt.

Wanneer de nieuwe update periode begint, wordt het bijgewerkte model gebruikt en ziet u de waarschijnlijke veranderingen.

</details>

<details>
<summary><b>Het leer proces is leren, maar lijkt niet meer te weten en de kwaliteit van de rang resultaten is niet goed. Wat moet ik doen?</b></summary>

**Antwoord**:
* Zorg ervoor dat u een evaluatie hebt voltooid en toegepast op de Azure Portal voor die persoonlijker resource (Learning-lus).
* Zorg ervoor dat alle beloningen worden verzonden via de belonings-API en verwerkt.

</details>


<details>
<summary><b>Hoe kan ik weet u dat de learning-lus regel matig wordt bijgewerkt en wordt gebruikt om mijn gegevens te scoren?</b></summary>

**Antwoord**: u kunt het tijdstip waarop het model voor het laatst is bijgewerkt, vinden op de pagina **model-en leer instellingen** van de Azure Portal. Als u een oud tijds tempel ziet, is dit waarschijnlijk omdat u niet de Rangings-en belonings aanroepen verzendt. Als de service geen inkomende gegevens heeft, wordt het leer proces niet bijgewerkt. Als u ziet dat de learning-lus niet vaak genoeg wordt bijgewerkt, kunt u de **model update frequentie**van de lus bewerken.

</details>

## <a name="offline-evaluations"></a>Offline evaluaties

<details>
<summary><b>De belang rijke functie van een evaluatie versie retourneert een lange lijst met honderden of duizenden items. Wat is er gebeurd?</b></summary>

**Antwoord**: dit wordt meestal veroorzaakt door tijds tempels, gebruikers-id's of andere verfijnde functies die worden verzonden in.

</details>

<details>
<summary><b>Ik heb een offline-evaluatie gemaakt en deze is bijna onmiddellijk voltooid. Waarom is dat? Ik zie geen resultaten?</b></summary>

**Antwoord**: de offline-evaluatie maakt gebruik van de getrainde model gegevens van de gebeurtenissen in die tijds periode. Als u geen gegevens hebt verzonden binnen de tijds periode tussen de begin-en eind tijd van de evaluatie, wordt deze zonder resultaat voltooid. Verzend een nieuwe offline-evaluatie door een tijds bereik te selecteren met de gebeurtenissen die u naar persoonlijke voor keuren hebt verzonden.

</details>


## <a name="learning-policy"></a>Leer beleid

<details>
<summary><b>Hoe kan ik een leer beleid importeren?</b></summary>

**Antwoord**: meer informatie over [concepten van het trainings beleid](concept-active-learning.md#understand-learning-policy-settings) en [het Toep assen](how-to-learning-policy.md) van een nieuw leer beleid. Als u geen leer beleid wilt selecteren, kunt u de [offline-evaluatie](how-to-offline-evaluation.md) gebruiken om een leer beleid te suggereren op basis van uw huidige gebeurtenissen.

</details>

## <a name="security"></a>Beveiliging

<details>
<summary><b>De API-sleutel voor mijn lus is aangetast. Wat kan ik doen?</b></summary>

**Antwoord**: u kunt één sleutel opnieuw genereren nadat u uw clients hebt gewisseld om de andere sleutel te gebruiken. Als u twee sleutels hebt, kunt u de sleutel op een luie manier door geven zonder uitval tijd. U wordt aangeraden dit te doen op een regel matige cyclus als beveiligings maatregel.

</details>

## <a name="next-steps"></a>Volgende stappen

[De update frequentie van het model configureren](how-to-settings.md#model-update-frequency)