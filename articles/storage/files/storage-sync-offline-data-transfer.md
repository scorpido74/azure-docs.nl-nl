---
title: Gegevens migreren naar Azure File Sync met Azure Data Box
description: Bulkgegevens migreren op een manier die compatibel is met Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9398aceeb7465392e82aeaa5760f6c0504f8e33d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159520"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Bulkgegevens migreren naar Azure File Sync met Azure DataBox
U bulkgegevens op twee manieren migreren naar Azure File Sync:

* **Upload uw bestanden met Azure File Sync.** Dit is de eenvoudigste methode. Verplaats uw bestanden lokaal naar Windows Server 2012 R2 of hoger en installeer de Azure File Sync-agent. Nadat u de synchronisatie hebt ingesteld, worden uw bestanden geüpload vanaf de server. (Onze klanten ervaren momenteel een gemiddelde uploadsnelheid van 1 TiB ongeveer om de twee dagen.) Als u ervoor wilt zorgen dat uw server niet te veel bandbreedte voor uw datacenter gebruikt, u een [bandbreedtebeperkingsschema](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)instellen.
* **Zet uw bestanden offline over.** Als u niet genoeg bandbreedte hebt, u mogelijk niet binnen een redelijke tijd bestanden uploaden naar Azure. De uitdaging is de eerste synchronisatie van de hele set bestanden. Gebruik offline bulkmigratietools zoals de [Azure Data Box-familie](https://azure.microsoft.com/services/storage/databox)om deze uitdaging het hoofd te bieden. 

In dit artikel wordt uitgelegd hoe u bestanden offline migreert op een manier die compatibel is met Azure File Sync. Volg deze instructies om bestandsconflicten te voorkomen en om de toegangscontrolelijsten voor bestands- en maptoegangsbeheer (ACL's) en tijdstempels te behouden nadat u de synchronisatie hebt ingeschakeld.

## <a name="migration-tools"></a>Hulpprogramma's voor migratie
Het proces dat we in dit artikel beschrijven werkt niet alleen voor Data Box, maar ook voor andere offline migratietools. Het werkt ook voor tools zoals AzCopy, Robocopy of partnertools en -services die rechtstreeks via het internet werken. Volg echter de stappen in dit artikel om deze hulpprogramma's te gebruiken op een manier die compatibel is met Azure File Sync.

In sommige gevallen moet u van de ene Windows Server naar de andere Windows Server gaan voordat u Azure File Sync overneemt. [Storage Migration Service](https://aka.ms/storagemigrationservice) (SMS) kan daarbij helpen. Of u nu moet migreren naar een Server OS-versie die wordt ondersteund door Azure File Sync (Windows Server 2012R2 en meer) of dat u gewoon moet migreren omdat u een nieuw systeem voor Azure File Sync koopt, SMS heeft tal van functies en voordelen die u helpen om uw migratie soepel verlopen.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Voordelen van het gebruik van een tool om gegevens offline over te dragen
Dit zijn de belangrijkste voordelen van het gebruik van een transfertool zoals Data Box voor offline migratie:

- U hoeft niet al uw bestanden via het netwerk te uploaden. Voor grote naamruimten kan deze tool aanzienlijke netwerkbandbreedte en -tijd besparen.
- Wanneer u Azure File Sync gebruikt, ongeacht welk transferprogramma u gebruikt (Data Box, Azure Import/Export-service, enzovoort), uploadt uw live-server alleen de bestanden die worden gewijzigd nadat u de gegevens naar Azure hebt verplaatst.
- Azure File Sync synchroniseert de acl. van uw bestand en map, zelfs als het hulpprogramma voor offline bulkmigratie geen ACL's transporteert.
- Data Box en Azure File Sync vereisen geen downtime. Wanneer u Data Box gebruikt om gegevens naar Azure over te zetten, gebruikt u de netwerkbandbreedte efficiënt en behoudt u de bestandsgetrouwheid. U houdt uw naamruimte ook up-to-date door alleen de bestanden te uploaden die worden gewijzigd nadat u de gegevens naar Azure hebt verplaatst.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Voorwaarden voor de offline gegevensoverdracht
U moet synchronisatie niet inschakelen op de server die u migreert voordat u uw offline gegevensoverdracht voltooit. Andere dingen om te overwegen voordat je begint zijn als volgt:

- Als u gegevensvak wilt gebruiken voor uw bulkmigratie: Bekijk de [vereisten voor de implementatie voor gegevensvak](../../databox/data-box-deploy-ordered.md#prerequisites).
- Uw definitieve Azure File Sync-topologie plannen: [een Azure-bestandssynchronisatie-implementatie plannen](storage-sync-files-planning.md)
- Selecteer Azure-opslagaccount(en) waarmee de bestandsshares worden behouden waarmee u wilt synchroniseren. Zorg ervoor dat uw bulkmigratie gebeurt met tijdelijke faseringsshares in dezelfde opslagaccount(en). Bulkmigratie kan alleen worden ingeschakeld met behulp van een final- en een staging-share die zich in hetzelfde opslagaccount bevindt.
- Een bulkmigratie kan alleen worden gebruikt wanneer u een nieuwe synchronisatierelatie met een serverlocatie maakt. U geen bulkmigratie inschakelen met een bestaande synchronisatierelatie.


## <a name="process-for-offline-data-transfer"></a>Proces voor offline gegevensoverdracht
U Azure File Sync als voorbeeld instellen op een manier die compatibel is met hulpprogramma's voor bulkmigratie, zoals Azure Data Box:

![Diagram met informatie over het instellen van Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Stap | Detail |
|---|---------------------------------------------------------------------------------------|
| ![Stap 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Bestel uw databox.](../../databox/data-box-deploy-ordered.md) De Data Box-familie biedt [verschillende producten](https://azure.microsoft.com/services/storage/databox/data) om aan uw behoeften te voldoen. Wanneer u uw gegevensvak ontvangt, volgt u de [documentatie om uw gegevens](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) naar dit UNC-pad te kopiëren in de gegevensvak: * \\<DeviceIPAddres\>\<StorageAccountName_AzFile\>\<ShareName\>*. Hier is *ShareName* de naam van het staging share. Stuur het gegevensvak terug naar Azure. |
| ![Stap 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Wacht tot uw bestanden worden weergegeven in de Azure-bestandsshares die u hebt gekozen als tijdelijke faseringsshares. *Schakel synchronisatie met deze shares niet in.* |
| ![Stap 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Maak een nieuwe lege share voor elke bestandsshare die gegevensvak voor u heeft gemaakt. Dit nieuwe aandeel moet in dezelfde opslagaccount staan als het aandeel Data Box. [Een nieuwe Azure-bestandsshare maken.](storage-how-to-create-file-share.md) |
| ![Stap 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Maak een synchronisatiegroep](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) in een opslagsynchronisatieservice. Verwijs naar het lege aandeel als een eindpunt in de cloud. Herhaal deze stap voor elke gegevensboxbestandsshare. [Azure File Sync instellen](storage-sync-files-deployment-guide.md). |
| ![Stap 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Voeg uw live servermap toe als servereindpunt.](storage-sync-files-deployment-guide.md#create-a-server-endpoint) Geef in het proces op dat u de bestanden naar Azure hebt verplaatst en verwijs naar de faseringsshares. U cloudlagen indien nodig in- of uitschakelen. Terwijl u een servereindpunt maakt op uw liveserver, verwijst u naar de faseringsshare. Selecteer op het **eindpuntblad Van** de server onder **Offline gegevensoverdracht**de optie **Ingeschakeld**en selecteer vervolgens het faseringsaandeel dat zich in hetzelfde opslagaccount moet bevinden als het eindpunt van de cloud. Hier wordt de lijst met beschikbare aandelen gefilterd op opslagaccount en aandelen die nog niet zijn gesynchroniseerd. |

![Schermafbeelding van de gebruikersinterface van de Azure-portal, waarin wordt weergegeven hoe u offline gegevensoverdracht inschakelen terwijl u een nieuw servereindpunt maakt](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Het aandeel synchroniseren
Nadat u het servereindpunt hebt gemaakt, wordt de synchronisatie gestart. Het synchronisatieproces bepaalt of elk bestand op de server ook bestaat in de faseringsshare waarin Gegevensvak de bestanden heeft gedeponeerd. Als het bestand daar bestaat, kopieert het synchronisatieproces het bestand van de faseringsshare in plaats van het te uploaden vanaf de server. Als het bestand niet bestaat in de faseringsshare of als er een nieuwere versie beschikbaar is op de lokale server, wordt het bestand van de lokale server geüpload.

> [!IMPORTANT]
> U de bulkmigratiemodus alleen inschakelen terwijl u een servereindpunt maakt. Nadat u een servereindpunt hebt ingesteld, u bulkgemigreerde gegevens van een reeds synchroniserende server niet integreren in de naamruimte.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL's en tijdstempels op bestanden en mappen
Azure File Sync zorgt ervoor dat acl. voor bestanden en mappen worden gesynchroniseerd vanaf de live-server, zelfs als het bulkmigratiehulpprogramma dat u hebt gebruikt, in eerste instantie geen ACL's heeft transporteren. Hierdoor hoeft het delen van de fasering geen ACL's voor bestanden en mappen te bevatten. Wanneer u de functie offlinegegevensmigratie inschakelt terwijl u een nieuw servereindpunt maakt, worden alle bestandsacl.s. Nieuw gemaakte en gewijzigde tijdstempels worden ook gesynchroniseerd.

## <a name="shape-of-the-namespace"></a>Vorm van de naamruimte
Wanneer u de synchronisatie inschakelt, bepaalt de inhoud van de server de vorm van de naamruimte. Als bestanden van de lokale server worden verwijderd nadat de momentopname gegevensvak en migratie zijn voltooid, worden deze bestanden niet verplaatst naar de live-naamruimte. Ze blijven in de enscenering delen, maar ze zijn niet gekopieerd. Dit is nodig omdat de synchronisatie de naamruimte volgens de live server houdt. De *snapshot* van de Data Box is slechts een faseringsgrond voor een efficiënte bestandskopie. Het is niet de autoriteit voor de vorm van de live naamruimte.

## <a name="cleaning-up-after-bulk-migration"></a>Opruimen na bulkmigratie 
Terwijl de server de eerste synchronisatie van de naamruimte voltooit, gebruiken de bestanden met bulkgemigreerde bestanden in het gegevensvak de bestandsshare met tijdelijke bestanden. Op het blade **Eigenschappen van servereindpunt** in Azure-portal verandert in de sectie **Offline gegevensoverdracht** de status van **In uitvoering** naar **Voltooid**. 

![Schermafbeelding van het blade Server Endpoint Properties, waarin de status en de besturingselementen voor offline gegevensoverdracht zich bevinden](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Nu u het deel van de fasering opschonen om kosten te besparen:

1. Selecteer offline **gegevensoverdracht uitschakelen**wanneer de status is voltooid , wanneer de status is **voltooid**, in het blad Eigenschappen **van servereindpunt** .
2. Overweeg het deel van de fasering te verwijderen om kosten te besparen. Het delen met tijdelijke bestanden bevat waarschijnlijk geen acl.n.v. bestands- en mapacl.the-staging share probably doesn't contain file and folder ACL's, so it is unlikely to be useful. Maak voor back-uppoint-in-time-doeleinden een echte [momentopname van de synchronisatie van Azure-bestandsshare.](storage-snapshots-files.md) U [Azure Backup instellen om snapshots]( ../../backup/backup-afs.md) te maken volgens een planning.

Schakel de offline gegevensoverdrachtmodus alleen uit wanneer de status is **voltooid** of wanneer u wilt annuleren vanwege een verkeerde configuratie. Als u de modus uitschakelt tijdens een implementatie, worden bestanden vanaf de server gestart, zelfs als uw faseringsaandeel nog beschikbaar is.

> [!IMPORTANT]
> Nadat u de modus offlinegegevensoverdracht hebt uitgeschakeld, u deze niet opnieuw inschakelen, zelfs niet als het faseringsaandeel van de bulkmigratie nog steeds beschikbaar is.

## <a name="next-steps"></a>Volgende stappen
- [Een Azure File Sync-implementatie plannen](storage-sync-files-planning.md)
- [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)
