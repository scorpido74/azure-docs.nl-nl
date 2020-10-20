---
title: bestand opnemen
description: bestand opnemen
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026324"
---
## <a name="transform-data-before-using-large-usage-files"></a>Gegevens transformeren voordat u grote gebruiksbestanden gebruikt

Soms is uw gebruiks- of afstemmingsbestand te groot om te openen. Het is ook mogelijk dat u slechts een deel van de gegevens nodig hebt om een probleem op te lossen. U heeft bijvoorbeeld alleen informatie over een bepaalde resource of alleen het verbruik voor enkele services of resourcegroepen nodig. U kunt de gegevens transformeren om deze samen te vatten voordat u draaitabellen maakt.

1. Open een lege werkmap in Excel.
1. Selecteer in het bovenste menu **Gegevens** > **Uit tekst/CSV**, selecteer uw gebruiksbestand en selecteer vervolgens **Importeren**.
1. Selecteer onder aan het venster **Gegevens transformeren**. In een nieuw venster wordt een samenvatting van de gegevens weergegeven.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="Voorbeeld van het weergeven van samengevatte gegevens" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. Als u een Microsoft-klantovereenkomst hebt, kunt u deze stap overslaan en doorgaan naar de volgende omdat MCA-gebruiksbestanden doorgaans de kolom titels in de eerste rijen hebben. Bereid de gegevens voor door de tabel te maken. Verwijder de bovenste rijen, zodat alleen de titels overblijven. Selecteer **Rijen verwijderen** > **Bovenste rijen verwijderen**.  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="Voorbeeld van het weergeven van samengevatte gegevens" :::
1. Voer in het venster Bovenste rijen verwijderen het aantal rijen in dat bovenaan moet worden verwijderd. Voor EA meestal 2, voor CSP meestal 1. Selecteer **OK**.
1. Selecteer **Eerste rij als headers gebruiken**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="Voorbeeld van het weergeven van samengevatte gegevens" :::
    
    In de tabelweergave worden de kolomtitels bovenaan weergegeven.
1. Voeg vervolgens een filter toe. Gebruik de selectiepijlen rechts van elke kolomtitel om te filteren. Voorgestelde filters zijn abonnements-id, servicenaam (metercategorie), exemplaar-id, resourcegroep. U kunt meerdere filters in hetzelfde document gebruiken. We raden u aan alle mogelijke filters toe te passen om de documentgrootte te verkleinen en het latere werk te vergemakkelijken.
1. Nadat u uw filters hebt toegepast, selecteert u **Sluiten & Laden**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="Voorbeeld van het weergeven van samengevatte gegevens" :::

Het bestand wordt geladen en er wordt een tabel met gefilterde gebruiksgegevens weergegeven. U kunt nu een nieuwe draaitabel maken om gebruiksproblemen op te lossen.