---
title: Back-ups maken van een SAP HANA Data Base naar Azure met Azure Backup
description: In dit artikel vindt u informatie over het maken van een back-up van een SAP HANA Data Base naar Azure virtual machines met de Azure Backup-service.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 2f42b119c361eaddcb84dda738b50c8accc84f9c
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439750"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Back-ups maken van SAP HANA-databases in virtuele Azure-machines

SAP HANA-data bases zijn kritieke workloads waarvoor een laag herstel punt (RPO) en lange termijn retentie is vereist. U kunt back-ups maken van SAP HANA-data bases die worden uitgevoerd op Azure virtual machines (Vm's) met behulp van [Azure backup](backup-overview.md).

In dit artikel wordt beschreven hoe u een back-up maakt van SAP HANA-data bases die worden uitgevoerd op virtuele Azure-machines naar een Azure Backup Recovery Services kluis.

In dit artikel leert u het volgende:
> [!div class="checklist"]
>
> * Een kluis maken en configureren
> * Databases detecteren
> * Back-ups configureren
> * Een back-uptaak op aanvraag uitvoeren

>[!NOTE]
>Vanaf 1 augustus 2020 is SAP HANA back-up voor RHEL (7,4, 7,6, 7,7 & 8,1) algemeen beschikbaar.

>[!NOTE]
>**Voorlopig verwijderen voor SQL Server in azure VM en voorlopig verwijderen voor SAP Hana in azure VM-workloads** is nu beschikbaar als preview-versie.<br>
>Als u zich wilt aanmelden voor de preview, kunt u een e-mail sturen naar ons [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="prerequisites"></a>Vereisten

Raadpleeg de [vereisten](tutorial-backup-sap-hana-db.md#prerequisites) en de [betekenis van het script dat voorafgaat](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) aan het registratie gedeelte voor het instellen van de Data Base voor back-up.

### <a name="establish-network-connectivity"></a>Netwerkverbinding tot stand brengen

Voor alle bewerkingen vereist een SAP HANA-data base die wordt uitgevoerd op een virtuele Azure-machine verbinding met de Azure Backup-Service, Azure Storage en Azure Active Directory. Dit kan worden bereikt door gebruik te maken van privé-eind punten of door toegang te verlenen tot de vereiste open bare IP-adressen of FQDN. De juiste connectiviteit met de vereiste Azure-Services kan leiden tot fouten in bewerkingen als database detectie, het configureren van back-ups, het uitvoeren van back-ups en het herstellen van gegevens.

De volgende tabel geeft een lijst van de verschillende alternatieven die u kunt gebruiken om verbinding te maken:

| **Optie**                        | **Voordelen**                                               | **Nadelen**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Privé-eindpunten                 | Back-ups via privé-IP-adressen in het virtuele netwerk toestaan  <br><br>   Nauw keurig beheer van het netwerk en de kluis zijde bieden | Standaard [kosten](https://azure.microsoft.com/pricing/details/private-link/) voor privé-eind punten |
| NSG-service Tags                  | Eenvoudiger om te beheren omdat veranderingen in het bereik automatisch worden samengevoegd   <br><br>   Geen extra kosten | Kan alleen worden gebruikt met NSG's  <br><br>    Biedt toegang tot de gehele service |
| Azure Firewall FQDN-Tags          | Eenvoudiger te beheren omdat de vereiste FQDN-s automatisch worden beheerd | Kan alleen worden gebruikt met Azure Firewall                         |
| Toegang tot FQDN/Ip's van service toestaan | Geen extra kosten   <br><br>  Werkt met alle netwerk beveiligings apparaten en firewalls | Er is mogelijk een groot aantal IP-adressen of FQDN-namen vereist om toegang te krijgen   |
| Een HTTP-proxy gebruiken                 | Eén internettoegangspunt voor virtuele machines                       | Extra kosten voor het uitvoeren van een virtuele machine met de proxysoftware         |

Meer informatie over het gebruik van deze opties vindt u hieronder:

#### <a name="private-endpoints"></a>Privé-eindpunten

Met persoonlijke eind punten kunt u veilig verbinding maken met servers in een virtueel netwerk naar uw Recovery Services kluis. Het persoonlijke eind punt gebruikt een IP-adres van de VNET-Address ruimte voor uw kluis. Het netwerk verkeer tussen uw resources binnen het virtuele netwerk en de kluis wordt verplaatst naar het virtuele netwerk en een privé-koppeling in het micro soft backbone-netwerk. Dit elimineert de bloot stelling van het open bare Internet. Lees [hier](./private-endpoints.md)meer over privé-eind punten voor Azure backup.

#### <a name="nsg-tags"></a>NSG-Tags

Als u netwerk beveiligings groepen (NSG) gebruikt, gebruikt u de *AzureBackup* -servicetag om uitgaande toegang tot Azure backup toe te staan. Naast het Azure Backup-label moet u ook connectiviteit voor verificatie en gegevens overdracht toestaan door soort gelijke [NSG regels](../virtual-network/security-overview.md#service-tags) te maken voor *Azure AD* en *Azure Storage*.  In de volgende stappen wordt het proces beschreven voor het maken van een regel voor de Azure Backup-tag:

1. In **Alle services** gaat u naar **Netwerkbeveiligingsgroepen** en selecteert u de netwerkbeveiligingsgroep.

1. Selecteer de optie **Uitgaande beveiligingsregels** onder **Instellingen**.

1. Selecteer **Toevoegen**. Voer alle vereiste details in voor het maken van een nieuwe regel, zoals beschreven in de [instellingen voor beveiligingsregels](../virtual-network/manage-network-security-group.md#security-rule-settings). Zorg ervoor dat de optie **bestemming** is ingesteld op *service label* en **doel service label** is ingesteld op *AzureBackup*.

1. Klik op **toevoegen** om de zojuist gemaakte uitgaande beveiligings regel op te slaan.

U kunt ook NSG uitgaande beveiligings regels maken voor Azure Storage en Azure AD. Zie [dit artikel](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)voor meer informatie over service tags.

#### <a name="azure-firewall-tags"></a>Azure Firewall Tags

Als u Azure Firewall gebruikt, maakt u een toepassings regel met behulp van de *AzureBackup* [Azure firewall FQDN-tag](../firewall/fqdn-tags.md). Hiermee wordt alle uitgaande toegang tot Azure Backup.

#### <a name="allow-access-to-service-ip-ranges"></a>Toegang tot de service-IP-adresbereiken toestaan

Als u ervoor kiest om toegang tot service Ip's toe te staan, raadpleegt u de IP-bereiken in het JSON-bestand dat u [hier](https://www.microsoft.com/download/confirmation.aspx?id=56519)kunt vinden. U moet toegang tot IP-adressen die overeenkomen met Azure Backup, Azure Storage en Azure Active Directory toestaan.

#### <a name="allow-access-to-service-fqdns"></a>Toegang tot FQDN-Services toestaan

U kunt ook de volgende FQDN-namen gebruiken om toegang te verlenen tot de vereiste services van uw servers:

| Service    | Te openen domein namen                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Toegang tot FQDN-gebieden onder de secties 56 en 59 toestaan volgens [dit artikel](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Een HTTP-proxy server gebruiken om verkeer te routeren

Wanneer u een back-up maakt van een SAP HANA data base die wordt uitgevoerd op een virtuele machine van Azure, gebruikt de back-upextensie op de VM de HTTPS-Api's om beheer opdrachten naar Azure Backup en gegevens naar Azure Storage te verzenden. De back-upextensie maakt ook gebruik van Azure AD voor verificatie. Leid het verkeer van de back-upextensie voor deze drie services via de HTTP-proxy. Gebruik de lijst met IP-adressen en FQDN-namen die hierboven worden genoemd om toegang tot de vereiste services toe te staan. Geverifieerde proxy servers worden niet ondersteund.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>De databases detecteren

1. Klik in de kluis bij **Aan de slag** op **Back-up**. In **Waar wordt uw werkbelasting uitgevoerd?** selecteert u **SAP HANA in Azure-VM**.
2. Klik op **Detectie starten**. Hiermee start u de detectie van niet-beveiligde virtuele Linux-machines in de regio van de kluis.

   * Na detectie worden onbeveiligde Vm's weer gegeven in de portal, vermeld op naam en resource groep.
   * Als een virtuele machine niet naar verwachting wordt vermeld, controleert u of er al een back-up is gemaakt in een kluis.
   * Meerdere virtuele machines kunnen dezelfde naam hebben, maar ze behoren tot verschillende resource groepen.

3. In **Virtuele machines selecteren** klikt u op de koppeling om het script dat machtigingen biedt voor de Azure Backup-service te downloaden, voor toegang tot de virtuele SAP HANA-machines voor de detectie van databases.
4. Voer het script uit op elke VM die als host fungeert voor SAP HANA-data bases waarvan u een back-up wilt maken.
5. Nadat u het script op de virtuele machines hebt uitgevoerd, selecteert u de virtuele machines in **virtual machines selecteren**. Klik vervolgens op **DB's detecteren**.
6. Azure Backup detecteert alle SAP HANA databases op de virtuele machine. Tijdens het detecteren registreert Azure Backup de virtuele machine met de kluis en wordt een extensie op de virtuele machine geïnstalleerd. Er is geen agent geïnstalleerd op de database.

    ![SAP HANA-data bases ontdekken](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Back-up configureren  

Schakel nu back-up in.

1. Klik in stap 2 op **back-up configureren**.

    ![Configureer back-up](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Selecteer in **items selecteren waarvan u een back**-up wilt maken de data bases die u wilt beveiligen > **OK**.

    ![Items selecteren waarvan u een back-up wilt maken](./media/backup-azure-sap-hana-database/select-items.png)
3. Kies in **back-upbeleid**  >  **back-upbeleid**, maak een nieuw back-upbeleid voor de data bases, in overeenstemming met de onderstaande instructies.

    ![Back-upbeleid kiezen](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Nadat u het beleid hebt gemaakt, klikt u in het menu **back-up** op **back-up inschakelen**.

    ![Back-up inschakelen](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Volg de voortgang van de back-upconfiguratie in het **Systeemvak** van de portal.

### <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Een back-upbeleid bepaalt wanneer back-ups worden gemaakt en hoe lang ze worden bewaard.

* Een beleid wordt gemaakt op kluisniveau.
* U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.

>[!NOTE]
>Azure Backup past de tijd niet automatisch aan de zomertijd aan wanneer u een back-up van een SAP HANA-database maakt die op een virtuele Azure-machine wordt uitgevoerd.
>
>Pas het beleid waar nodig handmatig aan.

Geef als volgt de beleidsinstellingen op:

1. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op.

   ![Beleids naam invoeren](./media/backup-azure-sap-hana-database/policy-name.png)
2. In **Beleid voor een volledige back-up** selecteert u een **back-upfrequentie** en kiest u **Dagelijks** of **Wekelijks**.
   * **Dagelijks**: Selecteer het uur en de tijd zone waarin de back-uptaak wordt gestart.
       * U moet een volledige back-up uitvoeren. U kunt deze optie niet uitschakelen.
       * Klik op **Volledige back-up** om het beleid te bekijken.
       * U kunt geen differentiële back-ups maken voor dagelijkse volledige back-ups.
   * **Wekelijks**: Selecteer de dag van de week, het uur en de tijd zone waarin de back-uptaak wordt uitgevoerd.

   ![Back-upfrequentie selecteren](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. In **Bewaartermijn** configureert u de bewaarinstellingen voor de volledige back-up.
    * Alle opties zijn standaard geselecteerd. Wis de limieten voor het Bewaar bereik die u niet wilt gebruiken en stel de beperkingen in die u doet.
    * De minimale bewaarperiode voor elk type back-up (volledig/differentieel/logboek) is zeven dagen.
    * Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
    * De back-up voor een specifieke dag wordt getagd en bewaard op basis van de wekelijkse bewaarperiode en uw instellingen.
    * De maandelijkse en jaarlijkse bewaarperioden werken op soortgelijke wijze.

4. In het menu **Beleid voor een volledige back-up** klikt u op **OK** om de instellingen te accepteren.
5. Selecteer **differentiële back-up** om een differentieel beleid toe te voegen.
6. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen.
    * U kunt maximaal één differentiële back-up per dag activeren.
    * Differentiële back-ups kunnen maximaal 180 dagen worden bewaard. Als dat voor u te kort is, moet u volledige back-ups gebruiken.

    ![Beleid voor differentiële back-ups](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Incrementele back-ups worden momenteel niet ondersteund.

7. Klik op **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.
8. Selecteer **Logboekback-up** als u een back-upbeleid voor een transactielogboek wilt toevoegen;
    * Selecteer in **logboek back-up**de optie **inschakelen**.  Dit kan niet worden uitgeschakeld omdat SAP HANA alle logboek back-ups beheert.
    * Stel de besturings elementen voor de frequentie en de retentie in.

    > [!NOTE]
    > Logboek back-ups gaan alleen naar de stroom nadat een geslaagde volledige back-up is voltooid.

9. Klik op **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.
10. Zodra u het back-upbeleid hebt gedefinieerd, klikt u op **OK**.

> [!NOTE]
> Elke logboek back-up wordt gekoppeld aan de vorige volledige back-up om een herstel keten te vormen. Deze volledige back-up wordt bewaard totdat de retentie van de laatste back-up van het logboek is verlopen. Dit kan betekenen dat de volledige back-up gedurende een extra periode wordt bewaard om ervoor te zorgen dat alle logboeken kunnen worden hersteld. We gaan ervan uit dat de gebruiker een wekelijkse volledige back-up, een dagelijks differentieel en twee uur logboeken heeft. Deze zijn allemaal 30 dagen bewaard. Maar de wekelijkse volledige kan echter echt worden opgeruimd/verwijderd nadat de volgende volledige back-up beschikbaar is, dat wil zeggen, na 30 en 7 dagen. Stel dat er een wekelijkse volledige back-up plaatsvindt op een zestiende. Volgens het Bewaar beleid moet het worden bewaard tot dec 16de. De laatste keer dat de back-up van het logboek is gemaakt voor deze volledige, wordt de volgende geplande volledige, op nov 22. Totdat dit logboek beschikbaar is tot dec 22, kan de zestien 16de volledig niet worden verwijderd. Tot en met dec 22 wordt dus de ge16dede volledige nov bewaard.

## <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

Back-ups worden uitgevoerd volgens het beleids schema. U kunt als volgt een back-up op aanvraag uitvoeren:

1. Klik in het menu kluis op **Back-upitems**.
2. Selecteer in **Back-upitems**de virtuele machine waarop de SAP Hana-data base wordt uitgevoerd en klik vervolgens op **Nu back-up maken**.
3. Kies in **Nu back-up**het type back-up dat u wilt uitvoeren. Klik vervolgens op **OK**. Deze back-up wordt bewaard op basis van het beleid dat aan dit back-upitem is gekoppeld.
4. De portal meldingen bewaken. U kunt de voortgang van de taak in het kluis dashboard controleren > **back-uptaken**worden  >  **uitgevoerd**. Afhankelijk van de grootte van de data base kan het maken van de eerste back-up enige tijd duren.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>SAP HANA Studio-back-up uitvoeren op een Data Base met Azure Backup ingeschakeld

Ga als volgt te werk als u een lokale back-up wilt maken (met behulp van HANA Studio) van een Data Base waarvan een back-up wordt gemaakt met Azure Backup:

1. Wacht totdat alle volledige of logboek back-ups voor de Data Base zijn voltooid. Controleer de status in SAP HANA Studio/cockpit.
1. Schakel logboek back-ups uit en stel de back-catalogus in op het bestands systeem voor de relevante data base.
1. U doet dit door te dubbel klikken op **SystemDB**-  >  **configuratie**  >  **database**  >  **filter (logboek)**.
1. Stel **enable_auto_log_backup** in op **Nee**.
1. Stel **log_backup_using_backint** in op **False**.
1. Stel **catalog_backup_using_backint** in op **False**.
1. Maak een volledige back-up op aanvraag van de data base.
1. Wacht tot de volledige back-up en catalogus back-up zijn voltooid.
1. De vorige instellingen herstellen voor Azure:
    * Stel **enable_auto_log_backup** in op **Ja**.
    * Stel **log_backup_using_backint** in op **waar**.
    * Stel **catalog_backup_using_backint** in op **waar**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het herstellen van SAP HANA-databases die worden uitgevoerd op virtuele Azure-machines](./sap-hana-db-restore.md)
* Meer informatie over [het beheren van SAP HANA-databases waarvan een back-up wordt gemaakt met behulp van Azure Backup](./sap-hana-db-manage.md)
