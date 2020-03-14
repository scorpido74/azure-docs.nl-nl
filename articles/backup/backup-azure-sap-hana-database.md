---
title: Back-ups maken van een SAP HANA Data Base naar Azure met Azure Backup
description: In dit artikel vindt u informatie over het maken van een back-up van een SAP HANA Data Base naar Azure virtual machines met de Azure Backup-service.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248058"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Back-ups maken van SAP HANA-databases in virtuele Azure-machines

SAP HANA-data bases zijn kritieke workloads waarvoor een laag herstel punt (RPO) en lange termijn retentie is vereist. U kunt back-ups maken van SAP HANA-data bases die worden uitgevoerd op Azure virtual machines (Vm's) met behulp van [Azure backup](backup-overview.md).

In dit artikel wordt beschreven hoe u een back-up maakt van SAP HANA-data bases die worden uitgevoerd op virtuele Azure-machines naar een Azure Backup Recovery Services kluis.

In dit artikel leert u hoe u:
> [!div class="checklist"]
>
> * Een kluis maken en configureren
> * Data bases ontdekken
> * Back-ups configureren
> * Een back-uptaak op aanvraag uitvoeren

>[!NOTE]
>**Voorlopig verwijderen voor SQL Server in azure VM en voorlopig verwijderen voor SAP Hana in azure VM-workloads** is nu beschikbaar als preview-versie.<br>
>Als u zich wilt aanmelden voor de preview, schrijft u voor AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Vereisten

Raadpleeg de [vereisten](tutorial-backup-sap-hana-db.md#prerequisites) en de [betekenis van het script dat voorafgaat](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) aan het registratie gedeelte voor het instellen van de Data Base voor back-up.

### <a name="set-up-network-connectivity"></a>Netwerk connectiviteit instellen

Voor alle bewerkingen vereist de SAP HANA VM verbinding met open bare IP-adressen van Azure. VM-bewerkingen (database detectie, het configureren van back-ups, het plannen van back-ups, herstel punten, enzovoort) mislukken zonder verbinding met open bare IP-adressen van Azure.

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

**Implementeer een HTTP-proxy server om verkeer te routeren**. Wanneer u een back-up maakt van een SAP HANA Data Base op een virtuele machine van Azure, gebruikt de back-upextensie op de VM de HTTPS-Api's om beheer opdrachten te verzenden naar Azure Backup en gegevens naar Azure Storage. Voor de back-upextensie wordt ook Azure AD gebruikt voor verificatie. Leid het verkeer van de back-upextensie voor deze drie services via de HTTP-proxy. De uitbrei dingen zijn het enige onderdeel dat is geconfigureerd voor toegang tot het open bare Internet.

Connectiviteits opties zijn onder andere de volgende voor delen en nadelen:

**Optie** | **Voordelen** | **Nadelen**
--- | --- | ---
IP-bereiken toestaan | Geen extra kosten | Complex om te beheren, omdat de IP-adresbereiken in de loop van de tijd veranderen. <br/><br/> Biedt toegang tot het hele Azure, niet alleen Azure Storage
NSG-service tags gebruiken | Eenvoudiger te beheren als bereik wijzigingen worden automatisch samengevoegd <br/><br/> Geen extra kosten <br/><br/> | Kan alleen worden gebruikt met Nsg's <br/><br/> Biedt toegang tot de volledige service
Azure Firewall FQDN-Tags gebruiken | Eenvoudiger te beheren omdat de vereiste FQDN-s automatisch worden beheerd | Kan alleen worden gebruikt met Azure Firewall
Een HTTP-proxy gebruiken | Nauw keurig beheer van de proxy via de opslag-Url's is toegestaan <br/><br/> Eén toegangs punt voor Internet toegang tot Vm's <br/><br/> Wijzigingen in het IP-adres van Azure worden niet onderhevig | Aanvullende kosten voor het uitvoeren van een virtuele machine met de proxy software

#### <a name="private-endpoints"></a>Privé-eind punten

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>De data bases ontdekken

1. Klik in de kluis in **aan**de slag op **back-up**. In **waar wordt uw workload uitgevoerd?** Selecteer **SAP Hana in azure VM**.
2. Klik op **detectie starten**. Hiermee initieert u de detectie van onbeveiligde virtuele Linux-machines in de kluis regio.

   * Na detectie worden onbeveiligde Vm's weer gegeven in de portal, vermeld op naam en resource groep.
   * Als een virtuele machine niet naar verwachting wordt vermeld, controleert u of er al een back-up is gemaakt in een kluis.
   * Meerdere virtuele machines kunnen dezelfde naam hebben, maar ze behoren tot verschillende resource groepen.

3. In **virtual machines selecteren**klikt u op de koppeling om het script te downloaden dat machtigingen biedt voor de Azure backup-service om toegang te krijgen tot de SAP Hana vm's voor database detectie.
4. Voer het script uit op elke VM die als host fungeert voor SAP HANA-data bases waarvan u een back-up wilt maken.
5. Nadat u het script op de virtuele machines hebt uitgevoerd, selecteert u de virtuele machines in **virtual machines selecteren**. Klik vervolgens op **Discover db's**.
6. Azure Backup detecteert alle SAP HANA-data bases op de virtuele machine. Tijdens de detectie wordt de virtuele machine door Azure Backup geregistreerd bij de kluis en wordt een extensie op de VM geïnstalleerd. Er is geen agent geïnstalleerd op de data base.

    ![SAP HANA-data bases ontdekken](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Back-up configureren  

Schakel nu back-up in.

1. Klik in stap 2 op **back-up configureren**.

    ![Configureer back-up](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Selecteer in **items selecteren waarvan u een back**-up wilt maken de data bases die u wilt beveiligen > **OK**.

    ![Items selecteren waarvan u een back-up wilt maken](./media/backup-azure-sap-hana-database/select-items.png)
3. Maak in **back-upbeleid** > **back-upbeleid kiezen**een nieuw back-upbeleid voor de data bases, in overeenstemming met de onderstaande instructies.

    ![Back-upbeleid kiezen](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Nadat u het beleid hebt gemaakt, klikt u in het menu **back-up** op **back-up inschakelen**.

    ![Back-up inschakelen](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Volg de voortgang van de configuratie van de back-up in het gebied **meldingen** van de portal.

### <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Een back-upbeleid definieert wanneer er back-ups worden gemaakt en hoe lang ze worden bewaard.

* Een beleid wordt gemaakt op kluisniveau.
* U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.

Geef de beleids instellingen als volgt op:

1. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op.

   ![Beleids naam invoeren](./media/backup-azure-sap-hana-database/policy-name.png)
2. In **Beleid voor een volledige back-up** selecteert u een **back-upfrequentie** en kiest u **Dagelijks** of **Wekelijks**.
   * **Dagelijks**: Selecteer het uur en de tijd zone waarin de back-uptaak wordt gestart.
       * U moet een volledige back-up uitvoeren. U kunt deze optie niet uitschakelen.
       * Klik op **Volledige back-up** om het beleid te bekijken.
       * U kunt geen differentiële back-ups maken voor dagelijkse volledige back-ups.
   * **Wekelijks**: Selecteer de dag van de week, het uur en de tijd zone waarin de back-uptaak wordt uitgevoerd.

   ![Back-upfrequentie selecteren](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. Configureer in **Bewaar termijn**de Bewaar instellingen voor de volledige back-up.
    * Alle opties zijn standaard geselecteerd. Wis de limieten voor het Bewaar bereik die u niet wilt gebruiken en stel de beperkingen in die u doet.
    * De minimale Bewaar periode voor elk type back-up (volledig/differentieel/logboek) is zeven dagen.
    * Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
    * De back-up voor een specifieke dag wordt gelabeld en bewaard op basis van het wekelijkse Bewaar bereik en de instelling.
    * De maandelijkse en jaarlijkse bewaarperioden werken op soortgelijke wijze.

4. Klik in het menu **beleid voor volledige back-up** op **OK** om de instellingen te accepteren.
5. Selecteer **differentiële back-up** om een differentieel beleid toe te voegen.
6. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen.
    * U kunt maximaal één differentiële back-up per dag activeren.
    * Differentiële back-ups kunnen maximaal 180 dagen worden bewaard. Als dat voor u te kort is, moet u volledige back-ups gebruiken.

    ![Differentiële back-upbeleid](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Incrementele back-ups worden momenteel niet ondersteund.

7. Klik op **OK** om het beleid op te slaan en terug te gaan naar het hoofd menu van het **back-upbeleid** .
8. Selecteer **logboek back-up** om een transactionele logboek back-upbeleid toe te voegen.
    * Selecteer in **logboek back-up**de optie **inschakelen**.  Dit kan niet worden uitgeschakeld omdat SAP HANA alle logboek back-ups beheert.
    * Stel de besturings elementen voor de frequentie en de retentie in.

    > [!NOTE]
    > Logboek back-ups gaan alleen naar de stroom nadat een geslaagde volledige back-up is voltooid.

9. Klik op **OK** om het beleid op te slaan en terug te gaan naar het hoofd menu van het **back-upbeleid** .
10. Klik op **OK**nadat u klaar bent met het definiëren van het back-upbeleid.

> [!NOTE]
> Elke logboek back-up wordt gekoppeld aan de vorige volledige back-up om een herstel keten te vormen. Deze volledige back-up wordt bewaard totdat de retentie van de laatste back-up van het logboek is verlopen. Dit kan betekenen dat de volledige back-up gedurende een extra periode wordt bewaard om ervoor te zorgen dat alle logboeken kunnen worden hersteld. We gaan ervan uit dat de gebruiker een wekelijkse volledige back-up, een dagelijks differentieel en twee uur logboeken heeft. Deze zijn allemaal 30 dagen bewaard. Maar de wekelijkse volledige kan echter echt worden opgeruimd/verwijderd nadat de volgende volledige back-up beschikbaar is, dat wil zeggen, na 30 en 7 dagen. Stel dat er een wekelijkse volledige back-up plaatsvindt op een zestiende. Conform het Bewaar beleid moet het worden bewaard tot dec 16de. De laatste keer dat de back-up van het logboek is gemaakt voor deze volledige, wordt de volgende geplande volledige, op nov 22. Totdat dit logboek beschikbaar is tot dec 22, kan de zestien 16de volledig niet worden verwijderd. Tot en met dec 22 wordt dus de ge16dede volledige nov bewaard.

## <a name="run-an-on-demand-backup"></a>Een back-up op aanvraag uitvoeren

Back-ups worden uitgevoerd volgens het beleids schema. U kunt als volgt een back-up op aanvraag uitvoeren:

1. Klik in het menu kluis op **Back-upitems**.
2. Selecteer in **Back-upitems**de virtuele machine waarop de SAP Hana-data base wordt uitgevoerd en klik vervolgens op **Nu back-up maken**.
3. In **Nu back-up**kunt u het besturings element kalender gebruiken om de laatste dag te selecteren dat het herstel punt moet worden bewaard. Klik vervolgens op **OK**.
4. De portal meldingen bewaken. U kunt de voortgang van de taak in het kluis dashboard controleren > **back-uptaken** > worden **uitgevoerd**. Afhankelijk van de grootte van de data base kan het maken van de eerste back-up enige tijd duren.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>SAP HANA Studio-back-up uitvoeren op een Data Base met Azure Backup ingeschakeld

Ga als volgt te werk als u een lokale back-up wilt maken (met behulp van HANA Studio) van een Data Base waarvan een back-up wordt gemaakt met Azure Backup:

1. Wacht totdat alle volledige of logboek back-ups voor de Data Base zijn voltooid. Controleer de status in SAP HANA Studio/cockpit.
2. Schakel logboek back-ups uit en stel de back-catalogus in op het bestands systeem voor de relevante data base.
3. U doet dit door te dubbel klikken op **systemdb** > **configuratie** > **Data Base** > **filter (logboek)** te selecteren.
4. Stel **enable_auto_log_backup** in op **Nee**.
5. Stel **log_backup_using_backint** in op **False**.
6. Maak een volledige back-up op aanvraag van de data base.
7. Wacht tot de volledige back-up en catalogus back-up zijn voltooid.
8. De vorige instellingen herstellen voor Azure:
    * Stel **enable_auto_log_backup** in op **Ja**.
    * Stel **log_backup_using_backint** in op **waar**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [herstellen van SAP Hana-data bases die worden uitgevoerd op virtuele Azure-machines](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Meer informatie over het [beheren van SAP Hana databases waarvan een back-up is gemaakt met behulp van Azure backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
