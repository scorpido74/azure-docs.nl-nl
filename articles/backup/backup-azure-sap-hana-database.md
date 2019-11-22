---
title: Back-ups maken van een SAP HANA Data Base naar Azure met Azure Backup
description: In dit artikel vindt u informatie over het maken van een back-up van een SAP HANA Data Base naar Azure virtual machines met de Azure Backup-service.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: ed47f18c9dabc685d6fbe02804562ef86a93190a
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285845"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Back-ups maken van SAP HANA-data bases in azure Vm's

SAP HANA-data bases zijn kritieke workloads waarvoor een laag herstel punt (RPO) en lange termijn retentie is vereist. U kunt back-ups maken van SAP HANA-data bases die worden uitgevoerd op Azure virtual machines (Vm's) met behulp van [Azure backup](backup-overview.md).

In dit artikel wordt beschreven hoe u een back-up maakt van SAP HANA-data bases die worden uitgevoerd op virtuele Azure-machines naar een Azure Backup Recovery Services kluis.

In dit artikel leert u hoe u:
> [!div class="checklist"]
>
> * Een kluis maken en configureren
> * Data bases ontdekken
> * Back-ups configureren
> * Een back-uptaak op aanvraag uitvoeren

## <a name="prerequisites"></a>Vereisten

Raadpleeg de secties [vereisten](tutorial-backup-sap-hana-db.md#prerequisites) en [machtigingen instellen](tutorial-backup-sap-hana-db.md#setting-up-permissions) om de Data Base voor back-up in te stellen.

### <a name="set-up-network-connectivity"></a>Netwerk connectiviteit instellen

Voor alle bewerkingen moet de SAP HANA VM verbinding hebben met open bare IP-adressen van Azure. VM-bewerkingen (database detectie, het configureren van back-ups, het plannen van back-ups, herstel punten, enzovoort) werken niet zonder connectiviteit. Verbinding maken door toegang te verlenen tot de IP-bereiken van het Azure-Data Center:

* U kunt de [IP-](https://www.microsoft.com/download/details.aspx?id=41653) adresbereiken voor Azure-data centers downloaden en vervolgens toegang tot deze IP-adressen toestaan.
* Als u netwerk beveiligings groepen (Nsg's) gebruikt, kunt u de code van de Cloud- [service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) gebruiken om alle open bare IP-adressen van Azure toe te staan. U kunt de [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) gebruiken om NSG-regels te wijzigen.
* Poort 443 moet worden toegevoegd aan de lijst met toegestane poorten omdat het Trans Port via HTTPS is.

## <a name="onboard-to-the-public-preview"></a>Onboarding voor de open bare preview

Onboarding voor de open bare preview als volgt:

* Registreer uw abonnements-ID bij de provider van de Recovery Services-service in de portal door [dit artikel te volgen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Voer deze cmdlet uit voor Power shell. Deze moet worden voltooid als ' geregistreerd '.

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

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
