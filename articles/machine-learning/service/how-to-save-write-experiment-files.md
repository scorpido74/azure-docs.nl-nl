---
title: Waar opslaan & write experimenten-bestanden
titleSuffix: Azure Machine Learning
description: Meer informatie over waar u uw experimentele invoer bestanden opslaat, en waar u uitvoer bestanden schrijft om te voor komen dat er problemen met de opslag beperking en de latentie van een experiment optreden.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 41b2602e57d295cfd7e475f4b3aa5657bd4e24d7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489601"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Locatie voor het opslaan en schrijven van bestanden voor Azure Machine Learning experimenten
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel wordt beschreven waar u invoer bestanden opslaat, en waar u uitvoer bestanden van uw experimenten schrijft om opslag limiet fouten en experiment latentie te voor komen.

Wanneer u training uitvoert op een [Compute-doel](how-to-set-up-training-targets.md), zijn ze geïsoleerd van buiten omgevingen. Het doel van dit ontwerp is om te zorgen voor reproduceer baarheid en draag baarheid van het experiment. Als u hetzelfde script twee keer uitvoert op hetzelfde of een ander Compute-doel, worden dezelfde resultaten weer gegeven. Met dit ontwerp kunt u Compute-doelen behandelen als resources zonder stateless reken kracht, die elk geen affiniteit hebben met de taken die worden uitgevoerd nadat ze zijn voltooid.

## <a name="where-to-save-input-files"></a>Waar de invoer bestanden worden opgeslagen

Voordat u een experiment kunt initiëren op een reken doel of op uw lokale computer, moet u ervoor zorgen dat de benodigde bestanden beschikbaar zijn voor het reken doel, zoals afhankelijkheids bestanden en gegevens bestanden die de code moet uitvoeren.

Azure Machine Learning oefent trainings scripts uit door de hele scripthost naar de doel Compute-context te kopiëren en vervolgens een moment opname te maken. De opslag limiet voor experimenten-moment opnamen is 300 MB en/of 2000 bestanden.

Daarom kunt u het beste het volgende doen:

* **Uw bestanden worden opgeslagen in een Azure Machine Learning [gegevens opslag](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).** Dit voor komt problemen met de latentie van experimenten en heeft de voor delen van het openen van gegevens van een extern Compute-doel, wat betekent dat verificatie en bevestiging wordt beheerd door Azure Machine Learning. Meer informatie over het opgeven van een gegevens opslag als bron directory en het uploaden van bestanden naar uw gegevens opslag in het artikel [toegangs gegevens van uw gegevens opslag](how-to-access-data.md) .

* **Als u alleen een aantal gegevens bestanden en afhankelijkheids scripts nodig hebt en u geen gegevens opslag kunt gebruiken, plaatst u** de bestanden in dezelfde map als uw trainings script. Geef deze map op als uw `source_directory` rechtstreeks in uw trainings script of in de code die uw trainings script aanroept.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Opslag limieten van experiment-moment opnamen

Voor experimenten maakt Azure Machine Learning automatisch een Experimenteer moment opname van uw code op basis van de directory die u suggereert bij het configureren van de uitvoering. Dit heeft een totale limiet van 300 MB en/of 2000 bestanden. Als u deze limiet overschrijdt, ziet u de volgende fout:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

U kunt deze fout oplossen door uw experiment-bestanden op te slaan in een gegevens opslag. Als u geen gegevens opslag kunt gebruiken, biedt de onderstaande tabel mogelijke alternatieve oplossingen.

Beschrijving van experiment&nbsp;|Oplossing voor opslag limiet
---|---
Minder dan 2000 bestanden & kan geen gegevens opslag gebruiken| Maximale grootte van moment opname overschrijven met <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Dit kan enkele minuten duren, afhankelijk van het aantal en de grootte van de bestanden.
U moet een specifieke script Directory gebruiken| Maak een `.amlignore` bestand om bestanden uit de moment opname van uw experiment uit te sluiten die geen deel uitmaken van de bron code. Voeg de bestands namen toe aan het `.amlignore`-bestand en plaats het in dezelfde map als uw trainings script. Het `.amlignore`-bestand gebruikt dezelfde [syntaxis en patronen](https://git-scm.com/docs/gitignore) als een `.gitignore` bestand.
Pijplijn|Voor elke stap een andere submap gebruiken
Jupyter-notebooks| Maak een `.amlignore` bestand of verplaats uw notitie blok naar een nieuwe, lege, submap en voer de code opnieuw uit.

## <a name="where-to-write-files"></a>Locatie voor het schrijven van bestanden

Vanwege de isolatie van trainings experimenten, zijn de wijzigingen aan bestanden die tijdens de uitvoering plaatsvinden, niet noodzakelijkerwijs buiten uw omgeving bewaard. Als uw script de bestanden Local op Compute wijzigt, worden de wijzigingen niet bewaard voor uw volgende experiment en worden ze niet automatisch door gegeven aan de client computer. De wijzigingen die zijn aangebracht tijdens het eerste experiment, worden dus niet in de tweede keer uitgevoerd.

Wanneer u wijzigingen schrijft, raden we u aan om bestanden naar een Azure Machine Learning gegevens opslag te schrijven. Bekijk [de toegang tot gegevens uit uw gegevens opslag](how-to-access-data.md).

Als u geen gegevens opslag nodig hebt, schrijft u de bestanden naar de map `./outputs` en/of `./logs`.

>[!Important]
> Twee mappen, *uitvoer* en *Logboeken*, een speciale behandeling ontvangen door Azure machine learning. Wanneer u tijdens de training bestanden naar`./outputs`-en`./logs`-mappen schrijft, worden de bestanden automatisch naar de uitvoerings geschiedenis geüpload, zodat u er toegang tot hebt wanneer de uitvoering is voltooid.

* Schrijf **voor uitvoer, zoals status berichten of Score resultaten,** bestanden naar de map `./outputs`, zodat ze persistent zijn als artefacten in de uitvoerings geschiedenis. Mindful zijn van het aantal en de grootte van bestanden die naar deze map worden geschreven, omdat er een latentie kan optreden wanneer de inhoud wordt geüpload om de geschiedenis uit te voeren. Als latentie een probleem is, wordt het schrijven van bestanden naar een gegevens opslag aanbevolen.

* Als **u geschreven bestanden wilt opslaan als Logboeken in uitvoerings geschiedenis,** schrijft u bestanden naar `./logs` map. De logboeken worden in realtime geüpload, dus deze methode is geschikt voor het streamen van live-updates vanaf een externe uitvoering.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het verkrijgen van toegang tot gegevens uit uw gegevens opslag](how-to-access-data.md).

* Meer informatie over [het instellen van trainings doelen](how-to-set-up-training-targets.md).
