---
title: Zelf studie voor het verzenden van Azure Data Box Heavy | Microsoft Docs
description: Meer informatie over het verzenden van uw Azure Data Box Heavy naar micro soft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: e438fb38afb649f6f4c7f595059ef64800977242
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240347"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Zelfstudie: Retour Azure Data Box Heavy en controleer het uploaden van gegevens naar Azure

::: zone-end

::: zone target = "chromeless"

# <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Retour Azure Data Box Heavy en controleer het uploaden van gegevens naar Azure

::: zone-end

::: zone target = "docs"

In deze zelf studie wordt beschreven hoe u Azure Data Box Heavy retourneert en controleert u de gegevens die zijn geüpload naar Azure.

In deze zelfstudie vindt u informatie over onderwerpen als:

> [!div class="checklist"]
> * Vereisten
> * Voorbereiding voor verzending
> * Data Box Heavy verzenden naar micro soft
> * De gegevensupload naar Azure controleren
> * Verwijdering van gegevens uit Data Box Heavy

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

- U hebt de zelfstudie [ Gegevens kopiëren naar Azure Data Box en deze gegevens controleren](data-box-heavy-deploy-copy-data.md).
- Kopieertaken zijn voltooid. Voorbereiding voor verzending kan niet worden uitgevoerd als er nog kopieertaken worden uitgevoerd.


## <a name="prepare-to-ship"></a>Voorbereiding voor verzending

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Voorbereiding voor verzending

Voordat u het verzenden voorbereidt, moet u ervoor zorgen dat de Kopieer taken zijn voltooid.

1. Ga naar voorbereiding voor verzending pagina in de lokale web-UI en start de voor bereiding voor de verzen ding.
2. Schakel het apparaat uit in de lokale webgebruikersinterface. Verwijder de kabels uit het apparaat.

U bent nu klaar om uw apparaat terug te leveren.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Data Box Heavy back verzenden

1. Zorg ervoor dat het apparaat is uitgeschakeld en dat alle kabels worden verwijderd. U kunt de 4 stroom kabels in de lade van de wachtrij plaatsen en deze veilig vanaf de achtergrond van het apparaat gebruiken.
2. Het apparaat verzendt LTL vracht via FedEx in de VS en DHL in de EU

    1. Neem contact op met [Data Box bewerkingen](mailto:DataBoxOps@microsoft.com) om te informeren over de ophaling en om het verzend label voor de retour zending op te halen.
    2. Bel het lokale nummer voor uw vervoerders om het ophalen te plannen.
    3. Zorg ervoor dat het verzend label prominent op de buiten kant van de verzen ding wordt weer gegeven.
    4. Zorg ervoor dat de oude verzendlabels van de vorige verzen ding worden verwijderd van het apparaat.
3. Zodra de Data Box Heavy wordt opgehaald en gescand door uw provider, wordt de status van de bestelling in de portal bijgewerkt om te worden **opgehaald**. Er wordt ook een tracerings-id weergegeven.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

Wanneer Microsoft het apparaat heeft ontvangen en gescand, wordt de orderstatus bijgewerkt naar **Ontvangen**. Het apparaat wordt vervolgens fysiek gecontroleerd op schade of op tekenen dat ermee is geknoeid.

Nadat de verificatie is voltooid, is de Data Box Heavy verbonden met het netwerk in het Azure-Data Center. Het kopiëren van de gegevens start automatisch. Afhankelijk van de gegevensgrootte kan de kopieerbewerking enkele uren tot enkele dagen duren. U kunt de voortgang van de kopieertaak bewaken via de portal.

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

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

Wanneer het Data Box Heavy apparaat is verbonden met het Azure Data Center-netwerk, wordt het uploaden van gegevens naar Azure automatisch gestart. Data Box-Service meldt u dat het kopiëren van de gegevens is voltooid via de Azure Portal.

- Controleer foutenlogboeken op eventuele fouten en onderneem toepasselijke acties.
- Controleer of uw gegevens zich in de opslagaccount(s) bevinden voordat u deze uit de bron verwijdert.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Verwijdering van gegevens uit Data Box Heavy
 
Nadat de gegevens naar Azure zijn geüpload, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Nadat de verwijdering is voltooid, kunt u [de order geschiedenis downloaden](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
> * Vereisten
> * Voorbereiding voor verzending
> * Data Box Heavy verzenden naar micro soft
> * De gegevensupload naar Azure controleren
> * Verwijdering van gegevens uit Data Box Heavy

Ga naar het volgende artikel voor meer informatie over het beheren van Data Box Heavy via de lokale web-UI.

> [!div class="nextstepaction"]
> [De lokale webgebruikersinterface gebruiken voor het beheren van de Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


