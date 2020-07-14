---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/01/2020
ms.author: alkohli
ms.openlocfilehash: d099e33e7b35381f5404c9f8964d3ea90d4f3908
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959453"
---
De laatste stap is de voorbereiding van het apparaat voor verzending. In deze stap worden alle shares van apparaten offline geplaatst. De shares zijn niet toegankelijk als u dit proces hebt gestart.

> [!IMPORTANT]
> Voorbereiding voor verzending is vereist omdat gegevens die niet aan de naamgevingsregels van Azure voldoen, worden gemarkeerd. Deze stap voorkomt mogelijke fouten bij het uploaden van gegevens vanwege gegevens die niet voldoen.

1. Ga naar **Voorbereiden voor verzending** en klik op **Voorbereiden starten**. Standaard worden er controlesommen berekend tijdens het kopiëren van gegevens. Deze berekening wordt afgerond tijdens de stap Voorbereiding voor verzending, waarna er een lijst met bestanden (een *stuklijst*) wordt opgesteld. Het berekenen van de controlesom kan, afhankelijk van de grootte van uw gegevens, uren tot dagen duren. 
   
    ![Voorbereiding voor verzending 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Als u om een bepaalde reden wilt stoppen met de voorbereiding van het apparaat, klikt u op **Voorbereiding stoppen**. U kunt de voorbereiding voor verzending later hervatten.
        
    ![Voorbereiding voor verzending 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. De voorbereiding voor verzending wordt gestart en de apparaat-shares gaan offline. <!--You see a reminder to download the shipping label once the device is ready.--> De status van het apparaat wordt bijgewerkt naar *Gereed voor verzending* en het apparaat wordt vergrendeld nadat de voorbereiding van het apparaat is voltooid.
        
    ![Voorbereiding voor verzending 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Als u meer gegevens naar het apparaat wilt kopiëren, kunt u het apparaat ontgrendelen, meer gegevens kopiëren en de voorbereiding voor verzending opnieuw uitvoeren.

    Als deze stap fouten bevat, wordt de status bijgewerkt naar *Scan voltooid met fouten*. Ontgrendel het apparaat en ga naar de pagina **Verbinding maken en kopiëren**, download de lijst met problemen en los de fouten op.

    ![Voorbereiding voor verzending 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

    Nadat de fouten zijn opgelost, voert u **Voorbereiding voor verzending** uit.

4. Nadat de voorbereiding voor verzending is voltooid (zonder fouten), voert u de volgende stappen uit:

    1. Noteer het referentienummer van de voltooiing. Afhankelijk van het land waar u zich bevindt, is dit nummer mogelijk vereist voor verschillende bewerkingen.
    2. Download de lijst met bestanden (ook wel het manifest genoemd) die in dit proces werden gekopieerd. U kunt deze lijst later gebruiken om de bestanden te verifiëren die naar Azure zijn geüpload. Zie [Stuklijst controleren tijdens voorbereiding voor verzending](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship) voor meer informatie.
        
        ![Voorbereiding voor verzending 1](media/data-box-prepare-to-ship/prepare-to-ship5.png)
    3. Selecteer en download de verzendinstructies voor het apparaat. De verzendinstructies verschillen afhankelijk van het land waarin u zich bevindt.
    4. Als het verzendlabel niet wordt weer gegeven in de E-ink, kunt u het label voor omgekeerde verzending hier downloaden. 

5. Schakel het apparaat uit. Ga naar de pagina **Uitschakelen of opnieuw opstarten** en klik op **Afsluiten**. Klik op **OK** om verder te gaan als u om bevestiging wordt gevraagd.

6. Verwijder de kabels. U moet het apparaat vervolgens naar Microsoft verzenden.
