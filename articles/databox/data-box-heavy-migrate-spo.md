---
title: Azure Data Box Heavy gebruiken om inhoud voor bestandsshare naar SharePoint Online te verplaatsen
description: Gebruik deze zelfstudie voor meer informatie over het migreren van inhoud voor het delen van bestanden naar Share Point Online met behulp van uw Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: f97ea17551d4415f7ed6371853172cfde30fe4b6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77560045"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>De Azure Data Box Heavy gebruiken om de inhoud van het delen van bestanden te migreren naar SharePoint Online

Gebruik uw Azure Data Box Heavy en het SharePoint Migration Tool (SPMT) om uw inhoud voor bestandsshare eenvoudig te migreren naar SharePoint Online en OneDrive. Door Data Box Heavy te gebruiken, u de afhankelijkheid van uw WAN-koppeling (Wide-area Network) verwijderen om de gegevens over te dragen.

De Microsoft Azure Data Box is een service waarmee u een apparaat bestellen via de Microsoft Azure-portal. U vervolgens terabytes aan gegevens van uw servers naar het apparaat kopiëren. Nadat u het terugnaar Microsoft hebt verzonden, worden uw gegevens gekopieerd naar Azure. Afhankelijk van de grootte van de gegevens die u wilt overdragen, u kiezen uit:

- [Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) met 35 TB bruikbare capaciteit per bestelling voor kleine tot middelgrote datasets.
- [Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) met 80 TB bruikbare capaciteit per apparaat voor middelgrote tot grote datasets.
- [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) met 770 TB bruikbare capaciteit per apparaat voor grote datasets.

In dit artikel wordt specifiek gesproken over hoe u de Gegevensvak Zwaar gebruiken om de inhoud van het delen van bestanden te migreren naar SharePoint Online.

## <a name="requirements-and-costs"></a>Eisen en kosten

### <a name="for-data-box-heavy"></a>Voor Data Box Heavy

- Data Box Heavy is alleen beschikbaar voor Enterprise Agreement (EA), Cloud solution provider (CSP) of Azure-sponsoraanbiedingen. Als uw abonnement niet in een van de bovenstaande typen valt, neemt u contact op met Microsoft Support om uw abonnement te upgraden of [azure-abonnementsprijzen te bekijken.](https://azure.microsoft.com/pricing/)
- Er zijn kosten verbonden aan het gebruik van Data Box Heavy. Controleer de [prijzen van Data Box Heavy.](https://azure.microsoft.com/pricing/details/databox/heavy/)


### <a name="for-sharepoint-online"></a>Voor SharePoint Online

- Bekijk de [minimumvereisten voor het SharePoint Migration Tool (SPMT)](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Overzicht van werkstroom

Voor deze werkstroom moet u stappen uitvoeren op Azure Data Box Heavy en op SharePoint Online.
De volgende stappen hebben betrekking op uw Azure Data Box Heavy.

1. Bestel Azure Data Box Heavy.
2. Ontvang en stel uw apparaat in.
3. Kopieer gegevens uit uw on-premises bestandsshare naar de map voor Azure-bestanden op uw apparaat.
4. Nadat de kopie is voltooid, verzendt u het apparaat terug volgens de instructies.
5. Wacht tot de gegevens volledig zijn geüpload naar Azure.

De volgende stappen hebben betrekking op SharePoint Online.

6. Maak een VM in de Azure-portal en monteer de Azure-bestandsshare erop.
7. Installeer het spmt-gereedschap op de Azure VM.
8. Voer het SPMT-gereedschap uit met de Azure-bestandsshare als *bron*.
9. Voer de laatste stappen van het gereedschap uit.
10. Verifieer en bevestig uw gegevens.

## <a name="use-data-box-heavy-to-copy-data"></a>Gegevensvak Zwaar gebruiken om gegevens te kopiëren

Volg de volgende stappen om gegevens naar uw Data Box Heavy te kopiëren.

1. [Bestel uw Data Box Heavy.](data-box-heavy-deploy-ordered.md)
2. Nadat u uw Data Box Heavy hebt ontvangen, [stelt u de Data Box Heavy in.](data-box-heavy-deploy-set-up.md) U bekabelt en configureert beide knooppunten op uw apparaat.
3. [Gegevens kopiëren naar Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md). Zorg er tijdens het kopiëren voor:

    - Gebruik alleen de *map StorageAccountName_AzFile* in de gegevensvak Zwaar om de gegevens te kopiëren. Dit komt omdat u wilt dat de gegevens in een Azure-bestandsshare terechtkomen, niet in blokblobs of paginablobs.
    - Bestanden kopiëren naar een map in *StorageAccountName_AzFile* map. Een submap in *StorageAccountName_AzFile* map maakt een bestandsshare. Bestanden die rechtstreeks naar *StorageAccountName_AzFile* map worden gekopieerd, mislukken en worden geüpload als blokblobs. Dit is het bestandsaandeel dat u in de volgende stap op uw VM monteert.
    - Kopieer gegevens naar beide knooppunten van uw Data Box Heavy.
3. Voer [Voorbereiden uit om](data-box-heavy-deploy-picked-up.md#prepare-to-ship) op uw apparaat te verzenden. Een succesvolle voorbereiding op het verzenden zorgt voor een succesvolle upload van bestanden naar Azure.
4. [Breng het apparaat terug.](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back)
5. [Controleer de gegevens uploaden naar Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>SPMT gebruiken om gegevens te migreren

Nadat u een bevestiging hebt ontvangen van het Azure-gegevensteam dat uw gegevenskopie is voltooid, migreert u uw gegevens naar SharePoint Online.

Voor de beste prestaties en connectiviteit raden we u aan een Azure Virtual Machine (VM) te maken.

1. Meld u aan bij de Azure-portal en [maak vervolgens een virtuele machine](../virtual-machines/windows/quick-create-portal.md).
2. [Monteer de Azure-bestandsshare op de VM](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Download het SharePoint-migratiehulpprogramma](https://spmtreleasescus.blob.core.windows.net/install/default.htm) en installeer het op uw Azure VM.
4. Start het SharePoint-migratiehulpprogramma. Klik **op Aanmelden** en voer uw gebruikersnaam en wachtwoord in voor Office 365.
5. Wanneer gevraagd Waar worden uw **File share** **gegevens?** Voer het pad naar uw Azure-bestandsshare in waar uw gegevens zich bevinden.
6. Volg de resterende aanwijzingen als normaal, inclusief uw doellocatie. Ga voor meer informatie naar [Hoe u het SharePoint-migratiehulpprogramma wilt gebruiken.](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool)

> [!IMPORTANT]
> - De snelheid waarmee gegevens worden ingenomen in SharePoint Online wordt beïnvloed door verschillende factoren, ongeacht of u uw gegevens al in Azure hebt. Als u deze factoren begrijpt, u de efficiëntie van uw migratie plannen en maximaliseren.  Ga voor meer informatie naar [SharePoint Online en OneDrive-migratiesnelheid](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Het risico bestaat dat bestaande machtigingen voor bestanden verloren gaan bij het migreren van de gegevens naar SharePoint Online. U ook bepaalde metagegevens verliezen, zoals *Gemaakt door* en *Datum gewijzigd door*.

## <a name="next-steps"></a>Volgende stappen

[Bestel uw Data Box Heavy](./data-box-heavy-deploy-ordered.md)