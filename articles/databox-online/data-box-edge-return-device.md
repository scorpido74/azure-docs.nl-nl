---
title: Uw Azure Data Box Edge-apparaat retourneren of vervangen | Microsoft Documenten
description: Beschrijft hoe u het Azure Data Box Edge-apparaat retourneren of vervangen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: 521277b2eed7edfba016f6a80e8f877decfb0ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651097"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Uw Azure Data Box Edge-apparaat retourneren of vervangen

In dit artikel wordt beschreven hoe u de gegevens wissen en vervolgens uw Azure Data Box Edge-apparaat retourneren. Nadat u het apparaat hebt geretourneerd, u ook de bron verwijderen die aan het apparaat is gekoppeld of een vervangend apparaat bestellen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * De gegevens van de gegevensschijven op het apparaat wissen
> * Open een Support-ticket om uw apparaat terug te sturen
> * Pak het apparaat in en plan een pick-up
> * De bron in Azure-portal verwijderen
> * Een vervangend apparaat zoeken

## <a name="erase-data-from-the-device"></a>Gegevens van het apparaat wissen

Als u de gegevens van de gegevensschijven van uw apparaat wilt wissen, moet u uw apparaat opnieuw instellen. U uw apparaat opnieuw instellen via de lokale web-gebruikersinterface of de PowerShell-interface.

Maak indien nodig een kopie van de lokale gegevens op het apparaat voordat u opnieuw instellen een kopie van de lokale gegevens op het apparaat. U de gegevens van het apparaat kopiëren naar een Azure Storage-container.

Als u uw apparaat opnieuw wilt instellen via de lokale webgebruikersinterface, neemt u de volgende stappen.

1. Ga in de lokale webgebruikersinterface naar **Onderhoud > Apparaat resetten**.
2. Selecteer **Apparaat opnieuw instellen**.

    ![Apparaat opnieuw instellen](media/data-box-edge-return-device/device-reset-1.png)

3. Wanneer u om bevestiging wordt gevraagd, controleert u de waarschuwing en selecteert **u Ja** om door te gaan.

    ![Reset bevestigen](media/data-box-edge-return-device/device-reset-2.png)  

Met de reset worden de gegevens van de apparaatgegevensschijven gewist. Afhankelijk van de hoeveelheid gegevens op uw apparaat duurt dit proces ongeveer 30-40 minuten.

