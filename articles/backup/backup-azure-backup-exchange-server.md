---
title: Een back-up maken van een Exchange-server via System Center DPM
description: Meer informatie over het maken van een back-up van een Exchange-server naar Azure Backup met System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: b45efa0623edbec47b8ae12d3a97b1e032626530
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396407"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Met System Center 2012 R2 DPM een back-up maken van een Exchange-server in Azure Backup

In dit artikel wordt beschreven hoe u een DPM-server (System Center 2012 R2 Data Protection Manager) configureert om een back-up te maken van een Microsoft Exchange-server naar Azure Backup.  

## <a name="updates"></a>Updates

Als u de DPM-server met Azure Backup wilt registreren, moet u de nieuwste updaterollup voor System Center 2012 R2 DPM en de nieuwste versie van de Azure Backup Agent installeren. Download de nieuwste updaterollup uit de [Microsoft-catalogus.](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)

> [!NOTE]
> Voor de voorbeelden in dit artikel is versie 2.0.8719.0 van de Azure Backup Agent geïnstalleerd en is Update Rollup 6 geïnstalleerd op System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Vereisten

Voordat u verdergaat, moet u ervoor zorgen dat aan alle [vereisten](backup-azure-dpm-introduction.md#prerequisites-and-limitations) voor het gebruik van Microsoft Azure Backup is voldaan om workloads te beschermen. Deze voorwaarden omvatten de volgende voorwaarden:

* Er is een back-upkluis op de Azure-site gemaakt.
* Agent- en kluisreferenties zijn gedownload naar de DPM-server.
* De agent is geïnstalleerd op de DPM-server.
* De vault credentials werden gebruikt om de DPM-server te registreren.
* Als u Exchange 2016 beschermt, u upgraden naar DPM 2012 R2 UR9 of hoger

## <a name="dpm-protection-agent"></a>DPM-beveiligingsmiddel

Voer de volgende stappen uit om de DPM-beveiligingsagent op de Exchange-server te installeren:

1. Zorg ervoor dat de firewalls correct zijn geconfigureerd. Zie [Firewalluitzonderingen configureren voor de agent](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Installeer de agent op de Exchange-server door te klikken op **Beheer > Agents > installeren** in de DPM Administrator Console. Zie [De DPM-beveiligingsagent installeren](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) voor gedetailleerde stappen.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Een beveiligingsgroep maken voor de Exchange-server

1. Klik in de DPM Administrator Console op **Beveiliging**en klik vervolgens op **Nieuw** op het gereedschapslint om de wizard **Nieuwe beveiligingsgroep maken** te openen.
2. Klik op het **welkomstscherm** van de wizard op **Volgende**.
3. Selecteer op het typetype **Selecteren** in het type **groep Servers** en klik op **Volgende**.
4. Selecteer de Exchange-serverdatabase die u wilt beveiligen en klik op **Volgende**.

   > [!NOTE]
   > Als u Exchange 2013 beschermt, controleert u de [vereisten voor Exchange 2013.](https://docs.microsoft.com/system-center/dpm/back-up-exchange?view=sc-dpm-2016)
   >
   >

    In het volgende voorbeeld wordt de Exchange 2010-database geselecteerd.

    ![Groepsleden selecteren](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecteer de methode voor gegevensbescherming.

    Geef de beveiligingsgroep een naam en selecteer beide opties:

   * Ik wil bescherming op korte termijn met Schijf.
   * Ik wil online bescherming.
6. Klik op **Volgende**.
7. Selecteer de optie **Eseutil uitvoeren om de integriteit van gegevens te controleren** als u de integriteit van de Exchange Server-databases wilt controleren.

    Nadat u deze optie hebt geselecteerd, wordt de controle van de back-upconsistentie uitgevoerd op de DPM-server om het I/O-verkeer te vermijden dat wordt gegenereerd door de **opdracht eseutil** uit te voeren op de Exchange-server.

   > [!NOTE]
   > Als u deze optie wilt gebruiken, moet u de bestanden Ese.dll en Eseutil.exe kopiëren naar de C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin directory op de DPM-server. Anders wordt de volgende fout geactiveerd:  
   > ![eseutil fout](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Klik op **Volgende**.
9. Selecteer de database voor **Back-up kopiëren**en klik op **Volgende**.

   > [!NOTE]
   > Als u 'Volledige back-up' niet selecteert voor ten minste één DAG-kopie van een database, worden logboeken niet afgekapt.
   >
   >
10. Configureer de doelen voor **back-up op korte termijn**en klik op **Volgende**.
11. Controleer de beschikbare schijfruimte en klik op **Volgende**.
12. Selecteer het tijdstip waarop de DPM-server de eerste replicatie maakt en klik op **Volgende**.
13. Selecteer de opties voor consistentiecontrole en klik op **Volgende**.
14. Kies de database waarvan u een back-up wilt maken naar Azure en klik op **Volgende**. Bijvoorbeeld:

    ![Onlinebeveiligingsgegevens opgeven](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definieer de planning voor **Azure Backup**en klik op **Volgende**. Bijvoorbeeld:

    ![Online back-upschema opgeven](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Opmerking Online herstelpunten zijn gebaseerd op express volledige herstelpunten. Daarom moet u het online herstelpunt plannen na de tijd die is opgegeven voor het uitdrukkelijke volledige herstelpunt.
    >
    >
16. Configureer het bewaarbeleid voor **Azure Backup**en klik op **Volgende**.
17. Kies een online replicatieoptie en klik op **Volgende**.

    Als u een grote database hebt, kan het lang duren voordat de eerste back-up via het netwerk wordt gemaakt. Om dit probleem te voorkomen, u een offline back-up maken.  

    ![Online bewaarbeleid opgeven](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Bevestig de instellingen en klik op **Groep maken**.
19. Klik op **Sluiten**.

## <a name="recover-the-exchange-database"></a>De Exchange-database herstellen

1. Als u een Exchange-database wilt herstellen, klikt u op **Herstel** in de DPM Administrator Console.
2. Zoek de Exchange-database die u wilt herstellen.
3. Selecteer een online herstelpunt in de vervolgkeuzelijst *hersteltijd.*
4. Klik **op Herstellen** om de wizard **Herstel**te starten.

Voor online herstelpunten zijn er vijf hersteltypen:

* **Herstellen naar de oorspronkelijke Exchange Server-locatie:** De gegevens worden hersteld naar de oorspronkelijke Exchange-server.
* **Herstellen naar een andere database op een Exchange Server:** De gegevens worden hersteld naar een andere database op een andere Exchange-server.
* **Herstellen naar een hersteldatabase:** De gegevens worden hersteld naar een Exchange Recovery Database (RDB).
* **Kopiëren naar een netwerkmap:** De gegevens worden hersteld naar een netwerkmap.
* **Kopiëren naar tape:** Als u een tapebibliotheek of een zelfstandige tapedrive hebt, is het herstelpunt gekopieerd naar een gratis tape.

    ![Online replicatie kiezen](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Volgende stappen

* [Veelgestelde vragen over Azure Backup](backup-azure-backup-faq.md)
