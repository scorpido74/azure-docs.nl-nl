---
title: Zelfstudie voor het terugsturen van de Azure Data Box Heavy | Microsoft Docs
description: In deze zelfstudie leert u hoe u de Azure Data Box Heavy kunt retourneren, inclusief het voorbereiden op verzending, het verzenden van de Data Box Heavy, het verifiëren van gegevensupload en het wissen van gegevens.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: fa4df7119763e835cf62e67512105b748c7e2265
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87921007"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Zelfstudie: De Azure Data Box Heavy terugsturen en de gegevens controleren die u naar Azure hebt geüpload

::: zone-end

::: zone target = "chromeless"

## <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>De Azure Data Box Heavy terugsturen en de gegevens controleren die u naar Azure hebt geüpload

::: zone-end

::: zone target = "docs"

In deze zelfstudie wordt beschreven hoe u de Azure Data Box Heavy terugstuurt en hoe u de gegevens kunt controleren die u naar Azure hebt geüpload.

In deze zelfstudie vindt u informatie over onderwerpen als:

> [!div class="checklist"]
> * Vereisten
> * Voorbereiding voor verzending
> * De Data Box Heavy naar Microsoft verzenden
> * De gegevensupload naar Azure controleren
> * Gegevens verwijderen van de Data Box Heavy

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

- U hebt de zelfstudie [ Gegevens kopiëren naar Azure Data Box en deze gegevens controleren](data-box-heavy-deploy-copy-data.md).
- Kopieertaken zijn voltooid. Voorbereiding voor verzending kan niet worden uitgevoerd als er nog kopieertaken worden uitgevoerd.


## <a name="prepare-to-ship"></a>Voorbereiding voor verzending

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Voorbereiding voor verzending

Voordat u het verzenden gaat voorbereiden, moet u ervoor zorgen dat de kopieertaken zijn voltooid.

1. Ga naar de pagina Voorbereiden voor verzending in de lokale webgebruikersinterface en start de voorbereiding voor verzending.
2. Schakel het apparaat uit in de lokale webgebruikersinterface. Verwijder de kabels uit het apparaat.

U bent nu klaar om uw apparaat terug te sturen.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Data Box Heavy verzenden

1. Zorg ervoor dat het apparaat uit staat en alle kabels zijn verwijderd. Wind de vier stroomkabels op en plaats ze in de lade aan de achterkant van het apparaat.
2. Het apparaat wordt als pakket verzonden; in de VS met FedEx en in de EU met DHL

    1. Neem contact op met [Data Box Operations](mailto:DataBoxOps@microsoft.com) om ze te informeren over het afhalen en om een retourlabel aan te vragen.
    2. Bel het lokale nummer van uw vervoerders om het ophalen te plannen.
    3. Zorg ervoor dat het verzendlabel prominent op de buitenkant van het pakket wordt weergegeven.
    4. Zorg ervoor dat de oude verzendlabels van vorige verzendingen van het apparaat zijn verwijderd.
3. Nadat de Data Box Heavy door de vervoerder is opgehaald en gescand, verandert de orderstatus in de portal in **Opgehaald**. Er wordt ook een tracerings-id weergegeven.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

Wanneer Microsoft het apparaat heeft ontvangen en gescand, wordt de orderstatus bijgewerkt naar **Ontvangen**. Het apparaat wordt vervolgens fysiek gecontroleerd op schade of op tekenen dat ermee is geknoeid.

Nadat de controle is uitgevoerd, wordt de Data Box Heavy met het netwerk in het Azure-datacenter verbonden. Het kopiëren van de gegevens start automatisch. Afhankelijk van de gegevensgrootte kan de kopieerbewerking enkele uren tot enkele dagen duren. U kunt de voortgang van de kopieertaak bewaken via de portal.

Nadat de kopie is voltooid, wordt de orderstatus bijgewerkt naar **Voltooid**.

Controleer of uw gegevens zijn geüpload naar Azure voordat u deze uit de bron verwijdert. Uw gegevens kunnen zich bevinden in:

- Uw Azure Storage-account(s). Als u de gegevens naar Data Box kopieert, worden de gegevens naar een van de volgende paden in uw Azure Storage-account geüpload, afhankelijk van het gegevenstype.

  - Voor blok-blobs en pagina-blobs: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Voor Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    U kunt ook naar uw Azure-opslagaccount in de Azure-portal gaan en van daaruit navigeren.

- Uw resourcegroep(en) op beheerde schijf. Bij het maken van beheerde schijven worden de VHD's geüpload als pagina-blobs en vervolgens naar beheerde schijven geconverteerd. De beheerde schijven worden gekoppeld aan de resourcegroepen die zijn opgegeven op het moment dat de order werd gemaakt. 

    - Als het kopiëren naar beheerde schijven in Azure is geslaagd, gaat u naar **Orderdetails** in de Azure-portal en noteert u de resourcegroepen die zijn opgegeven voor beheerde schijven.

        ![Resourcegroepen op beheerde schijven identificeren](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Ga naar de genoteerde resourcegroep en zoek uw beheerde schijven.

        ![Beheerde schijf gekoppeld aan resourcegroepen](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Als u een VHDX of een dynamische/differentiërende VHD hebt gekopieerd, wordt de VHDX/VHD als een pagina-blob naar het faseringsopslagaccount geüpload, maar de conversie van de VHD naar een beheerde schijf mislukt. Ga naar uw **Faseringsopslagaccount > Blobs** en selecteer de juiste container: Standard - SSD, Standard - HDD of Premium - SSD. De VHD's worden als pagina-blobs geüpload in uw faseringsopslagaccount.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

Wanneer het Data Box Heavy-apparaat wordt verbonden met het datacenternetwerk van Azure, wordt de gegevensupload naar Azure automatisch gestart. De Data Box-service meldt via de Azure-portal dat het kopiëren van de gegevens is voltooid.

- Controleer foutenlogboeken op eventuele fouten en onderneem toepasselijke acties.
- Controleer of uw gegevens zich in de opslagaccount(s) bevinden voordat u deze uit de bron verwijdert.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Gegevens verwijderen van de Data Box Heavy
 
Nadat de gegevens naar Azure zijn geüpload, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Nadat de verwijdering is voltooid, kunt u [de ordergeschiedenis downloaden](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
> * Vereisten
> * Voorbereiding voor verzending
> * De Data Box Heavy naar Microsoft verzenden
> * De gegevensupload naar Azure controleren
> * Gegevens verwijderen van de Data Box Heavy

Ga naar het volgende artikel voor informatie over het beheren van de Data Box Heavy via de lokale webgebruikersinterface.

> [!div class="nextstepaction"]
> [De lokale webgebruikersinterface gebruiken voor het beheren van de Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


