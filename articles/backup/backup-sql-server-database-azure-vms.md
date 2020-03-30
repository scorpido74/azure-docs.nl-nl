---
title: Back-ups maken van SQL Server-databases in virtuele Azure-machines
description: In dit artikel leest u hoe u een back-up maakt van SQL Server-databases op virtuele Azure-machines met Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5b10907738feeecbec06669175e82578f2915f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273330"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Back-ups maken van SQL Server-databases in virtuele Azure-machines

SQL Server-databases zijn kritieke workloads waarvoor een RPO (low recovery-point objective) en langetermijnretentie vereist zijn. U een back-up maken van SQL Server-databases die worden uitgevoerd op Virtuele Azure-machines (VM's) met [Azure Backup.](backup-overview.md)

In dit artikel ziet u hoe u een back-up maakt van een SQL Server-database die wordt uitgevoerd op een Azure VM naar een Azure Backup Recovery Services-kluis.

In dit artikel leer je hoe je:

> [!div class="checklist"]
>
> * Een kluis maken en configureren.
> * Databases ontdekken en back-ups instellen.
> * Automatische beveiliging van databases instellen.

>[!NOTE]
>**Soft delete for SQL server in Azure VM and soft delete for SAP HANA in Azure VM workloads** is now available in preview.<br>
>Als u zich wilt aanmelden voor de preview, schrijft u ons opAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Vereisten

Controleer de volgende criteria voordat u een back-up maakt van een SQL Server-database:

1. Een vault [van Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) identificeren of maken in dezelfde regio en een abonnement als de VM die het SQL Server-exemplaar host.
2. Controleer of de VM [netwerkconnectiviteit](backup-sql-server-database-azure-vms.md#establish-network-connectivity)heeft.
3. Controleer of de SQL Server-databases de [richtlijnen voor databasenaamgeving voor Azure Backup](#database-naming-guidelines-for-azure-backup)volgen.
4. Controleer of er geen andere back-upoplossingen zijn ingeschakeld voor de database. Schakel alle andere SQL Server-back-ups uit voordat u een back-up van de database maakt.

> [!NOTE]
> U Azure Backup inschakelen voor een Azure VM en ook voor een SQL Server-database die zonder conflict op de VM wordt uitgevoerd.

### <a name="establish-network-connectivity"></a>Netwerkverbinding tot stand brengen

Voor alle bewerkingen vereist een SQL Server VM connectiviteit met openbare IP-adressen van Azure. VM-bewerkingen (databasedetectie, back-ups configureren, back-ups plannen, herstelherstelpunten, enzovoort) mislukken zonder connectiviteit met openbare IP-adressen van Azure.

De connectiviteit tot stand brengen met een van de volgende opties:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>De IP-bereiken van Azure-datacenter toestaan

Met deze optie kunnen de [IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653) in het gedownloade bestand worden weergegeven. Als u toegang wilt krijgen tot een netwerkbeveiligingsgroep (NSG), gebruikt u de cmdlet Set-AzureNetworkSecurityRule. Als uw lijst met veilige ontvangers alleen regiospecifieke IP's bevat, moet u ook de lijst met veilige ontvangers bijwerken van de Azure Active Directory-servicetag (Azure AD) om verificatie in te schakelen.

#### <a name="allow-access-using-nsg-tags"></a>Toegang toestaan met NSG-tags

Als u NSG gebruikt om de connectiviteit te beperken, moet u azurebackup-servicetag gebruiken om uitgaande toegang tot Azure Backup mogelijk te maken. Daarnaast moet u ook connectiviteit toestaan voor verificatie en gegevensoverdracht met behulp van [regels](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) voor Azure AD en Azure Storage. Dit kan via de Azure-portal of via PowerShell.

Ga als lid van het nieuwe bedrijf naar een regel met behulp van de portal:

  1. Ga in **Alle services**naar **netwerkbeveiligingsgroepen** en selecteer de netwerkbeveiligingsgroep.
  2. Selecteer **Uitgaande beveiligingsregels** onder **Instellingen**.
  3. Selecteer **Toevoegen**. Voer alle vereiste gegevens in voor het maken van een nieuwe regel zoals beschreven in [de instellingen van de beveiligingsregel](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Controleer of de optie **Bestemming** is ingesteld op **servicetag** en **doelservicetag** is ingesteld op **AzureBackup.**
  4. Klik **op Toevoegen**om de nieuw gemaakte uitgaande beveiligingsregel op te slaan.

Ga als een toepassing op PowerShell:

 1. Azure-accountreferenties toevoegen en de nationale clouds bijwerken<br/>
      `Add-AzureRmAccount`<br/>

 2. Selecteer het NSG-abonnement<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Selecteer de NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Uitgaande regel toestaan toevoegen voor Azure Backup-servicetag<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Uitgaande regel toestaan toevoegen voor opslagservicetag<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Uitgaande regel toestaan toevoegen voor AzureActiveDirectory-servicetag<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Sla de NSG op<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Toegang toestaan met Azure Firewall-tags**. Als u Azure Firewall gebruikt, maakt u een toepassingsregel met de AzureBackup [FQDN-tag](https://docs.microsoft.com/azure/firewall/fqdn-tags). Hierdoor heeft u uitgaande toegang tot Azure Backup.

**Een HTTP-proxyserver implementeren om verkeer te routeren**. Wanneer u een back-up maakt van een SQL Server-database op een Azure VM, gebruikt de back-upextensie op de VM de HTTPS-API's om beheeropdrachten naar Azure Backup en gegevens naar Azure Storage te verzenden. De back-upextensie maakt ook gebruik van Azure AD voor verificatie. Leid het verkeer van de back-upextensie voor deze drie services via de HTTP-proxy. Er zijn geen jokerdomeinen die worden gebruikt met Azure Backup om toe te voegen aan de lijst toestaan voor uw proxyregels. U moet de openbare IP-bereiken gebruiken voor deze services die door Azure worden geleverd. De extensies zijn het enige onderdeel dat is geconfigureerd voor toegang tot het openbare internet.

Connectiviteitsopties omvatten de volgende voor- en nadelen:

**Optie** | **Voordelen** | **Nadelen**
--- | --- | ---
IP-bereiken toestaan | Geen extra kosten | Complex om te beheren omdat de ip-adresbereiken in de loop van de tijd veranderen <br/><br/> Biedt toegang tot het geheel van Azure, niet alleen Azure Storage
NSG-servicetags gebruiken | Eenvoudiger te beheren als bereikwijzigingen automatisch worden samengevoegd <br/><br/> Geen extra kosten <br/><br/> | Kan alleen met NSG's worden gebruikt <br/><br/> Biedt toegang tot de gehele service
Azure Firewall FQDN-tags gebruiken | Eenvoudiger te beheren omdat de vereiste FQDN's automatisch worden beheerd | Kan alleen met Azure Firewall worden gebruikt
Een HTTP-proxy gebruiken | Eén punt van internettoegang tot VM's <br/> | Extra kosten voor het uitvoeren van een VM met de proxysoftware <br/> Geen gepubliceerde FQDN-adressen, regels toestaan zijn onderhevig aan wijzigingen in azure-IP-adres

#### <a name="private-endpoints"></a>Privéeindpunten

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

### <a name="database-naming-guidelines-for-azure-backup"></a>Richtlijnen voor databasenaamgeving voor Azure Backup

Vermijd het gebruik van de volgende elementen in databasenamen:

* Trailing en leidende spaties
* Achterblijvende uitroeptekens (!)
* Vierkante haakjes sluiten (])
* Puntkomma ';'
* Slash '/' doorsturen

Aliasing is beschikbaar voor niet-ondersteunde tekens, maar we raden u aan deze tekens te vermijden. Zie [Het gegevensmodel van de tabelservice](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN) voor meer informatie.

>[!NOTE]
>De bewerking **Beveiliging configureren** voor databases met speciale tekens zoals '+' of '&' in hun naam wordt niet ondersteund. U de naam van de database wijzigen of **Autobeveiliging**inschakelen, waardoor deze databases met succes kunnen worden beschermd.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server-databases detecteren

Databases ontdekken die op een virtuele machine worden uitgevoerd:

1. In de [Azure-portal](https://portal.azure.com) opent u de Recovery Services-kluis die u gebruikt om een back-up te maken van de database.

2. Selecteer **Back-up**in het **kluisdashboard van Recovery Services** .

   ![Backup selecteren om het menu Doel van de back-up te openen](./media/backup-azure-sql-database/open-backup-menu.png)

3. Stel in **Back-updoel**de set **Azure** **Waar wordt uw werkbelasting uitgevoerd?**

4. In **Waarvan wilt u een back-up maken?** selecteert u **SQL Server in Azure-VM**.

    ![SQL Server in Azure VM voor de back-up selecteren](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Selecteer **detectie starten** om te zoeken naar onbeveiligde VM's in het abonnement in **Back-updoeldetectie** > **in VM's.** Deze zoekopdracht kan een tijdje duren, afhankelijk van het aantal onbeschermde VM's in het abonnement.

   * Niet-beveiligde virtuele machines zouden na detectie in de lijst moeten verschijnen, gesorteerd op naam en resourcegroep.
   * Als een virtuele machine niet wordt weergegeven zoals u verwacht, controleert u of er al een back-up van is gemaakt in een kluis.
   * Meerdere VM's kunnen dezelfde naam hebben, maar ze behoren tot verschillende resourcegroepen.

     ![Back-up in behandeling tijdens het zoeken naar databases in VM's](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Selecteer in de lijst met virtuele machines de VM waarop de SQL Server-database wordt uitgevoerd > **DB's detecteren**.

7. Databasedetectie bijhouden in **Meldingen**. De tijd die nodig is voor deze actie is afhankelijk van het aantal VM-databases. Wanneer de geselecteerde databases zijn gedetecteerd, wordt er een slagingsbericht weergegeven.

    ![Bericht dat de implementatie is geslaagd](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup detecteert alle SQL Server-databases op de virtuele machine. Tijdens de ontdekking treden de volgende elementen op de achtergrond op:

    * Azure Backup registreert de VM met de kluis voor workloadback-up. Alle databases op de geregistreerde VM kunnen alleen worden geback-upt naar deze kluis.
    * Azure Backup installeert de extensie AzureBackupWindowsWorkload op de virtuele machine. Er is geen agent geïnstalleerd in een SQL-database.
    * Azure Backup maakt het serviceaccount NT Service\AzureWLBackupPluginSvc op de virtuele machine.
      * Het serviceaccount wordt gebruikt voor alle back-up- en herstelbewerkingen.
      * NT Service\AzureWLBackupPluginSvc vereist SQL sysadmin-machtigingen. Alle SQL Server VM's die in de Marketplace zijn gemaakt, worden geleverd met de SqlIaaSExtension geïnstalleerd. De extensie AzureBackupWindowsWorkload maakt gebruik van de extensie SQLIaaSExtension om automatische de benodigde machtigingen op te halen.
    * Als u de VM niet hebt gemaakt vanaf de Marketplace of als u SQL 2008 en 2008 R2 hebt ingeschakeld, heeft de VM mogelijk niet de SqlIaaSExtension geïnstalleerd en mislukt de detectiebewerking met het foutbericht UserErrorSQLNoSysAdminMembershipMembership. Als u dit probleem wilt oplossen, volgt u de instructies onder [VM-machtigingen instellen](backup-azure-sql-database.md#set-vm-permissions).

        ![De VM en database selecteren](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Back-up configureren  

1. Selecteer **Back-up**configureren in stap 2 **back-up voor** > back-up:**Back-up configureren**.

   ![Back-up configureren selecteren](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. In **Items selecteren tot back-up**ziet u alle geregistreerde beschikbaarheidsgroepen en zelfstandige SQL Server-exemplaren. Selecteer de pijl links van een rij om de lijst met alle onbeveiligde databases in dat exemplaar of de groep Always On beschikbaarheid uit te vouwen.  

    ![Alle SQL Server-exemplaren met zelfstandige databases weergeven](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Kies alle databases die u wilt beveiligen en selecteer **OK**.

   ![De database beveiligen](./media/backup-azure-sql-database/select-database-to-protect.png)

   Ter optimalisering van de back-upbelastingen stelt Azure Backup het maximumaantal databases in één back-uptaak in op 50.

     * Om meer dan 50 back-ups te beschermen moet u meerdere databases configureren.
     * Als u de hele instantie of de groep Beschikbaarheid altijd wilt [inschakelen,](#enable-auto-protection) selecteert u in de vervolgkeuzelijst **AUTOPROTECT** **AAN**en selecteert u **OK**.

    > [!NOTE]
    > De [functie voor automatische beveiliging](#enable-auto-protection) maakt niet alleen bescherming mogelijk op alle bestaande databases tegelijk, maar beschermt ook automatisch alle nieuwe databases die aan dat exemplaar of de beschikbaarheidsgroep zijn toegevoegd.  

4. Selecteer **OK** om **back-upbeleid**te openen .

    ![Automatische beveiliging inschakelen voor de groep Beschikbaarheid always on](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Kies **in Back-upbeleid**een beleid en selecteer **OK**.

   * Selecteer het standaardbeleid als HourlyLogBackup.
   * Een bestaand back-upbeleid kiezen dat u eerder hebt gemaakt voor SQL.
   * Een nieuw beleid definiëren op basis van uw RPO en retentiebereik.

     ![Back-upbeleid selecteren](./media/backup-azure-sql-database/select-backup-policy.png)

6. Selecteer **Back-up inschakelen**in **Back-up**.

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

1. Selecteer **Back-upbeleid** > **Toevoegen in**de kluis .
2. Selecteer SQL **Server in Azure VM in** **Toevoegen**om het beleidstype te definiëren.

   ![Een beleidstype voor het nieuwe back-upbeleid kiezen](./media/backup-azure-sql-database/policy-type-details.png)

3. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op.
4. Selecteer **in het beleid Volledige back-up**een **back-upfrequentie**. Kies **dagelijks** of **wekelijks**.

   * Als u **Dagelijks** kiest, selecteert u het tijdstip en de tijdzone waarop de back-uptaak moet worden gestart.
   * Als u **Wekelijks** kiest, selecteert u de dag van de week, het tijdstip en de tijdzone waarop de back-uptaak moet worden gestart.
   * Voer een volledige back-up uit, omdat u de optie **Volledige back-up** niet uitschakelen.
   * Selecteer **Volledige back-up** om het beleid weer te geven.
   * U kunt geen differentiële back-ups maken voor dagelijkse volledige back-ups.

     ![Velden voor nieuw back-upbeleid](./media/backup-azure-sql-database/full-backup-policy.png)  

5. In **BEWAARBEREIK**zijn alle opties standaard geselecteerd. Schakel de limieten voor het bewaarbereik uit die u niet wilt en stel de intervallen in die u wilt gebruiken.

    * Minimale bewaartermijn voor elk type back-up (volledig, differentieel en logboek) is zeven dagen.
    * Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
    * De back-up voor een bepaalde dag wordt getagd en bewaard op basis van het wekelijkse bewaarbereik en de wekelijkse bewaarinstelling.
    * Maandelijkse en jaarlijkse bewaarbereiken gedragen zich op een vergelijkbare manier.

       ![Intervalinstellingen voor bewaarperiode](./media/backup-azure-sql-database/retention-range-interval.png)

6. In het menu voor het **beleid voor een volledige back-up** selecteert u **OK** om de instellingen te accepteren.
7. Als u een beleid voor een differentiële back-up wilt toevoegen, selecteert u **Differentiële back-up**.

   ![Intervalinstellingen voor bewaarperiode](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Het menu voor het beleid voor een differentiële back-up openen](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen.

    * U slechts één differentiële back-up per dag activeren.
    * Differentiële back-ups kunnen maximaal 180 dagen worden bewaard. Gebruik volledige back-ups voor langere retentie.

9. Selecteer **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.

10. Als u een back-upbeleid voor een transactielogboek wilt toevoegen, selecteert u **Logboekback-up**.
11. In **Logboekback-up** selecteert u **Inschakelen** en stelt u de frequentie- en bewaarinstellingen in. Log back-ups kunnen zo vaak optreden als elke 15 minuten en kan worden bewaard voor maximaal 35 dagen.
12. Selecteer **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.

    ![Het logboekback-upbeleid bewerken](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Kies in het menu **Back-upbeleid** of u **SQL Backup Compression** wilt inschakelen of niet. Deze optie is standaard uitgeschakeld. Als sql server is ingeschakeld, stuurt het een gecomprimeerde back-upstream naar de VDI.  Houd er rekening mee dat Azure Backup standaardinstellingen op instantieniveau overschrijft met COMPRESSIE / NO_COMPRESSION-clausule, afhankelijk van de waarde van dit besturingselement.

14. Als u klaar bent met het bewerken van het back-upbeleid, selecteert u **OK**.

> [!NOTE]
> Elke logboekback-up is verbonden aan de vorige volledige back-up om een herstelketen te vormen. Deze volledige back-up wordt bewaard totdat de bewaring van de laatste logboekback-up is verlopen. Dit kan betekenen dat de volledige back-up wordt bewaard voor een extra periode om ervoor te zorgen dat alle logboeken kunnen worden hersteld. Laten we aannemen dat de gebruiker heeft een wekelijkse volledige back-up, dagelijks differentieel en 2 uur logs. Ze worden allemaal 30 dagen bewaard. Maar, de wekelijkse volledige kan echt worden opgeruimd / verwijderd pas na de volgende volledige back-up beschikbaar is, dat wil zeggen, na 30 + 7 dagen. Zeg, een wekelijkse volledige back-up gebeurt op 16 november. Volgens het bewaarbeleid moet het tot 16 december worden bewaard. De laatste log back-up voor deze volledige gebeurt voor de volgende geplande volledige, op 22 november. Totdat dit logboek beschikbaar is tot 22 december, kan de 16 november volledige niet worden verwijderd. Dus, de 16 november vol is behouden tot 22 december.

## <a name="enable-auto-protection"></a>Automatische beveiliging inschakelen  

U automatische beveiliging inschakelen om automatisch een back-up te maken van alle bestaande en toekomstige databases naar een zelfstandige SQL Server-instantie of naar een beschikbaarheidsgroep Always On.

* Er is geen limiet aan het aantal databases dat u selecteren voor automatische beveiliging in één keer.
* U databases niet selectief beschermen of uitsluiten van beveiliging in een instantie op het moment dat u automatische beveiliging inschakelt.
* Als uw instantie al een aantal beveiligde databases bevat, blijven ze beschermd onder hun respectieve beleid, zelfs nadat u automatische beveiliging hebt ingeschakeld. Alle onbeveiligde databases die later worden toegevoegd, hebben slechts één beleid dat u definieert op het moment dat u automatische beveiliging inschakelt, weergegeven onder **Back-up configureren.** U het beleid dat is gekoppeld aan een automatisch beveiligde database later echter wijzigen.  

Ga als gegaoft op automatische beveiliging:

  1. In **Items voor back-up** selecteert u het exemplaar waarvoor u automatische beveiliging wilt inschakelen.
  2. Selecteer de vervolgkeuzelijst onder **AUTOPROTECT,** kies **AAN**en selecteer **OK**.

      ![Automatische beveiliging inschakelen in de beschikbaarheidsgroep](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Back-up wordt voor alle databases tegelijk getriggerd en kan worden gevolgd in **Back-uptaken**.

Als u automatische beveiliging wilt uitschakelen, selecteert u de instantienaam onder **Back-up configureren**en selecteert u **Autoprotect uitschakelen** voor de instantie. Er wordt nog steeds een back-up van alle databases gemaakt, maar toekomstige databases worden niet automatisch beveiligd.

![Automatische beveiliging uitschakelen op dat exemplaar](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:

* [Back-upSQL Server-databases herstellen](restore-sql-database-azure-vm.md)
* [Back-upSQL Server-databases beheren](manage-monitor-sql-database-backup.md)
