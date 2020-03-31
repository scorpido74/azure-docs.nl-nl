---
title: Triggers op basis van gebeurtenissen maken in Azure Data Factory
description: Meer informatie over het maken van een trigger in Azure Data Factory die een pijplijn uitvoert als reactie op een gebeurtenis.
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
ms.openlocfilehash: e4301afafb48fb9a1b0c9e36dde9800e2b8390f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443922"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Een trigger maken waarop een pijplijn wordt uitgevoerd als reactie op een gebeurtenis

In dit artikel worden de op gebeurtenissen gebaseerde triggers beschreven die u maken in uw Data Factory-pijplijnen.

Event-driven architecture (EDA) is een veelvoorkomend gegevensintegratiepatroon dat productie, detectie, verbruik en reactie op gebeurtenissen omvat. Gegevensintegratiescenario's vereisen vaak dat klanten van Data Factory pijplijnen activeren op basis van gebeurtenissen zoals het binnenhalen of verwijderen van een bestand in uw Azure Storage-account. Data Factory is nu geïntegreerd met [Azure Event Grid,](https://azure.microsoft.com/services/event-grid/)waarmee u pijplijnen activeren op een gebeurtenis.

Voor een tien minuten durende introductie en demonstratie van deze functie, bekijk de volgende video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> De integratie die in dit artikel wordt beschreven, is afhankelijk van [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Controleer of uw abonnement is geregistreerd bij de resourceprovider Event Grid. Zie [Resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)voor meer informatie.

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory

In deze sectie ziet u hoe u een gebeurtenistrigger maakt binnen de gebruikersinterface van Azure Data Factory.

1. Naar **het ontwerpcanvas gaan**

1. Klik in de linkerbenedenhoek op de knop **Triggers**

1. Klik **op + Nieuw** die de triggerside-navigatie van de trigger maakt opent

1. Gebeurtenis voor triggertype **selecteren**

    ![Nieuwe gebeurtenistrigger maken](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Selecteer uw opslagaccount in de vervolgkeuzelijst voor Azure-abonnementen of handmatig met de bron-id voor opslagaccount. Kies op welke container u de gebeurtenissen wilt laten plaatsvinden. Containerselectie is optioneel, maar houd er rekening mee dat het selecteren van alle containers kan leiden tot een groot aantal gebeurtenissen.

   > [!NOTE]
   > De gebeurtenistrigger ondersteunt momenteel alleen Azure Data Lake Storage Gen2- en Algemene versie 2-opslagaccounts. Vanwege een Azure Event Grid-beperking ondersteunt Azure Data Factory slechts maximaal 500 gebeurtenistriggers per opslagaccount.

1. Met het **Blob-pad begint en** **blobpad eindigt met** eigenschappen, u de containers, mappen en blobnamen opgeven waarvoor u gebeurtenissen wilt ontvangen. Voor de trigger van uw gebeurtenis moet ten minste één van deze eigenschappen worden gedefinieerd. U verschillende patronen gebruiken voor beide **Blob-paden begint met** en **Blob pad eindigt met** eigenschappen, zoals weergegeven in de voorbeelden later in dit artikel.

    * **Blobpad begint met:** Het blobpad moet beginnen met een mappad. Geldige waarden `2018/` `2018/april/shoes.csv`zijn o.a. Dit veld kan niet worden geselecteerd als een container niet is geselecteerd.
    * **Blobpad eindigt met:** Het blobpad moet eindigen met een bestandsnaam of extensie. Geldige waarden `shoes.csv` `.csv`zijn o.a. De naam van de container en de map is `/blobs/` optioneel, maar wanneer deze zijn opgegeven, moeten ze worden gescheiden door een segment. Een container met de naam 'orders' `/orders/blobs/2018/april/shoes.csv`kan bijvoorbeeld een waarde hebben van . Als u een map in een container wilt opgeven, laat u het hoofdteken '/' weg. Als `april/shoes.csv` u bijvoorbeeld een gebeurtenis activeert op een bestand dat in map een zogenaamde 'april' in een container wordt genoemd. `shoes.csv` 

1. Selecteer of de trigger reageert op een **blob-gebeurtenis,** **blob-verwijderde** gebeurtenis of beide. Op uw opgegeven opslaglocatie wordt bij elke gebeurtenis de pijplijnen van Gegevensfabriek geactiveerd die aan de trigger zijn gekoppeld.

    ![De gebeurtenistrigger configureren](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Selecteer of de trigger blobs met nul bytes negeert.

1. Zodra u de trigger hebt geconfigureerd, klikt u op **Volgende: Voorbeeld van gegevens**. In dit scherm worden de bestaande blobs weergegeven die overeenkomen met de configuratie van de gebeurtenistrigger. Zorg ervoor dat je specifieke filters hebt. Het configureren van te brede filters kan overeenkomen met een groot aantal bestanden die zijn gemaakt/verwijderd en kan een aanzienlijke impact hebben op uw kosten. Zodra de filtervoorwaarden zijn geverifieerd, klikt u op **Voltooien**.

    ![Voorbeeld van gebeurtenistriggergegevens](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Als u een pijplijn aan deze trigger wilt koppelen, gaat u naar het pijplijncanvas en klikt u op **Trigger toevoegen** en selecteert u **Nieuw/Bewerken**. Wanneer de zijnavigatie wordt weergegeven, klikt u op de **vervolgkeuzelijst Trigger kiezen...** en selecteert u de trigger die u hebt gemaakt. Klik op **Volgende: Gegevensvoorbeeld** om te bevestigen dat de configuratie correct is en **vervolgens is vervolgens de** voorbeeldvan gegevens correct.

1. Als uw pijplijn parameters heeft, u deze opgeven op de navigatie aan de parameterzijde van de trigger. De gebeurtenistrigger legt het mappad en de `@triggerBody().folderPath` bestandsnaam van de blob vast in de eigenschappen en `@triggerBody().fileName`. Als u de waarden van deze eigenschappen in een pijplijn wilt gebruiken, moet u de eigenschappen toewijzen aan pijplijnparameters. Nadat u de eigenschappen aan parameters hebt in kaart `@pipeline().parameters.parameterName` gebracht, hebt u toegang tot de waarden die door de trigger zijn vastgelegd via de expressie in de pijplijn. Klik **op Voltooien** zodra u klaar bent.

    ![Toewijzingseigenschappen aan pijplijnparameters](media/how-to-create-event-trigger/event-based-trigger-image4.png)

In het voorgaande voorbeeld wordt de trigger geconfigureerd om te vuren wanneer een blobpad dat eindigt in .csv wordt gemaakt in de mapgebeurtenistests in de steekproefgegevens van de container. Met de eigenschappen **mapPath** en **fileName** wordt de locatie van de nieuwe blob vastgelegd. Wanneer MoviesDB.csv bijvoorbeeld wordt toegevoegd aan het `@triggerBody().folderPath` pad sample-data/event-testing, een waarde heeft van `sample-data/event-testing` en `@triggerBody().fileName` een waarde heeft van `moviesDB.csv`. Deze waarden worden in het voorbeeld `sourceFolder` toegewezen `sourceFile` aan de pijplijnparameters `@pipeline().parameters.sourceFolder` en `@pipeline().parameters.sourceFile` die in de hele pijplijn kunnen worden gebruikt als en naar gelang van het jaar.

## <a name="json-schema"></a>JSON-schema

In de volgende tabel vindt u een overzicht van de schema-elementen die gerelateerd zijn aan gebeurtenistriggers:

| **JSON-element** | **Beschrijving** | **Type** | **Toegestane waarden** | **Vereist** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Scope** | De Azure Resource Manager-bron-id van het opslagaccount. | Tekenreeks | Azure Resource Manager-id | Ja |
| **Gebeurtenissen** | Het soort gebeurtenissen waardoor deze trigger wordt geactiveerd. | Matrix    | Microsoft.Storage.BlobGemaakt, Microsoft.Storage.BlobVerwijderd | Ja, elke combinatie van deze waarden. |
| **blobPathBeginsWith** | Het blobpad moet beginnen met het patroon dat is opgegeven om de trigger te kunnen afvuren. Vuurt `/records/blobs/december/` bijvoorbeeld alleen de trigger af `december` voor `records` blobs in de map onder de container. | Tekenreeks   | | U moet een waarde voor ten minste `blobPathBeginsWith` een `blobPathEndsWith`van deze eigenschappen: of . |
| **blobPathEndsWith** | Het blobpad moet eindigen met het patroon dat is opgegeven om de trigger te kunnen afvuren. Vuurt `december/boxes.csv` bijvoorbeeld alleen de trigger `boxes` af `december` voor blobs die in een map zijn genoemd. | Tekenreeks   | | U moet een waarde voor ten minste `blobPathBeginsWith` een `blobPathEndsWith`van deze eigenschappen: of . |
| **negerenEmptyBlobs** | Of zero-byte blobs een pijplijnrun activeren. Standaard is dit ingesteld op true. | Booleaans | waar of onwaar | Nee |

## <a name="examples-of-event-based-triggers"></a>Voorbeelden van op gebeurtenissen gebaseerde triggers

In deze sectie vindt u voorbeelden van op gebeurtenissen gebaseerde triggerinstellingen.

> [!IMPORTANT]
> U moet het `/blobs/` segment van het pad opnemen, zoals in de volgende voorbeelden wordt weergegeven wanneer u container en map, container en bestand of container, map en bestand opgeeft. Voor **blobPathBeginsWith**voegt de gebruikersinterface van `/blobs/` de gegevensfabriek automatisch toe tussen de map en de containernaam in de trigger JSON.

| Eigenschap | Voorbeeld | Beschrijving |
|---|---|---|
| **Blobpad begint met** | `/containername/` | Ontvangt gebeurtenissen voor elke blob in de container. |
| **Blobpad begint met** | `/containername/blobs/foldername/` | Ontvangt gebeurtenissen voor blobs `containername` in `foldername` de container en map. |
| **Blobpad begint met** | `/containername/blobs/foldername/subfoldername/` | U ook verwijzen naar een submap. |
| **Blobpad begint met** | `/containername/blobs/foldername/file.txt` | Ontvangt gebeurtenissen voor `file.txt` een `foldername` blob die `containername` is vernoemd in de map onder de container. |
| **Blobpad eindigt met** | `file.txt` | Ontvangt gebeurtenissen voor `file.txt` een blob die in een willekeurig pad is genoemd. |
| **Blobpad eindigt met** | `/containername/blobs/file.txt` | Ontvangt gebeurtenissen voor `file.txt` een `containername`blob met de naam onder container . |
| **Blobpad eindigt met** | `foldername/file.txt` | Ontvangt gebeurtenissen voor `file.txt` een `foldername` blob die is vernoemd in map onder een container. |

## <a name="next-steps"></a>Volgende stappen
Zie [Pijplijnuitvoering en triggers](concepts-pipeline-execution-triggers.md#triggers)voor gedetailleerde informatie over triggers.
