---
title: Zelf studie-back-ups maken van SAP HANA-data bases in azure Vm's
description: In deze zelf studie leert u hoe u een back-up maakt van SAP HANA-data bases die worden uitgevoerd op Azure VM naar een Azure Backup Recovery Services kluis.
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: a622370fca3144aeb6a5d7c071c227b3c21cf135
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288760"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Zelf studie: back-ups maken van SAP HANA-data bases in een Azure VM

In deze zelf studie leert u hoe u een back-up maakt van SAP HANA-data bases die worden uitgevoerd op Azure-Vm's naar een Azure Backup Recovery Services kluis. In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een kluis maken en configureren
> * Data bases ontdekken
> * Back-ups configureren

[Hier](sap-hana-backup-support-matrix.md#scenario-support) vindt u alle scenario's die momenteel worden ondersteund.

## <a name="onboard-to-the-public-preview"></a>Onboarding voor de open bare preview

Onboarding voor de open bare preview als volgt:

* Registreer uw abonnements-ID bij de provider van de Recovery Services-service in de portal door [dit artikel te volgen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).

* Voer deze cmdlet uit voor Power shell. Deze moet worden voltooid als ' geregistreerd '.

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u het volgende doet voordat u back-ups configureert:

1. Op de VM waarop de SAP HANA-data base wordt uitgevoerd, kunt u als volgt ODBC-stuur programmapakketten installeren en inschakelen vanuit het officiële SLES-pakket/-medium met behulp van Zypper:

```bash
sudo zypper update
sudo zypper install unixODBC
```

>[!NOTE]
> Als u de opslag plaatsen niet bijwerkt, moet u ervoor zorgen dat de versie van unixODBC mini maal 2.3.4 is. Als u wilt weten wat de versie van uniXODBC is, voert u `odbcinst -j` als root uit
>

2. Connectiviteit van de virtuele machine met Internet toestaan, zodat deze Azure kan bereiken, zoals wordt beschreven in de [onderstaande procedure](#set-up-network-connectivity).

3. Voer het script voor voorafgaande registratie uit in de virtuele machine waarin HANA is geïnstalleerd als een hoofd gebruiker. Met [Dit script](https://aka.ms/scriptforpermsonhana) worden de [juiste machtigingen](#setting-up-permissions)ingesteld.

## <a name="set-up-network-connectivity"></a>Netwerk connectiviteit instellen

Voor alle bewerkingen moet de SAP HANA VM verbinding hebben met open bare IP-adressen van Azure. VM-bewerkingen (database detectie, het configureren van back-ups, het plannen van back-ups, herstel punten, enzovoort) werken niet zonder connectiviteit. Verbinding maken door toegang te verlenen tot de IP-bereiken van het Azure-Data Center:

* U kunt de [IP-](https://www.microsoft.com/download/details.aspx?id=41653) adresbereiken voor Azure-data centers downloaden en vervolgens toegang tot deze IP-adressen toestaan.
* Als u netwerk beveiligings groepen (Nsg's) gebruikt, kunt u de code van de Cloud- [service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) gebruiken om alle open bare IP-adressen van Azure toe te staan. U kunt de [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) gebruiken om NSG-regels te wijzigen.
* Poort 443 moet worden toegevoegd aan de lijst met toegestane poorten omdat het Trans Port via HTTPS is.

## <a name="setting-up-permissions"></a>Machtigingen instellen

Het pre-registratie script voert de volgende acties uit:

1. Maakt AZUREWLBACKUPHANAUSER in het HANA-systeem en voegt deze vereiste rollen en machtigingen toe:
   * DATABASE beheerder: Maak tijdens het terugzetten een nieuwe Db's.
   * CATALOGUS gelezen: voor het lezen van de back-catalogus.
   * SAP_INTERNAL_HANA_SUPPORT: voor toegang tot een paar persoonlijke tabellen.
2. Voegt een sleutel toe aan Hdbuserstore voor de HANA-invoeg toepassing voor het afhandelen van alle bewerkingen (database query's, herstel bewerkingen, het configureren en uitvoeren van back-ups).

U kunt controleren of de sleutel is gemaakt door de opdracht HDBSQL uit te voeren op de HANA-computer met SIDADM-referenties:

```hdbsql
hdbuserstore list
```

De uitvoer van de opdracht moet de sleutel {SID} {DBNAME} bevatten, waarbij de gebruiker als AZUREWLBACKUPHANAUSER wordt weer gegeven.

>[!NOTE]
> Zorg ervoor dat u een unieke set SSFS-bestanden hebt onder/usr/sap/{SID}/home/.hdb/. Dit pad mag slechts één map bevatten.
>

## <a name="create-a-recovery-service-vault"></a>Een Recovery service-kluis maken

Een Recovery Services kluis is een entiteit waarin de back-ups en herstel punten worden opgeslagen die in de loop van de tijd zijn gemaakt. De Recovery Services kluis bevat ook het back-upbeleid dat is gekoppeld aan de beveiligde virtuele machines.

Een Recovery Services-kluis maken:

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

2. Selecteer in het menu links **alle services**

![Alle services selecteren](./media/tutorial-backup-sap-hana-db/all-services.png)

3. In het dialoogvenster **Alle services** voert u **Recovery Services** in. De lijst met resources filtert op basis van uw invoer. Selecteer **Recovery Services kluizen**in de lijst met resources.

![Recovery Services kluizen selecteren](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Selecteer **toevoegen**op het **Recovery Services** kluizen-dash board.

![Recovery Services kluis toevoegen](./media/tutorial-backup-sap-hana-db/add-vault.png)

Het dialoog venster **Recovery Services kluis** wordt geopend. Geef waarden op voor de **naam, het abonnement, de resource groep** en de **locatie**

![Een Recovery Services-kluis maken](./media/tutorial-backup-sap-hana-db/create-vault.png)

* **Naam**: de naam wordt gebruikt voor het identificeren van de Recovery Services-kluis en moet uniek zijn voor het Azure-abonnement. Geef een naam op met ten minste twee, maar niet meer dan 50 tekens. De naam moet beginnen met een letter en mag alleen bestaan uit letters, cijfers en afbreek streepjes. Voor deze zelf studie hebben we de naam **SAPHanaVault**gebruikt.
* **Abonnement**: Kies het abonnement dat u wilt gebruiken. Als u lid bent van slechts één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u moet gebruiken, gebruikt u het standaard abonnement (aanbevolen). Er zijn alleen meerdere opties als uw werk-of school account is gekoppeld aan meer dan één Azure-abonnement. Hier hebben we het abonnement abonnement voor de **SAP Hana Solution Lab** gebruikt.
* **Resource groep**: gebruik een bestaande resource groep of maak een nieuwe. Hier hebben we **SAPHANADemo**gebruikt.<br>
Als u de lijst met beschik bare resource groepen in uw abonnement wilt weer geven, selecteert u **bestaande gebruiken**en selecteert u vervolgens een resource in de vervolg keuzelijst. Als u een nieuwe resource groep wilt maken, selecteert u **nieuwe maken** en voert u de naam in. Zie [Azure Resource Manager Overview](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)voor meer informatie over resource groepen.
* **Locatie**: Selecteer de geografische regio voor de kluis. De kluis moet zich in dezelfde regio bevinden als de virtuele machine met SAP HANA. We hebben **VS-Oost 2**gebruikt.

5. Selecteer **Controleren + maken**.

   ![Selecteer controleren & maken](./media/tutorial-backup-sap-hana-db/review-create.png)

De Recovery Services-kluis is nu gemaakt.

## <a name="discover-the-databases"></a>De data bases ontdekken

1. Klik in de kluis in **aan**de slag op **back-up**. In **waar wordt uw workload uitgevoerd?** Selecteer **SAP Hana in azure VM**.
2. Klik op **detectie starten**. Hiermee initieert u de detectie van onbeveiligde virtuele Linux-machines in de kluis regio. U ziet de virtuele Azure-machine die u wilt beveiligen.
3. In **virtual machines selecteren**klikt u op de koppeling om het script te downloaden dat machtigingen biedt voor de Azure backup-service om toegang te krijgen tot de SAP Hana vm's voor database detectie.
4. Voer het script uit op de VM die als host fungeert voor de SAP HANA data base (s) waarvan u een back-up wilt maken.
5. Nadat u het script op de virtuele machine hebt uitgevoerd, selecteert u de virtuele machine in **virtual machines selecteren**. Klik vervolgens op **Discover db's**.
6. Azure Backup detecteert alle SAP HANA-data bases op de virtuele machine. Tijdens de detectie wordt de virtuele machine door Azure Backup geregistreerd bij de kluis en wordt een extensie op de VM geïnstalleerd. Er is geen agent geïnstalleerd op de data base.

   ![De data bases ontdekken](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Back-up configureren

Nu de data bases waarvan u een back-up wilt maken, zijn gedetecteerd, kunt u back-up inschakelen.

1. Klik op **back-up configureren**.

![Back-up configureren](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Selecteer in **items selecteren**waarvan u een back-up wilt maken een of meer data bases die u wilt beveiligen en klik vervolgens op **OK**.

![Items selecteren waarvan u een back-up wilt maken](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. Maak in **back-upbeleid > back-upbeleid kiezen**een nieuw back-upbeleid voor de data base (s), in overeenstemming met de instructies in de volgende sectie.

![Back-upbeleid kiezen](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Nadat u het beleid hebt gemaakt, klikt u in het **menu back-up**op **back-up inschakelen**.

   ![Klik op back-up inschakelen](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Volg de voortgang van de configuratie van de back-up in het gebied **meldingen** van de portal.

## <a name="creating-a-backup-policy"></a>Een back-upbeleid maken

Een back-upbeleid definieert wanneer er back-ups worden gemaakt en hoe lang ze worden bewaard.

* Een beleid wordt gemaakt op kluisniveau.
* U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.

Geef de beleids instellingen als volgt op:

1. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op. In dit geval voert u **SAPHANA**in.

![Voer een naam in voor het nieuwe beleid](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. Selecteer een **back-upfrequentie**in het **beleid volledige back-up**. U kunt **dagelijks** of **wekelijks**kiezen. Voor deze zelf studie hebben we de **dagelijkse** back-up gekozen.

![Een back-upfrequentie selecteren](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. Configureer in **Bewaar termijn**de Bewaar instellingen voor de volledige back-up.
   * Standaard zijn alle opties geselecteerd. Wis de limieten voor het Bewaar bereik die u niet wilt gebruiken en stel deze in.
   * De minimale Bewaar periode voor elk type back-up (volledig/differentieel/logboek) is zeven dagen.
   * Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
   * De back-up voor een specifieke dag wordt gelabeld en bewaard op basis van het wekelijkse Bewaar bereik en de instelling.
   * De maandelijkse en jaarlijkse bewaarperioden werken op soortgelijke wijze.
4. Klik in het menu **beleid voor volledige back-up** op **OK** om de instellingen te accepteren.
5. Selecteer vervolgens **differentiële back-up** om een differentieel beleid toe te voegen.
6. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen. We hebben elke **zondag** om **2:00 uur**een differentiële back-up ingeschakeld, die **30 dagen**wordt bewaard.

   ![Differentiële back-upbeleid](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

>[!NOTE]
>Incrementele back-ups worden momenteel niet ondersteund.
>

7. Klik op **OK** om het beleid op te slaan en terug te gaan naar het hoofd menu van het **back-upbeleid** .
8. Selecteer **logboek back-up** om een transactionele logboek back-upbeleid toe te voegen.
   * **Logboek back-up** is standaard ingesteld op **inschakelen**. Dit kan niet worden uitgeschakeld omdat SAP HANA alle logboek back-ups beheert.
   * We hebben **twee uur** ingesteld als back-upschema en **15 dagen** aan Bewaar periode.

    ![Logboek back-upbeleid](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

>[!NOTE]
> Logboek back-ups worden alleen naar de stroom begonnen nadat een volledige back-up is voltooid.
>

9. Klik op **OK** om het beleid op te slaan en terug te gaan naar het hoofd menu van het **back-upbeleid** .
10. Klik op **OK**nadat u klaar bent met het definiëren van het back-upbeleid.

U hebt nu back-ups geconfigureerd voor uw SAP HANA-data base (s).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [uitvoeren van on-demand back-ups op SAP Hana data bases die worden uitgevoerd op virtuele machines van Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Meer informatie over het [herstellen van SAP Hana-data bases die worden uitgevoerd op virtuele Azure-machines](sap-hana-db-restore.md)
* Meer informatie over het [beheren van SAP Hana databases waarvan een back-up is gemaakt met behulp van Azure backup](sap-hana-db-manage.md)
* Informatie over het [oplossen van veelvoorkomende problemen bij het maken van back-ups van SAP Hana-data bases](backup-azure-sap-hana-database-troubleshoot.md)
