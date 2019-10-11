---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ec825bb59840784ca45e8a38caa664c19883fb32
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263320"
---
|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|routePrefix|api|Het route voorvoegsel dat van toepassing is op alle routes. Gebruik een lege teken reeks om het standaard voorvoegsel te verwijderen. |
|maxOutstandingRequests|200<sup>*</sup>|Het maximum aantal openstaande aanvragen dat op een bepaald moment wordt bewaard. Deze limiet omvat aanvragen die in de wachtrij zijn geplaatst, maar die nog niet zijn gestart, evenals de uitvoeringen die worden uitgevoerd. Alle binnenkomende aanvragen die deze limiet overschrijden, worden geweigerd met een antwoord van 429 ' bezet '. Hiermee kunnen aanroepers op tijd gebaseerde strategieën voor nieuwe pogingen gebruiken en kunt u ook de maximum latentie van aanvragen beheren. Hiermee beheert u alleen de wachtrij die zich in het uitvoerings traject van de Script Host voordoet. Andere wacht rijen, zoals de ASP.NET-aanvraag wachtrij, blijven van kracht en worden niet beïnvloed door deze instelling. <sup>*</sup> De standaard waarde voor versie 1. x is ongebonden (`-1`). De standaard waarde voor versie 2. x in een verbruiks abonnement is 200. De standaard waarde voor versie 2. x in een toegewezen plan is ongebonden (`-1`).|
|maxConcurrentRequests|100<sup>*</sup>|Het maximum aantal HTTP-functies dat parallel wordt uitgevoerd. Op die manier kunt u gelijktijdigheid beheren, waardoor het resource gebruik kan worden beheerd. Stel dat u een http-functie hebt die gebruikmaakt van veel systeem bronnen (geheugen/CPU/sockets), zodat er problemen ontstaan wanneer gelijktijdigheid te hoog is. Het is ook mogelijk dat u een functie hebt waarmee uitgaande aanvragen voor een service van derden worden uitgevoerd. deze aanroepen moeten een beperkt aantal zijn. In dergelijke gevallen kan het Toep assen van een beperking hier helpen. <sup>*</sup> De standaard waarde voor versie 1. x is ongebonden (`-1`). De standaard waarde voor versie 2. x in een verbruiks abonnement is 100. De standaard waarde voor versie 2. x in een toegewezen plan is ongebonden (`-1`).|
|dynamicThrottlesEnabled|waar<sup>*</sup>|Als deze instelling is ingeschakeld, wordt door de aanvraag verwerkings pijplijn periodiek systeem prestatie meter items gecontroleerd, zoals verbindingen/threads/processen/geheugen/CPU/etc. als een van deze prestatie meter items een ingebouwde hoge drempel waarde (80%) heeft, worden aanvragen Er is een antwoord van 429 ' te druk ' weer gegeven totdat de teller (s) de normale niveaus retour neren. <sup>*</sup> De standaard waarde voor versie 1. x is onwaar. De standaard waarde voor versie 2. x in een verbruiks abonnement is waar. De standaard waarde voor versie 2. x in een speciaal plan is onwaar.|
