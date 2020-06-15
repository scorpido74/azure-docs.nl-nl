---
title: 'Zelfstudie: back-ups maken van SAP HANA-databases in virtuele Azure-machines'
description: In deze zelfstudie ontdekt u hoe u een back-up naar een Azure Backup Recovery Services-kluis maakt van SAP HANA-databases die op een virtuele Azure-machine worden uitgevoerd.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 52ffc6bf83ff2a2dcc22fd7c5ad8ab1480f9ce50
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417290"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Zelfstudie: Een back-up maken van SAP HANA-databases in een Azure-VM

In deze zelfstudie ziet u hoe u een back-up naar een Azure Backup Recovery Services-kluis maakt van SAP HANA-databases die op een virtuele Azure-machine worden uitgevoerd. In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een kluis maken en configureren
> * Databases detecteren
> * Back-ups configureren

[Hier](sap-hana-backup-support-matrix.md#scenario-support) vindt u alle scenario's die we momenteel ondersteunen.

>[!NOTE]
>[Aan de slag](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) met de preview voor back-ups van SAP HANA voor RHEL (7.4, 7.6, 7.7 or 8.1). Neem voor verdere vragen contact met ons op via [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).

## <a name="prerequisites"></a>Vereisten

Doe het volgende voordat u back-ups gaat configureren:

* Sta connectiviteit van de virtuele machine naar internet toe, zodat de virtuele machine Azure kan bereiken. Dit wordt beschreven in de onderstaande procedure [De netwerkverbinding instellen](#set-up-network-connectivity).
* Er moet een sleutel in de **hdbuserstore** bestaan waarmee aan de volgende criteria wordt voldaan:
  * De sleutel moet aanwezig zijn in de standaard-**hdbuserstore**. De standaardwaarde is het `<sid>adm`-account waaronder SAP HANA is geïnstalleerd.
  * Voor MDC moet de sleutel verwijzen naar de SQL-poort van **NAMESERVER**. In het geval van SDC moet worden verwezen naar de SQL-poort van **INDEXSERVER**
  * De sleutel moet over referenties beschikken om gebruikers toe te voegen en te verwijderen
* Voer als de hoofdgebruiker het SAP HANA-back-upconfiguratiescript uit (script vóór registratie) op de virtuele machine waarop HANA is geïnstalleerd. [Met behulp van dit script](https://aka.ms/scriptforpermsonhana) wordt het HANA-systeem voorbereid op de back-up. Raadpleeg de sectie [Wat doet het script vóór registratie](#what-the-pre-registration-script-does) voor meer informatie over het script vóór registratie.

>[!NOTE]
>Het script voor registratie vooraf installeert de **compat-unixODBC234** voor SAP HANA-workloads die worden uitgevoerd op RHEL (7.4, 7.6 en 7.7) en **unixODBC** voor RHEL 8.1. [Dit pakket bevindt zich in de opslagplaats RHEL for SAP HANA (voor RHEL 7 Server) Update Services for SAP Solutions (RPM's)](https://access.redhat.com/solutions/5094721).  Voor de Azure Marketplace RHEL-installatie kopie is de opslagplaats **rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms**.

## <a name="set-up-network-connectivity"></a>De netwerkverbinding instellen

Voor alle bewerkingen is voor de virtuele SAP HANA-machine een verbinding met openbare IP-adressen van Azure nodig. VM-bewerkingen (databasedetectie, back-ups configureren, back-ups plannen, herstelpunten herstellen, enzovoort) zullen mislukken zonder verbinding met de openbare IP-adressen van Azure.

Breng een verbinding tot stand met behulp van één van de volgende opties:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>De IP-bereiken voor Azure Datacenter toestaan

Door deze optie zijn de [IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653) in het gedownloade bestand toegestaan. Voor toegang tot een NSG (netwerkbeveiligingsgroep) gebruikt u de cmdlet Set-AzureNetworkSecurityRule. Als uw lijst met veilige ontvangers alleen regiospecifieke IP-adressen bevat, moet u ook de lijst met veilige ontvangers en de servicetag van Azure Active Directory (Azure AD) bijwerken om verificatie in te schakelen.

### <a name="allow-access-using-nsg-tags"></a>Toegang toestaan met behulp van NSG-tags

Als u NSG gebruikt om de connectiviteit te beperken, moet u de AzureBackup-servicetag gebruiken om uitgaande toegang tot Azure Backup toe te staan. Daarnaast moet u ook connectiviteit voor verificatie en gegevensoverdracht toestaan met behulp van [regels](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) voor Azure AD en Azure Storage. U kunt dit instellen via Azure Portal of via PowerShell.

U kunt als volgt een regel maken via de portal:

  1. In **Alle services** gaat u naar **Netwerkbeveiligingsgroepen** en selecteert u de netwerkbeveiligingsgroep.
  2. Selecteer de optie **Uitgaande beveiligingsregels** onder **Instellingen**.
  3. Selecteer **Toevoegen**. Voer alle vereiste details in voor het maken van een nieuwe regel, zoals beschreven in de [instellingen voor beveiligingsregels](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Controleer of de optie **Doel** is ingesteld op **Servicetag** en **Doelservicetag** is ingesteld op **AzureBackup**.
  4. Klik op **Toevoegen** om de zojuist gemaakt uitgaande beveiligingsregel op te slaan.

U kunt als volgt een regel maken met behulp van PowerShell:

 1. Azure-accountreferenties toevoegen en de nationale clouds bijwerken<br/>
      `Add-AzureRmAccount`<br/>

 2. Het NSG-abonnement selecteren<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. De NSG selecteren<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. De regel Uitgaand toestaan toevoegen voor Azure Backup-servicetag<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. De regel Uitgaand toestaan toevoegen voor Storage-servicetag<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. De regel Uitgaand toestaan toevoegen voor AzureActiveDirectory-servicetag<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. De NSG opslaan<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Toegang toestaan met behulp van Azure Firewall-tags**. Als u Azure Firewall gebruikt, maakt u een toepassingsregel met behulp van de [FQDN-tag](https://docs.microsoft.com/azure/firewall/fqdn-tags) AzureBackup. Hierdoor is uitgaande toegang tot Azure Backup toegestaan.

**Implementeer een HTTP-proxyserver om verkeer te routeren**. Wanneer u een back-up maakt van een SAP HANA-database op een virtuele Azure-machine, gebruikt de back-upextensie op de virtuele machine de HTTPS-API's voor het verzenden van beheeropdrachten naar Azure Backup en gegevens naar Azure Storage. De back-upextensie maakt ook gebruik van Azure AD voor verificatie. Leid het verkeer van de back-upextensie voor deze drie services via de HTTP-proxy. De extensies zijn het enige onderdeel dat wordt geconfigureerd voor toegang tot het openbare internet.

Onder ander de volgende voor- en nadelen behoren tot de connectiviteitsopties:

**Optie** | **Voordelen** | **Nadelen**
--- | --- | ---
IP-bereiken toestaan | Geen extra kosten | Ingewikkeld om te beheren omdat de IP-adresbereiken na verloop van tijd veranderen <br/><br/> Biedt toegang tot heel Azure, niet alleen tot Azure Storage
NSG-servicetags gebruiken | Eenvoudiger om te beheren omdat veranderingen in het bereik automatisch worden samengevoegd <br/><br/> Geen extra kosten <br/><br/> | Kan alleen worden gebruikt met NSG's <br/><br/> Biedt toegang tot de gehele service
Azure Firewall FQDN-tags gebruiken | Eenvoudiger om te beheren omdat de vereiste FQDN's automatisch worden beheerd | Kan alleen worden gebruikt met Azure Firewall
Een HTTP-proxy gebruiken | Gedetailleerde controle via de proxy over de opslag-URL's is toegestaan <br/><br/> Eén internettoegangspunt voor virtuele machines <br/><br/> Niet onderhevig aan Azure-IP-adreswijzigingen | Extra kosten voor het uitvoeren van een virtuele machine met de proxysoftware

## <a name="what-the-pre-registration-script-does"></a>Wat doet het script vóór registratie

Door het script vóór registratie uit te voeren, worden de volgende functies uitgevoerd:

* Afhankelijk van uw Linux-distributie installeert het script alle eventueel benodigde pakketten die vereist zijn door de Azure Backup-agent of werkt deze bij.
* De uitgaande netwerkverbinding met Azure Backup-servers en afhankelijke services zoals Azure Active Directory en Azure Storage wordt gecontroleerd.
* Er worden logboeken vastgelegd in uw HANA-systeem met behulp van de gebruikerssleutel die wordt vermeld als onderdeel van de [vereisten](#prerequisites). De gebruikerssleutel wordt gebruikt om een back-upgebruiker (AZUREWLBACKUPHANAUSER) in het HANA-systeem te maken, en de gebruikerssleutel kan worden verwijderd nadat het script vóór registratie is uitgevoerd.
* Deze vereiste rollen en machtigingen worden aan AZUREWLBACKUPHANAUSER toegewezen:
  * DATABASE ADMIN (in het geval van MDC) en BACKUP ADMIN (in het geval van SDC): om nieuwe databases te maken tijdens de herstelbewerking.
  * CATALOG READ: om de back-upcatalogus te lezen.
  * SAP_INTERNAL_HANA_SUPPORT: voor toegang tot een aantal privétabellen.
* Het script voegt een sleutel toe aan **hdbuserstore** voor AZUREWLBACKUPHANAUSER, zodat de HANA-back-upinvoegtoepassing alle bewerkingen (databasequery's, herstelbewerkingen, back-ups configureren en uitvoeren) kan verwerken.

>[!NOTE]
> U kunt de gebruikerssleutel die wordt vermeld als onderdeel van de [vereisten](#prerequisites) expliciet als een parameter aan het script vóór registratie doorgeven: `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Gebruik de opdracht `bash msawb-plugin-config-com-sap-hana.sh --help` voor meer informatie over de andere parameters die door het script worden geaccepteerd

Als u het maken van de sleutel wilt bevestigen, voert u de HDBSQL-opdracht uit op de HANA-machine met SIDADM-referenties:

```hdbsql
hdbuserstore list
```

In de uitvoer van de opdracht moet de sleutel {SID}{DBNAME} worden weergegeven en moet de gebruiker als AZUREWLBACKUPHANAUSER worden weergegeven.

>[!NOTE]
> Controleer of u over een unieke set SSFS-bestanden beschikt onder `/usr/sap/{SID}/home/.hdb/`. Als het goed is, bevat dit pad maar één map.

## <a name="create-a-recovery-service-vault"></a>Een Recovery Service-kluis maken

Een Recovery Service-kluis is een entiteit waarin de back-ups en herstelpunten worden opgeslagen die in de loop van de tijd zijn gemaakt. De Recovery Service-kluis bevat ook de beleidsregels voor back-up die aan de beveiligde virtuele machines zijn gekoppeld.

Een Recovery Services-kluis maken:

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

2. Selecteer in het menu links **Alle services**.

   ![Alle services selecteren](./media/tutorial-backup-sap-hana-db/all-services.png)

3. In het dialoogvenster **Alle services** voert u **Recovery Services** in. De lijst met resources wordt gefilterd op basis van uw invoer. In de lijst met resources selecteert u **Recovery Service-kluizen**.

   ![Recovery Services-kluizen selecteren](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Op het dashboard van de **Recovery Services**-kluizen selecteert u **Toevoegen**.

   ![Recovery Services-kluis toevoegen](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Het dialoogvenster **Recovery Service-kluis** wordt geopend. Waarden opgeven voor **Naam, Abonnement, Resourcegroep** en **Locatie**

   ![Een Recovery Services-kluis maken](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Naam**: De naam wordt gebruikt om de Recovery Service-kluis te identificeren en deze moet uniek zijn in het Azure-abonnement. Geef een naam op van minimaal 2 en maximaal 50 tekens. De naam moet beginnen met een letter en mag alleen uit letters, cijfers en afbreekstreepjes bestaan. Voor deze zelfstudie hebben we de naam **SAPHanaVault** gebruikt.
   * **Abonnement**: Kies het abonnement dat u wilt gebruiken. Als u lid bent van maar één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u moet gebruiken, gebruikt u het standaardabonnement (voorgesteld). Er zijn alleen meerdere mogelijkheden als uw werk- of schoolaccount is gekoppeld aan meerdere Azure-abonnementen. Hier hebben we het **SAP HANA Solution Lab-abonnement** gebruikt.
   * **Resourcegroep**: Gebruik een bestaande resourcegroep of maak een nieuwe. Hier hebben we **SAPHANADemo** gebruikt.<br>
   Als u de lijst met beschikbare resourcegroepen in uw abonnement wilt weergeven, selecteert u **Bestaande gebruiken** en vervolgens selecteert u een resource uit de vervolgkeuzelijst. Als u een nieuwe resourcegroep wilt maken, selecteert u **Nieuwe maken** en voert u de naam in. Zie [Overzicht van Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) voor meer informatie over resourcegroepen.
   * **Locatie**: Selecteer de geografische regio voor de kluis. De kluis moet zich in dezelfde regio bevinden als de virtuele machine waarop SAP HANA wordt uitgevoerd. We hebben **US - oost 2** gebruikt.

5. Selecteer **Controleren + maken**.

   ![Controleren + maken selecteren](./media/tutorial-backup-sap-hana-db/review-create.png)

De Recovery Service-kluis is nu gemaakt.

## <a name="discover-the-databases"></a>De databases detecteren

1. Klik in de kluis bij **Aan de slag** op **Back-up**. In **Waar wordt uw werkbelasting uitgevoerd?** selecteert u **SAP HANA in Azure-VM**.
2. Klik op **Detectie starten**. Hiermee start u de detectie van niet-beveiligde virtuele Linux-machines in de regio van de kluis. U ziet de virtuele Azure-machine die u wilt beveiligen.
3. In **Virtuele machines selecteren** klikt u op de koppeling om het script dat machtigingen biedt voor de Azure Backup-service te downloaden, voor toegang tot de virtuele SAP HANA-machines voor de detectie van databases.
4. Voer het script uit op de virtuele machine waarop de SAP HANA-database(s) wordt/worden gehost waarvan u een back-up wilt maken.
5. Nadat het script op de virtuele machine is uitgevoerd, selecteert u de virtuele machine in **Virtuele machines selecteren**. Klik vervolgens op **DB's detecteren**.
6. Azure Backup detecteert alle SAP HANA databases op de virtuele machine. Tijdens het detecteren registreert Azure Backup de virtuele machine met de kluis en wordt een extensie op de virtuele machine geïnstalleerd. Er is geen agent geïnstalleerd op de database.

   ![De databases detecteren](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Back-up configureren

Nu de databases waarvan we een back-up willen maken zijn gedetecteerd, gaan we back-up inschakelen.

1. Klik op **Back-up configureren**.

   ![Back-up configureren](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. In **Items selecteren waarvan u een back-up wilt maken** selecteert u een of meer databases die u wilt beveiligen en vervolgens klikt u op **OK**.

   ![Items selecteren waarvan u een back-up wilt maken](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. In **Back-upbeleid > Back-upbeleid kiezen** maakt u een nieuw back-upbeleid voor de database(s), in overeenstemming met de instructies in de volgende sectie.

   ![Back-upbeleid kiezen](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Nadat u het beleid heb gemaakt, klikt u in het **menu Back-up** op **Back-up inschakelen**.

   ![Op Back-up inschakelen klikken](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Volg de voortgang van de back-upconfiguratie in het **Systeemvak** van de portal.

## <a name="creating-a-backup-policy"></a>Een back-upbeleid maken

Een back-upbeleid bepaalt wanneer back-ups worden gemaakt en hoe lang ze worden bewaard.

* Een beleid wordt gemaakt op kluisniveau.
* U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.

Geef als volgt de beleidsinstellingen op:

1. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op. Voer in dit geval **SAPHANA** in.

   ![Een naam voor het nieuwe beleid invoeren](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. In **Beleid voor volledige back-ups** selecteert u een **Back-upfrequentie**. U kunt **Dagelijks** of **Wekelijks** kiezen. Voor deze zelfstudie hebben we de **dagelijkse** back-up gekozen.

   ![Een back-upfrequentie selecteren](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. In **Bewaartermijn** configureert u de bewaarinstellingen voor de volledige back-up.
   * Standaard zijn alle opties geselecteerd. Schakel alle bewaartermijnlimieten uit die u niet wilt gebruiken en stel de limieten in die u wilt gebruiken.
   * De minimale bewaarperiode voor elk type back-up (volledig/differentieel/logboek) is zeven dagen.
   * Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
   * De back-up voor een specifieke dag wordt getagd en bewaard op basis van de wekelijkse bewaarperiode en uw instellingen.
   * De maandelijkse en jaarlijkse bewaarperioden werken op soortgelijke wijze.
4. In het menu **Beleid voor een volledige back-up** klikt u op **OK** om de instellingen te accepteren.
5. Selecteer vervolgens **Differentiële back-up** om een beleid voor differentiële back-ups toe te voegen.
6. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen. We hebben een differentiële back-up ingeschakeld op elke **zondag** om **2.00 uur**. Deze back-up wordt gedurende **30 dagen** bewaard.

   ![Beleid voor differentiële back-ups](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Incrementele back-ups worden momenteel niet ondersteund.
   >

7. Klik op **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.
8. Selecteer **Logboekback-up** als u een back-upbeleid voor een transactielogboek wilt toevoegen;
   * standaard is **Logboekback-up** ingesteld op **Inschakelen**. Dit kan niet worden uitgeschakeld omdat SAP HANA alle logboekback-ups beheert.
   * We hebben **2 uur** als back-upschema ingesteld, en een bewaarperiode van **15 dagen**.

    ![Beleid voor logboekback-ups](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > De stroom van logboekback-ups begint pas nadat één volledige back-up is voltooid.
   >

9. Klik op **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.
10. Zodra u het back-upbeleid hebt gedefinieerd, klikt u op **OK**.

U hebt nu back-up(s) voor uw SAP HANA-database(s) geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het uitvoeren van on-demand back-ups op SAP HANA-databases die worden uitgevoerd op virtuele Azure-machines](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Meer informatie over [het herstellen van SAP HANA-databases die worden uitgevoerd op virtuele Azure-machines](sap-hana-db-restore.md)
* Meer informatie over [het beheren van SAP HANA-databases waarvan een back-up wordt gemaakt met behulp van Azure Backup](sap-hana-db-manage.md)
* Meer informatie over [het oplossen van algemene problemen tijdens het maken van back-ups van SAP HANA-databases](backup-azure-sap-hana-database-troubleshoot.md)
