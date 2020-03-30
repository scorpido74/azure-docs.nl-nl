---
title: Back-ups maken van bestanden in Azure Stack VM's
description: Gebruik Azure Backup om een back-up te maken en Azure Stack-bestanden en -toepassingen te herstellen naar uw Azure Stack-omgeving.
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: 2bcdf7f720708db1487d7d5cdaee41dc93c05728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172321"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Back-ups maken van bestanden en toepassingen op Azure Stack

U Azure Backup gebruiken om bestanden en toepassingen op Azure Stack te beveiligen (of een back-up te maken). Als u een back-up wilt maken van bestanden en toepassingen, installeert u Microsoft Azure Backup Server als een virtuele machine die wordt uitgevoerd op Azure Stack. U de bestanden op elke Azure Stack-server in hetzelfde virtuele netwerk beveiligen. Zodra u Azure Backup Server hebt geïnstalleerd, voegt u Azure-schijven toe om de lokale opslag te vergroten die beschikbaar is voor back-upgegevens op korte termijn. Azure Backup Server maakt gebruik van Azure-opslag voor langdurige bewaring.

> [!NOTE]
> Hoewel Azure Backup Server en System Center Data Protection Manager (DPM) vergelijkbaar zijn, wordt DPM niet ondersteund voor gebruik met Azure Stack.
>

Dit artikel heeft geen betrekking op het installeren van Azure Backup Server in de Azure Stack-omgeving. Als u Azure Backup Server wilt installeren op Azure Stack, raadpleegt u het artikel, [Azure Backup Server installeren](backup-mabs-install-azure-stack.md).

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Back-ups maken van bestanden en mappen in Azure Stack VM's naar Azure

Als u Azure Backup Server wilt configureren om bestanden in virtuele Azure Stack-machines te beveiligen, opent u de Azure Backup Server-console. U gebruikt de console om beveiligingsgroepen te configureren en de gegevens op uw virtuele machines te beschermen.

