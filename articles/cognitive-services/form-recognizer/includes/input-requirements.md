---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379423"
---
Formulier herkenning werkt op invoer documenten die aan de volgende vereisten voldoen:

* Indeling moet JPG, PNG, PDF (tekst of gescand) of TIFF zijn. Tekst-Inge sloten Pdf's zijn het beste omdat er geen mogelijke fout is in teken extractie en locatie.
* Als uw Pdf's met een wacht woord zijn vergrendeld, moet u de vergren deling verwijderen voordat u ze verzendt.
* PDF-en TIFF-documenten moeten 200 pagina's of minder zijn en de totale grootte van de set met trainings gegevens moet 500 pagina's of minder zijn.
* Voor afbeeldingen moeten de afmetingen tussen 600 x 100 pixels en 4200 x 4200 pixels liggen.
* Als u scant vanuit papieren documenten, moeten formulieren kwalitatief hoogwaardige scans zijn.
* Tekst moet het Latijnse alfabet (Engelse tekens) gebruiken.
* Gegevens moeten sleutels en waarden bevatten voor leren zonder super visie (geen gelabelde gegevens).
* Voor niet-verouderd leren (zonder gelabelde gegevens), moeten sleutels boven of links van de waarden worden weer gegeven. ze kunnen niet onder of rechts worden weer gegeven.

Dit type invoer gegevens wordt momenteel niet door de formulier herkenning ondersteund:

* Complexe tabellen (geneste tabellen, samengevoegde kopteksten of cellen, enzovoort).
* Selectie vakjes of keuze rondjes.
