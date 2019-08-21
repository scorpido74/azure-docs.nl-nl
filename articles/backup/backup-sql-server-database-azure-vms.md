---
title: Back-ups maken van SQL Server-data bases in azure-Vm's | Microsoft Docs
description: Meer informatie over het maken van een back-up van SQL Server-data bases in azure Vm's
ms.reviewer: vijayts
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: e18d6519d1ee3c1750757af5c59157de8bdde80c
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637920"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Back-ups maken van SQL Server-databases in virtuele Azure-machines

SQL Server-data bases zijn kritieke workloads waarvoor een laag herstel punt (RPO) en lange termijn retentie is vereist. U kunt back-ups maken van SQL Server-data bases die worden uitgevoerd op Azure virtual machines (Vm's) met behulp van [Azure backup](backup-overview.md).

In dit artikel wordt beschreven hoe u een back-up maakt van een SQL Server Data Base die wordt uitgevoerd op een virtuele Azure-machine naar een Azure Backup Recovery Services kluis.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een kluis maken en configureren.
> * Ontdek data bases en stel back-ups in.
> * Automatische beveiliging van databases instellen.


## <a name="prerequisites"></a>Vereisten

Controleer de volgende criteria voordat u een back-up maakt van een SQL Server Data Base:

1. Identificeer of maak een [Recovery Services kluis](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) in dezelfde regio of locatie als de virtuele machine die als host fungeert voor het SQL Server exemplaar.
2. Controleer of de virtuele machine [verbinding](backup-sql-server-database-azure-vms.md#establish-network-connectivity)heeft met het netwerk.
3. Zorg ervoor dat de SQL Server-data bases voldoen [aan de richt lijnen voor de naamgeving van data bases voor Azure backup](#database-naming-guidelines-for-azure-backup).
4. Voeg, met name voor SQL 2008 en 2008 R2, [register sleutel](#add-registry-key-to-enable-registration) toe om Server registratie in te scha kelen. Deze stap is niet vereist wanneer de functie algemeen beschikbaar is.
5. Controleer of er geen andere back-upoplossingen zijn ingeschakeld voor de data base. Schakel alle andere SQL Server back-ups uit voordat u een back-up van de data base maakt.

> [!NOTE]
> U kunt Azure Backup inschakelen voor een virtuele machine van Azure en ook voor een SQL Server-Data Base die op de virtuele machine wordt uitgevoerd zonder conflict.


### <a name="establish-network-connectivity"></a>Netwerkverbinding tot stand brengen

Voor alle bewerkingen vereist een SQL Server VM connectiviteit met open bare IP-adressen van Azure. VM-bewerkingen (database detectie, het configureren van back-ups, het plannen van back-ups, herstel punten, enzovoort) mislukken zonder verbinding met open bare IP-adressen van Azure.

Verbinding maken met behulp van een van de volgende opties:

- **Sta de IP-adresbereiken van Azure Data Center toe**. Met deze optie worden [IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653) in de down load toegestaan. Gebruik de cmdlet Set-AzureNetworkSecurityRule om toegang te krijgen tot een netwerk beveiligings groep (NSG). Als u een lijst met alleen de regio-specifieke Ip's wilt weer geven, moet u ook de lijst met veilige geadresseerden de service label van de Azure Active Directory (Azure AD) bijwerken om verificatie in te scha kelen.

- **Toegang toestaan met behulp van NSG-Tags**.  Als u NSG gebruikt om de connectiviteit te beperken, moet u AzureBackup-service label gebruiken om uitgaande toegang tot Azure Backup toe te staan. Daarnaast moet u ook connectiviteit voor verificatie en gegevens overdracht toestaan met behulp van [regels](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) voor Azure AD en Azure Storage. U kunt dit doen vanuit de portal of Power shell.

    Een regel maken met behulp van de portal:
    
    - In **alle services**gaat u naar **netwerk beveiligings groepen** en selecteert u de netwerk beveiligings groep.
    - Selecteer **uitgaande beveiligings regels** onder **instellingen**.
    - Selecteer **Toevoegen**. Voer alle vereiste gegevens voor het maken van een nieuwe regel in, zoals beschreven in de instellingen van de [beveiligings regel](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Zorg ervoor dat de optie **bestemming** is ingesteld op **service label** en **doel service label** is ingesteld op **AzureBackup**.
    - Klik op **toevoegen**om de zojuist gemaakte uitgaande beveiligings regel op te slaan.
    
   Een regel maken met behulp van Power shell:

   - Referenties van een Azure-account toevoegen en de nationale Clouds bijwerken<br/>
    ``Add-AzureRmAccount``
  - Het NSG-abonnement selecteren<br/>
    ``Select-AzureRmSubscription "<Subscription Id>"``
  - Selecteer de NSG<br/>
    ```$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"```
  - Regel voor uitgaande verbindingen voor Azure Backup servicetag toevoegen<br/>
   ```Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"```
  - De NSG opslaan<br/>
    ```Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg```

   
- **Toegang toestaan met behulp van Azure firewall-Tags**. Als u Azure Firewall gebruikt, maakt u een toepassings regel met behulp van de AzureBackup [FQDN-tag](https://docs.microsoft.com/azure/firewall/fqdn-tags). Hierdoor is uitgaande toegang tot Azure Backup mogelijk.
- **Implementeer een HTTP-proxy server om verkeer te routeren**. Wanneer u een back-up maakt van een SQL Server Data Base op een virtuele machine van Azure, gebruikt de back-upextensie op de VM de HTTPS-Api's om beheer opdrachten te verzenden naar Azure Backup en gegevens naar Azure Storage. Voor de back-upextensie wordt ook Azure AD gebruikt voor verificatie. Leid het verkeer van de back-upextensie voor deze drie services via de HTTP-proxy. De uitbrei dingen zijn het enige onderdeel dat is geconfigureerd voor toegang tot het open bare Internet.

Connectiviteits opties zijn onder andere de volgende voor delen en nadelen:

**Optie** | **Voordelen** | **Nadelen**
--- | --- | ---
IP-bereiken toestaan | Geen extra kosten | Complex om te beheren, omdat de IP-adresbereiken in de loop van de tijd veranderen. <br/><br/> Biedt toegang tot het hele Azure, niet alleen Azure Storage
NSG-service tags gebruiken | Eenvoudiger te beheren als bereik wijzigingen worden automatisch samengevoegd <br/><br/> Geen extra kosten <br/><br/> | Kan alleen worden gebruikt met Nsg's <br/><br/> Biedt toegang tot de volledige service
Azure Firewall FQDN-Tags gebruiken | Eenvoudiger te beheren omdat de vereiste FQDN-s automatisch worden beheerd | Kan alleen worden gebruikt met Azure Firewall
Een HTTP-proxy gebruiken | Nauw keurig beheer van de proxy via de opslag-Url's is toegestaan <br/><br/> Eén toegangs punt voor Internet toegang tot Vm's <br/><br/> Wijzigingen in het IP-adres van Azure worden niet onderhevig | Aanvullende kosten voor het uitvoeren van een virtuele machine met de proxy software

### <a name="database-naming-guidelines-for-azure-backup"></a>Richt lijnen voor database naamgeving voor Azure Backup

Vermijd het gebruik van de volgende elementen in database namen:

  * Afsluitende en voorloop spaties
  * Afsluitende uitroep tekens (!)
  * Vier Kante haken sluiten (])
  * Punt komma '; '
  * Slash/

Aliasing is beschikbaar voor niet-ondersteunde tekens, maar we raden u aan om deze te vermijden. Zie [Het gegevensmodel van de tabelservice](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN) voor meer informatie.

### <a name="add-registry-key-to-enable-registration"></a>Register sleutel toevoegen om registratie in te scha kelen

1. Regedit openen
2. Maak het pad naar de map voor het REGI ster: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook (u moet de sleutel TestHook maken onder WorkloadBackup die op zijn beurt moet worden gemaakt onder micro soft).
3. Maak onder het pad naar de map van het REGI ster een nieuwe teken reeks waarde met de teken reeks naam **AzureBackupEnableWin2K8R2SP1** en waarde: **Echte**

    ![RegEdit voor het inschakelen van registratie](media/backup-azure-sql-database/reg-edit-sqleos-bkp.png)

U kunt deze stap ook automatiseren door het REG-bestand uit te voeren met de volgende opdracht:

```csharp
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook]
"AzureBackupEnableWin2K8R2SP1"="True"
```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server-databases detecteren

Data bases detecteren die worden uitgevoerd op een virtuele machine:

1. In de [Azure-portal](https://portal.azure.com) opent u de Recovery Services-kluis die u gebruikt om een back-up te maken van de database.

2. Selecteer **back-up**in het dash board van **Recovery Services kluis** .

   ![Backup selecteren om het menu Doel van de back-up te openen](./media/backup-azure-sql-database/open-backup-menu.png)

3. In **back-updoel**stelt u in **waar wordt uw workload uitgevoerd?** naar **Azure**.

4. In **Waarvan wilt u een back-up maken?** selecteert u **SQL Server in Azure-VM**.

    ![SQL Server in Azure VM voor de back-up selecteren](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. In **Doel van de back-up** > **DB's detecteren in VM's** selecteert u **Detectie starten** om te zoeken naar niet-beveiligde VM's in het abonnement. Deze zoek opdracht kan enige tijd duren, afhankelijk van het aantal niet-beveiligde virtuele machines in het abonnement.

   - Niet-beveiligde virtuele machines zouden na detectie in de lijst moeten verschijnen, gesorteerd op naam en resourcegroep.
   - Als een virtuele machine niet wordt vermeld zoals u verwacht, kunt u zien of er al een back-up is gemaakt in een kluis.
   - Meerdere virtuele machines kunnen dezelfde naam hebben, maar ze behoren tot verschillende resource groepen.

     ![Back-up in behandeling tijdens het zoeken naar databases in VM's](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Selecteer in de lijst met virtuele machines de VM waarop de SQL Server-database wordt uitgevoerd > **DB's detecteren**.

7. Spoor database detectie op in **meldingen**. De tijd die nodig is voor deze actie is afhankelijk van het aantal VM-data bases. Wanneer de geselecteerde databases zijn gedetecteerd, wordt er een slagingsbericht weergegeven.

    ![Bericht dat de implementatie is geslaagd](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup detecteert alle SQL Server-databases op de virtuele machine. Tijdens de detectie vinden de volgende elementen plaats op de achtergrond:

    - Azure Backup registreert de virtuele machine met de kluis voor de back-up van de werk belasting. Alleen voor alle data bases op de geregistreerde virtuele machine kan een back-up worden gemaakt naar deze kluis.
    - Azure Backup installeert de AzureBackupWindowsWorkload-extensie op de VM. Er is geen agent geïnstalleerd op een SQL database.
    - Azure Backup maakt het service account NT Service\AzureWLBackupPluginSvc op de virtuele machine.
      - Het serviceaccount wordt gebruikt voor alle back-up- en herstelbewerkingen.
      - NT Service\AzureWLBackupPluginSvc vereist SQL sysadmin-machtigingen. Op alle SQL Server Vm's die op Marketplace zijn gemaakt, wordt de SqlIaaSExtension geïnstalleerd. De AzureBackupWindowsWorkload-extensie gebruikt de SQLIaaSExtension om automatisch de vereiste machtigingen op te halen.
    - Als u de virtuele machine niet hebt gemaakt op basis van de Marketplace of als u zich in SQL 2008 en 2008 R2 bevindt, is de virtuele machine mogelijk niet op de SqlIaaSExtension geïnstalleerd en mislukt de detectie bewerking met het fout bericht UserErrorSQLNoSysAdminMembership. Volg de instructies onder [set VM permissions](backup-azure-sql-database.md#set-vm-permissions)om dit probleem op te lossen.

        ![De VM en database selecteren](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Back-up configureren  

1. In **doel stelling** > **voor back-up, stap 2: Configureer de**back-up en selecteer **configureren van back-up**.

   ![Back-up configureren selecteren](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. In **items selecteren om een back-up te maken**, ziet u alle geregistreerde beschikbaarheids groepen en zelfstandige SQL Server exemplaren. Selecteer de pijl links van een rij om de lijst met alle onbeveiligde data bases in die instantie of AlwaysOn-beschikbaarheids groep uit te vouwen.  

    ![Alle SQL Server-exemplaren met zelfstandige databases weergeven](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Kies alle data bases die u wilt beveiligen en selecteer vervolgens **OK**.

   ![De database beveiligen](./media/backup-azure-sql-database/select-database-to-protect.png)

   Ter optimalisering van de back-upbelastingen stelt Azure Backup het maximumaantal databases in één back-uptaak in op 50.

     * Om meer dan 50 back-ups te beschermen moet u meerdere databases configureren.
     * Om het [](#enable-auto-protection) hele exemplaar of de always on-beschikbaarheids groep in te scha kelen. Selecteer in de vervolg keuzelijst **beveiliging** **op**en selecteer vervolgens **OK**.

    > [!NOTE]
    > De functie voor [automatisch beveiligen](#enable-auto-protection) biedt niet alleen beveiliging op alle bestaande data bases tegelijk, maar beveiligt ook automatisch nieuwe data bases die zijn toegevoegd aan het betreffende exemplaar of de beschikbaarheids groep.  

4. Selecteer **OK** om het **back-upbeleid**te openen.

    ![Automatische beveiliging inschakelen voor de AlwaysOn-beschikbaarheids groep](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Kies in **back-upbeleid**een beleid en selecteer vervolgens **OK**.

   - Selecteer het standaard beleid als HourlyLogBackup.
   - Een bestaand back-upbeleid kiezen dat u eerder hebt gemaakt voor SQL.
   - Definieer een nieuw beleid op basis van uw RPO-en bewaar periode.

     ![Back-upbeleid selecteren](./media/backup-azure-sql-database/select-backup-policy.png)

6. Selecteer **back-up inschakelen**in **back-up**.

    ![Het gekozen back-upbeleid inschakelen](./media/backup-azure-sql-database/enable-backup-button.png)

7. De voortgang van de configuratie volgen in het **Systeemvak** van de portal.

    ![Systeemvak](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Een back-upbeleid bepaalt wanneer back-ups worden gemaakt en hoe lang ze worden bewaard.

- Een beleid wordt gemaakt op kluisniveau.
- U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.
- Wanneer u een back-upbeleid maakt, is een dagelijkse volledige back-up de standaardinstelling.
- U kunt een differentiële back-up toevoegen, maar alleen als u een wekelijkse volledige back-up configureert.
- Meer informatie over [verschillende typen back-upbeleid](backup-architecture.md#sql-server-backup-types).

Ga als volgt te werk om een back-upbeleid te maken:

1. Selecteer in de kluis de optie **back-upbeleid** > **toevoegen**.
2. In **toevoegen**selecteert u **SQL Server in azure VM** om het beleids type te definiëren.

   ![Een beleidstype voor het nieuwe back-upbeleid kiezen](./media/backup-azure-sql-database/policy-type-details.png)

3. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op.
4. Selecteer een **back-upfrequentie**in het **beleid volledige back-up**. Kies **dagelijks** of **wekelijks**.

   - Als u **Dagelijks** kiest, selecteert u het tijdstip en de tijdzone waarop de back-uptaak moet worden gestart.
   - Als u **Wekelijks** kiest, selecteert u de dag van de week, het tijdstip en de tijdzone waarop de back-uptaak moet worden gestart.
   - Voer een volledige back-up uit omdat u de optie **volledige back-up** niet kunt uitschakelen.
   - Selecteer **volledige back-up** om het beleid weer te geven.
   - U kunt geen differentiële back-ups maken voor dagelijkse volledige back-ups.

     ![Velden voor nieuw back-upbeleid](./media/backup-azure-sql-database/full-backup-policy.png)  

5. In **Bewaar termijn**worden alle opties standaard geselecteerd. Wis de limieten voor het Bewaar bereik die u niet wilt en stel vervolgens de intervallen in voor gebruik.

    - De minimale Bewaar periode voor elk type back-up (volledig, differentieel en logboek) is zeven dagen.
    - Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
    - De back-up voor een specifieke dag wordt gelabeld en bewaard op basis van de wekelijkse Bewaar termijn en de instelling voor wekelijkse Bewaar periode.
    - Maandelijkse en jaarlijkse Bewaar perioden gedragen zich op een vergelijk bare manier.

       ![Intervalinstellingen voor bewaarperiode](./media/backup-azure-sql-database/retention-range-interval.png)

6. In het menu voor het **beleid voor een volledige back-up** selecteert u **OK** om de instellingen te accepteren.
7. Als u een beleid voor een differentiële back-up wilt toevoegen, selecteert u **Differentiële back-up**.

   ![Intervalinstellingen voor bewaarperiode](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Het menu voor het beleid voor een differentiële back-up openen](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen.

    - U kunt slechts één differentiële back-up per dag activeren.
    - Differentiële back-ups kunnen maximaal 180 dagen worden bewaard. Gebruik voor langere retentie volledige back-ups.

9. Selecteer **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.

10. Als u een back-upbeleid voor een transactielogboek wilt toevoegen, selecteert u **Logboekback-up**.
11. In **Logboekback-up** selecteert u **Inschakelen** en stelt u de frequentie- en bewaarinstellingen in. Logboek back-ups kunnen worden uitgevoerd op elke 15 minuten en kunnen Maxi maal 35 dagen worden bewaard.
12. Selecteer **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.

    ![Het logboekback-upbeleid bewerken](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Kies in het menu **Back-upbeleid** of u **Compressie van SQL-back-ups** wilt inschakelen.
    - Compressie is standaard uitgeschakeld.
    - Op de back-end maakt Azure Backup gebruik van systeemeigen compressie van SQL-back-ups.

14. Als u klaar bent met het bewerken van het back-upbeleid, selecteert u **OK**.


### <a name="modify-policy"></a>Beleid wijzigen
Wijzig het beleid om de back-upfrequentie of het Bewaar bereik te wijzigen.

> [!NOTE]
> Elke wijziging in de retentie periode wordt retro actief toegepast op alle oudere herstel punten, naast de nieuwe.

Ga in het kluis dashboard naar beleid voor het **beheren** > van**back-ups** en kies het beleid dat u wilt bewerken.

  ![Back-upbeleid beheren](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Automatische beveiliging inschakelen  

U kunt automatische beveiliging inschakelen om automatisch een back-up te maken van alle bestaande en toekomstige data bases naar een zelfstandig SQL Server exemplaar of naar een AlwaysOn-beschikbaarheids groep.

- Er is geen limiet voor het aantal data bases dat u tegelijk voor automatische beveiliging kunt selecteren.
- U kunt data bases niet selectief beveiligen of uitsluiten van beveiliging in een exemplaar op het moment dat u automatische beveiliging inschakelt.
- Als uw exemplaar al beveiligde data bases bevat, blijven ze beschermd onder hun respectieve beleid, zelfs nadat u automatische beveiliging hebt ingeschakeld. Alle niet-beveiligde data bases die later worden toegevoegd, hebben slechts één beleid dat u definieert op het moment van het inschakelen van automatische beveiliging, vermeld onder **back-up configureren**. U kunt echter later het beleid wijzigen dat is gekoppeld aan een automatisch beveiligde data base.  

Automatische beveiliging inschakelen:

  1. In **Items voor back-up** selecteert u het exemplaar waarvoor u automatische beveiliging wilt inschakelen.
  2. Selecteer de vervolg keuzelijst onder AutoProtect, kies **aan**en selecteer **OK**.

      ![Automatische beveiliging inschakelen voor de beschikbaarheids groep](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Back-up wordt voor alle databases tegelijk getriggerd en kan worden gevolgd in **Back-uptaken**.

Als u automatische beveiliging wilt uitschakelen, selecteert u de naam van het exemplaar onder **back-up configureren**en selecteert u vervolgens automatische **beveiliging uitschakelen** voor het exemplaar. Er wordt nog steeds een back-up van alle data bases gemaakt, maar toekomstige data bases worden niet automatisch beveiligd.

![Automatische beveiliging voor dat exemplaar uitschakelen](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:

- [Back-ups van SQL Server-data bases herstellen](restore-sql-database-azure-vm.md)
- [Back-ups van SQL Server-data bases beheren](manage-monitor-sql-database-backup.md)
