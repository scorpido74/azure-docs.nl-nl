---
title: 'Zelf studie: back-ups maken van SQL Server-data bases naar Azure'
description: In deze zelf studie leert u hoe u een back-up maakt van een SQL Server-Data Base die wordt uitgevoerd op een virtuele Azure-machine naar een Azure Backup Recovery Services kluis.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 96a9940826570bd687fdef5726bdc103139f9902
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074665"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>Back-ups maken van een SQL Server-data base in een Azure VM

In deze zelf studie wordt uitgelegd hoe u een back-up maakt van een SQL Server-Data Base die wordt uitgevoerd op een virtuele Azure-machine naar een Azure Backup Recovery Services kluis. In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een kluis maken en configureren.
> * Databases detecteren en back-ups instellen.
> * Automatische beveiliging van databases instellen.
> * Een back-up op aanvraag uitvoeren.

## <a name="prerequisites"></a>Vereisten

Voordat u een back-up van uw SQL Server-database maakt, controleert u de volgende voorwaarden:

1. Identificeer of [Maak](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) een Recovery Services kluis in dezelfde regio of locatie als de virtuele machine die als host fungeert voor het SQL Server exemplaar.
2. [Controleer de VM-machtigingen](backup-azure-sql-database.md#set-vm-permissions) die nodig zijn voor de back-up van de SQL-databases.
3. Controleer of de virtuele machine [netwerkverbinding](backup-sql-server-database-azure-vms.md#establish-network-connectivity) heeft.
4. Controleer of de SQL Server-databases zijn benoemd in overeenstemming met de [naamgevingsrichtlijnen](#verify-database-naming-guidelines-for-azure-backup) van Azure Backup.
5. Controleer of er geen andere back-upoplossingen zijn ingeschakeld voor de database. Schakel alle andere SQL Server-back-ups uit voordat u dit scenario instelt. U kunt zonder problemen Azure Backup inschakelen voor een Azure-VM en tegelijkertijd voor een SQL Server-database die wordt uitgevoerd op de virtuele machine.

### <a name="establish-network-connectivity"></a>Netwerkverbinding tot stand brengen

Voor alle bewerkingen is voor de SQL Server-VM een verbinding met openbare IP-adressen van Azure nodig. VM-bewerkingen (database detectie, het configureren van back-ups, het plannen van back-ups, herstel punten, enzovoort) mislukken zonder verbinding met de open bare IP-adressen. Maak verbinding met een van deze opties:

* **De IP-bereiken van het Azure-Data Center toestaan**: Hiermee staat u de [IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653) in de down load toe. Gebruik de cmdlet **set-AzureNetworkSecurityRule** om toegang te krijgen tot de netwerk beveiligings groep (NSG).
* **Een HTTP-proxy server implementeren om verkeer te routeren**: wanneer u een back-up maakt van een SQL Server Data Base op een Azure-VM, gebruikt de back-upextensie op de VM de https-api's om beheer opdrachten te verzenden naar Azure backup en gegevens naar Azure Storage. De back-upextensie maakt ook gebruik van Azure Active Directory (Azure AD) voor verificatie. Leid het verkeer van de back-upextensie voor deze drie services via de HTTP-proxy. De uitbrei dingen zijn het enige onderdeel dat is geconfigureerd voor toegang tot het open bare Internet.

Elke optie heeft voor-en nadelen

**Optie** | **Voordelen** | **Nadelen**
--- | --- | ---
IP-bereiken toestaan | Geen extra kosten. | Ingewikkeld om te beheren, omdat de IP-adresbereiken na verloop van tijd veranderen. <br/><br/> Biedt toegang tot heel Azure, niet alleen tot Azure Storage.
Een HTTP-proxy gebruiken   | Gedetailleerde controle via de proxy over de opslag-URL's is toegestaan. <br/><br/> Eén internettoegangspunt voor VM's. <br/><br/> Niet onderhevig aan Azure-IP-adreswijzigingen. | Extra kosten voor het uitvoeren van een VM met de proxysoftware.

### <a name="set-vm-permissions"></a>VM-machtigingen instellen

Azure Backup doet een aantal dingen wanneer u een back-up voor een SQL Server-database configureert:

* Voegt de extensie **AzureBackupWindowsWorkload** toe.
* Om databases op de virtuele machine te detecteren, wordt door Azure Backup het account **NT SERVICE\AzureWLBackupPluginSvc** gemaakt. Dit account wordt gebruikt voor back-up en herstel en vereist systeembeheerdersrechten voor SQL.
* Het account **NT AUTHORITY\SYSTEM** wordt door Azure Backup gebruikt voor databasedetectie/-aanvragen, dus dit account moet een openbare aanmelding via SQL zijn.

Als u de SQL Server-VM niet hebt gemaakt vanuit de Azure Marketplace, ontvangt u mogelijk het foutbericht **UserErrorSQLNoSysadminMembership**. Volg in dit geval [deze instructies](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Controleer de naamgevingsrichtlijnen voor databases van Azure Backup

Vermijd bij databasenamen het volgende:

* Voorloop- en volgspaties
* Volguitroepteken '!'
* Vierkante haak sluiten ‘]’
* Data base namen die beginnen met ' F:\ '

We hebben wel aliasing voor niet-ondersteunde tekens in Azure-tabellen, maar we raden aan deze te vermijden. [Meer informatie](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server-databases detecteren

Detecteer databases die op de virtuele machine worden uitgevoerd.

1. In de [Azure-portal](https://portal.azure.com) opent u de Recovery Services-kluis die u gebruikt om een back-up te maken van de database.

2. In het dashboard van de **Recovery Services-kluis** selecteert u **Backup**.

   ![Backup selecteren om het menu Doel van de back-up te openen](./media/backup-azure-sql-database/open-backup-menu.png)

3. Stel in het menu **Doel van de back-up** de optie **Waar wordt uw werkbelasting uitgevoerd?** in op **Azure** (de standaardwaarde).

4. In **Waarvan wilt u een back-up maken?** selecteert u **SQL Server in Azure-VM**.

    ![SQL Server in Azure VM voor de back-up selecteren](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. In **Doel van de back-up** > **DB's detecteren in VM's** selecteert u **Detectie starten** om te zoeken naar niet-beveiligde VM's in het abonnement. Dit kan een tijdje duren, afhankelijk van het aantal niet-beveiligde virtuele machines in het abonnement.

   * Niet-beveiligde virtuele machines zouden na detectie in de lijst moeten verschijnen, gesorteerd op naam en resourcegroep.
   * Als een virtuele machine niet zoals verwacht op de lijst verschijnt, controleert u of hiervan al een back-up bestaat in een kluis.
   * Meerdere virtuele machines kunnen dezelfde naam hebben, maar behoren altijd tot verschillende resourcegroepen.

     ![Back-up in behandeling tijdens het zoeken naar databases in VM's](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Selecteer in de lijst met virtuele machines de VM waarop de SQL Server-database wordt uitgevoerd > **DB's detecteren**.

7. Database-detectie bijhouden in het gebied **Meldingen**. Het kan even duren voor de taak is voltooid, afhankelijk van hoeveel databases zich op de virtuele machine bevinden. Wanneer de geselecteerde databases zijn gedetecteerd, wordt er een slagingsbericht weergegeven.

    ![Bericht dat de implementatie is geslaagd](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup detecteert alle SQL Server-databases op de virtuele machine. Tijdens de detectie gebeurt het volgende op de achtergrond:

    * Azure Backup registreert de virtuele machine met de kluis voor een back-up van de werkbelasting. Van alle databases op de geregistreerde virtuele machine kan alleen op deze kluis een back-up worden gemaakt.
    * Azure Backup installeert de extensie **AzureBackupWindowsWorkload** op de virtuele machine. Er is geen agent geïnstalleerd op de SQL-database.
    * Azure Backup maakt het serviceaccount **NT Service\AzureWLBackupPluginSvc** op de virtuele machine.
      * Het serviceaccount wordt gebruikt voor alle back-up- en herstelbewerkingen.
      * Voor **NT-Service\AzureWLBackupPluginSvc** zijn systeembeheerdersrechten voor SQL vereist. Op alle SQL Server-VM's die in de Azure Marketplace zijn gemaakt, is **SqlIaaSExtension** geïnstalleerd. De extensie **AzureBackupWindowsWorkload** maakt gebruik van de extensie **SQLIaaSExtension** om automatische de benodigde machtigingen op te halen.
    * Als u de virtuele machine niet vanuit de marketplace hebt gemaakt, beschikt de virtuele machine niet over **SqlIaaSExtension** en niet over de detectiebewerkingsfouten met het foutbericht **UserErrorSQLNoSysAdminMembership**. Volg de [instructies](backup-azure-sql-database.md#set-vm-permissions) om dit probleem op te lossen.

        ![De VM en database selecteren](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Back-up configureren  

Configureer de back-up als volgt:

1. In **back-updoel**selecteert u **back-up configureren**.

   ![Back-up configureren selecteren](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Klik op **back-up configureren**. de Blade **items selecteren voor back-up** wordt weer gegeven. Hiermee worden alle geregistreerde beschikbaarheids groepen en zelfstandige SQL-servers weer gegeven. Vouw de dubbele punt haak links van de rij uit om alle onbeveiligde data bases in die instantie te bekijken of altijd op AG.  

    ![Alle SQL Server-exemplaren met zelfstandige databases weergeven](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Selecteer alle databases die u wilt beveiligen > **OK**.

   ![De database beveiligen](./media/backup-azure-sql-database/select-database-to-protect.png)

   Ter optimalisering van de back-upbelastingen stelt Azure Backup het maximumaantal databases in één back-uptaak in op 50.

     * U kunt ook automatische beveiliging inschakelen voor het gehele exemplaar of de beschikbaarheids groep altijd door de optie **op** te selecteren in de bijbehorende vervolg keuzelijst in de kolom automatisch **beveiligen** . De functie voor automatische beveiliging biedt niet alleen beveiliging op alle bestaande data bases in één keer, maar beveiligt ook automatisch nieuwe data bases die worden toegevoegd aan dat exemplaar of de beschikbaarheids groep in de toekomst.  

4. Klik op **OK** om de Blade **back-upbeleid** te openen.

    ![Automatische beveiliging inschakelen voor de AlwaysOn-beschikbaarheidsgroep](./media/backup-azure-sql-database/enable-auto-protection.png)

5. In **Back-upbeleid kiezen** selecteert u een beleid en klikt u vervolgens op **OK**.

   * Selecteer het standaard beleid: HourlyLogBackup.
   * Een bestaand back-upbeleid kiezen dat u eerder hebt gemaakt voor SQL.
   * Definieer een nieuw beleid op basis van uw RPO-en bewaar periode.

     ![Back-upbeleid selecteren](./media/backup-azure-sql-database/select-backup-policy.png)

6. Selecteer in het menu **back-up** de optie **back-up inschakelen**.

    ![Het gekozen back-upbeleid inschakelen](./media/backup-azure-sql-database/enable-backup-button.png)

7. De voortgang van de configuratie volgen in het **Systeemvak** van de portal.

    ![Systeemvak](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Een back-upbeleid bepaalt wanneer back-ups worden gemaakt en hoe lang ze worden bewaard.

* Een beleid wordt gemaakt op kluisniveau.
* U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.
* Wanneer u een back-upbeleid maakt, is een dagelijkse volledige back-up de standaardinstelling.
* U kunt een differentiële back-up toevoegen, maar alleen als u een wekelijkse volledige back-up configureert.
* [Meer informatie](backup-architecture.md#sql-server-backup-types) over verschillende soorten back-upbeleid.

Ga als volgt te werk om een back-upbeleid te maken:

1. Klik in de kluis op **Back-upbeleid** > **Toevoegen**.
2. Klik in het menu **toevoegen** op **SQL Server in azure VM** om het beleids type te definiëren.

   ![Een beleidstype voor het nieuwe back-upbeleid kiezen](./media/backup-azure-sql-database/policy-type-details.png)

3. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op.
4. In **Beleid voor een volledige back-up** selecteert u een **back-upfrequentie** en kiest u **Dagelijks** of **Wekelijks**.

   * Als u **Dagelijks** kiest, selecteert u het tijdstip en de tijdzone waarop de back-uptaak moet worden gestart.
   * U moet een volledige back-up uitvoeren omdat u de optie **volledige back-up** niet kunt uitschakelen.
   * Klik op **Volledige back-up** om het beleid te bekijken.
   * U kunt geen differentiële back-ups maken voor dagelijkse volledige back-ups.
   * Als u **Wekelijks** kiest, selecteert u de dag van de week, het tijdstip en de tijdzone waarop de back-uptaak moet worden gestart.

     ![Velden voor nieuw back-upbeleid](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Standaard zijn alle opties voor een **bewaartermijn** geselecteerd. Schakel alle ongewenste bewaartermijnlimieten die u niet wilt gebruiken uit en stel de intervallen in die u wilt gebruiken.

    * De minimale Bewaar periode voor elk type back-up (volledig/differentieel/logboek) is zeven dagen.
    * Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
    * De back-up voor een specifieke dag wordt getagd en bewaard op basis van de wekelijkse bewaarperiode en uw wekelijkse bewaarbeleid.
    * De maandelijkse en jaarlijkse bewaarperioden werken op soortgelijke wijze.

   ![Intervalinstellingen voor bewaarperiode](./media/backup-azure-sql-database/retention-range-interval.png)

6. In het menu voor het **beleid voor een volledige back-up** selecteert u **OK** om de instellingen te accepteren.
7. Als u een beleid voor een differentiële back-up wilt toevoegen, selecteert u **Differentiële back-up**.

   ![Intervalinstellingen voor bewaarperiode](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Het menu voor het beleid voor een differentiële back-up openen](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen.

    * U kunt maximaal één differentiële back-up per dag activeren.
    * Differentiële back-ups kunnen maximaal 180 dagen worden bewaard. Als dat voor u te kort is, moet u volledige back-ups gebruiken.

9. Selecteer **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.

10. Als u een back-upbeleid voor een transactielogboek wilt toevoegen, selecteert u **Logboekback-up**.
11. In **Logboekback-up** selecteert u **Inschakelen** en stelt u de frequentie- en bewaarinstellingen in. Logboekback-ups kunnen elke 15 minuten worden geactiveerd en maximaal 35 dagen worden bewaard.
12. Selecteer **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.

    ![Het logboekback-upbeleid bewerken](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Kies in het menu **Back-upbeleid** of u **Compressie van SQL-back-ups** wilt inschakelen.
    * Compressie is standaard uitgeschakeld.
    * Op de back-end maakt Azure Backup gebruik van systeemeigen compressie van SQL-back-ups.

14. Als u klaar bent met het bewerken van het back-upbeleid, selecteert u **OK**.

## <a name="run-an-on-demand-backup"></a>Een back-up op aanvraag uitvoeren

1. Kies in uw Recovery Services kluis back-upitems.
2. Klik op SQL in azure VM.
3. Klik met de rechter muisknop op een Data Base en kies nu back-up.
4. Het back-uptype kiezen (volledig/differentieel/logboek/kopie alleen volledig) en compressie (inschakelen/uitschakelen)
5. Selecteer OK om de back-up te starten.
6. Controleer de back-uptaak door naar uw Recovery Services kluis te gaan en ' back-uptaken ' te kiezen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure Portal gebruikt voor het volgende:

> [!div class="checklist"]
>
> * Een kluis maken en configureren.
> * Databases detecteren en back-ups instellen.
> * Automatische beveiliging van databases instellen.
> * Een back-up op aanvraag uitvoeren.

Ga verder met de volgende zelfstudie in Azure om een virtuele machine te herstellen vanaf schijf.

> [!div class="nextstepaction"]
> [SQL Server data bases herstellen op virtuele Azure-machines](./restore-sql-database-azure-vm.md)
