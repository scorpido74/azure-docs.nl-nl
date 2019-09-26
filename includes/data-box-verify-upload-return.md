---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/19/2019
ms.author: alkohli
ms.openlocfilehash: a23b0b2c71207bf84a4938d54a78a62efb6cbcbd
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172623"
---
Wanneer Microsoft het apparaat heeft ontvangen en gescand, wordt de orderstatus bijgewerkt naar **Ontvangen**. Het apparaat wordt vervolgens fysiek gecontroleerd op schade of op tekenen dat ermee is geknoeid.

Nadat de controle is uitgevoerd, wordt de Data Box met het netwerk in het Azure-datacenter verbonden. Het kopiëren van de gegevens start automatisch. Afhankelijk van de gegevensgrootte kan de kopieerbewerking enkele uren tot enkele dagen duren. U kunt de voortgang van de kopieertaak bewaken via de portal.

Nadat de kopie is voltooid, wordt de orderstatus bijgewerkt naar **Voltooid**.

Controleer of uw gegevens zijn geüpload naar Azure voordat u deze uit de bron verwijdert. Uw gegevens kunnen zich bevinden in:

- Uw Azure Storage-account(s). Als u de gegevens naar Data Box kopieert, worden de gegevens naar een van de volgende paden in uw Azure Storage-account geüpload, afhankelijk van het gegevenstype.

  - Voor blok-blobs en pagina-blobs: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Voor Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    U kunt ook naar uw Azure-opslagaccount in de Azure-portal gaan en van daaruit navigeren.

- Uw resourcegroep(en) op beheerde schijf. Bij het maken van beheerde schijven worden de VHD's geüpload als pagina-blobs en vervolgens naar beheerde schijven geconverteerd. De beheerde schijven worden gekoppeld aan de resourcegroepen die zijn opgegeven op het moment dat de order werd gemaakt. 

    - Als het kopiëren naar beheerde schijven in Azure is geslaagd, gaat u naar **Orderdetails** in de Azure-portal en noteert u de resourcegroepen die zijn opgegeven voor beheerde schijven.

        ![Resourcegroepen op beheerde schijven identificeren](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        Ga naar de genoteerde resourcegroep en zoek uw beheerde schijven.

        ![Beheerde schijf gekoppeld aan resourcegroepen](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - Als u een VHDX of een dynamische/differentiërende VHD hebt gekopieerd, wordt de VHDX/VHD als een pagina-blob naar het faseringsopslagaccount geüpload, maar de conversie van de VHD naar een beheerde schijf mislukt. Ga naar uw **Faseringsopslagaccount > Blobs** en selecteer de juiste container: Standard - SSD, Standard - HDD of Premium - SSD. De VHD's worden als pagina-blobs geüpload in uw faseringsopslagaccount.


## <a name="erasure-of-data-from-data-box"></a>Gegevens verwijderen uit de Data Box
 
Nadat de gegevens naar Azure zijn geüpload, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).


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
> [De lokale webgebruikersinterface gebruiken voor het beheren van de Azure Data Box](../articles/databox/data-box-local-web-ui-admin.md)