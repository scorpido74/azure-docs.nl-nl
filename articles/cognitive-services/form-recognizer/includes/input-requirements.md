---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75379423"
---
Form Recognizer werkt aan invoerdocumenten die aan deze vereisten voldoen:

* De opmaak moet JPG, PNG, PDF (tekst of gescand) of TIFF zijn. Tekstingesloten PDF's zijn het beste omdat er geen kans is op fout in tekenextractie en locatie.
* Als uw PDF's met een wachtwoord zijn vergrendeld, moet u het slot verwijderen voordat u ze indient.
* PDF- en TIFF-documenten moeten 200 pagina's of minder zijn en de totale grootte van de trainingsgegevensset moet 500 pagina's of minder zijn.
* Voor afbeeldingen moeten de afmetingen tussen 600 x 100 pixels en 4200 x 4200 pixels liggen.
* Als gescand op basis van papieren documenten, formulieren moeten worden van hoge kwaliteit scans.
* Tekst moet het Latijnse alfabet (Engelse tekens) gebruiken.
* Voor onbegeleid leren (zonder gelabelde gegevens) moeten gegevens sleutels en waarden bevatten.
* Voor leren zonder toezicht (zonder gelabelde gegevens) moeten toetsen boven of links van de waarden worden weergegeven; ze kunnen niet onder of rechts worden weergegeven.

Form Recognizer ondersteunt momenteel geen dit soort invoergegevens:

* Complexe tabellen (geneste tabellen, samengevoegde kopteksten of cellen, enzovoort).
* Selectievakjes of keuzerondjes.
