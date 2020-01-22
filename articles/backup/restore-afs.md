---
title: Azure-bestands shares herstellen
description: Meer informatie over het gebruik van de Azure Portal om een volledige bestands share of specifieke bestanden te herstellen vanaf een herstel punt dat is gemaakt door Azure Backup-service.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: b16eb4120ff2d269135ae8ae6555ef4fdbdbda5d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294394"
---
# <a name="restore-azure-file-shares"></a>Azure-bestands shares herstellen

In dit artikel wordt uitgelegd hoe u de Azure Portal kunt gebruiken om een volledige bestands share of specifieke bestanden te herstellen vanaf een herstel punt dat is gemaakt door de [Azure backup](https://docs.microsoft.com/azure/backup/backup-overview) -service.

In deze hand leiding leert u het volgende:

* Een volledige Azure-bestands share herstellen
* Afzonderlijke bestanden of mappen herstellen
* De status van de herstel bewerking bijhouden

## <a name="steps-to-perform-restore"></a>Stappen voor het uitvoeren van herstellen

### <a name="select-the-file-share-to-restore"></a>Selecteer de bestands share die u wilt herstellen

1. Open in de [Azure Portal](https://portal.azure.com/)de Recovery Services kluis die u hebt gebruikt voor het configureren van de back-up voor de bestands share.

2. Klik op **Back-upitems** in de sectie **beveiligde items** van de Blade **overzicht** .

    ![Klik op Back-upitems](./media/restore-afs/backup-items.png)

3. Zodra u op **Back-upitems**klikt, wordt een nieuwe blade met alle typen back-upbeheer weer gegeven naast de Blade **overzicht** :

    ![Typen back-upbeheer](./media/restore-afs/backup-management.png)

4. In **Back-upitems**, onder **type back-upbeheer**, selecteert u **Azure Storage (Azure files)** . U ziet een lijst met alle bestands shares en de bijbehorende opslag accounts waarvan een back-up is gemaakt met behulp van deze kluis.

    ![Lijst met alle bestands shares](./media/restore-afs/file-shares.png)

5. Selecteer in de lijst met Azure-bestands shares de gewenste bestands share waarvoor u de herstel bewerking wilt uitvoeren.

### <a name="full-share-recovery"></a>Herstel van volledige share

U kunt deze terugzet optie gebruiken om de volledige bestands share te herstellen op de oorspronkelijke of een alternatieve locatie.

1. Selecteer de optie **share terugzetten** op de Blade **back-upitem** die wordt weer gegeven nadat u de gewenste bestands share hebt geselecteerd om terug te zetten in stap 5 van de sectie [Selecteer de bestands share die u wilt terugzetten](#select-the-file-share-to-restore) .

   ![Share terugzetten selecteren](./media/restore-afs/restore-share.png)

2. Zodra u op **delen herstellen**klikt, wordt de Blade **herstellen** geopend met een **herstel punt** menu met een lijst met herstel punten die beschikbaar zijn voor de geselecteerde bestands share.

3. Selecteer het herstel punt dat u wilt gebruiken voor het uitvoeren van de herstel bewerking en klik op **OK**.

    ![Herstel punt selecteren](./media/restore-afs/restore-point.png)

4. Nadat u op OK hebt geklikt, wordt in het menu Blade herstellen overgeschakeld naar de **locatie voor terugzetten**. Geef bij **terugzet locatie**op waar (of hoe) de gegevens moeten worden hersteld. U kunt een van de volgende twee opties kiezen:

    * **Oorspronkelijke locatie**: herstel de volledige bestands share naar dezelfde locatie als de oorspronkelijke bron.
    * **Alternatieve locatie**: herstel de volledige **Bestands share naar**een andere locatie en behoud de oorspronkelijke bestands share.

#### <a name="restore-to-original-location"></a>Herstellen naar de oorspronkelijke locatie

1. Kies **oorspronkelijke locatie** als **herstel doel** en selecteer of u wilt overs Laan of overschrijven als er conflicten zijn. Klik op **OK** nadat u de gewenste selectie hebt gemaakt.

    ![Oorspronkelijke locatie kiezen](./media/restore-afs/original-location.png)

2. Klik op **herstellen** om de herstel bewerking te starten.

    ![Klik op herstellen om te starten](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Herstellen naar een alternatieve locatie

1. Kies **alternatieve locatie** als herstel bestemming.
2. Selecteer het doel-opslag account, waar u de inhoud van de back-up wilt herstellen, in de vervolg keuzelijst van het veld **opslag account** .
3. Op basis van het opslag account dat u in stap 2 hebt geselecteerd, wordt in de vervolg keuzelijst **Bestands share selecteren** de lijst met bestands shares weer gegeven die aanwezig zijn in het geselecteerde opslag account. Selecteer de bestands share waar u de inhoud van de back-up wilt terugzetten.
4. Geef in het veld **mapnaam** de naam op van de map die u in de doel bestands share wilt maken met de herstelde inhoud.
5. Selecteer of u wilt overs Laan of overschrijven als er conflicten zijn.
6. Klik op **OK** nadat u de juiste waarden hebt ingevoerd in alle velden.

    ![Alternatieve locatie selecteren](./media/restore-afs/alternate-location.png)

7. Klik op herstellen om de herstel bewerking te starten.

    ![Klik op herstellen om te starten](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Herstel op item niveau

U kunt deze terugzet optie gebruiken om afzonderlijke bestanden of mappen op de oorspronkelijke of een alternatieve locatie te herstellen.

1. Selecteer de optie **bestands herstel** op de Blade **back-upitem** die wordt weer gegeven na het selecteren van de gewenste bestands share in stap 5 van de sectie [Selecteer de bestands share die u wilt terugzetten](#select-the-file-share-to-restore) .

    ![Bestands herstel selecteren](./media/restore-afs/file-recovery.png)

2. Wanneer u op **File Recovery**klikt, wordt de Blade **herstellen** geopend met een **herstel punt** menu met een lijst met herstel punten die beschikbaar zijn voor de geselecteerde bestands share.

3. Selecteer het herstel punt dat u wilt gebruiken voor het uitvoeren van de herstel bewerking en klik op **OK**.

    ![Herstel punt selecteren](./media/restore-afs/restore-point.png)

4. Nadat u op OK hebt geklikt, wordt in het menu Blade herstellen overgeschakeld naar de **locatie voor terugzetten**. Geef bij **terugzet locatie**op waar (of hoe) de gegevens moeten worden hersteld. U kunt een van de volgende twee opties kiezen:

    * **Oorspronkelijke locatie**: de geselecteerde bestanden/mappen herstellen naar dezelfde bestands share als de oorspronkelijke bron.
    * **Alternatieve locatie**: geselecteerde bestanden/mappen terugzetten op een andere locatie en de oorspronkelijke bestands share **-inhoud blijven**gebruiken.

#### <a name="restore-to-original-location"></a>Herstellen naar de oorspronkelijke locatie

1. Kies **oorspronkelijke locatie** als **herstel doel** en selecteer of u wilt overs Laan of overschrijven als er conflicten zijn.

    ![Oorspronkelijke locatie voor herstel op item niveau](./media/restore-afs/original-location-item-level.png)

2. Klik op **bestand selecteren** om de bestanden/mappen te kiezen die u wilt herstellen.

    ![Klik op bestand selecteren](./media/restore-afs/select-file.png)

3. Wanneer u klikt op **bestand selecteren**, Blade bestands share, wordt de inhoud weer gegeven van het herstel punt dat u hebt geselecteerd voor herstellen.

4. Schakel het selectie vakje in dat overeenkomt met het bestand dat of de map die u wilt herstellen en klik op **selecteren**.

    ![Bestand of map selecteren](./media/restore-afs/select-file-folder.png)

5. Herhaal stap 2-4 om meerdere bestanden/mappen te selecteren die u wilt herstellen.
6. Klik op **OK**nadat u alle items hebt geselecteerd die u wilt herstellen.

    ![Klik op OK nadat u alle items hebt geselecteerd die u wilt herstellen](./media/restore-afs/after-selecting-items.png)

7. Klik op herstellen om de herstel bewerking te starten.

    ![Klik op herstellen om te starten](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Herstellen naar een alternatieve locatie

1. Kies **alternatieve locatie** als herstel bestemming.
2. Selecteer het doel-opslag account, waar u de inhoud van de back-up wilt herstellen, in de vervolg keuzelijst van het veld **opslag account** .
3. Op basis van het opslag account dat u in stap 2 hebt geselecteerd, wordt in de vervolg keuzelijst **Bestands share selecteren** de lijst weer gegeven met de bestands shares die aanwezig zijn in het geselecteerde opslag account. Selecteer de bestands share waar u de inhoud van de back-up wilt terugzetten.
4. Geef in het veld **mapnaam** de naam op van de map die u in de doel bestands share wilt maken met de herstelde inhoud.
5. Selecteer of u wilt overs Laan of overschrijven als er conflicten zijn.
6. Klik op **bestand selecteren** om de bestanden/mappen te kiezen die u wilt herstellen.

    ![Selecteer items om terug te zetten naar een alternatieve locatie](./media/restore-afs/restore-to-alternate-location.png)

7. Wanneer u klikt op **bestand selecteren**, Blade bestands share, wordt de inhoud weer gegeven van het herstel punt dat u hebt geselecteerd voor herstellen.
8. Schakel het selectie vakje in dat overeenkomt met het bestand dat of de map die u wilt herstellen en klik op **selecteren**.

    ![Herstel bestemming selecteren](./media/restore-afs/recovery-destination.png)

9. Herhaal stap 6-8 om meerdere bestanden/mappen te selecteren die u wilt herstellen.
10. Klik op **OK**nadat u alle items hebt geselecteerd die u wilt herstellen.

    [Klik op OK nadat u alle bestanden hebt geselecteerd](./media/restore-afs/after-selecting-all-items.png)

11. Klik op **herstellen** om de herstel bewerking te starten.

## <a name="track-restore-operation"></a>Herstel bewerking bijhouden

Nadat u de herstel bewerking hebt geactiveerd, maakt de back-upservice een taak voor tracering. Azure Backup worden meldingen over de taak weer gegeven in de portal. Als u bewerkingen voor de taak wilt weer geven, klikt u op de koppeling meldingen.

![Klik op de Hyper Link meldingen](./media/restore-afs/notifications-link.png)

U kunt ook de voortgang van herstel bewaken vanuit de Recovery Services-kluis. Hier volgen de stappen voor het controleren van de status van de herstel bewerking:

1. Open de Recovery Services kluis van waaruit u de herstel bewerking hebt geactiveerd.
2. Klik op **back-uptaken** onder het **gedeelte bewaking** van de Blade **overzicht** voor een overzicht van de status van de bewerkingen die worden uitgevoerd op verschillende werk belastingen.

    ![Klik op back-uptaken](./media/restore-afs/backup-jobs.png)

3. Klik op de naam van de werk belasting die overeenkomt met de bestands share om meer informatie over de herstel bewerking weer te geven, zoals overgedragen gegevens, het aantal herstelde bestanden, enzovoort.

    ![Details van hersteld weer geven](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [beheren van back-ups van Azure-bestands shares](manage-afs-backup.md)