U ook verbinding maken met de PowerShell-interface van het apparaat en de `Reset-HcsAppliance` cmdlet gebruiken om de gegevens van de gegevensschijven te wissen. Zie [Uw apparaat opnieuw instellen](data-box-edge-connect-powershell-interface.md#reset-your-device)voor meer informatie.

> [!NOTE]
> - Als u een nieuw apparaat inwisselt of upgradet, raden we u aan uw apparaat pas opnieuw in te stellen nadat u het nieuwe apparaat hebt ontvangen.
> - De reset van het apparaat verwijdert alleen alle lokale gegevens van het apparaat. De gegevens in de cloud worden niet verwijderd en worden [kosten in rekening gebracht.](https://azure.microsoft.com/pricing/details/storage/) Deze gegevens moeten afzonderlijk worden verwijderd met behulp van een hulpprogramma voor cloudopslagbeheer zoals [Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

## <a name="open-a-support-ticket"></a>Een ondersteuningsticket openen

Als u het retourproces wilt starten, neemt u de volgende stappen.

1. Open een ondersteuningsticket met Microsoft Support waaruit blijkt dat u het apparaat wilt retourneren. Selecteer het probleemtype als **Data Box Edge Hardware**.

    ![Ondersteuningsticket openen](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Een Microsoft Support-technicus neemt contact met u op. Geef de verzendgegevens op.
3. Als u een retourverzenddoos nodig hebt, u deze aanvragen. Antwoord **ja** op de vraag **Noodzaak een leeg vak om terug te keren**.


## <a name="schedule-a-pickup"></a>Een pick-up plannen

1. Schakel het apparaat uit. Ga in de lokale webgebruikersinterface naar **De instellingen voor onderhoud > energie**.
2. Selecteer **Afsluiten**. Klik op **Ja** om door te gaan wanneer u om bevestiging wordt gevraagd. Zie [Macht beheren](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)voor meer informatie .
3. Haal de stekker uit de stroomkabels en verwijder alle netwerkkabels van het apparaat.
4. Bereid het verzendpakket voor met uw eigen doos of de lege doos die u van Azure hebt ontvangen. Plaats het apparaat en de stroomkabels die met het apparaat in de doos zijn verzonden.
5. Breng het verzendlabel aan dat u van Azure hebt ontvangen op het pakket.
6. Maak een afspraak met een transportbedrijf voor het ophalen van de zending. Als u het apparaat in de VS retourt, kan uw provider UPS of FedEx zijn. Een pick-up plannen met UPS:

    1. Bel de lokale UPS (land-specifieke gratis nummer).
    2. Vermeld in uw gesprek het volgnummer voor omgekeerde verzending zoals weergegeven op uw afgedrukte label.
    3. Als het volgnummer niet wordt geciteerd, moet u van UPS extra kosten betalen tijdens het ophalen.

    In plaats van de pick-up te plannen, u de Data Box Edge ook bij de dichtstbijzijnde inleverlocatie afleveren.

## <a name="delete-the-resource"></a>De bron verwijderen

Nadat het apparaat is ontvangen in het Azure-datacenter, wordt het apparaat gecontroleerd op schade of tekenen van manipulatie.

- Als het apparaat intact en in goede staat aankomt, stopt de factureringsmeter voor die resource. Microsoft Support neemt contact met u op om te bevestigen dat het apparaat is geretourneerd. Vervolgens u de resource verwijderen die is gekoppeld aan het apparaat in de Azure-portal.
- Als het apparaat aanzienlijk beschadigd aankomt, kunnen boetes van toepassing zijn. Zie voor meer informatie de [veelgestelde vragen over verloren of beschadigde apparaten](https://azure.microsoft.com/pricing/details/databox/edge/) en [productvoorwaarden.](https://www.microsoft.com/licensing/product-licensing/products)  


U het apparaat verwijderen in de Azure-portal:
-   Nadat u de bestelling hebt geplaatst en voordat het apparaat door Microsoft is voorbereid.
-   Nadat u het apparaat hebt teruggebracht naar Microsoft, wordt de fysieke inspectie in het Azure-datacenter doorstaan en wordt Microsoft Support gebeld om te bevestigen dat het apparaat is geretourneerd.

Als u het apparaat hebt geactiveerd tegen een ander abonnement of locatie, verplaatst Microsoft uw bestelling binnen één werkdag naar het nieuwe abonnement of de nieuwe locatie. Nadat de order is verplaatst, u deze bron verwijderen.


Volg de volgende stappen om het apparaat en de bron in azure-portal te verwijderen.

1. Ga in de Azure-portal naar uw resource en vervolgens naar **Overzicht.** Selecteer **Verwijderen**op de opdrachtbalk .

    ![Verwijderen selecteren](media/data-box-edge-return-device/delete-resource-1.png)

2. Typ in het **apparaatblad verwijderen** de naam van het apparaat dat u wilt verwijderen en selecteer **Verwijderen**.

    ![De verwijdering bevestigen](media/data-box-edge-return-device/delete-resource-2.png)

U wordt hiervan op de hoogte gesteld nadat het apparaat is verwijderd en de bijbehorende bron is verwijderd.

## <a name="get-a-replacement-device"></a>Een vervangend apparaat zoeken

Een vervangend apparaat is nodig wanneer het bestaande apparaat een hardwarestoring heeft of een upgrade nodig heeft. Volg de volgende stappen wanneer uw apparaat een hardwareprobleem heeft:

1. [Open een Support-ticket voor hardwareprobleem.](#open-a-support-ticket) Microsoft Support bepaalt dat een Field Replacement Unit (FRU) niet beschikbaar is voor dit exemplaar of dat het apparaat een hardware-upgrade nodig heeft. In beide gevallen bestelt Ondersteuning een vervangend apparaat.
2. [Maak een nieuwe bron](data-box-edge-deploy-prep.md#create-a-new-resource) voor het vervangende apparaat. Schakel het selectievakje in tegen **Ik heb een Data Box Edge-apparaat**. 
3. Nadat u een vervangend apparaat hebt ontvangen, [installeert](data-box-edge-deploy-install.md) en [activeert u](data-box-edge-deploy-connect-setup-activate.md) het vervangende apparaat tegen de nieuwe bron.
4. Volg alle stappen om het oorspronkelijke apparaat terug te sturen:
    1. Open een ander ticket om het oorspronkelijke apparaat terug te sturen.
    2. [Wist de gegevens op het apparaat](#erase-data-from-the-device).
    3. [Plan een pick-up.](#schedule-a-pickup)
    5. [Verwijder de resource](#delete-the-resource) die is gekoppeld aan het geretourneerde apparaat.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Bandbreedte beheren](data-box-edge-manage-bandwidth-schedules.md).
