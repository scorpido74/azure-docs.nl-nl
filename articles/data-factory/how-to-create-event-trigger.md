---
title: Triggers op basis van gebeurtenissen maken in Azure Data Factory | Microsoft Docs
description: Meer informatie over het maken van een trigger in Azure Data Factory die een pijp lijn uitvoert in reactie op een gebeurtenis.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: a2c046d4375b891d17d545edd804d0fa1da6ee75
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142357"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Een trigger maken waarmee een pijp lijn wordt uitgevoerd als reactie op een gebeurtenis

In dit artikel worden de activerings methoden beschreven die u kunt maken in uw Data Factory-pijp lijnen.

Event-aangedreven architectuur (EDA) is een gemeen schappelijk gegevens integratie patroon dat betrekking heeft op productie, detectie, verbruik en reactie op gebeurtenissen. Voor scenario's voor gegevens integratie zijn vaak Data Factory-klanten vereist om pijp lijnen te activeren op basis van gebeurtenissen, zoals de ontvangst of verwijdering van een bestand in uw Azure Storage-account. Data Factory is nu geïntegreerd met [Azure Event grid](https://azure.microsoft.com/services/event-grid/), waarmee u pijp lijnen kunt activeren voor een gebeurtenis.

Bekijk de volgende video voor een inleiding en demonstratie van tien minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> De integratie die in dit artikel wordt beschreven, is afhankelijk van [Azure Event grid](https://azure.microsoft.com/services/event-grid/). Zorg ervoor dat uw abonnement is geregistreerd bij de resource provider Event Grid. Zie [resource providers en-typen](../azure-resource-manager/resource-manager-supported-services.md#azure-portal)voor meer informatie.

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory

In deze sectie wordt beschreven hoe u een gebeurtenis trigger maakt in de gebruikers interface van Azure Data Factory.

1. Ga naar het **ontwerp canvas**

2. Klik in de linkerbenedenhoek op de knop **Triggers**

3. Klik op **+ Nieuw** , waardoor de linkernavigatie trigger maken wordt geopend

4. **Gebeurtenis** trigger type selecteren

![Nieuwe gebeurtenis trigger maken](media/how-to-create-event-trigger/event-based-trigger-image1.png)

5. Selecteer uw opslag account in de vervolg keuzelijst van het Azure-abonnement of hand matig met de resource-ID van het opslag account. Kies op welke container de gebeurtenissen moeten worden uitgevoerd. Container selectie is optioneel, maar mindful het selecteren van alle containers kan leiden tot een groot aantal gebeurtenissen.

   > [!NOTE]
   > De gebeurtenis trigger ondersteunt momenteel alleen Azure Data Lake Storage Gen2 en algemene opslag accounts voor versie 2. Als gevolg van een Azure Event Grid beperking, ondersteunt Azure Data Factory slechts een maximum van 500 gebeurtenis triggers per opslag account.

6. Het **BLOB-pad begint met** en het **BLOB-pad eindigt** op Eigenschappen, zodat u de containers, mappen en BLOB-namen kunt opgeven waarvoor u gebeurtenissen wilt ontvangen. Voor uw gebeurtenis trigger moet ten minste één van deze eigenschappen worden gedefinieerd. U kunt verschillende patronen gebruiken voor beide **BLOB-paden begint met** en het **BLOB-pad eindigt met** eigenschappen, zoals wordt weer gegeven in de voor beelden verderop in dit artikel.

    * **Pad van BLOB begint met:** Het BLOB-pad moet beginnen met een mappad. Geldige waarden zijn `2018/` : `2018/april/shoes.csv`en. Dit veld kan niet worden geselecteerd als er geen container is geselecteerd.
    * **Pad naar BLOB eindigt op:** Het pad naar de BLOB moet eindigen met een bestands naam of-extensie. Geldige waarden zijn `shoes.csv` : `.csv`en. De naam van de container en map zijn optioneel, maar als u deze opgeeft, moeten `/blobs/` ze worden gescheiden door een segment. Een container met de naam ' orders ' kan bijvoorbeeld een waarde van `/orders/blobs/2018/april/shoes.csv`hebben. Als u een map in een container wilt opgeven, laat u het voorloop teken '/' weg. Er `april/shoes.csv` wordt bijvoorbeeld een gebeurtenis geactiveerd voor elk bestand in de `shoes.csv` map met de naam ' april ' in een wille keurige container. 

7. Selecteer of uw trigger reageert op een gebeurtenis **die** door een blob is gemaakt, een gebeurtenis voor het verwijderen van een **BLOB** of beide. In de opgegeven opslag locatie worden met elke gebeurtenis de Data Factory pijp lijnen geactiveerd die zijn gekoppeld aan de trigger.

    ![De gebeurtenis trigger configureren](media/how-to-create-event-trigger/event-based-trigger-image2.png)

8. Zodra u de trigger hebt geconfigureerd, klikt **u op volgende: Voor beeld**van gegevens. In dit scherm ziet u de bestaande blobs die overeenkomen met de configuratie van de gebeurtenis trigger. Zorg ervoor dat u specifieke filters hebt. Het configureren van filters die te breed zijn, kan overeenkomen met een groot aantal bestanden dat is gemaakt/verwijderd. Dit kan aanzienlijk invloed hebben op uw kosten. Klik op **volt ooien**als de filter voorwaarden zijn gecontroleerd.

    ![Voor beeld van gebeurtenis trigger gegevens](media/how-to-create-event-trigger/event-based-trigger-image3.png)

9. Als u een pijp lijn aan deze trigger wilt koppelen, gaat u naar het pijp lijn-canvas en klikt u op **trigger toevoegen** en selecteert u **Nieuw/bewerken**. Wanneer de side-Navigator wordt weer gegeven, klikt u op de vervolg keuzelijst **Trigger selecteren...** en selecteert u de trigger die u hebt gemaakt. Klik op **Next: Voor beeld** van gegevens om te bevestigen dat de configuratie juist is en controleer vervolgens of de voorbeeld gegevens correct zijn.

10. Als uw pijp lijn para meters heeft, kunt u deze opgeven op de activerings parameter zijde navigatie. De gebeurtenis trigger legt het mappad en de bestands naam van de BLOB vast in de eigenschappen `@triggerBody().folderPath` en `@triggerBody().fileName`. Als u de waarden van deze eigenschappen in een pijp lijn wilt gebruiken, moet u de eigenschappen toewijzen aan pijplijn parameters. Nadat u de eigenschappen hebt toegewezen aan para meters, kunt u toegang krijgen tot de waarden `@pipeline().parameters.parameterName` die zijn vastgelegd door de trigger via de expressie in de pijp lijn. Klik op **volt ooien** wanneer u klaar bent.

    ![Eigenschappen toewijzen aan pijplijn parameters](media/how-to-create-event-trigger/event-based-trigger-image4.png)

In het vorige voor beeld wordt de trigger zo geconfigureerd dat deze wordt geactiveerd wanneer een BLOB-pad dat eindigt op. csv wordt gemaakt in de map Event-test in de voorbeeld gegevens van de container. Met de eigenschappen **FolderPath** en **filename** wordt de locatie van de nieuwe BLOB vastgelegd. Als bijvoorbeeld MoviesDB. csv wordt toegevoegd aan het pad voor beeld/ `@triggerBody().folderPath` gebeurtenis testen, heeft de `sample-data/event-testing` waarde `moviesDB.csv`en `@triggerBody().fileName` de waarde. Deze waarden worden in het voor beeld toegewezen aan de pijplijn parameters `sourceFolder` en `sourceFile` kunnen worden gebruikt in `@pipeline().parameters.sourceFile` de hele pijp lijn als `@pipeline().parameters.sourceFolder` respectievelijk.

## <a name="json-schema"></a>JSON-schema

De volgende tabel bevat een overzicht van de schema-elementen die zijn gerelateerd aan triggers op basis van gebeurtenissen:

| **JSON-element** | **Beschrijving** | **Type** | **Toegestane waarden** | **Vereist** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **ligt** | De Azure Resource Manager Resource-ID van het opslag account. | Tekenreeks | Azure Resource Manager-ID | Ja |
| **evenementen** | Het type gebeurtenissen dat ervoor zorgt dat deze trigger wordt gestart. | Array    | Micro soft. storage. BlobCreated, micro soft. storage. BlobDeleted | Ja, een wille keurige combi natie van deze waarden. |
| **blobPathBeginsWith** | Het BLOB-pad moet beginnen met het patroon dat is ingesteld voor de trigger om te starten. De trigger wordt `/records/blobs/december/` bijvoorbeeld alleen geactiveerd voor blobs in de `december` map onder de `records` container. | Tekenreeks   | | U moet een waarde opgeven voor ten minste een van deze eigenschappen: `blobPathBeginsWith` of. `blobPathEndsWith` |
| **blobPathEndsWith** | Het BLOB-pad moet eindigen met het patroon dat is ingesteld voor de trigger om te starten. De trigger wordt `december/boxes.csv` bijvoorbeeld alleen geactiveerd voor blobs met de `boxes` naam in `december` een map. | Tekenreeks   | | U moet een waarde opgeven voor ten minste een van deze eigenschappen: `blobPathBeginsWith` of. `blobPathEndsWith` |

## <a name="examples-of-event-based-triggers"></a>Voor beelden van triggers op basis van gebeurtenissen

Deze sectie bevat voor beelden van activerings instellingen op basis van gebeurtenissen.

> [!IMPORTANT]
> U moet het `/blobs/` segment van het pad toevoegen, zoals wordt weer gegeven in de volgende voor beelden, wanneer u container en map, container, bestand, container, map en bestand opgeeft. Voor **blobPathBeginsWith**voegt de Data Factory gebruikers interface automatisch toe `/blobs/` tussen de map en de container naam in de JSON van de trigger.

| Eigenschap | Voorbeeld | Description |
|---|---|---|
| **Pad van BLOB begint met** | `/containername/` | Hiermee ontvangt u gebeurtenissen voor alle blobs in de container. |
| **Pad van BLOB begint met** | `/containername/blobs/foldername/` | Hiermee ontvangt u gebeurtenissen voor alle blobs `containername` in de `foldername` container en de map. |
| **Pad van BLOB begint met** | `/containername/blobs/foldername/subfoldername/` | U kunt ook verwijzen naar een submap. |
| **Pad van BLOB begint met** | `/containername/blobs/foldername/file.txt` | Hiermee ontvangt u gebeurtenissen voor een `file.txt` blob met `foldername` de naam in `containername` de map onder de container. |
| **BLOB eindigt op** | `file.txt` | Hiermee ontvangt u gebeurtenissen voor een `file.txt` blob met de naam in een wille keurig pad. |
| **BLOB eindigt op** | `/containername/blobs/file.txt` | Hiermee ontvangt u gebeurtenissen voor een `file.txt` blob met `containername`de naam onder container. |
| **BLOB eindigt op** | `foldername/file.txt` | Hiermee ontvangt u gebeurtenissen voor een `file.txt` BLOB `foldername` met de naam in de map onder een wille keurige container. |

## <a name="next-steps"></a>Volgende stappen
Zie [pijp lijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md#triggers)voor meer informatie over triggers.
