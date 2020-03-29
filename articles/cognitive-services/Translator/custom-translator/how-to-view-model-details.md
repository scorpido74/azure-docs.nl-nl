---
title: Modeldetails weergeven - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Het tabblad Modellen onder een project toont details van elk model, zoals modelnaam, modelstatus, BLEU-score, training, tuning, het tellen van de uitspreken van de zin.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595563"
---
# <a name="view-model-details"></a>Modelgegevens weergeven

Het tabblad Modellen onder project toont alle modellen in dat project. Alle modellen die voor dat project zijn getraind, worden op dit tabblad weergegeven.

Voor elk model in het project worden deze details weergegeven.

1.  Modelnaam: toont de modelnaam van een bepaald model.

2.  Status: Geeft de status van een bepaald model weer. Uw nieuwe training heeft de status Ingediend totdat deze is geaccepteerd. De status wordt gewijzigd in Gegevensverwerking terwijl de service de inhoud van uw documenten evalueert. Wanneer de evaluatie van uw documenten is voltooid, wordt de status gewijzigd in Uitvoeren en u het aantal zinnen zien dat deel uitmaakt van de training, inclusief de stem- en testsets die automatisch voor u zijn gemaakt. Hieronder vindt u een lijst met modelstatus die de status van de modellen beschrijft.

    -  Ingezonden: geeft aan dat de backend de documenten voor dat model verwerkt.

    -  TrainingQueued: geeft aan dat de training in de wachtrij staat voor het MT-systeem voor dat model.

    -  Uitvoeren: geeft aan dat de training wordt uitgevoerd in het MT-systeem voor dat model.

    -  Geslaagd: geeft aan dat de training is geslaagd in het MT-systeem en dat er een model beschikbaar is. In deze status wordt een BLEU-score weergegeven voor dat model.

    -  Geïmplementeerd: geeft aan dat het succesvol getrainde model wordt ingediend bij het MT-systeem voor implementatie.

    -  Implementatie wordt ongedaan maken: hiermee geeft u op dat het geïmplementeerde model niet wordt geïmplementeerd.

    -  Niet geïmplementeerd: hiermee geeft u aan dat het implementatieproces van een model is voltooid.

    -  Training mislukt: geeft aan dat de training is mislukt. Als er een trainingsfout optreedt, probeert u de trainingstaak opnieuw. Als de fout blijft bestaan, neem dan contact met ons op. Verwijder het mislukte model niet.

    - Gegevensverwerking mislukt: geeft aan dat de verwerking van gegevens is mislukt voor een of meer documenten die tot het model behoren.

    - Implementatie mislukt: geeft op dat de modelimplementatie is mislukt.

    - Gemigreerdconcept: geeft aan dat het model in conceptstatus is na migratie van Hub naar Aangepaste vertaler.

4.  BLEU Score: toont BLEU (Tweetalige Evaluatie Understudy) score van het model, met vermelding van de kwaliteit van uw vertaalsysteem. Deze score vertelt u hoe nauw de vertalingen die door het vertaalsysteem worden gedaan als gevolg van deze training overeenkomen met de referentiezinnen in de testgegevensset. De BLEU-score wordt weergegeven als de training succesvol is voltooid. Als de training niet is voltooid/ mislukt, ziet u geen BLEU-score.

5.  Aantal trainingszinnen: geeft het totale aantal zinnen weer dat wordt gebruikt als trainingsset.

6.  Aantal stemzinen: geeft het totale aantal zinnen weer dat wordt gebruikt als stemset.

7.  Aantal trainingszinnen: geeft het totale aantal zinnen weer dat wordt gebruikt als testset.

8.  Aantal monozin: geeft het totale aantal zinnen weer dat als monoset wordt gebruikt.

9.  Actieknop implementeren: voor een met succes getraind model wordt de knop 'Implementeren' weergegeven als deze niet is geïmplementeerd. Als een model wordt geïmplementeerd, wordt de knop 'Implementeren ontrollen' weergegeven.

10. Verwijderen: u deze knop gebruiken als u het model wilt verwijderen. Als u een model verwijdert, worden geen documenten verwijderd die worden gebruikt om dat model te maken.

    ![Modelgegevens weergeven](media/how-to/how-to-view-model-details.png)

>[!Note]
>Om opeenvolgende trainingen voor dezelfde systemen te vergelijken, is het belangrijk om de tuningset en testset constant te houden.

## <a name="view-model-training-details"></a>Details van modeltraining weergeven

Wanneer uw training is voltooid, u details over de training bekijken op de pagina details. Selecteer een project, zoek en selecteer het tabblad modellen en kies een model.

De modelpagina heeft twee tabbladen: Trainingsdetails en Test.

1.  **Trainingsgegevens:** Op het tabblad ziet u de lijst met documenten die in de training worden gebruikt:

    -  Naam documenten: in dit veld wordt de naam van het document weergegeven

    -  Documenttype: in dit veld wordt weergegeven of dit document parallel/ mono is.

    -  Aantal zinnen in brontaal: dit veld geeft aan dat het aantal zinnen er is als onderdeel van de brontaal.

    -  Aantal zinnen in doeltaal: dit veld geeft aan dat het aantal zinnen er is als onderdeel van doeltaal.

    -  Uitgelijnde zinnen: in dit veld wordt het aantal zinnen weergegeven dat is uitgelijnd door Aangepaste vertaler tijdens het uitlijnenproces.

    -  Gebruikte zinnen: Dit veld toont het aantal zinnen dat door Custom Translator tijdens deze training is gebruikt.

    ![Modeltrainingsgegevens](media/how-to/how-to-model-training-details.png)

2.  **Test:** Op dit tabblad worden de testgegevens voor een succesvolle training weergegeven.

## <a name="next-steps"></a>Volgende stappen

- [Testresultaten](how-to-view-system-test-results.md) bekijken en trainingsresultaten analyseren.