1. Klik in de Azure Backup Server-console op **Beveiliging** en klik op de werkbalk op **Nieuw** om de wizard **Nieuwe beveiligingsgroep maken** te openen.

   ![Beveiliging configureren in Azure Backup Server-console](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Het kan enkele seconden duren voordat de wizard is geopend. Zodra de wizard is geopend, klikt u op **Volgende** om door te gaan naar het scherm **Type beveiligingsgroep selecteren.**

   ![Wizard Nieuwe groep Beveiliging wordt geopend](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Kies op het scherm **Type beveiligingsgroep** selecteren de optie **Servers** en klik op **Volgende**.

    ![Wizard Nieuwe groep Beveiliging wordt geopend](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Het scherm **Groepsleden selecteren** wordt geopend.

    ![Wizard Nieuwe groep Beveiliging wordt geopend](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Klik in het scherm **+** **Groepsleden selecteren** om de lijst met subitems uit te vouwen. Schakel het selectievakje in voor alle items die u wilt beveiligen. Klik op **Volgende**.

    ![Wizard Nieuwe groep Beveiliging wordt geopend](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Microsoft raadt aan om alle gegevens die een beschermingsbeleid delen, in één beschermingsgroep te plaatsen. Zie het DPM-artikel Systeemcentrum, [Beveiligingsgroepen implementeren voor](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801)volledige informatie over het plannen en implementeren van beveiligingsgroepen.

4. Typ in het scherm **Gegevensbeschermingsmethode selecteren** een naam voor de beveiligingsgroep. Schakel het selectievakje in voor **ik wil bescherming op korte termijn met:** en ik wil online **bescherming**. Klik op **Volgende**.

    ![Wizard Nieuwe groep Beveiliging wordt geopend](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Als u **Wilt selecteren Wil ik online bescherming**, moet u eerst selecteren Ik wil bescherming op korte termijn met behulp **van:** Schijf. Azure Backup Server beschermt niet tegen tape, dus schijf is de enige keuze voor bescherming op korte termijn.

5. Kies in het scherm **Doelen op korte termijn opgeven** hoe lang de herstelpunten die op de schijf zijn opgeslagen, behouden en wanneer incrementele back-ups moeten worden opgeslagen. Klik op **Volgende**.

    > [!IMPORTANT]
    > U mag **niet** langer dan vijf dagen operationele herstelgegevens (back-upgegevens) bewaren op schijven die aan azure backup server zijn gekoppeld.
    >

    ![Wizard Nieuwe groep Beveiliging wordt geopend](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    In plaats van een interval te selecteren voor incrementele back-ups, klikt u op **Vlak voor een herstelpunt**om een volledige uitdrukkelijke back-up uit te voeren. Als u toepassingsworkloads beschermt, maakt Azure Backup Server herstelpunten volgens het frequentieschema synchronisatie (mits de toepassing incrementele back-ups ondersteunt). Als de toepassing geen incrementele back-ups ondersteunt, voert Azure Backup Server een volledige back-up met een uitdrukkelijke back-up uit.

    Geef **voor Bestandsherstelpunten**op wanneer herstelpunten moeten worden gemaakt. Klik **op Wijzigen** om de tijden en dagen van de week in te stellen waarop herstelpunten worden gemaakt.

6. Controleer in het scherm **Schijftoewijzing controleren** de schijfruimte voor de opslaggroep die is toegewezen aan de beveiligingsgroep.

    **De totale gegevensgrootte** is de grootte van de gegevens waarvan u een back-up wilt maken en **schijfruimte die moet worden ingericht** op Azure Backup Server is de aanbevolen ruimte voor de beveiligingsgroep. Azure Backup Server kiest het ideale back-upvolume op basis van de instellingen. U kunt echter de keuzes voor het back-upvolume bewerken in Schijftoewijzingsdetails. Selecteer voor de werkbelastingen de gewenste opslag in het vervolgkeuzemenu. Met uw bewerkingen worden de waarden voor Totale opslag en Vrije opslagruimte in het deelvenster Beschikbare schijfopslag gewijzigd. Ondergeformeerde ruimte is de hoeveelheid opslagruimte die Azure Backup Server voorstelt toe te voegen aan het volume, om in de toekomst probleemloos door te gaan met back-ups.

7. Selecteer in **De methode Voor het maken van replica's**hoe u de eerste volledige gegevensreplicatie wilt verwerken. Als u besluit te repliceren via het netwerk, raadt Azure u aan een daluren te kiezen. Voor grote hoeveelheden gegevens of minder dan optimale netwerkomstandigheden u overwegen de gegevens te repliceren met behulp van verwisselbare media.

8. Selecteer in **Opties voor consistentiecontrole selecteren** hoe u consistentiecontroles wilt automatiseren. Schakel consistentiecontroles alleen uit wanneer de gegevensreplicatie inconsistent wordt of volgens een planning. Als u automatische consistentiecontrole niet wilt configureren, voert u op elk gewenst moment een handmatige controle uit door:
    * Klik in het **gebied Beveiliging** van de Azure Backup Server-console met de rechtermuisknop op de beveiligingsgroep en selecteer **Consistentiecontrole uitvoeren**.

9. Als u ervoor kiest een back-up te maken met Azure, controleert u op de pagina **Online beveiligingsgegevens opgeven** of de workloads die u wilt back-ups maken met Azure, zijn geselecteerd.

10. Geef in **Online back-upschema opgeven**op wanneer incrementele back-ups naar Azure moeten worden uitgevoerd.

    U kunt back-ups dagelijks/wekelijks/maandelijks/jaarlijks uitvoeren en de tijd en datum opgeven waarop u deze wilt uitvoeren. U kunt maximaal twee keer per dag back-ups uitvoeren. Elke keer dat een back-uptaak wordt uitgevoerd, wordt in Azure een gegevensherstelpunt gemaakt op basis van de kopie van de back-upgegevens die zijn opgeslagen op de Azure Backup Server-schijf.

11. Geef **in Online bewaarbeleid opgeven**hoe de herstelpunten die zijn gemaakt met de dagelijkse/wekelijkse/maandelijkse/jaarlijkse back-ups in Azure worden bewaard.

12. Geef **in Online replicatie kiezen**op hoe de eerste volledige replicatie van gegevens plaatsvindt.

13. Bekijk **in Overzicht**uw instellingen. Wanneer u op **Groep maken**klikt, vindt de eerste gegevensreplicatie plaats. Wanneer de gegevensreplicatie is voltooid, wordt op de pagina **Status** de status van de beveiligingsgroep weergegeven als **OK**. De eerste back-uptaak vindt plaats in overeenstemming met de instellingen van de beveiligingsgroep.

## <a name="recover-file-data"></a>Bestandsgegevens herstellen

Gebruik azure backup server-console om gegevens naar uw virtuele machine te herstellen.

1. Klik op de Azure Backup Server-console op de navigatiebalk op **Herstel** en blader naar de gegevens die u wilt herstellen. Selecteer de gewenste gegevens in het resultatenvenster.

2. In de agenda in de sectie Herstelpunten geven datums in vet aan dat herstelpunten beschikbaar zijn. Selecteer de datum die u wilt herstellen.

3. Selecteer in het deelvenster **Herstelbare items** het item dat u wilt herstellen.

4. Klik in het deelvenster **Acties** op **Herstellen** om de wizard Herstellen te openen.

5. U kunt gegevens als volgt herstellen:

    * **Herstellen naar de oorspronkelijke locatie** - Als de clientcomputer via VPN is verbonden, werkt deze optie niet. Gebruik in plaats daarvan een alternatieve locatie en kopieer vervolgens gegevens van die locatie.
    * **Herstellen naar een alternatieve locatie**

6. Geef de herstelopties op:

    * Selecteer Voor **bestaand herstelgedrag**van de versie de optie **Kopiëren**maken, **Overslaan**of **Overschrijven**. Overschrijven is alleen beschikbaar bij het herstellen naar de oorspronkelijke locatie.
    * Kies **Voor Beveiliging herstellen**de optie Instellingen van de **doelcomputer toepassen** of De **beveiligingsinstellingen van de versie van het herstelpunt toepassen**.
    * Klik op **Wijzigen** om beperking van het gebruik van netwerkbandbreedte in te schakelen voor beperking van het gebruik van de **netwerkbandbreedte.**
    * **Kennisgeving** Klik **op Een e-mail verzenden wanneer het herstel is voltooid**en geef de ontvangers op die de melding ontvangen. Scheid de e-mailadressen met komma's.
    * Klik na het maken van de selecties op **Volgende**

7. Controleer uw herstelinstellingen en klik op **Herstellen**.

    >[!Note]
    >Terwijl de hersteltaak aan de gang is, worden alle synchronisatietaken voor de geselecteerde herstelitems geannuleerd.

Als u MBS (Modern Backup Storage) gebruikt, wordt het herstel van de eindgebruiker van De eindgebruiker (EUR) niet ondersteund. File Server EUR is afhankelijk van De Shadow Copy Service (Volume Shadow Copy Service), die moderne back-upopslag niet gebruikt. Als EUR is ingeschakeld, gebruikt u de volgende stappen om gegevens te herstellen:

1. Navigeer naar de beveiligde bestanden en klik met de rechtermuisknop op de bestandsnaam en selecteer **Eigenschappen**.

2. Klik **in** het menu Eigenschappen op **Vorige versies** en kies de versie die u wilt herstellen.

## <a name="view-azure-backup-server-with-a-vault"></a>Azure Backup Server weergeven met een kluis

Als u Azure Backup Server-entiteiten in de Azure-portal wilt weergeven, u de volgende stappen volgen:

1. Kluis van Herstelservices openen.
2. Klik op Back-upinfrastructuur.
3. Back-upbeheerservers weergeven.

## <a name="next-steps"></a>Volgende stappen

Zie een van de volgende artikelen voor informatie over het gebruik van Azure Backup Server om andere workloads te beschermen:

* [Back-ups van SharePoint-farm](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
* [Een back-up maken van SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
