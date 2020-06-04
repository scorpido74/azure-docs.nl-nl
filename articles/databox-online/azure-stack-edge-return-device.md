---
title: Uw Azure Stack edge-apparaat retour neren of vervangen | Microsoft Docs
description: Hierin wordt beschreven hoe u het Azure Stack edge-apparaat retourneert of vervangt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: 6e0581912a619bec11be6d322e2987a4498c5170
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84339362"
---
# <a name="return-or-replace-your-azure-stack-edge-device"></a>Uw Azure Stack edge-apparaat retour neren of vervangen

In dit artikel wordt beschreven hoe u de gegevens wist en vervolgens uw Azure Stack edge-apparaat retourneert. Nadat u het apparaat hebt geretourneerd, kunt u ook de resource die aan het apparaat is gekoppeld, verwijderen of een vervangend apparaat best Ellen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * De gegevens van de gegevens schijven op het apparaat wissen
> * Een ondersteunings ticket openen om uw apparaat te retour neren
> * Het apparaat inpakken en een ophaling plannen
> * De resource in Azure Portal verwijderen
> * Een vervangend apparaat ophalen

## <a name="erase-data-from-the-device"></a>Gegevens wissen van het apparaat

Als u de gegevens van de gegevens schijven van uw apparaat wilt wissen, moet u uw apparaat opnieuw instellen. U kunt uw apparaat opnieuw instellen met behulp van de lokale webgebruikersinterface of de Power shell-interface.

Voordat u opnieuw instelt, maakt u indien nodig een kopie van de lokale gegevens op het apparaat. U kunt de gegevens van het apparaat naar een Azure Storage-container kopiëren.

Voer de volgende stappen uit om uw apparaat opnieuw in te stellen met behulp van de lokale web-UI.

1. Ga in de lokale web-UI naar **onderhoud > apparaat opnieuw instellen**.
2. Selecteer **apparaat opnieuw instellen**.

    ![Apparaat opnieuw instellen](media/azure-stack-edge-return-device/device-reset-1.png)

3. Als u om bevestiging wordt gevraagd, controleert u de waarschuwing en selecteert u **Ja** om door te gaan.

    ![Opnieuw instellen bevestigen](media/azure-stack-edge-return-device/device-reset-2.png)  

Met de reset worden de gegevens van de gegevens schijven van apparaten gewist. Afhankelijk van de hoeveelheid gegevens op uw apparaat, neemt dit proces ongeveer 30-40 minuten in beslag.

