---
title: Back-ups maken van SQL Server-databases in virtuele Azure-machines
description: In dit artikel vindt u informatie over het maken van een back-up van SQL Server-data bases op virtuele machines van Azure met Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 887f15deed74330cf132e0574d166c074d2c7cad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81685716"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Back-ups maken van SQL Server-databases in virtuele Azure-machines

SQL Server-data bases zijn kritieke workloads waarvoor een laag herstel punt (RPO) en lange termijn retentie is vereist. U kunt back-ups maken van SQL Server-data bases die worden uitgevoerd op Azure virtual machines (Vm's) met behulp van [Azure backup](backup-overview.md).

In dit artikel wordt beschreven hoe u een back-up maakt van een SQL Server Data Base die wordt uitgevoerd op een virtuele Azure-machine naar een Azure Backup Recovery Services kluis.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een kluis maken en configureren.
> * Ontdek data bases en stel back-ups in.
> * Automatische beveiliging van databases instellen.

>[!NOTE]
>**Voorlopig verwijderen voor SQL Server in azure VM en voorlopig verwijderen voor SAP Hana in azure VM-workloads** is nu beschikbaar als preview-versie.<br>
>Als u zich wilt aanmelden voor de preview, schrijft u voorAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Vereisten

Controleer de volgende criteria voordat u een back-up maakt van een SQL Server Data Base:

1. Identificeer of maak een [Recovery Services kluis](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) in dezelfde regio en hetzelfde abonnement als de virtuele machine die als host fungeert voor het SQL Server exemplaar.
2. Controleer of de virtuele machine [verbinding](backup-sql-server-database-azure-vms.md#establish-network-connectivity)heeft met het netwerk.
3. Zorg ervoor dat de SQL Server-data bases voldoen [aan de richt lijnen voor de naamgeving van data bases voor Azure backup](#database-naming-guidelines-for-azure-backup).
4. Controleer of er geen andere back-upoplossingen zijn ingeschakeld voor de data base. Schakel alle andere SQL Server back-ups uit voordat u een back-up van de data base maakt.

> [!NOTE]
> U kunt Azure Backup inschakelen voor een virtuele machine van Azure en ook voor een SQL Server-Data Base die op de virtuele machine wordt uitgevoerd zonder conflict.

### <a name="establish-network-connectivity"></a>Netwerkverbinding tot stand brengen

Voor alle bewerkingen vereist een SQL Server VM connectiviteit met open bare IP-adressen van Azure. VM-bewerkingen (database detectie, het configureren van back-ups, het plannen van back-ups, herstel punten, enzovoort) mislukken zonder verbinding met open bare IP-adressen van Azure.

Verbinding maken met behulp van een van de volgende opties:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>De IP-bereiken van Azure Data Center toestaan

Met deze optie worden de [IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653) in het gedownloade bestand toegestaan. Gebruik de cmdlet Set-AzureNetworkSecurityRule om toegang te krijgen tot een netwerk beveiligings groep (NSG). Als uw lijst met veilige geadresseerden alleen landspecifieke Ip's bevat, moet u ook de lijst met veilige geadresseerden de service label van de Azure Active Directory (Azure AD) bijwerken om verificatie in te scha kelen.

#### <a name="allow-access-using-nsg-tags"></a>Toegang toestaan met behulp van NSG-Tags

Als u NSG gebruikt om de connectiviteit te beperken, moet u AzureBackup-service label gebruiken om uitgaande toegang tot Azure Backup toe te staan. Daarnaast moet u ook connectiviteit voor verificatie en gegevens overdracht toestaan met behulp van [regels](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) voor Azure AD en Azure Storage. Dit kan worden gedaan via de Azure Portal of via Power shell.

Een regel maken met behulp van de portal:

  1. In **alle services**gaat u naar **netwerk beveiligings groepen** en selecteert u de netwerk beveiligings groep.
  2. Selecteer **uitgaande beveiligings regels** onder **instellingen**.
  3. Selecteer **Toevoegen**. Voer alle vereiste gegevens voor het maken van een nieuwe regel in, zoals beschreven in de instellingen van de [beveiligings regel](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Zorg ervoor dat de optie **bestemming** is ingesteld op **service label** en **doel service label** is ingesteld op **AzureBackup**.
  4. Klik op **toevoegen**om de zojuist gemaakte uitgaande beveiligings regel op te slaan.

Een regel maken met behulp van Power shell:

 1. Referenties van een Azure-account toevoegen en de nationale Clouds bijwerken<br/>
      `Add-AzureRmAccount`<br/>

 2. Het NSG-abonnement selecteren<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Selecteer de NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Regel voor uitgaande verbindingen voor Azure Backup servicetag toevoegen<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Regel voor uitgaand verkeer voor opslag service label toevoegen<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Regel voor uitgaand service-label voor AzureActiveDirectory toevoegen toestaan<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. De NSG opslaan<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Toegang toestaan met behulp van Azure firewall-Tags**. Als u Azure Firewall gebruikt, maakt u een toepassings regel met behulp van de AzureBackup [FQDN-tag](https://docs.microsoft.com/azure/firewall/fqdn-tags). Hierdoor is uitgaande toegang tot Azure Backup mogelijk.

**Implementeer een HTTP-proxy server om verkeer te routeren**. Wanneer u een back-up maakt van een SQL Server Data Base op een virtuele machine van Azure, gebruikt de back-upextensie op de VM de HTTPS-Api's om beheer opdrachten te verzenden naar Azure Backup en gegevens naar Azure Storage. Voor de back-upextensie wordt ook Azure AD gebruikt voor verificatie. Leid het verkeer van de back-upextensie voor deze drie services via de HTTP-proxy. Er zijn geen joker teken domeinen in gebruik met Azure Backup om toe te voegen aan de acceptatie lijst voor uw proxy regels. U moet de open bare IP-adresbereiken gebruiken voor de services die door Azure worden ondersteund. De uitbrei dingen zijn het enige onderdeel dat is geconfigureerd voor toegang tot het open bare Internet.

Connectiviteits opties zijn onder andere de volgende voor delen en nadelen:

**Optie** | **Voordelen** | **Nadelen**
--- | --- | ---
IP-bereiken toestaan | Geen extra kosten | Complex om te beheren, omdat de IP-adresbereiken in de loop van de tijd veranderen. <br/><br/> Biedt toegang tot het hele Azure, niet alleen Azure Storage
NSG-service tags gebruiken | Eenvoudiger te beheren als bereik wijzigingen worden automatisch samengevoegd <br/><br/> Geen extra kosten <br/><br/> | Kan alleen worden gebruikt met Nsg's <br/><br/> Biedt toegang tot de volledige service
Azure Firewall FQDN-Tags gebruiken | Eenvoudiger te beheren omdat de vereiste FQDN-s automatisch worden beheerd | Kan alleen worden gebruikt met Azure Firewall
Een HTTP-proxy gebruiken | Eén toegangs punt voor Internet toegang tot Vm's <br/> | Aanvullende kosten voor het uitvoeren van een virtuele machine met de proxy software <br/> Geen gepubliceerde FQDN-adressen, toestaan dat regels worden onderhevig aan wijzigingen in het Azure IP-adres

#### <a name="private-endpoints"></a>Privé-eind punten

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

### <a name="database-naming-guidelines-for-azure-backup"></a>Richt lijnen voor database naamgeving voor Azure Backup

Vermijd het gebruik van de volgende elementen in database namen:

* Afsluitende en voorloop spaties
* Afsluitende uitroep tekens (!)
* Vier Kante haken sluiten (])
* Punt komma '; '
* Slash/

Aliasing is beschikbaar voor niet-ondersteunde tekens, maar we raden u aan om deze te vermijden. Zie [Het gegevensmodel van de tabelservice](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) voor meer informatie.

>[!NOTE]
>Het **configureren** van de beveiliging voor data bases met speciale tekens zoals "+" of "&" in hun naam wordt niet ondersteund. U kunt de naam van de data base wijzigen of **automatische beveiliging**inschakelen, waardoor deze data bases kunnen worden beveiligd.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server-databases detecteren

Data bases detecteren die worden uitgevoerd op een virtuele machine:

1. In de [Azure-portal](https://portal.azure.com) opent u de Recovery Services-kluis die u gebruikt om een back-up te maken van de database.

2. Selecteer **back-up**in het dash board van **Recovery Services kluis** .

   ![Backup selecteren om het menu Doel van de back-up te openen](./media/backup-azure-sql-database/open-backup-menu.png)

3. In **back-updoel**stelt u in **waar wordt uw workload uitgevoerd?** naar **Azure**.

4. In **Waarvan wilt u een back-up maken?** selecteert u **SQL Server in Azure-VM**.

    ![SQL Server in Azure VM voor de back-up selecteren](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. In **back-updoel** > **detecteren db's in vm's**, selecteert u **detectie starten** om niet-beveiligde vm's in het abonnement te zoeken. Deze zoek opdracht kan enige tijd duren, afhankelijk van het aantal niet-beveiligde virtuele machines in het abonnement.

   * Niet-beveiligde virtuele machines zouden na detectie in de lijst moeten verschijnen, gesorteerd op naam en resourcegroep.
   * Als een virtuele machine niet wordt vermeld zoals u verwacht, kunt u zien of er al een back-up is gemaakt in een kluis.
   * Meerdere virtuele machines kunnen dezelfde naam hebben, maar ze behoren tot verschillende resource groepen.

     ![Back-up in behandeling tijdens het zoeken naar databases in VM's](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Selecteer in de lijst met virtuele machines de VM waarop de SQL Server-database wordt uitgevoerd > **DB's detecteren**.

7. Spoor database detectie op in **meldingen**. De tijd die nodig is voor deze actie is afhankelijk van het aantal VM-data bases. Wanneer de geselecteerde databases zijn gedetecteerd, wordt er een slagingsbericht weergegeven.

    ![Bericht dat de implementatie is geslaagd](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup detecteert alle SQL Server-databases op de virtuele machine. Tijdens de detectie vinden de volgende elementen plaats op de achtergrond:

    * Azure Backup registreert de virtuele machine met de kluis voor de back-up van de werk belasting. Alleen voor alle data bases op de geregistreerde virtuele machine kan een back-up worden gemaakt naar deze kluis.
    * Azure Backup installeert de extensie AzureBackupWindowsWorkload op de virtuele machine. Er is geen agent geïnstalleerd op een SQL database.
    * Azure Backup maakt het serviceaccount NT Service\AzureWLBackupPluginSvc op de virtuele machine.
      * Het serviceaccount wordt gebruikt voor alle back-up- en herstelbewerkingen.
      * NT Service\AzureWLBackupPluginSvc vereist SQL sysadmin-machtigingen. Op alle SQL Server Vm's die op Marketplace zijn gemaakt, wordt de SqlIaaSExtension geïnstalleerd. De extensie AzureBackupWindowsWorkload maakt gebruik van de extensie SQLIaaSExtension om automatische de benodigde machtigingen op te halen.
    * Als u de virtuele machine niet hebt gemaakt op basis van de Marketplace of als u zich in SQL 2008 en 2008 R2 bevindt, is de virtuele machine mogelijk niet op de SqlIaaSExtension geïnstalleerd en mislukt de detectie bewerking met het fout bericht UserErrorSQLNoSysAdminMembership. Volg de instructies onder [set VM permissions](backup-azure-sql-database.md#set-vm-permissions)om dit probleem op te lossen.

        ![De VM en database selecteren](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Back-up configureren  

1. Selecteer in **back-updoel** > **stap 2: back**-up configureren de optie **back-up configureren**.

   ![Back-up configureren selecteren](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. In **items selecteren om een back-up te maken**, ziet u alle geregistreerde beschikbaarheids groepen en zelfstandige SQL Server exemplaren. Selecteer de pijl links van een rij om de lijst met alle onbeveiligde data bases in die instantie of AlwaysOn-beschikbaarheids groep uit te vouwen.  

    ![Alle SQL Server-exemplaren met zelfstandige databases weergeven](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Kies alle data bases die u wilt beveiligen en selecteer vervolgens **OK**.

   ![De database beveiligen](./media/backup-azure-sql-database/select-database-to-protect.png)

   Ter optimalisering van de back-upbelastingen stelt Azure Backup het maximumaantal databases in één back-uptaak in op 50.

     * Om meer dan 50 back-ups te beschermen moet u meerdere databases configureren.
     * Als u de volledige instantie of de AlwaysOn-beschikbaarheids groep wilt [inschakelen](#enable-auto-protection) , selecteert u in de vervolg KEUZELIJST **voor** **beveiliging** en selecteert u vervolgens **OK**.

    > [!NOTE]
    > De functie voor [automatisch beveiligen](#enable-auto-protection) biedt niet alleen beveiliging op alle bestaande data bases tegelijk, maar beveiligt ook automatisch nieuwe data bases die zijn toegevoegd aan het betreffende exemplaar of de beschikbaarheids groep.  

4. Selecteer **OK** om het **back-upbeleid**te openen.

    ![Automatische beveiliging inschakelen voor de AlwaysOn-beschikbaarheids groep](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Kies in **back-upbeleid**een beleid en selecteer vervolgens **OK**.

   * Selecteer het standaard beleid als HourlyLogBackup.
   * Een bestaand back-upbeleid kiezen dat u eerder hebt gemaakt voor SQL.
   * Een nieuw beleid definiëren op basis van uw RPO en retentiebereik.

     ![Back-upbeleid selecteren](./media/backup-azure-sql-database/select-backup-policy.png)

6. Selecteer **back-up inschakelen**in **back-up**.

    ![Het gekozen back-upbeleid inschakelen](./media/backup-azure-sql-database/enable-backup-button.png)

7. De voortgang van de configuratie volgen in het **Systeemvak** van de portal.

    ![Systeemvak](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Een back-upbeleid bepaalt wanneer back-ups worden gemaakt en hoe lang ze worden bewaard.

* Een beleid wordt gemaakt op kluisniveau.
* U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.
* Wanneer u een back-upbeleid maakt, is een dagelijkse volledige back-up de standaardinstelling.
* U kunt een differentiële back-up toevoegen, maar alleen als u een wekelijkse volledige back-up configureert.
* Meer informatie over [verschillende typen back-upbeleid](backup-architecture.md#sql-server-backup-types).

Ga als volgt te werk om een back-upbeleid te maken:

1. Selecteer in de kluis de optie **back-upbeleid** > **toevoegen**.
2. In **toevoegen**selecteert u **SQL Server in azure VM** om het beleids type te definiëren.

   ![Een beleidstype voor het nieuwe back-upbeleid kiezen](./media/backup-azure-sql-database/policy-type-details.png)

3. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op.
4. Selecteer een **back-upfrequentie**in het **beleid volledige back-up**. Kies **dagelijks** of **wekelijks**.

   * Als u **Dagelijks** kiest, selecteert u het tijdstip en de tijdzone waarop de back-uptaak moet worden gestart.
   * Als u **Wekelijks** kiest, selecteert u de dag van de week, het tijdstip en de tijdzone waarop de back-uptaak moet worden gestart.
   * Voer een volledige back-up uit omdat u de optie **volledige back-up** niet kunt uitschakelen.
   * Selecteer **volledige back-up** om het beleid weer te geven.
   * U kunt geen differentiële back-ups maken voor dagelijkse volledige back-ups.

     ![Velden voor nieuw back-upbeleid](./media/backup-azure-sql-database/full-backup-policy.png)  

5. In **Bewaar termijn**worden alle opties standaard geselecteerd. Wis de limieten voor het Bewaar bereik die u niet wilt en stel vervolgens de intervallen in voor gebruik.

    * De minimale Bewaar periode voor elk type back-up (volledig, differentieel en logboek) is zeven dagen.
    * Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
    * De back-up voor een specifieke dag wordt gelabeld en bewaard op basis van de wekelijkse Bewaar termijn en de instelling voor wekelijkse Bewaar periode.
    * Maandelijkse en jaarlijkse Bewaar perioden gedragen zich op een vergelijk bare manier.

       ![Intervalinstellingen voor bewaarperiode](./media/backup-azure-sql-database/retention-range-interval.png)

6. In het menu voor het **beleid voor een volledige back-up** selecteert u **OK** om de instellingen te accepteren.
7. Als u een beleid voor een differentiële back-up wilt toevoegen, selecteert u **Differentiële back-up**.

   ![Intervalinstellingen voor bewaarperiode](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Het menu voor het beleid voor een differentiële back-up openen](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen.

    * U kunt slechts één differentiële back-up per dag activeren.
    * Differentiële back-ups kunnen maximaal 180 dagen worden bewaard. Gebruik voor langere retentie volledige back-ups.

9. Selecteer **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.

10. Als u een back-upbeleid voor een transactielogboek wilt toevoegen, selecteert u **Logboekback-up**.
11. In **Logboekback-up** selecteert u **Inschakelen** en stelt u de frequentie- en bewaarinstellingen in. Logboek back-ups kunnen worden uitgevoerd op elke 15 minuten en kunnen Maxi maal 35 dagen worden bewaard.
12. Selecteer **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.

    ![Het logboekback-upbeleid bewerken](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Kies in het menu **back-upbeleid** of u **SQL-back-upcompressie** wilt inschakelen of niet. Deze optie is standaard uitgeschakeld. Als deze functie is ingeschakeld, stuurt SQL Server een gecomprimeerde back-upstream naar de VDI.  Houd er rekening mee dat Azure Backup de standaard instellingen voor instantie niveau overschrijft met compressie/NO_COMPRESSION-component, afhankelijk van de waarde van dit besturings element.

14. Als u klaar bent met het bewerken van het back-upbeleid, selecteert u **OK**.

> [!NOTE]
> Elke logboek back-up wordt gekoppeld aan de vorige volledige back-up om een herstel keten te vormen. Deze volledige back-up wordt bewaard totdat de retentie van de laatste back-up van het logboek is verlopen. Dit kan betekenen dat de volledige back-up gedurende een extra periode wordt bewaard om ervoor te zorgen dat alle logboeken kunnen worden hersteld. We gaan ervan uit dat de gebruiker een wekelijkse volledige back-up, een dagelijks differentieel en twee uur logboeken heeft. Deze zijn allemaal 30 dagen bewaard. Maar de wekelijkse volledige kan echter echt worden opgeruimd/verwijderd nadat de volgende volledige back-up beschikbaar is, dat wil zeggen, na 30 en 7 dagen. Stel dat er een wekelijkse volledige back-up plaatsvindt op een zestiende. Conform het Bewaar beleid moet het worden bewaard tot dec 16de. De laatste keer dat de back-up van het logboek is gemaakt voor deze volledige, wordt de volgende geplande volledige, op nov 22. Totdat dit logboek beschikbaar is tot dec 22, kan de zestien 16de volledig niet worden verwijderd. Tot en met dec 22 wordt dus de ge16dede volledige nov bewaard.

## <a name="enable-auto-protection"></a>Automatische beveiliging inschakelen  

U kunt automatische beveiliging inschakelen om automatisch een back-up te maken van alle bestaande en toekomstige data bases naar een zelfstandig SQL Server exemplaar of naar een AlwaysOn-beschikbaarheids groep.

* Er is geen limiet voor het aantal data bases dat u tegelijk voor automatische beveiliging kunt selecteren.
* U kunt data bases niet selectief beveiligen of uitsluiten van beveiliging in een exemplaar op het moment dat u automatische beveiliging inschakelt.
* Als uw exemplaar al beveiligde data bases bevat, blijven ze beschermd onder hun respectieve beleid, zelfs nadat u automatische beveiliging hebt ingeschakeld. Alle niet-beveiligde data bases die later worden toegevoegd, hebben slechts één beleid dat u definieert op het moment van het inschakelen van automatische beveiliging, vermeld onder **back-up configureren**. U kunt echter later het beleid wijzigen dat is gekoppeld aan een automatisch beveiligde data base.  

Automatische beveiliging inschakelen:

  1. In **Items voor back-up** selecteert u het exemplaar waarvoor u automatische beveiliging wilt inschakelen.
  2. Selecteer de vervolg keuzelijst onder **AutoProtect**, kies **aan**en selecteer **OK**.

      ![Automatische beveiliging inschakelen voor de beschikbaarheids groep](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Back-up wordt voor alle databases tegelijk getriggerd en kan worden gevolgd in **Back-uptaken**.

Als u automatische beveiliging wilt uitschakelen, selecteert u de naam van het exemplaar onder **back-up configureren**en selecteert u vervolgens automatische **beveiliging uitschakelen** voor het exemplaar. Er wordt nog steeds een back-up van alle data bases gemaakt, maar toekomstige data bases worden niet automatisch beveiligd.

![Automatische beveiliging voor dat exemplaar uitschakelen](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:

* [Back-ups van SQL Server-data bases herstellen](restore-sql-database-azure-vm.md)
* [Back-ups van SQL Server-data bases beheren](manage-monitor-sql-database-backup.md)
