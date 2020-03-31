---
title: Waar u experimentbestanden & schrijven
titleSuffix: Azure Machine Learning
description: Lees waar u uw experimentinvoerbestanden opslaan en waar u uitvoerbestanden schrijven om opslagbeperkende fouten en experimentlatentie te voorkomen.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 12a38b08fd429280f34b4eb02d4b72187b622261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078451"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Waar bestanden opslaan en schrijven voor Azure Machine Learning-experimenten
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u waar u invoerbestanden opslaan en waar uitvoerbestanden uit uw experimenten moeten worden geschreven om opslaglimietfouten en experimentlatentie te voorkomen.

Bij het starten van training wordt uitgevoerd op een [compute target,](how-to-set-up-training-targets.md)ze zijn geïsoleerd van externe omgevingen. Het doel van dit ontwerp is om reproduceerbaarheid en draagbaarheid van het experiment te waarborgen. Als u hetzelfde script twee keer uitvoert, op hetzelfde of een ander rekendoel, ontvangt u dezelfde resultaten. Met dit ontwerp u rekendoelen behandelen als stateloze berekeningsbronnen, die elk geen affiniteit hebben met de taken die worden uitgevoerd nadat ze zijn voltooid.

## <a name="where-to-save-input-files"></a>Waar invoerbestanden opslaan

Voordat u een experiment starten op een compute target of uw lokale machine, moet u ervoor zorgen dat de benodigde bestanden beschikbaar zijn voor dat rekendoel, zoals afhankelijkheidsbestanden en gegevensbestanden die uw code moet uitvoeren.

Azure Machine Learning voert trainingsscripts uit door de hele scriptmap naar de doelzoekcontext te kopiëren en vervolgens een momentopname te maken. De opslaglimiet voor momentopnamen van experimenten is 300 MB en/of 2000 bestanden.

Daarom raden we aan:

* **Uw bestanden opslaan in een Azure Machine [Learning-gegevensarchief.](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)** Dit voorkomt problemen met de latentie van experimenten en heeft de voordelen van toegang tot gegevens van een extern gegevensdoel, wat betekent dat verificatie en montage worden beheerd door Azure Machine Learning. Meer informatie over het opgeven van een gegevensarchief als uw bronmap en het uploaden van bestanden naar uw gegevensarchief in het artikel [Access-gegevens uit uw gegevensarchief.](how-to-access-data.md)

* **Als u slechts een paar gegevensbestanden en afhankelijkheidsscripts nodig hebt en geen gegevensarchief gebruiken,** plaatst u de bestanden in dezelfde mapmap als uw trainingsscript. Geef deze map `source_directory` op als uw rechtstreeks in uw trainingsscript of in de code die uw trainingsscript aanroept.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Opslaglimieten van experimentmomentopnamen

Voor experimenten maakt Azure Machine Learning automatisch een experimentmomentopname van uw code op basis van de map die u voorstelt wanneer u de run configureert. Dit heeft een totale limiet van 300 MB en/of 2000 bestanden. Als u deze limiet overschrijdt, ziet u de volgende fout:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Als u deze fout wilt oplossen, slaat u uw experimentbestanden op in een gegevensarchief. Als u geen datastore gebruiken, biedt de onderstaande tabel mogelijke alternatieve oplossingen.

Beschrijving&nbsp;van experiment|Oplossing voor opslaglimiet
---|---
Minder dan 2000 bestanden & geen datastore kunnen gebruiken| De groottelimiet voor momentopnamen overschrijven met <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Dit kan enkele minuten duren, afhankelijk van het aantal en de grootte van bestanden.
Moet specifieke scriptmap gebruiken| Maak `.amlignore` een bestand om bestanden uit te sluiten van uw experimentmomentopname die geen deel uitmaken van de broncode. Voeg de bestandsnamen `.amlignore` toe aan het bestand en plaats deze in dezelfde map als uw trainingsscript. Het `.amlignore` bestand gebruikt dezelfde [syntaxis en patronen](https://git-scm.com/docs/gitignore) als een `.gitignore` bestand.
Pijplijn|Voor elke stap een andere submap gebruiken
Jupyter-notebooks| Maak `.amlignore` een bestand of verplaats uw notitieblok naar een nieuwe, lege submap en voer uw code opnieuw uit.

## <a name="where-to-write-files"></a>Waar bestanden te schrijven

Als gevolg van de isolatie van trainingsexperimenten, de wijzigingen in bestanden die gebeuren tijdens het uitvoeren van worden niet noodzakelijkerwijs volgehouden buiten uw omgeving. Als uw script de bestanden lokaal wijzigt om te berekenen, worden de wijzigingen niet gehandhaafd voor uw volgende experiment en worden ze niet automatisch naar de clientmachine gebladerd. Daarom hebben de wijzigingen die tijdens de eerste experimentrun zijn aangebracht geen invloed op die in de tweede.

Bij het schrijven van wijzigingen raden we u aan bestanden te schrijven naar een Azure Machine Learning-gegevensarchief. Zie [Toegangsgegevens van uw gegevenswinkels](how-to-access-data.md).

Als u geen gegevensarchief nodig hebt, `./outputs` schrijft u `./logs` bestanden naar de en/of map.

>[!Important]
> Twee mappen, *uitvoer en logboeken* , ontvangen een speciale behandeling door Azure Machine Learning. *logs* Tijdens de training worden`./outputs` de`./logs` bestanden automatisch geüpload naar uw hardloopgeschiedenis, zodat u er toegang toe hebt zodra uw hardlooprun is voltooid.

* **Voor uitvoer, zoals statusberichten of scoreresultaten,** schrijft u bestanden naar de `./outputs` map, zodat ze worden volgehouden als artefacten in de rungeschiedenis. Houd rekening met het aantal en de grootte van bestanden die naar deze map zijn geschreven, omdat latentie kan optreden wanneer de inhoud wordt geüpload om de geschiedenis uit te voeren. Als latentie een probleem is, wordt het schrijven van bestanden naar een datastore aanbevolen.

* **Als u geschreven bestand wilt opslaan als logboeken in de run-geschiedenis,** schrijft u bestanden naar `./logs` map. De logboeken worden in realtime geüpload, dus deze methode is geschikt voor het streamen van live-updates vanaf een externe run.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het openen van gegevens van uw datastores](how-to-access-data.md).

* Meer informatie over [het instellen van trainingsdoelen](how-to-set-up-training-targets.md).
