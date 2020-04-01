---
title: Zelfstudie - Back-ups maken van SAP HANA-databases in Azure VM's
description: In deze zelfstudie leert u hoe u een back-up maakt van SAP HANA-databases die op Azure VM worden uitgevoerd, naar een azure backup recovery services-kluis.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: f64dd74ad0e038c5cad152e20ae2255de03114e3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79501448"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Zelfstudie: back-ups maken van SAP HANA-databases in een Azure-vm

In deze zelfstudie ziet u hoe u een back-up maakt van SAP HANA-databases die op Azure VM's worden uitgevoerd, naar een Azure Backup Recovery Services-kluis. In dit artikel leer je hoe je:

> [!div class="checklist"]
>
> * Een kluis maken en configureren
> * Databases ontdekken
> * Back-ups configureren

[Hier](sap-hana-backup-support-matrix.md#scenario-support) zijn alle scenario's die we momenteel ondersteunen.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u het volgende doet voordat u back-ups configureert:

* Sta connectiviteit van de VM toe op het internet, zodat deze Azure kan bereiken, zoals beschreven in de [onderstaande procedure voor netwerkconnectiviteit instellen.](#set-up-network-connectivity)
* Een sleutel moet bestaan in de **hdbuserstore** die voldoet aan de volgende criteria:
  * Het moet aanwezig zijn in de standaard **hdbuserstore**
  * Voor MDC moet de sleutel naar de SQL-poort van **NAMESERVER**wijzen. In het geval van SDC moet het wijzen op de SQL-poort van **INDEXSERVER**
  * Het moet referenties hebben om gebruikers toe te voegen en te verwijderen
* Voer het SAP HANA-back-upconfiguratiescript (preregistratiescript) uit in de virtuele machine waar HANA is geïnstalleerd, als hoofdgebruiker. [Dit script](https://aka.ms/scriptforpermsonhana) maakt het HANA-systeem klaar voor back-up. Raadpleeg de sectie [Wat doet het pre-registratiescript](#what-the-pre-registration-script-does) om meer te begrijpen over het pre-registratiescript.

## <a name="set-up-network-connectivity"></a>Netwerkconnectiviteit instellen

Voor alle bewerkingen vereist de SAP HANA VM connectiviteit met openbare IP-adressen van Azure. VM-bewerkingen (databasedetectie, back-ups configureren, back-ups plannen, herstelherstelpunten, enzovoort) mislukken zonder connectiviteit met openbare IP-adressen van Azure.

De connectiviteit tot stand brengen met een van de volgende opties:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>De IP-bereiken van Azure-datacenter toestaan

Met deze optie kunnen de [IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653) in het gedownloade bestand worden weergegeven. Als u toegang wilt krijgen tot een netwerkbeveiligingsgroep (NSG), gebruikt u de cmdlet Set-AzureNetworkSecurityRule. Als uw lijst met veilige ontvangers alleen regiospecifieke IP's bevat, moet u ook de lijst met veilige ontvangers bijwerken van de Azure Active Directory-servicetag (Azure AD) om verificatie in te schakelen.

### <a name="allow-access-using-nsg-tags"></a>Toegang toestaan met NSG-tags

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

## <a name="what-the-pre-registration-script-does"></a>Wat het preregistratiescript doet

Als u het preregistratiescript uitvoert, worden de volgende functies uitgevoerd:

* Het installeert of werkt alle benodigde pakketten die nodig zijn door de Azure Backup-agent op uw distributie.
* Het voert uitgaande netwerkconnectiviteitscontroles uit met Azure Backup-servers en afhankelijke services zoals Azure Active Directory en Azure Storage.
* Het logt in op uw HANA-systeem met behulp van de gebruiker sleutel vermeld als onderdeel van de [voorwaarden](#prerequisites). De gebruikerssleutel wordt gebruikt om een back-upgebruiker (AZUREWLBACKUPHANAUSER) in het HANA-systeem te maken en de gebruikerssleutel kan worden verwijderd nadat het preregistratiescript succesvol is uitgevoerd.
* AZUREWLBACKUPHANAUSER krijgt deze vereiste rollen en machtigingen toegewezen:
  * DATABASE ADMIN (in het geval van MDC) en BACKUP ADMIN (in het geval van SDC): om nieuwe databases te maken tijdens het herstellen.
  * CATALOGUS LEZEN: om de back-up catalogus te lezen.
  * SAP_INTERNAL_HANA_SUPPORT: toegang tot een paar privétafels.
* Het script voegt een sleutel toe aan **hdbuserstore** voor AZUREWLBACKUPHANAUSER voor de HANA-back-upplug-in voor alle bewerkingen (databasequery's, herstelbewerkingen, configureren en uitvoeren van back-ups).

>[!NOTE]
> U de gebruikerssleutel die als onderdeel van de [vereisten](#prerequisites) wordt vermeld expliciet als parameter doorgeven aan het preregistratiescript:`-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Als u wilt weten welke andere parameters het script accepteert, gebruikt u de opdracht`bash msawb-plugin-config-com-sap-hana.sh --help`

Voer de HDBSQL-opdracht op de HANA-machine met SIDADM-referenties uit om de sleutelcreatie te bevestigen:

```hdbsql
hdbuserstore list
```

De opdrachtuitvoer moet de {SID}{DBNAME}-sleutel weergeven, waarbij de gebruiker wordt weergegeven als AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Zorg ervoor dat u een unieke set `/usr/sap/{SID}/home/.hdb/`SSFS-bestanden hebt onder . Er zou slechts één map op dit pad moeten zijn.

## <a name="create-a-recovery-service-vault"></a>Een kluis van de herstelservice maken

Een vault van Recovery Services is een entiteit die de back-ups en herstelpunten opslaat die in de loop van de tijd zijn gemaakt. De kluis Recovery Services bevat ook het back-upbeleid dat is gekoppeld aan de beveiligde virtuele machines.

Een Recovery Services-kluis maken:

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

2. Selecteer alle **services** in het linkermenu

   ![Alle services selecteren](./media/tutorial-backup-sap-hana-db/all-services.png)

3. In het dialoogvenster **Alle services** voert u **Recovery Services** in. De lijst met bronnen filtert op basis van uw invoer. Selecteer in de lijst met bronnen de **optie Kluizen van Recovery Services**.

   ![Vaults voor Herstelservices selecteren](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Selecteer In het dashboard van de **vaults** van Recovery Services de optie **Toevoegen**.

   ![Kluis Herstelservices toevoegen](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Het **dialoogvenster Herstelservices wordt** geopend. Waarden opgeven voor **naam, abonnement, resourcegroep** en **locatie**

   ![Een Recovery Services-kluis maken](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Naam:** De naam wordt gebruikt om de kluis van herstelservices te identificeren en moet uniek zijn voor het Azure-abonnement. Geef een naam op met ten minste twee, maar niet meer dan 50 tekens. De naam moet beginnen met een letter en alleen bestaan uit letters, cijfers en koppeltekens. Voor deze zelfstudie hebben we de naam **SAPHanaVault**gebruikt.
   * **Abonnement**: Kies het te gebruiken abonnement. Als u lid bent van slechts één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u wilt gebruiken, gebruikt u het standaardabonnement (voorgesteld). Er zijn alleen meerdere opties als uw werk- of schoolaccount is gekoppeld aan meer dan één Azure-abonnement. Hier hebben we gebruik gemaakt van het **SAP HANA solution lab abonnement.**
   * **Resourcegroep:** gebruik een bestaande resourcegroep of maak een nieuwe groep. Hier hebben we **SAPHANADemo**gebruikt.<br>
   Als u de lijst met beschikbare brongroepen in uw abonnement wilt weergeven, selecteert u **Bestaande gebruiken**en selecteert u vervolgens een resource in de vervolgkeuzelijst. Als u een nieuwe resourcegroep wilt maken, selecteert u **Nieuw maken** en voert u de naam in. Zie overzicht van Azure [Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)voor volledige informatie over resourcegroepen.
   * **Locatie:** Selecteer het geografische gebied voor de kluis. De kluis moet zich in dezelfde regio bevinden als de virtuele machine met SAP HANA. We hebben gebruik gemaakt **van East US 2**.

5. Selecteer **Controleren + maken**.

   ![Selecteer Controleren & maken](./media/tutorial-backup-sap-hana-db/review-create.png)

De kluis van de herstelservices is nu gemaakt.

## <a name="discover-the-databases"></a>Ontdek de databases

1. Klik in de kluis in **Aan de slag**op **Back-up**. Selecteer **SAP HANA in Azure VM**in Waar wordt uw **werkbelasting uitgevoerd?**
2. Klik **op Detectie starten**. Dit initieert de ontdekking van onbeschermde Linux VM's in het kluisgebied. U ziet de Azure VM die u wilt beveiligen.
3. Klik **in Virtuele machines selecteren**op de koppeling om het script te downloaden waarmee machtigingen voor de Azure Backup-service zijn opgenomen om toegang te krijgen tot de SAP HANA VM's voor databasedetectie.
4. Voer het script uit op de VM-hosting-SAP HANA-database(s) waarvan u een back-up wilt maken.
5. Nadat u het script op de virtuele machine hebt uitgevoerd, selecteert u in **Virtuele machines selecteren**de VM. Klik vervolgens op **DB's ontdekken**.
6. Azure Backup detecteert alle SAP HANA-databases op de VM. Tijdens de detectie registreert Azure Backup de VM met de kluis en installeert het een extensie op de VM. Er is geen agent geïnstalleerd in de database.

   ![Ontdek de databases](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Back-up configureren

Nu de databases waarvan we een back-up willen maken, worden ontdekt, schakelen we back-ups in.

1. Klik **op Back-up configureren**.

   ![Back-up configureren](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Selecteer **in Items selecteren om een back-up te maken**een of meer databases die u wilt beveiligen en klik op **OK**.

   ![Items selecteren om een back-up van te maken](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. Maak in **back-upbeleid > Back-upbeleid**kiezen een nieuw back-upbeleid voor de database(en), in overeenstemming met de instructies in de volgende sectie.

   ![Back-upbeleid kiezen](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Klik na het maken van het beleid in het **menu Back-up**op **Back-up inschakelen**.

   ![Klik op Back-up inschakelen](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Houd de voortgang van de back-upconfiguratie bij in het gebied **Meldingen** van de portal.

## <a name="creating-a-backup-policy"></a>Een back-upbeleid maken

Een back-upbeleid bepaalt wanneer back-ups worden gemaakt en hoe lang deze worden bewaard.

* Een beleid wordt gemaakt op kluisniveau.
* U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.

Geef de beleidsinstellingen als volgt op:

1. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op. Voer in dit geval **SAPHANA**in.

   ![Naam invoeren voor nieuw beleid](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. Selecteer **in het beleid Volledige back-up**een **back-upfrequentie**. U kiezen voor **Daily** of **Weekly**. Voor deze tutorial hebben we gekozen voor de **Daily** back-up.

   ![Een back-upfrequentie selecteren](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. Configureer in **bewaarbereik**bewaarinstellingen voor de volledige back-up.
   * Standaard zijn alle opties geselecteerd. Duidelijke bewaarlimieten die u niet wilt gebruiken en stel de limieten in die u wel doet.
   * De minimale bewaartermijn voor elk type back-up (volledig/differentieel/log) is zeven dagen.
   * Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
   * De back-up voor een bepaalde dag wordt getagd en behouden op basis van het wekelijkse bewaarbereik en de instelling.
   * De maandelijkse en jaarlijkse bewaarperioden werken op soortgelijke wijze.
4. Klik in het **menu Volledige back-up** op **OK** om de instellingen te accepteren.
5. Selecteer vervolgens **Differentiële back-up** om een differentieelbeleid toe te voegen.
6. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen. We hebben elke **zondag** om **02:00 uur**een differentiële back-up ingeschakeld, die **30 dagen**wordt bewaard.

   ![Differentiaal back-upbeleid](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Incrementele back-ups worden momenteel niet ondersteund.
   >

7. Klik op **OK** om het beleid op te slaan en terug te keren naar het **hoofdmenu Back-upbeleid.**
8. Selecteer **Logboekback-up** om een back-upbeleid voor een transactioneel logboek toe te voegen,
   * **Log back-up** is standaard ingesteld op **Inschakelen**. Dit kan niet worden uitgeschakeld omdat SAP HANA alle logboekback-ups beheert.
   * We hebben **2 uur** ingesteld als back-upschema en **15 dagen** bewaartermijn.

    ![Beleid voor logboekback-up](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Logboekbackups beginnen pas te stromen nadat één succesvolle volledige back-up is voltooid.
   >

9. Klik op **OK** om het beleid op te slaan en terug te keren naar het **hoofdmenu Back-upbeleid.**
10. Nadat u het back-upbeleid hebt gedefinieerd, klikt u op **OK**.

U hebt nu met succes back-up(s) geconfigureerd voor uw SAP HANA-database(s).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het uitvoeren van [on-demand back-ups op SAP HANA-databases die worden uitgevoerd op Azure VM's](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Meer informatie over het [herstellen van SAP HANA-databases die worden uitgevoerd op Azure VM's](sap-hana-db-restore.md)
* Meer informatie over het [beheren van SAP HANA-databases waarvan een back-up wordt gemaakt met Azure Backup](sap-hana-db-manage.md)
* Meer informatie over het [oplossen van veelvoorkomende problemen bij het maken van back-ups van SAP HANA-databases](backup-azure-sap-hana-database-troubleshoot.md)