U kunt ook verbinding maken met de Power shell-interface van het apparaat en de `Reset-HcsAppliance` cmdlet gebruiken om de gegevens van de gegevens schijven te wissen. Zie [uw apparaat opnieuw instellen](azure-stack-edge-connect-powershell-interface.md#reset-your-device)voor meer informatie.

> [!NOTE]
> - Als u uitwisselt of een upgrade naar een nieuw apparaat uitvoert, raden wij u aan uw apparaat opnieuw in te stellen nadat u het nieuwe apparaat hebt ontvangen.
> - Bij het opnieuw instellen van het apparaat worden alleen alle lokale gegevens van het apparaat verwijderd. De gegevens die zich in de Cloud bevindt, worden niet verwijderd en er worden [kosten](https://azure.microsoft.com/pricing/details/storage/)in rekening gebracht. Deze gegevens moeten afzonderlijk worden verwijderd met behulp van een beheer programma voor Cloud opslag, zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Een ondersteunings ticket openen

Voer de volgende stappen uit om het retour proces te starten.

1. Open een ondersteunings ticket met Microsoft Ondersteuning dat aangeeft dat u het apparaat wilt retour neren. Selecteer het probleem type als **Azure stack edge-hardware**.

    ![Ondersteuningsticket openen](media/azure-stack-edge-return-device/open-support-ticket-1.png)  

2. Een Microsoft Ondersteuning-Engineer neemt contact met u op. Geef de verzend gegevens op.
3. Als u een verzend doos nodig hebt, kunt u deze aanvragen. Antwoord **Ja** op de vraag **moet een leeg vak zijn om te retour neren**.


## <a name="schedule-a-pickup"></a>Een ophaling plannen

1. Schakel het apparaat uit. Ga in de lokale web-UI naar **onderhoud > energie-instellingen**.
2. Selecteer **Afsluiten**. Wanneer u om bevestiging wordt gevraagd, klikt u op **Ja** om door te gaan. Zie [energie beheren](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)voor meer informatie.
3. Koppel de stroom kabels los en verwijder alle netwerk kabels van het apparaat.
4. Bereid het verzend pakket voor met behulp van uw eigen box of het lege vak dat u van Azure hebt ontvangen. Plaats het apparaat en de stroom snoeren die met het apparaat in het vak zijn geleverd.
5. Breng het verzend label op dat u hebt ontvangen van Azure op het pakket.
6. Maak een afspraak met een transportbedrijf voor het ophalen van de zending. Als u het apparaat in ons retourneert, kan uw provider UPS of FedEx zijn. Een ophaling plannen met UPS:

    1. Bel met UPS in de buurt (gratis land-/regiospecifiek nummer).
    2. Neem in uw gesprek het tracerings nummer van omgekeerde verzen ding op zoals op het afgedrukte label wordt weer gegeven.
    3. Als het tracking nummer niet wordt vermeld, moet u een extra kosten betalen tijdens het ophalen.

    In plaats van het ophalen te plannen, kunt u ook de Azure Stack rand op de dichtstbijzijnde neerzet locatie neerzetten.

## <a name="delete-the-resource"></a>De resource verwijderen

Nadat het apparaat is ontvangen in het Azure-Data Center, wordt het apparaat geïnspecteerd op beschadiging of op een geknoeid tijdstip.

- Als het apparaat intact en goed inkomt, wordt de facturerings meter voor die resource gestopt. Microsoft Ondersteuning neemt dan contact met u op om te bevestigen dat het apparaat is geretourneerd. U kunt vervolgens de resource die aan het apparaat is gekoppeld, verwijderen in de Azure Portal.
- Als het apparaat aanzienlijk is aangekomen, kunnen de verfijningen van toepassing zijn. Raadpleeg de [Veelgestelde vragen op verloren of beschadigd apparaat](https://azure.microsoft.com/pricing/details/databox/edge/) en [product service voorwaarden](https://www.microsoft.com/licensing/product-licensing/products)voor meer informatie.  


U kunt het apparaat verwijderen in de Azure Portal:
-    Nadat u de order hebt geplaatst en voordat het apparaat door micro soft wordt voor bereid.
-    Nadat u het apparaat hebt geretourneerd naar micro soft, wordt de fysieke inspectie op het Azure-Data Center door gegeven en worden Microsoft Ondersteuning-aanroepen om te bevestigen dat het apparaat is geretourneerd.

Als u het apparaat hebt geactiveerd op basis van een ander abonnement of een andere locatie, zal micro soft uw bestelling binnen één werkdag naar het nieuwe abonnement of deze locatie verplaatsen. Nadat de order is verplaatst, kunt u deze resource verwijderen.


Voer de volgende stappen uit om het apparaat en de resource in Azure Portal te verwijderen.

1. Ga in het Azure Portal naar uw resource en klik vervolgens op **overzicht**. Selecteer **verwijderen**op de opdracht balk.

    ![Selecteer verwijderen](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Typ op de Blade **apparaat verwijderen** de naam van het apparaat dat u wilt verwijderen en selecteer **verwijderen**.

    ![De verwijdering bevestigen](media/azure-stack-edge-return-device/delete-resource-2.png)

U wordt gewaarschuwd wanneer het apparaat en de bijbehorende resource zijn verwijderd.

## <a name="get-a-replacement-device"></a>Een vervangend apparaat ophalen

Er is een vervangend apparaat nodig wanneer het bestaande apparaat een hardwarestoring heeft of een upgrade nodig heeft. Voer de volgende stappen uit wanneer het apparaat een hardwareprobleem heeft:

1. [Open een ondersteunings ticket voor hardware-probleem](#open-a-support-ticket). Microsoft Ondersteuning stelt vast dat een vervangende eenheid voor een veld (FRU) niet beschikbaar is voor dit exemplaar of dat het apparaat een hardware-upgrade nodig heeft. In beide gevallen zal ondersteuning een vervangend apparaat best Ellen.
2. [Maak een nieuwe resource](azure-stack-edge-deploy-prep.md#create-a-new-resource) voor het vervangende apparaat. Zorg ervoor dat u het selectie vakje inschakelt voor **een Azure stack edge-apparaat**. 
3. Nadat u een vervangend apparaat hebt ontvangen, [installeert](azure-stack-edge-deploy-install.md) en [activeert](azure-stack-edge-deploy-connect-setup-activate.md) u het vervangende apparaat op basis van de nieuwe bron.
4. Volg alle stappen om het oorspronkelijke apparaat te retour neren:
    1. Open een ander ticket om het oorspronkelijke apparaat te retour neren.
    2. [De gegevens op het apparaat wissen](#erase-data-from-the-device).
    3. [Een ophaling plannen](#schedule-a-pickup).
    5. [Verwijder de resource](#delete-the-resource) die aan het geretourneerde apparaat is gekoppeld.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Bandbreedte beheren](azure-stack-edge-manage-bandwidth-schedules.md).
