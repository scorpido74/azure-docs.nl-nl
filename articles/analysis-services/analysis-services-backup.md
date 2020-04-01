---
title: Back-up en herstel van Azure Analysis Services-database | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u modelmetagegevens en gegevens uit een Azure Analysis Services-database back-ups maken en herstellen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 83da2024ab74b705b45a5891f6b40251020dad31
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408659"
---
# <a name="backup-and-restore"></a>Back-ups en herstellen

Het maken van back-ups van tabelmodeldatabases in Azure Analysis Services is vrijwel hetzelfde als voor on-premises Analysis Services. Het belangrijkste verschil is waar u uw back-upbestanden opslaat. Back-upbestanden moeten worden opgeslagen in een container in een [Azure-opslagaccount.](../storage/common/storage-create-storage-account.md) U een opslagaccount en een container gebruiken die u al hebt, of deze kunnen worden gemaakt bij het configureren van opslaginstellingen voor uw server.

> [!NOTE]
> Het maken van een opslagaccount kan resulteren in een nieuwe factureerbare service. Zie [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie.
> 
> 

Back-ups worden opgeslagen met een .abf-extensie. Voor tabelmodellen in het geheugen worden zowel modelgegevens als metagegevens opgeslagen. Voor tabelmodellen van DirectQuery worden alleen modelmetagegevens opgeslagen. Back-ups kunnen worden gecomprimeerd en versleuteld, afhankelijk van de opties die u kiest.


## <a name="configure-storage-settings"></a>Opslaginstellingen configureren
Voordat u een back-up maakt, moet u opslaginstellingen voor uw server configureren.


### <a name="to-configure-storage-settings"></a>Opslaginstellingen configureren
1.  Klik in Azure-portal **>-instellingen**op **Back-up**.

    ![Back-ups in instellingen](./media/analysis-services-backup/aas-backup-backups.png)

2.  Klik **op Ingeschakeld**en klik vervolgens op **Opslaginstellingen**.

    ![Inschakelen](./media/analysis-services-backup/aas-backup-enable.png)

3. Selecteer uw opslagaccount of maak een nieuw account.

4. Selecteer een container of maak een nieuwe.

    ![Container selecteren](./media/analysis-services-backup/aas-backup-container.png)

5. Sla uw back-upinstellingen op.

    ![Back-upinstellingen opslaan](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Een back-up maken met SSMS

1. Klik in SSMS met de rechtermuisknop op een database > **Back-up maken.**

2. Klik in **back-updatabaseback-upbestand** > **Backup file**op **Bladeren**.

3. Controleer in het **dialoogvenster Bestand opslaan als** het mappad en typ vervolgens een naam voor het back-upbestand. 

4. Selecteer opties in het dialoogvenster **Back-updatabase.**

    **Bestand overschrijven toestaan** - Selecteer deze optie om back-upbestanden met dezelfde naam te overschrijven. Als deze optie niet is geselecteerd, kan het bestand dat u opslaat niet dezelfde naam hebben als een bestand dat al op dezelfde locatie bestaat.

    **Compressie toepassen** : Selecteer deze optie om het back-upbestand te comprimeren. Gecomprimeerde back-upbestanden besparen schijfruimte, maar vereisen een iets hoger CPU-gebruik. 

    **Back-upbestand versleutelen** : selecteer deze optie om het back-upbestand te versleutelen. Voor deze optie is een door de gebruiker opgegeven wachtwoord vereist om het back-upbestand te beveiligen. Het wachtwoord voorkomt het lezen van de back-upgegevens op een andere manier dan een herstelbewerking. Als u ervoor kiest back-ups te versleutelen, slaat u het wachtwoord op een veilige locatie op.

5. Klik op **OK** om het back-upbestand te maken en op te slaan.


### <a name="powershell"></a>PowerShell
Gebruik de cmdlet [Backup-ASDatabase.](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)

## <a name="restore"></a>Herstellen
Bij het herstellen moet uw back-upbestand zich in het opslagaccount bevindt dat u voor uw server hebt geconfigureerd. Als u een back-upbestand van een on-premises locatie naar uw opslagaccount wilt verplaatsen, gebruikt u [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) of het [azcopy-hulpprogramma.](../storage/common/storage-use-azcopy.md) 



> [!NOTE]
> Als u herstelt vanaf een on-premises server, moet u alle domeingebruikers uit de rollen van het model verwijderen en deze weer toevoegen aan de rollen als Azure Active Directory-gebruikers.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Herstellen met SSMS

1. Klik in SSMS met de rechtermuisknop op een database > **Herstellen**.

2. Klik in het dialoogvenster **Back-updatabase** in **back-upbestand**op **Bladeren**.

3. Selecteer in het dialoogvenster **Databasebestanden zoeken** het bestand dat u wilt herstellen.

4. Selecteer in **Database herstellen**de database.

5. Opties opgeven. Beveiligingsopties moeten overeenkomen met de back-upopties die u hebt gebruikt bij het maken van een back-up.


### <a name="powershell"></a>PowerShell

Gebruik de cmdlet [Restore-ASDatabase.](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)


## <a name="related-information"></a>Gerelateerde informatie

[Azure-opslagaccounts](../storage/common/storage-create-storage-account.md)  
[Hoge beschikbaarheid](analysis-services-bcdr.md)     
[Azure-analyseservices beheren](analysis-services-manage.md)
