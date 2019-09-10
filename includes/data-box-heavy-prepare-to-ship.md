---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "66419571"
---
De laatste stap is de voorbereiding van het apparaat voor verzending. In deze stap worden alle shares van apparaten offline geplaatst. De shares zijn niet toegankelijk als u dit proces hebt gestart.

> [!IMPORTANT]
> Voorbereiding voor verzending is vereist omdat gegevens die niet aan de naamgevingsregels van Azure voldoen, worden gemarkeerd. Als u deze stap overslaat, kan dit leiden tot fouten bij het uploaden van gegevens vanwege gegevens die niet voldoen.

1. Ga naar **Voorbereiden voor verzending** en klik op **Voorbereiden starten**. Standaard worden er controlesommen berekend tijdens het kopiëren van gegevens. Deze berekening wordt afgerond tijdens de stap Voorbereiding voor verzending, waarna er een lijst met bestanden (ook wel *stuklijst* of manifest genoemd) wordt opgesteld. Het berekenen van de controlesom kan, afhankelijk van de grootte van uw gegevens, uren tot dagen duren.
   
    ![Voorbereiding voor verzending 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Als u om een bepaalde reden wilt stoppen met de voorbereiding van het apparaat, klikt u op **Voorbereiding stoppen**. U kunt de voorbereiding voor verzending later hervatten.
        
    ![Voorbereiding voor verzending 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. De voorbereiding voor verzending wordt gestart en de apparaat-shares gaan offline. Er wordt een herinnering om het verzendlabel te downloaden weergegeven zodra het apparaat klaar is.

    ![Herinnering voor downloaden van verzendlabel](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. De status van het apparaat wordt bijgewerkt naar *Gereed voor verzending* en het apparaat wordt vergrendeld nadat de voorbereiding van het apparaat is voltooid.
        
    ![Voorbereiding voor verzending 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Als u meer gegevens naar het apparaat wilt kopiëren, kunt u het apparaat ontgrendelen, meer gegevens kopiëren en de voorbereiding voor verzending opnieuw uitvoeren.

    Als deze stap fouten bevat, downloadt u het foutenlogboek en verhelpt u de fouten. Nadat de fouten zijn opgelost, voert u **Voorbereiding voor verzending** uit.

4. Nadat de stap Voorbereiding voor verzending is voltooid (zonder fouten), downloadt u de lijst met bestanden (ook wel bekend als het *manifest*) die in dit proces zijn gekopieerd. 

    ![Lijst met bestanden of stuklijst downloaden](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   U kunt deze lijst later gebruiken om de bestanden te verifiëren die naar Azure zijn geüpload. Zie [Stuklijst controleren tijdens voorbereiding voor verzending](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship) voor meer informatie.
        
    ![Voorbeeld van stuklijst](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Schakel het apparaat uit. Ga naar de pagina **Uitschakelen of opnieuw opstarten** en klik op **Afsluiten**. Klik op **OK** om verder te gaan als u om bevestiging wordt gevraagd.

    ![Eerste knooppunt van apparaat afsluiten](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Herhaal alle bovenstaande stappen voor het andere knooppunt van het apparaat.
7. Het apparaat volledig is volledig uitgeschakeld als alle led's op de achterkant van het apparaat niet meer branden. De volgende stap is het verwijderen van alle kabels en het apparaat verzenden naar Microsoft.
