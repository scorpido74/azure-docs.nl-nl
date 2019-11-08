---
title: Gelijktijdigheids beheer | Azure Marketplace
description: Strategieën voor gelijktijdigheids beheer voor de Cloud Partner-portal Publishing-Api's.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6e2f8922d42e40d14338f06be983d3913b20859d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819749"
---
# <a name="concurrency-control"></a>Gelijktijdigheids beheer

Elke aanroep van de Cloud Partner-portal Publishing-Api's moet expliciet bepalen welke gelijktijdigheids beheer strategie u wilt gebruiken. Als de header **if-match** niet kan worden opgegeven, resulteert dit in een HTTP 400-fout reactie. We bieden twee strategieën voor gelijktijdigheids beheer.

-   **Optimistische** -de client die de update uitvoert, controleert of de gegevens zijn gewijzigd sinds de laatste keer dat de gegevens zijn gelezen.
-   **Laatste één WINS** : de client werkt de gegevens rechtstreeks bij, ongeacht of deze zijn gewijzigd sinds de laatste Lees tijd.

<a name="optimistic-concurrency-workflow"></a>Optimistische gelijktijdigheids werk stroom
-------------------------------

We raden u aan de optimistische gelijktijdigheids strategie met de volgende werk stroom te gebruiken om te garanderen dat er geen onverwachte bewerkingen worden uitgevoerd op uw resources.

1.  Een entiteit ophalen met behulp van de Api's. Het antwoord bevat een ETag-waarde waarmee de momenteel opgeslagen versie van de entiteit (op het moment van de reactie) wordt geïdentificeerd.
2.  Neem op het moment van de update dezelfde ETag-waarde op in de aanvraag header verplichte **if-match** .
3.  De API vergelijkt de ETag-waarde die in de aanvraag is ontvangen met de huidige ETag-waarde van de entiteit in een Atomic-trans actie.
    *   Als de ETag-waarden verschillen, retourneert de API een `412 Precondition Failed` HTTP-antwoord. Deze fout geeft aan dat de entiteit door een ander proces is bijgewerkt sinds de client de laatste keer is opgehaald, of dat de ETag-waarde die is opgegeven in de aanvraag, onjuist is.
    *  Als de ETag-waarden hetzelfde zijn of als de header **if-match** het Joker teken asterisk (`*`) bevat, voert de API de aangevraagde bewerking uit. Met de API-bewerking wordt ook de opgeslagen ETag-waarde van de entiteit bijgewerkt.


> [!NOTE]
> Het opgeven van het Joker teken (*) in de **if-match-** header resulteert in de API met de laatste-één-WINS-strategie voor gelijktijdigheid. In dit geval wordt de ETag-vergelijking niet uitgevoerd en wordt de resource zonder controles bijgewerkt. 
