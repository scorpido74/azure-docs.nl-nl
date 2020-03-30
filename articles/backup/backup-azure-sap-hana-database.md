---
title: Een back-up maken van een SAP HANA-database naar Azure met Azure Backup
description: In dit artikel vindt u een back-up van een SAP HANA-database naar virtuele Azure-machines met de Azure Backup-service.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248058"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Back-ups maken van SAP HANA-databases in virtuele Azure-machines

SAP HANA-databases zijn kritieke workloads die een lage herstelpuntdoelstelling (RPO) en langdurige retentie vereisen. U een back-up maken van SAP HANA-databases die worden uitgevoerd op Virtuele Azure-machines (VM's) met [Azure Backup.](backup-overview.md)

In dit artikel ziet u hoe u een back-up maakt van SAP HANA-databases die worden uitgevoerd op Azure VM's naar een Azure Backup Recovery Services-kluis.

In dit artikel leert u hoe u:
> [!div class="checklist"]
>
> * Een kluis maken en configureren
> * Databases ontdekken
> * Back-ups configureren
> * Een back-uptaak op aanvraag uitvoeren

>[!NOTE]
>**Soft delete for SQL server in Azure VM and soft delete for SAP HANA in Azure VM workloads** is now available in preview.<br>
>Als u zich wilt aanmelden voor de preview, schrijft u ons opAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Vereisten

Raadpleeg de [vereisten](tutorial-backup-sap-hana-db.md#prerequisites) en het [script Wat doet het preregistratiescript](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) om de database in te stellen voor back-up.

### <a name="set-up-network-connectivity"></a>Netwerkconnectiviteit instellen

Voor alle bewerkingen vereist de SAP HANA VM connectiviteit met openbare IP-adressen van Azure. VM-bewerkingen (databasedetectie, back-ups configureren, back-ups plannen, herstelherstelpunten, enzovoort) mislukken zonder connectiviteit met openbare IP-adressen van Azure.

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

**Een HTTP-proxyserver implementeren om verkeer te routeren**. Wanneer u een back-up maakt van een SAP HANA-database op een Azure VM, gebruikt de back-upextensie op de VM de HTTPS-API's om beheeropdrachten naar Azure Backup en gegevens naar Azure Storage te verzenden. De back-upextensie maakt ook gebruik van Azure AD voor verificatie. Leid het verkeer van de back-upextensie voor deze drie services via de HTTP-proxy. De extensies zijn het enige onderdeel dat is geconfigureerd voor toegang tot het openbare internet.

Connectiviteitsopties omvatten de volgende voor- en nadelen:

**Optie** | **Voordelen** | **Nadelen**
--- | --- | ---
IP-bereiken toestaan | Geen extra kosten | Complex om te beheren omdat de ip-adresbereiken in de loop van de tijd veranderen <br/><br/> Biedt toegang tot het geheel van Azure, niet alleen Azure Storage
NSG-servicetags gebruiken | Eenvoudiger te beheren als bereikwijzigingen automatisch worden samengevoegd <br/><br/> Geen extra kosten <br/><br/> | Kan alleen met NSG's worden gebruikt <br/><br/> Biedt toegang tot de gehele service
Azure Firewall FQDN-tags gebruiken | Eenvoudiger te beheren omdat de vereiste FQDN's automatisch worden beheerd | Kan alleen met Azure Firewall worden gebruikt
Een HTTP-proxy gebruiken | Granulaire besturingselement in de proxy ten opzichte van de opslag-URL's is toegestaan <br/><br/> Eén punt van internettoegang tot VM's <br/><br/> Niet onderhevig aan wijzigingen in Azure IP-adres | Extra kosten voor het uitvoeren van een VM met de proxysoftware

#### <a name="private-endpoints"></a>Privéeindpunten

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Ontdek de databases

1. Klik in de kluis in **Aan de slag**op **Back-up**. Selecteer **SAP HANA in Azure VM**in Waar wordt uw **werkbelasting uitgevoerd?**
2. Klik **op Detectie starten**. Dit initieert de ontdekking van onbeschermde Linux VM's in het kluisgebied.

   * Na detectie worden onbeveiligde VM's weergegeven in de portal, weergegeven op naam en resourcegroep.
   * Als een virtuele machine niet wordt weergegeven zoals verwacht, controleert u of er al een back-up is gemaakt in een kluis.
   * Meerdere VM's kunnen dezelfde naam hebben, maar ze behoren tot verschillende resourcegroepen.

3. Klik **in Virtuele machines selecteren**op de koppeling om het script te downloaden waarmee machtigingen voor de Azure Backup-service zijn opgenomen om toegang te krijgen tot de SAP HANA VM's voor databasedetectie.
4. Voer het script uit op elke VM-hosting van SAP HANA-databases waarvan u een back-up wilt maken.
5. Nadat u het script op de VM's hebt uitgevoerd, selecteert u in **Virtuele machines**selecteren de VM's. Klik vervolgens op **DB's ontdekken**.
6. Azure Backup detecteert alle SAP HANA-databases op de VM. Tijdens de detectie registreert Azure Backup de VM met de kluis en installeert het een extensie op de VM. Er is geen agent geïnstalleerd in de database.

    ![Ontdek SAP HANA databases](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Back-up configureren  

Schakel nu back-up in.

1. Klik in stap 2 op **Back-up configureren**.

    ![Configureer back-up](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Selecteer **in Items selecteren om een back-up te maken**alle databases die u wilt beveiligen > **OK**.

    ![Items selecteren om een back-up van te maken](./media/backup-azure-sap-hana-database/select-items.png)
3. Maak **in Back-upbeleid** > **Een back-upbeleid**voor de databases, in overeenstemming met de onderstaande instructies.

    ![Back-upbeleid kiezen](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Klik na het maken van het beleid in het menu **Back-up** op **Back-up inschakelen**.

    ![Back-up inschakelen](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Houd de voortgang van de back-upconfiguratie bij in het gebied **Meldingen** van de portal.

### <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Een back-upbeleid bepaalt wanneer back-ups worden gemaakt en hoe lang deze worden bewaard.

* Een beleid wordt gemaakt op kluisniveau.
* U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.

Geef de beleidsinstellingen als volgt op:

1. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op.

   ![Beleidsnaam invoeren](./media/backup-azure-sap-hana-database/policy-name.png)
2. In **Beleid voor een volledige back-up** selecteert u een **back-upfrequentie** en kiest u **Dagelijks** of **Wekelijks**.
   * **Dagelijks**: Selecteer de uur- en tijdzone waarin de back-uptaak begint.
       * Je moet een volledige back-up draaien. Je deze optie niet uitschakelen.
       * Klik op **Volledige back-up** om het beleid te bekijken.
       * U kunt geen differentiële back-ups maken voor dagelijkse volledige back-ups.
   * **Wekelijks**: Selecteer de dag van de week, het uur en de tijdzone waarin de back-uptaak wordt uitgevoerd.

   ![Back-upfrequentie selecteren](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. Configureer in **bewaarbereik**bewaarinstellingen voor de volledige back-up.
    * Standaard zijn alle opties geselecteerd. Duidelijke bewaarlimieten die u niet wilt gebruiken en stel de limieten in die u wel wilt gebruiken.
    * De minimale bewaartermijn voor elk type back-up (volledig/differentieel/log) is zeven dagen.
    * Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
    * De back-up voor een bepaalde dag wordt getagd en behouden op basis van het wekelijkse bewaarbereik en de instelling.
    * De maandelijkse en jaarlijkse bewaarperioden werken op soortgelijke wijze.

4. Klik in het **menu Volledige back-up** op **OK** om de instellingen te accepteren.
5. Selecteer **Differentiële back-up** om een differentieelbeleid toe te voegen.
6. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen.
    * U kunt maximaal één differentiële back-up per dag activeren.
    * Differentiële back-ups kunnen maximaal 180 dagen worden bewaard. Als dat voor u te kort is, moet u volledige back-ups gebruiken.

    ![Differentiaal back-upbeleid](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Incrementele back-ups worden momenteel niet ondersteund.

7. Klik op **OK** om het beleid op te slaan en terug te keren naar het **hoofdmenu Back-upbeleid.**
8. Selecteer **Logboekback-up** om een back-upbeleid voor een transactioneel logboek toe te voegen,
    * Selecteer in **Logboekbackup** **inschakelen .**  Dit kan niet worden uitgeschakeld omdat SAP HANA alle logboekback-ups beheert.
    * Stel de frequentie- en bewaarbesturingselementen in.

    > [!NOTE]
    > Logboekbackups beginnen pas te stromen nadat een succesvolle volledige back-up is voltooid.

9. Klik op **OK** om het beleid op te slaan en terug te keren naar het **hoofdmenu Back-upbeleid.**
10. Nadat u het back-upbeleid hebt gedefinieerd, klikt u op **OK**.

> [!NOTE]
> Elke logboekback-up is verbonden aan de vorige volledige back-up om een herstelketen te vormen. Deze volledige back-up wordt bewaard totdat de bewaring van de laatste logboekback-up is verlopen. Dit kan betekenen dat de volledige back-up wordt bewaard voor een extra periode om ervoor te zorgen dat alle logboeken kunnen worden hersteld. Laten we aannemen dat de gebruiker heeft een wekelijkse volledige back-up, dagelijks differentieel en 2 uur logs. Ze worden allemaal 30 dagen bewaard. Maar, de wekelijkse volledige kan echt worden opgeruimd / verwijderd pas na de volgende volledige back-up beschikbaar is, dat wil zeggen, na 30 + 7 dagen. Zeg, een wekelijkse volledige back-up gebeurt op 16 november. Volgens het bewaarbeleid moet het tot 16 december worden bewaard. De laatste log back-up voor deze volledige gebeurt voor de volgende geplande volledige, op 22 november. Totdat dit logboek beschikbaar is tot 22 december, kan de 16 november volledige niet worden verwijderd. Dus, de 16 november vol is behouden tot 22 december.

## <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

Back-ups worden uitgevoerd in overeenstemming met het beleidsschema. U een back-up op aanvraag als volgt uitvoeren:

1. Klik in het kluismenu op **Back-upitems**.
2. Selecteer **in Back-upitems**de VM waarop de SAP HANA-database wordt uitgevoerd en klik nu op **Back-up**.
3. Gebruik **in Nu back-up**het agendabesturingselement om de laatste dag te selecteren waarop het herstelpunt moet worden behouden. Klik vervolgens op **OK**.
4. Controleer de portalmeldingen. U de voortgang van de taak in het kluisdashboard controleren > **Back-uptaken** > **in uitvoering**. Afhankelijk van de grootte van uw database kan het maken van de eerste back-up even duren.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>SAP HANA Studio-back-up uitvoeren in een database met Azure Backup ingeschakeld

Als u een lokale back-up (met HANA Studio) wilt maken van een database waarvan een back-up wordt gemaakt met Azure Backup, gaat u als volgt te werk:

1. Wacht tot alle volledige of log back-ups voor de database te voltooien. Controleer de status in SAP HANA Studio / Cockpit.
2. Schakel logboekback-ups uit en stel de back-upcatalogus in op het bestandssysteem voor relevante database.
3. Dubbelklik hiervoor op **systemdb** > **Configuration** > **Select Database** > **Filter (Log).**
4. Stel **enable_auto_log_backup** in op **Nee.**
5. Stel **log_backup_using_backint** **in op False**.
6. Neem een on-demand volledige back-up van de database.
7. Wacht tot de volledige back-up en catalogusback-up is voltooid.
8. De vorige instellingen terugzetten naar die voor Azure:
    * Stel **enable_auto_log_backup** **in op Ja**.
    * Stel **log_backup_using_backint** **in op True**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [herstellen van SAP HANA-databases die worden uitgevoerd op Azure VM's](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Meer informatie over het [beheren van SAP HANA-databases waarvan een back-up wordt gemaakt met Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
