---
title: Gelijktijdigheidscontrole | Azure Marketplace
description: Strategieën voor gelijktijdigheidscontrole voor de API's voor het publiceren van API's voor de cloudpartnerportal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d0f035d77e74f157b793b9edf3ab5d3494096d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288645"
---
# <a name="concurrency-control"></a>Gelijktijdigheidscontrole

In elk gesprek naar de API's voor het publiceren van de Cloud Partner Portal moet expliciet worden opgegeven welke strategie voor gelijktijdigheidscontrole moet worden gebruikt. Als u de **kop-Inhoud niet** geeft, wordt een HTTP 400-foutreactie weergegeven. Wij bieden twee strategieën voor gelijktijdigheidscontrole.

-   **Optimistisch** - De client die de update uitvoert, controleert of de gegevens zijn gewijzigd sinds deze de gegevens voor het laatst hebben gelezen.
-   **Laatste wint** - De client werkt de gegevens direct bij, ongeacht of een andere toepassing deze heeft gewijzigd sinds de laatste leestijd.

<a name="optimistic-concurrency-workflow"></a>Optimistische gelijktijdigheidsworkflow
-------------------------------

We raden u aan de optimistische gelijktijdigheidsstrategie, met de volgende workflow, te gebruiken om te garanderen dat er geen onverwachte bewerkingen worden aangebracht aan uw resources.

1.  Een entiteit ophalen met behulp van de API's. Het antwoord bevat een ETag-waarde die de momenteel opgeslagen versie van de entiteit identificeert (op het moment van het antwoord).
2.  Neem op het moment van de update dezelfde ETag-waarde op in de verplichte **if-match-aanvraagkop.**
3.  De API vergelijkt de ETag-waarde die in de aanvraag is ontvangen met de huidige ETag-waarde van de entiteit in een atoomtransactie.
    *   Als de ETag-waarden verschillen, `412 Precondition Failed` retourneert de API een HTTP-antwoord. Deze fout geeft aan dat een ander proces de entiteit heeft bijgewerkt sinds de client deze voor het laatst heeft opgehaald of dat de ETag-waarde die in de aanvraag is opgegeven, onjuist is.
    *  Als de ETag-waarden hetzelfde zijn of als de kop Tekst`*` **Als-Match** het sterretje () bevat, voert de API de gevraagde bewerking uit. De API-bewerking werkt ook de opgeslagen ETag-waarde van de entiteit bij.


> [!NOTE]
> Het opgeven van het jokerteken (*) in de **koptekst If-Match** resulteert in de API met de gelijktijdigvalutastrategie Last-one-wins. In dit geval vindt de ETag-vergelijking niet plaats en wordt de resource zonder controles bijgewerkt. 
