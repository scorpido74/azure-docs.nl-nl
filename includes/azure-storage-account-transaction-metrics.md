---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: b2e753a3f9741856dd8b81755912ad7bd78b5557
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711270"
---
Azure Storage levert de volgende metrische gegevens over trans acties in Azure Monitor.

| Gegevens | Beschrijving |
| ------------------- | ----------------- |
| Transacties | Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. <br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Toepasselijke dimensies: ResponseType, geotype, ApiName en Authentication ([definitie](#metrics-dimensions))<br/> Waarde-voor beeld: 1024 |
| Inkomend verkeer | De hoeveelheid inkomende gegevens. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure. <br/><br/> Eenheid: bytes <br/> Aggregatie type: totaal <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 1024 |
| Uitgaand verkeer | De hoeveelheid uitgaande gegevens. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer. <br/><br/> Eenheid: bytes <br/> Aggregatie type: totaal <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 1024 |
| SuccessServerLatency | De gemiddelde tijd die nodig is om een aanvraag door Azure Storage te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency. <br/><br/> Eenheid: milliseconden <br/> Aggregatie type: gemiddeld <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 1024 |
| SuccessE2ELatency | De gemiddelde end-to-end-latentie van geslaagde aanvragen aan een opslagservice of de opgegeven API-bewerking. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen. <br/><br/> Eenheid: milliseconden <br/> Aggregatie type: gemiddeld <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 1024 |
| Beschikbaarheid | Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde voor het totale aantal factureerbare aanvragen te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. <br/><br/> Eenheid: percentage <br/> Aggregatie type: gemiddeld <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 99,99 |