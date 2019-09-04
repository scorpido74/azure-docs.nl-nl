---
title: Zelf studie voor het verzenden van Azure Data Box | Microsoft Docs
description: Lees hoe u uw Azure Data Box naar Microsoft verzendt
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 8/27/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 5b78dc8b815802502cda5baf2bf5e9646922d671
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241435"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Zelfstudie: Azure Data Box terugsturen en de gegevens controleren die u naar Azure hebt geüpload

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Retour Data Box en controleer het uploaden van gegevens naar Azure

::: zone-end

::: zone target="docs"

In deze zelfstudie wordt beschreven hoe u de Azure Data Box terugstuurt en hoe u de gegevens kunt controleren die u naar Azure hebt geüpload.

In deze zelfstudie vindt u informatie over onderwerpen als:

> [!div class="checklist"]
> * Vereisten
> * Voorbereiding voor verzending
> * De Data Box naar Microsoft verzenden
> * De gegevensupload naar Azure controleren
> * Gegevens verwijderen uit de Data Box

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

- U hebt de [Zelfstudie: Gegevens kopiëren naar Azure Data Box en deze gegevens controleren](data-box-deploy-copy-data.md). 
- Kopieertaken zijn voltooid. Voorbereiding voor verzending kan niet worden uitgevoerd als er nog kopieertaken worden uitgevoerd.

## <a name="prepare-to-ship"></a>Voorbereiding voor verzending

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Wanneer het kopiëren van de gegevens is voltooid, kunt u het apparaat voorbereiden en verzenden. Wanneer het apparaat een Azure-Data Center bereikt, worden de gegevens automatisch naar Azure geüpload.

## <a name="prepare-to-ship"></a>Voorbereiding voor verzending

Voordat u het verzenden voorbereidt, moet u ervoor zorgen dat de Kopieer taken zijn voltooid.

1. Ga naar de pagina **Voorbereiden voor verzending** in de lokale webgebruikersinterface en start de voorbereiding voor verzending. 
2. Schakel het apparaat uit in de lokale webgebruikersinterface. Verwijder de kabels uit het apparaat. 

De volgende stappen worden bepaald door de locatie waar u het apparaat retourneert.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>De Data Box terugsturen

Zorg ervoor dat het kopiëren van gegevens naar het apparaat is voltooid en dat **voorbereiding voor verzending** worden uitgevoerd. De procedure is afhankelijk van de regio waarin u het apparaat verzendt.

::: zone-end

## <a name="ship-in-us-canada-europe"></a>Verzenden in de Verenigde Staten, Canada, Europa

Voer de volgende stappen uit als u het apparaat in VS, Canada of Europa retourneert.

1. Zorg ervoor dat het apparaat is uitgeschakeld en dat de kabels worden verwijderd. 
2. Rol het netsnoer op en plaats het veilig aan de achterzijde van het apparaat.
3. Zorg ervoor dat het verzendlabel wordt weergegeven op het E-ink-scherm en plan met de vervoerder een tijdstip waarop het kan worden opgehaald. Neem contact op met de Microsoft-ondersteuning als het label beschadigd is of ontbreekt of niet wordt weergegeven op het e-inkscherm. Als de ondersteuning wordt voorgesteld, gaat u naar **overzicht > het downloaden van het verzend label** in de Azure Portal. Download het verzendlabel en bevestig dat op het apparaat. 
4. Als u het apparaat retourneert, spreekt u met UPS een tijdstip af waarop het kan worden opgehaald. Een ophaling plannen:

    - Roep de lokale nood voeding (land/regio-specifiek gratis nummer) aan.
    - Neem in uw gesprek het tracerings nummer van omgekeerde verzen ding op zoals weer gegeven in de E-inkt weergave of op uw afgedrukte label.
    - Als het tracking nummer niet wordt vermeld, moet u tijdens het ophalen een extra kosten betalen.

    In plaats van het ophalen te plannen, kunt u ook de Data Box op de dichtstbijzijnde uitval locatie afhalen.
4. Nadat de Data Box door de vervoerder is opgehaald en gescand, verandert de orderstatus in de portal in **Opgehaald**. Er wordt ook een tracerings-id weergegeven.


## <a name="ship-in-australia"></a>In Australië verzenden

Azure-data centers in Australië hebben een extra beveiligings melding. Alle inkomende verzen dingen moeten een geavanceerde melding hebben. Voer de volgende stappen uit om in Australië te verzenden.


1. Behoud de oorspronkelijke doos die wordt gebruikt om het apparaat te verzenden voor retour zending.
2. Zorg ervoor dat het kopiëren van gegevens naar het apparaat is voltooid en dat **voorbereiding voor verzending worden uitgevoerd** .
3. Schakel het apparaat uit en verwijder de kabels.
4. Maak de stroom in de wachtrij en plaats het netsnoer dat is meegeleverd met het apparaat op de achterkant van het apparaat.
5. Quantium-oplossingen verzenden om een ophaling aan te vragen. Raadpleeg het service verwijzings nummer dat is opgegeven op de Azure Portal. Gebruik de volgende e-mail sjabloon:- *aanvraag voor omgekeerde verzend label met de letter tau-code*. Zorg ervoor dat u de volgende gegevens opneemt in het e-mail bericht: 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. Quantium Solutions Australië stuurt een e-mail adres terug naar de retour nering.
7. Druk het retour label af en breng dit aan op de verpakking.
8. Voor het pakket naar de Courier.

