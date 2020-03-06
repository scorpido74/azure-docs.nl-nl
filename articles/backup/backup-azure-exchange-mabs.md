---
title: Back-up maken van Exchange server met Azure Backup Server
description: Meer informatie over het maken van een back-up van een Exchange-Server naar Azure Backup met Azure Backup Server
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 9e623b1bdce93c340ccd0e61f9f5145e7154beff
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78295841"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Een back-up maken van een Exchange-Server naar Azure met Azure Backup Server

In dit artikel wordt beschreven hoe u Microsoft Azure Backup Server (MABS) configureert om een back-up te maken van een micro soft Exchange-Server naar Azure.  

## <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, moet u ervoor zorgen dat Azure Backup Server is [geïnstalleerd en voor bereid](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>MABS-beveiligings agent

Voer de volgende stappen uit om de MABS-beveiligings agent te installeren op de Exchange-Server:

1. Zorg ervoor dat de firewalls correct zijn geconfigureerd. Zie [firewall-uitzonde ringen voor de agent configureren](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Installeer de agent op de Exchange-Server door te klikken op **beheer > agents > geïnstalleerd** in de MABS-beheerders console. Zie [de MABS-beveiligings agent installeren](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) voor gedetailleerde stappen.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Een beveiligings groep maken voor de Exchange-Server

1. Klik in de MABS Administrator-console op **beveiliging**en klik vervolgens op **Nieuw** op het lint met hulp middelen om de wizard **nieuwe beveiligings groep maken** te openen.
2. Klik in het **welkomst** scherm van de wizard op **volgende**.
3. Selecteer op het scherm **type beveiligings groep selecteren** de optie **servers** en klik op **volgende**.
4. Selecteer de Exchange Server-Data Base die u wilt beveiligen en klik op **volgende**.

   > [!NOTE]
   > Als u Exchange 2013 beveiligt, controleert u de [vereisten voor exchange 2013](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/dn751029(v=sc.12)).
   >
   >

    In het volgende voor beeld is de Exchange 2010-data base geselecteerd.

    ![Groeps leden selecteren](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecteer de methode voor gegevens beveiliging.

    Geef de beveiligings groep een naam en selecteer beide van de volgende opties:

   * Ik wil kortetermijnbeveiliging met schijf.
   * Ik wil online beveiliging.
6. Klik op **Volgende**.
7. Selecteer de optie **Eseutil uitvoeren om gegevens integriteit te controleren** als u de integriteit van de Exchange server-data bases wilt controleren.

    Nadat u deze optie hebt geselecteerd, wordt de consistentie controle van de back-up op MABS uitgevoerd om te voor komen dat het I/O-verkeer dat wordt gegenereerd door de opdracht **Eseutil** uit te voeren op de Exchange-Server.

   > [!NOTE]
   > Als u deze optie wilt gebruiken, moet u de bestanden ESE. dll en Eseutil. exe kopiëren naar de map C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin op de MAB-server. Anders wordt de volgende fout geactiveerd:  
   > ![Eseutil-fout](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Klik op **Volgende**.
9. Selecteer de Data Base voor **back-up kopiëren**en klik vervolgens op **volgende**.

   > [!NOTE]
   > Als u geen volledige back-up voor ten minste één DAG kopie van een Data Base selecteert, worden logboeken niet afgekapt.
   >
   >
10. Configureer de doel stellingen voor **back-up op korte termijn**en klik vervolgens op **volgende**.
11. Controleer de beschik bare schijf ruimte en klik vervolgens op **volgende**.
12. Selecteer het tijdstip waarop de MAB-server de initiële replicatie zal maken en klik vervolgens op **volgende**.
13. Selecteer de opties voor consistentie controle en klik vervolgens op **volgende**.
14. Kies de data base waarvan u een back-up wilt maken naar Azure en klik vervolgens op **volgende**. Bijvoorbeeld:

    ![Gegevens voor online beveiliging opgeven](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definieer het schema voor **Azure backup**en klik vervolgens op **volgende**. Bijvoorbeeld:

    ![Online back-upschema opgeven](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Houd er rekening mee dat online herstel punten zijn gebaseerd op snelle volledige herstel punten. Daarom moet u het online herstel punt plannen na het tijdstip dat is opgegeven voor het snelle volledige herstel punt.
    >
    >
16. Configureer het Bewaar beleid voor **Azure backup**en klik vervolgens op **volgende**.
17. Kies een optie voor online replicatie en klik op **volgende**.

    Als u een grote data base hebt, kan het enige tijd duren voordat de eerste back-up via het netwerk is gemaakt. Als u dit probleem wilt voor komen, kunt u een offline back-up maken.  

    ![Online Bewaar beleid opgeven](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Bevestig de instellingen en klik vervolgens op **groep maken**.
19. Klik op **Sluiten**.

## <a name="recover-the-exchange-database"></a>De Exchange-data base herstellen

1. Als u een Exchange-Data Base wilt herstellen, klikt u in de MABS Administrator-console op **herstellen** .
2. Zoek de Exchange-data base die u wilt herstellen.
3. Selecteer een online herstel punt in de vervolg keuzelijst *herstel tijd* .
4. Klik op **herstellen** om de **wizard herstellen**te starten.

Voor online herstel punten zijn er vijf herstel typen:

* **Herstellen naar de oorspronkelijke Exchange Server-locatie:** De gegevens worden hersteld op de oorspronkelijke Exchange-Server.
* **Herstellen naar een andere Data Base op een Exchange-Server:** De gegevens worden hersteld naar een andere Data Base op een andere Exchange-Server.
* **Herstellen naar een herstel database:** De gegevens worden hersteld naar een Exchange Recovery-Data Base (RDB).
* **Kopiëren naar een** netwerkmap: De gegevens worden hersteld naar een netwerkmap.
* **Kopiëren naar tape:** Als u een tape wisselaar of een zelfstandig tape station hebt aangesloten en geconfigureerd op MABS, wordt het herstel punt naar een vrije tape gekopieerd.

    ![Kies online replicatie](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Volgende stappen

* [Veelgestelde vragen over Azure Backup](backup-azure-backup-faq.md)
