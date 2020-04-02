---
title: Gegevens importeren
titleSuffix: Azure Machine Learning
description: Meer informatie over het importeren van uw gegevens in Azure Machine Learning-ontwerper uit verschillende gegevensbronnen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: 1ad7677607d625f673546a6ea29ea58b80a8d1b5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546759"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>Uw gegevens importeren in Azure Machine Learning-ontwerper (voorbeeld)

In dit artikel leert u hoe u uw eigen gegevens in de ontwerper importeert om aangepaste oplossingen te maken. Er zijn twee manieren waarop u gegevens in de ontwerper importeren: 

* **Azure Machine Learning-gegevenssets** - [Gegevenssets](concept-data.md#datasets) registreren in Azure Machine Learning om geavanceerde functies in te schakelen waarmee u uw gegevens beheren.
* **Module Gegevens importeren** - Gebruik de module [Gegevens importeren](algorithm-module-reference/import-data.md) om rechtstreeks toegang te krijgen tot gegevens uit online gegevensbronnen.

## <a name="use-azure-machine-learning-datasets"></a>Azure Machine Learning-gegevenssets gebruiken

We raden u aan [gegevenssets](concept-data.md#datasets) te gebruiken om gegevens in de ontwerper te importeren. Wanneer u een gegevensset registreert, u optimaal profiteren van geavanceerde gegevensfuncties zoals [versiebeheer, tracking](how-to-version-track-datasets.md) en [gegevensbewaking.](how-to-monitor-datasets.md)

### <a name="register-a-dataset"></a>Een gegevensset registreren

U bestaande gegevenssets [programmatisch](how-to-create-register-datasets.md#use-the-sdk) registreren bij de SDK of [visueel in Azure Machine Learning-studio.](how-to-create-register-datasets.md#use-the-ui)

U de uitvoer ook registreren voor elke designermodule als gegevensset.

1. Selecteer de module waarmee de gegevens worden uitgevoerd die u wilt registreren.

1. Selecteer in het deelvenster Eigenschappen de**gegevensset** **Uitvoerregister** > .

    ![Schermafbeelding van hoe u naar de optie Gegevensset registreren navigeren](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Een gegevensset gebruiken

Uw geregistreerde gegevenssets zijn te vinden in het modulepalet onder **Gegevenssets** > **Mijn gegevenssets**. Als u een gegevensset wilt gebruiken, sleept u deze en zet u deze op het pijplijncanvas. Sluit vervolgens de uitvoerpoort van de gegevensset aan op andere modules in het palet.

![Schermafbeelding van de locatie van opgeslagen gegevenssets in het ontwerppalet](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> De ontwerper ondersteunt momenteel alleen verwerking [tabelgegevenssets](how-to-create-register-datasets.md#dataset-types). Als u [bestandsgegevenssets](how-to-create-register-datasets.md#dataset-types)wilt gebruiken, gebruikt u de Azure Machine Learning SDK die beschikbaar is voor Python en R.

## <a name="import-data-using-the-import-data-module"></a>Gegevens importeren met de module Gegevens importeren

Hoewel we u aanraden gegevenssets te gebruiken om gegevens te importeren, u ook de module [Gegevens importeren](algorithm-module-reference/import-data.md) gebruiken. De module Gegevens importeren slaat het registreren van uw gegevensset in Azure Machine Learning over en importeert gegevens rechtstreeks vanuit een [gegevensarchief](concept-data.md#datastores) of HTTP-URL.

Zie de [referentiepagina Gegevens importeren](algorithm-module-reference/import-data.md)voor meer informatie over het gebruik van de module Gegevens importeren.

> [!NOTE]
> Als uw gegevensset te veel kolommen bevat, u de volgende fout tegenkomen: Validatie is mislukt vanwege groottebeperking. Om dit te voorkomen, [registreert u de gegevensset in de interface Gegevenssets](how-to-create-register-datasets.md#use-the-ui).

## <a name="supported-sources"></a>Ondersteunde bronnen

In deze sectie worden de gegevensbronnen weergegeven die door de ontwerper worden ondersteund. Gegevens komen in de ontwerper van een datastore of uit [tabeltabelgegevensset.](how-to-create-register-datasets.md#dataset-types)

### <a name="datastore-sources"></a>Datastore-bronnen
Zie [Access-gegevens in Azure-opslagservices](how-to-access-data.md#supported-data-storage-service-types)voor een lijst met ondersteunde gegevensarchiefbronnen.

### <a name="tabular-dataset-sources"></a>Tabelgegevenssetbronnen

De ontwerper ondersteunt tabelgegevenssets die zijn gemaakt op basis van de volgende bronnen:
 * Afgebakende bestanden
 * JSON-bestanden
 * Parquet-bestanden
 * SQL-query's

## <a name="data-types"></a>Gegevenstypen

De ontwerper herkent intern de volgende gegevenstypen:

* Tekenreeks
* Geheel getal
* Decimal
* Booleaans
* Date

De ontwerper gebruikt een intern gegevenstype om gegevens tussen modules door te geven. U uw gegevens expliciet omzetten in gegevenstabelindeling met de module [Converteren naar gegevensset.](algorithm-module-reference/convert-to-dataset.md) Elke module die andere indelingen dan de interne indeling accepteert, converteert de gegevens in stilte voordat deze wordt doorgestoofd naar de volgende module.

## <a name="data-constraints"></a>Gegevensbeperkingen

Modules in de ontwerper worden beperkt door de grootte van het rekendoel. Voor grotere gegevenssets moet u een grotere Azure Machine Learning-compute resource gebruiken. Zie [Wat zijn rekendoelen in Azure Machine Learning voor](concept-compute-target.md#azure-machine-learning-compute-managed) meer informatie over Azure Machine Learning compute?

## <a name="next-steps"></a>Volgende stappen

Leer de basisprincipes van de ontwerper met [tutorial: voorspel autoprijs met de ontwerper.](tutorial-designer-automobile-price-train-score.md)