Als dat nodig is, kunt u een e- Azure@quantiumsolutions.com mail adres voor de Quantium-oplossing op of telefoon bieden.

Voor informatie over uw bestelling via de telefoon:

- Een e-mail verzenden om eerst op te halen.
- Geef de naam van uw bestelling op de telefoon op.

## <a name="ship-in-japan"></a>Verzenden in Japan 

1. Behoud de oorspronkelijke doos die wordt gebruikt om het apparaat te verzenden voor retour zending.
2. Schakel het apparaat uit en verwijder de kabels.
3. Maak de stroom in de wachtrij en plaats het netsnoer dat is meegeleverd met het apparaat op de achterkant van het apparaat.
4. Schrijf uw bedrijfs naam en adres gegevens in de begeleide notitie als gegevens van de afzender.
5. Quantium-oplossing voor e-mail met de volgende e-mail sjabloon.

    - Als er geen Chakubarai-zending van Japan post is opgenomen of ontbreekt, noteert u deze in dit e-mail bericht. Quantium Solutions Japan vraagt een Japanse post aan om de zending op te halen wanneer deze wordt opgehaald.
    - Als u meerdere orders hebt, moet u een e-mail verzenden om afzonderlijke op te halen.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. Ontvang een e-mail bevestiging van Quantium-oplossingen nadat u een ophaling hebt vastgelegd. De e-mail bevestiging bevat ook informatie over het Chakubarai-begeleidings document.

Als dat nodig is, kunt u contact opnemen met de ondersteuning van de Quantium-oplossing (Japanse taal) met de volgende informatie: 

- E-mail:Customerservice.JP@quantiumsolutions.com 
- Telefoon: 03-5755-0150 

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

Wanneer Microsoft het apparaat heeft ontvangen en gescand, wordt de orderstatus bijgewerkt naar **Ontvangen**. Het apparaat wordt vervolgens fysiek gecontroleerd op schade of op tekenen dat ermee is geknoeid.

Nadat de controle is uitgevoerd, wordt de Data Box met het netwerk in het Azure-datacenter verbonden. Het kopiëren van de gegevens start automatisch. Afhankelijk van de gegevensgrootte kan de kopieerbewerking enkele uren tot enkele dagen duren. U kunt de voortgang van de kopieertaak bewaken via de portal.

Nadat de kopie is voltooid, wordt de orderstatus bijgewerkt naar **Voltooid**.

Controleer of uw gegevens zijn geüpload naar Azure voordat u deze verwijdert uit de bron. Uw gegevens kunnen de volgende zijn:

- Uw Azure Storage-account (s). Als u de gegevens naar Data Box kopieert, worden de gegevens naar een van de volgende paden in uw Azure Storage-account geüpload, afhankelijk van het gegevenstype.

  - Voor blok-blobs en pagina-blobs: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Voor Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    U kunt ook naar uw Azure-opslagaccount in de Azure-portal gaan en van daaruit navigeren.

- De resource groep (en) van de beheerde schijf. Bij het maken van beheerde schijven worden de Vhd's geüpload als pagina-blobs en vervolgens geconverteerd naar Managed disks. De beheerde schijven worden gekoppeld aan de resource groepen die zijn opgegeven op het moment dat de order wordt gemaakt. 

    - Als uw kopie naar beheerde schijven in Azure is geslaagd, gaat u naar de details van de **order** in het Azure Portal en noteert u de resource groepen die zijn opgegeven voor beheerde schijven.

        ![Resource groepen voor beheerde schijven identificeren](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Ga naar de genoteerde resource groep en zoek uw beheerde schijven.

        ![Beheerde schijf die is gekoppeld aan resource groepen](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Als u een VHDX of een dynamische/differentiërende VHD hebt gekopieerd, wordt de VHDX/VHD geüpload naar het staging Storage-account als een pagina-blob, maar wordt de conversie van de VHD naar een beheerde schijf mislukt. Ga naar uw staging- **opslag account > blobs** en selecteer vervolgens de juiste container-Standard-SSD, Standard-HDD of Premium-SSD. De Vhd's worden geüpload als pagina-blobs in uw staging Storage-account.

::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

::: zone-end

## <a name="erasure-of-data-from-data-box"></a>Gegevens verwijderen uit de Data Box
 
Nadat de gegevens naar Azure zijn geüpload, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone target="docs"

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
> * Vereisten
> * Voorbereiding voor verzending
> * De Data Box naar Microsoft verzenden
> * De gegevensupload naar Azure controleren
> * Gegevens verwijderen uit de Data Box

Ga naar het volgende artikel voor informatie over het beheren van de Data Box via de lokale webgebruikersinterface.

> [!div class="nextstepaction"]
> [De lokale webgebruikersinterface gebruiken voor het beheren van de Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


