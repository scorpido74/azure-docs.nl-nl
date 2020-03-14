---
title: 'Zelf studie: vereisten voor de beschikbaarheids groep'
description: In deze zelf studie ziet u hoe u de vereisten configureert voor het maken van een SQL Server AlwaysOn-beschikbaarheids groep op virtuele machines van Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.custom: seo-lt-2019
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.openlocfilehash: 4c89489964410104726b65e1b1fc3577945ce14a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249878"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Vereisten voor het maken van AlwaysOn-beschikbaarheids groepen op SQL Server op virtuele machines van Azure

In deze zelf studie ziet u hoe u de vereisten voltooit voor het maken van een SQL Server AlwaysOn [-beschikbaarheids groep op Azure virtual machines (vm's)](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Wanneer u klaar bent met de vereisten, hebt u een domein controller, twee SQL Server Vm's en een Witness-server in één resource groep.

**Geschatte tijd**: het kan enkele uren duren om de vereisten te volt ooien. Veel van deze tijd wordt besteed aan het maken van virtuele machines.

In het volgende diagram ziet u wat u in de zelf studie maakt.

![Beschikbaarheidsgroep](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Documentatie voor de beschikbaarheids groep bekijken

In deze zelf studie wordt ervan uitgegaan dat u een basis memorandum hebt van SQL Server AlwaysOn-beschikbaarheids groepen. Zie overzicht van AlwaysOn [Availability groups (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx)als u niet bekend bent met deze technologie.


## <a name="create-an-azure-account"></a>Een Azure-account maken
U hebt een Azure-account nodig. U kunt [een gratis Azure-account openen of de](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) [voor delen van Visual Studio-abonnee activeren](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Een resourcegroep maken
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Klik op **+** om een nieuw object te maken in de portal.

   ![Nieuw object](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Geef de **resource groep** op in het zoek venster voor **Marketplace** .

   ![Resourcegroep](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Klik op **resource groep**.
5. Klik op **Create**.
6. Typ onder **naam van resource groep**een naam voor de resource groep. Typ bijvoorbeeld **SQL-ha-RG**.
7. Als u meerdere Azure-abonnementen hebt, controleert u of het abonnement het Azure-abonnement is waarin u de beschikbaarheids groep wilt maken.
8. Selecteer een locatie. De locatie is de Azure-regio waar u de beschikbaarheids groep wilt maken. In dit artikel worden alle resources op één Azure-locatie gebouwd.
9. Controleer of het selectie vakje **vastmaken aan dash board** is ingeschakeld. Deze optionele instelling plaatst een snelkoppeling voor de resource groep op het Azure Portal dash board.

   ![Resourcegroep](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Klik op **Maken** om de resourcegroep te maken.

De resource groep wordt door Azure gemaakt en er wordt een snelkoppeling naar de resource groep in de portal gespeld.

## <a name="create-the-network-and-subnets"></a>Het netwerk en de subnetten maken
De volgende stap is het maken van de netwerken en subnetten in de Azure-resource groep.

De oplossing maakt gebruik van één virtueel netwerk met twee subnetten. Het [overzicht van virtuele](../../../virtual-network/virtual-networks-overview.md) netwerken biedt meer informatie over netwerken in Azure.

Het virtuele netwerk maken:

1. Klik in de Azure Portal in uw resource groep op **+ toevoegen**. 

   ![Nieuw item](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Zoek naar het **virtuele netwerk**.

     ![Virtueel netwerk zoeken](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Klik op **virtueel netwerk**.
4. Klik in het **virtuele netwerk**op het **Resource Manager** -implementatie model en klik vervolgens op **maken**.

    De volgende tabel bevat de instellingen voor het virtuele netwerk:

   | **Veld** | Waarde |
   | --- | --- |
   | **Naam** |autoHAVNET |
   | **Adresruimte** |10.33.0.0/24 |
   | **Subnetnaam** |Beheerder |
   | **Subnetadresbereik** |10.33.0.0/29 |
   | **Abonnement** |Geef het abonnement op dat u wilt gebruiken. Het **abonnement** is leeg als u slechts één abonnement hebt. |
   | **Resourcegroep** |Kies **bestaande gebruiken** en kies de naam van de resource groep. |
   | **Locatie** |De Azure-locatie opgeven. |

   De adres ruimte en het adres bereik van het subnet kunnen afwijken van de tabel. Afhankelijk van uw abonnement stelt de portal een beschik bare adres ruimte en een bijbehorend adres bereik voor het subnet voor. Als er onvoldoende adres ruimte beschikbaar is, gebruikt u een ander abonnement.

   In het voor beeld wordt de naam **beheerder**van het subnet gebruikt. Dit subnet is voor de domein controllers.

5. Klik op **Create**.

   ![Het virtuele netwerk configureren](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure keert u terug naar het portal-dash board en waarschuwt u wanneer het nieuwe netwerk is gemaakt.

### <a name="create-a-second-subnet"></a>Een tweede subnet maken
Het nieuwe virtuele netwerk heeft één subnet met de naam **admin**. De domein controllers gebruiken dit subnet. De SQL Server-Vm's gebruiken een tweede subnet met de naam **SQL**. Dit subnet configureren:

1. Klik op het dash board op de resource groep die u hebt gemaakt, **SQL-ha-RG**. Zoek het netwerk in de resource groep onder **resources**.

    Als **SQL-ha-RG** niet zichtbaar is, zoekt u deze door te klikken op **resource groepen** en te filteren op de naam van de resource groep.
2. Klik op **autoHAVNET** in de lijst met resources. 
3. Selecteer **subnetten**onder **instellingen** in het virtuele netwerk van **autoHAVNET** .

    Noteer het subnet dat u al hebt gemaakt.

   ![Het virtuele netwerk configureren](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Maak een tweede subnet. Klik op **+ subnet**.
6. Configureer het subnet in **subnet toevoegen**door **sqlsubnet** onder **naam**te typen. Azure geeft automatisch een geldig **adres bereik**op. Controleer of dit adres bereik ten minste 10 adressen bevat. In een productie omgeving hebt u mogelijk meer adressen nodig.
7. Klik op **OK**.

    ![Het virtuele netwerk configureren](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

De volgende tabel geeft een overzicht van de netwerk configuratie-instellingen:

| **Veld** | Waarde |
| --- | --- |
| **Naam** |**autoHAVNET** |
| **Adresruimte** |Deze waarde is afhankelijk van de beschik bare adres ruimten in uw abonnement. Een typische waarde is 10.0.0.0/16. |
| **Subnetnaam** |**beheerder** |
| **Subnetadresbereik** |Deze waarde is afhankelijk van de beschik bare adresbereiken in uw abonnement. Een typische waarde is 10.0.0.0/24. |
| **Subnetnaam** |**sqlsubnet** |
| **Subnetadresbereik** |Deze waarde is afhankelijk van de beschik bare adresbereiken in uw abonnement. Een typische waarde is 10.0.1.0/24. |
| **Abonnement** |Geef het abonnement op dat u wilt gebruiken. |
| **Resourcegroep** |**SQL-HA-RG** |
| **Locatie** |Geef dezelfde locatie op die u hebt gekozen voor de resource groep. |

## <a name="create-availability-sets"></a>Beschikbaarheidssets maken

Voordat u virtuele machines maakt, moet u beschikbaarheids sets maken. Beschikbaarheids sets verminderen de uitval tijd voor geplande of niet-geplande onderhouds gebeurtenissen. Een beschikbaarheidsset van Azure is een logische groep resources die Azure plaatst op fysieke fout domeinen en update domeinen. Een fout domein zorgt ervoor dat de leden van de beschikbaarheidsset afzonderlijke stroom-en netwerk bronnen hebben. Een update domein zorgt ervoor dat de leden van de beschikbaarheidsset niet tegelijkertijd voor onderhoud worden ingesteld. Zie [de beschik baarheid van virtuele machines beheren](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie.

U hebt twee beschikbaarheids sets nodig. Een voor de domein controllers. De tweede is voor de SQL Server Vm's.

Als u een beschikbaarheidsset wilt maken, gaat u naar de resource groep en klikt u op **toevoegen**. Filter de resultaten door de **beschikbaarheidsset**in te voeren. Klik op **beschikbaarheidsset** in de resultaten en klik vervolgens op **maken**.

Configureer twee beschikbaarheids sets volgens de para meters in de volgende tabel:

| **Veld** | Beschikbaarheidsset van domein controller | Beschikbaarheidsset SQL Server |
| --- | --- | --- |
| **Naam** |adavailabilityset |sqlavailabilityset |
| **Resourcegroep** |SQL-HA-RG |SQL-HA-RG |
| **Fout domeinen** |3 |3 |
| **Domeinen bijwerken** |5 |3 |

Nadat u de beschikbaarheids sets hebt gemaakt, keert u terug naar de resource groep in de Azure Portal.

## <a name="create-domain-controllers"></a>Domein controllers maken
Nadat u de netwerk-, subnetten-en beschikbaarheids sets hebt gemaakt, kunt u de virtuele machines voor de domein controllers maken.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Virtuele machines maken voor de domein controllers
Als u de domein controllers wilt maken en configureren, gaat u terug naar de resource groep **SQL-ha-RG** .

1. Klik op **Toevoegen**. 
2. Typ **Windows Server 2016 Data Center**.
3. Klik op **Windows Server 2016 Data Center**. Controleer in **Windows Server 2016 Data Center**of het implementatie model **Resource Manager**is en klik vervolgens op **maken**. 

Herhaal de voor gaande stappen om twee virtuele machines te maken. Noem de twee virtuele machines:

* ad-primary-dc
* AD-secundair-DC

  > [!NOTE]
  > De virtuele machine **met AD-secondaire domein controller** is optioneel en biedt een hoge Beschik baarheid voor Active Directory Domain Services.
  >
  >

De volgende tabel bevat de instellingen voor deze twee computers:

| **Veld** | Waarde |
| --- | --- |
| **Naam** |Eerste domein controller: *ad-Primary-DC*.</br>Tweede domein controller *ad-secundair-DC*. |
| **Type VM-schijf** |SSD |
| **Gebruikersnaam** |Domein Administrator |
| **Wachtwoord** |Contoso!0000 |
| **Abonnement** |*Uw abonnement* |
| **Resourcegroep** |SQL-HA-RG |
| **Locatie** |*Uw locatie* |
| **Grootte** |DS1_V2 |
| **Storage** | **Managed disks gebruiken** - **Ja** |
| **Virtueel netwerk** |autoHAVNET |
| **Subnet** |beheerder |
| **Openbaar IP-adres** |*Dezelfde naam als de virtuele machine* |
| **Netwerkbeveiligingsgroep** |*Dezelfde naam als de virtuele machine* |
| **Beschikbaarheidsset** |adavailabilityset </br>**Fout domeinen**: 2 </br>**Update domeinen**: 2|
| **Diagnostics** |Ingeschakeld |
| **Opslag account voor diagnostische gegevens** |*Automatisch gemaakt* |

   >[!IMPORTANT]
   >U kunt een virtuele machine alleen in een beschikbaarheidsset plaatsen wanneer u deze maakt. U kunt de beschikbaarheidsset niet wijzigen nadat een virtuele machine is gemaakt. Zie [de beschik baarheid van virtuele machines beheren](../manage-availability.md).

De virtuele machines worden gemaakt door Azure.

Nadat de virtuele machines zijn gemaakt, configureert u de domein controller.

### <a name="configure-the-domain-controller"></a>De domein controller configureren
In de volgende stappen configureert u de **ad-Primary-DC-** computer als een domein controller voor Corp.contoso.com.

1. Open in de Portal de resource groep **SQL-ha-RG** en selecteer de **ad-Primary-DC-** computer. Op **ad-Primary-DC**klikt u op **verbinding maken** om een RDP-bestand te openen voor toegang tot extern bureau blad.

    ![Verbinding maken met een virtuele machine](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Meld u aan met uw geconfigureerde beheerders account ( **\DomainAdmin**) en wacht woord (**Contoso! 0000**).
3. Het **Serverbeheer** dash board moet standaard worden weer gegeven.
4. Klik op de koppeling **functies en onderdelen toevoegen** op het dash board.

    ![Serverbeheer-functies toevoegen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Selecteer **volgende** totdat u toegang hebt tot de sectie **Server functies** .
6. Selecteer de **Active Directory Domain Services** **-en DNS-server** functies. Wanneer u hierom wordt gevraagd, voegt u extra functies toe die vereist zijn voor deze rollen.

   > [!NOTE]
   > Windows waarschuwt u dat er geen statisch IP-adres is. Als u de configuratie wilt testen, klikt u op **door gaan**. Stel voor productie scenario's het IP-adres in op statisch in het Azure Portal, of [gebruik Power shell om het statische IP-adres van de computer van de domein controller](../../../virtual-network/virtual-networks-reserved-private-ip.md)in te stellen.
   >
   >

    ![Dialoog venster Rollen toevoegen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Klik op **volgende** totdat u het **bevestigings** gedeelte hebt bereikt. Schakel het selectie vakje **de doel server automatisch opnieuw opstarten als dit vereist** is in.
8. Klik op **Installeren**.
9. Nadat de installatie van de onderdelen is voltooid, gaat u terug naar het dash board van **Serverbeheer** .
10. Selecteer de optie nieuw **AD DS** in het linkerdeel venster.
11. Klik op de koppeling **meer** op de gele waarschuwings balk.

    ![AD DS dialoog venster op de DNS-Server-VM](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. Klik in de kolom **actie** van het dialoog venster **alle Server taak Details** op **deze server promo veren naar een domein controller**.
13. Gebruik de volgende waarden in de **Wizard Active Directory Domain Services configuratie**:

    | **Faxvoorblad** | Instelling |
    | --- | --- |
    | **Implementatie configuratie** |**Een nieuw forest toevoegen**<br/> **Naam van hoofd domein** = Corp.contoso.com |
    | **Opties voor domein controller** |**Wacht woord voor DSRM** = contoso! 0000<br/>**Bevestig het wacht woord** = contoso! 0000 |
14. Klik op **volgende** om door de andere pagina's in de wizard te gaan. Controleer op de pagina **controle van vereisten** of het volgende bericht wordt weer **gegeven: alle controles van vereisten zijn geslaagd**. U kunt alle toepasselijke waarschuwings berichten bekijken, maar het is mogelijk om door te gaan met de installatie.
15. Klik op **Installeren**. De virtuele machine van **ad-Primary-DC** wordt automatisch opnieuw opgestart.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Noteer het IP-adres van de primaire domein controller

Gebruik de primaire domein controller voor DNS. Noteer het IP-adres van de primaire domein controller.

Een manier om het IP-adres van de primaire domein controller op te halen, is via de Azure Portal.

1. Open de resource groep op het Azure Portal.

2. Klik op de primaire domein controller.

3. Klik op **netwerk interfaces**op de primaire domein controller.

![Netwerkinterfaces](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Noteer het privé-IP-adres voor deze server.

### <a name="configure-the-virtual-network-dns"></a>De DNS van het virtuele netwerk configureren
Nadat u de eerste domein controller hebt gemaakt en DNS op de eerste server inschakelt, configureert u het virtuele netwerk voor het gebruik van deze server voor DNS.

1. Klik in het Azure Portal op het virtuele netwerk.

2. Klik onder **instellingen**op **DNS-server**.

3. Klik op **aangepast**en typ het privé-IP-adres van de primaire domein controller.

4. Klik op **Opslaan**.

### <a name="configure-the-second-domain-controller"></a>De tweede domein controller configureren
Nadat de primaire domein controller opnieuw is opgestart, kunt u de tweede domein controller configureren. Deze optionele stap is voor hoge Beschik baarheid. Volg deze stappen om de tweede domein controller te configureren:

1. Open in de Portal de resource groep **SQL-ha-RG** en selecteer de **ad-Secondary-DC-** computer. Klik in **ad-Secondary-DC**op **verbinding maken** om een RDP-bestand te openen voor toegang tot extern bureau blad.
2. Meld u aan bij de virtuele machine met behulp van uw geconfigureerde beheerders account (**BUILTIN\DomainAdmin**) en wacht woord (**Contoso! 0000**).
3. Wijzig het adres van de voor Keurs-DNS-server in het adres van de domein controller.
4. Klik in **netwerk centrum**op de netwerk interface.
   ![Netwerkinterface](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Klik op **Eigenschappen**.
6. Selecteer **Internet Protocol versie 4 (TCP/IPv4)** en klik op **Eigenschappen**.
7. Selecteer **de volgende DNS-server adressen gebruiken** en geef het adres van de primaire domein controller op in de **voor Keurs-DNS-server**.
8. Klik op **OK**en vervolgens op **sluiten** om de wijzigingen door te voeren. U kunt nu lid worden van de virtuele machine aan **Corp.contoso.com**.

   >[!IMPORTANT]
   >Als u de verbinding met uw externe bureau blad hebt verbroken nadat u de DNS-instelling hebt gewijzigd, gaat u naar de Azure Portal en start u de virtuele machine opnieuw op.

9. Open **Serverbeheer dash board**van het externe bureau blad naar de secundaire domein controller.
10. Klik op de koppeling **functies en onderdelen toevoegen** op het dash board.

    ![Serverbeheer-functies toevoegen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Selecteer **volgende** totdat u toegang hebt tot de sectie **Server functies** .
12. Selecteer de **Active Directory Domain Services** **-en DNS-server** functies. Wanneer u hierom wordt gevraagd, voegt u extra functies toe die vereist zijn voor deze rollen.
13. Nadat de installatie van de onderdelen is voltooid, gaat u terug naar het dash board van **Serverbeheer** .
14. Selecteer de optie nieuw **AD DS** in het linkerdeel venster.
15. Klik op de koppeling **meer** op de gele waarschuwings balk.
16. Klik in de kolom **actie** van het dialoog venster **alle Server taak Details** op **deze server promo veren naar een domein controller**.
17. Onder **implementatie configuratie**selecteert **u een domein controller toevoegen aan een bestaand domein**.
    ![implementatie configuratie](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Klik op **Selecteren**.
19. Verbinding maken met behulp van het beheerders account (**Corp. CONTOSO. COM\domainadmin**) en het wacht woord (**Contoso! 0000**).
20. Klik in **een domein in het forest selecteren**op uw domein en klik vervolgens op **OK**.
21. Gebruik in de **Opties voor domein controller**de standaard waarden en stel een DSRM-wacht woord in.

    >[!NOTE]
    >Op de pagina **DNS-opties** wordt mogelijk gewaarschuwd dat er geen delegering voor deze DNS-server kan worden gemaakt. U kunt deze waarschuwing negeren in niet-productie omgevingen.
22. Klik op **volgende** totdat het dialoog venster de controle van de **vereisten** bereikt. Klik vervolgens op **Installeren**.

Nadat de configuratie wijzigingen door de server zijn voltooid, start u de server opnieuw op.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Voeg het privé-IP-adres toe aan de tweede domein controller aan de VPN DNS-server

Wijzig in de Azure Portal onder virtueel netwerk de DNS-server zodanig dat het IP-adres van de secundaire domein controller bevat. Met deze instelling wordt de DNS-service redundantie toegestaan.

### <a name="DomainAccounts"></a>Domein accounts configureren

In de volgende stappen configureert u de Active Directory accounts. De volgende tabel bevat de accounts:

| |Installatie account<br/> |sqlserver-0 <br/>SQL Server-en SQL Agent-service account |sqlserver-1<br/>SQL Server-en SQL Agent-service account
| --- | --- | --- | ---
|**Voor naam** |Installeren |SQLSvc1 | SQLSvc2
|**SamAccountName van gebruiker** |Installeren |SQLSvc1 | SQLSvc2

Gebruik de volgende stappen om elk account te maken.

1. Meld u aan bij de **ad-Primary-DC-** computer.
2. Selecteer in **Serverbeheer** **extra**en klik vervolgens op **Active Directory-beheercentrum**.   
3. Selecteer **Corp (lokaal)** in het linkerdeel venster.
4. Selecteer in het deel venster rechter **taken** de optie **Nieuw**en klik vervolgens op **gebruiker**.
   ![Active Directory-beheercentrum](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Stel een complex wacht woord in voor elk account.<br/> Voor niet-productie omgevingen stelt u het gebruikers account in op nooit verlopen.

5. Klik op **OK** om de gebruiker te maken.
6. Herhaal de voor gaande stappen voor elk van de drie accounts.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>De vereiste machtigingen verlenen aan het installatie account
1. Selecteer in de **Active Directory-beheercentrum** **Corp (lokaal)** in het linkerdeel venster. Klik vervolgens in **het deel venster** aan de rechter kant op **Eigenschappen**.

    ![Eigenschappen van CORP-gebruikers](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Selecteer **uitbrei dingen**en klik vervolgens op de knop **Geavanceerd** op het tabblad **beveiliging** .
3. Klik in het dialoog venster **Geavanceerde beveiligings instellingen voor Corp** op **toevoegen**.
4. Klik op **een principal selecteren**, zoek naar **CORP\Install**en klik vervolgens op **OK**.
5. Schakel het selectie vakje **alle eigenschappen lezen** in.

6. Schakel het selectie vakje **computer objecten maken** in.

     ![Corp-gebruikers machtigingen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Klik op **OK**en vervolgens nogmaals op **OK** . Sluit het venster Eigenschappen van **Corp** .

Nu u klaar bent met het configureren van Active Directory en de gebruikers objecten, maakt u twee SQL Server Vm's en een Witness-Server-VM. Voeg vervolgens alle drie toe aan het domein.

## <a name="create-sql-server-vms"></a>SQL Server Vm's maken

Maak drie extra virtuele machines. De oplossing vereist twee virtuele machines met SQL Server exemplaren. Een derde virtuele machine werkt als Witness. Windows Server 2016 kan gebruikmaken van een [Cloud-Witness](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), maar voor consistentie met eerdere besturings systemen in dit document wordt een virtuele machine gebruikt voor een Witness.  

Voordat u verdergaat, moet u rekening houden met de volgende ontwerp beslissingen.

* **Opslag-Azure-Managed Disks**

   Gebruik Azure Managed Disks voor de opslag van de virtuele machine. Micro soft adviseert Managed Disks voor SQL Server virtuele machines. Managed Disks verwerken de opslag achter de schermen. Bovendien distribueert Azure de opslagresources zodat voldoende redundantie wordt geboden wanneer virtuele machines met Managed Disks zich in dezelfde beschikbaarheidsset bevinden. Zie [Azure Managed Disks overview](../managed-disks-overview.md) (Overzicht van Azure Managed Disks) voor meer informatie. Zie [Managed disks voor virtuele machines in een beschikbaarheidsset gebruiken](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)voor meer informatie over beheerde schijven in een beschikbaarheidsset.

* **Netwerk-privé-IP-adressen in productie**

   Voor de virtuele machines gebruikt deze zelf studie open bare IP-adressen. Met een openbaar IP-adres kan externe verbinding rechtstreeks met de virtuele machine via internet worden gemaakt. de configuratie stappen worden eenvoudiger. In productie omgevingen raadt micro soft alleen privé-IP-adressen aan om de kwets baarheid van de VM-resource van het SQL Server exemplaar te verminderen.

### <a name="create-and-configure-the-sql-server-vms"></a>De SQL Server Vm's maken en configureren
Maak vervolgens drie Vm's: twee SQL Server Vm's en een virtuele machine voor een extra cluster knooppunt. Als u elk van de virtuele machines wilt maken, gaat u terug naar de resource groep **SQL-ha-RG** , klikt u op **toevoegen**, zoekt u naar het juiste galerie-item, klikt u op **virtuele machine**en klikt u vervolgens op **uit galerie**. Gebruik de informatie in de volgende tabel om u te helpen bij het maken van de virtuele machines:


| Faxvoorblad | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Het juiste galerie-item selecteren |**Windows Server 2016 Data Center** |**SQL Server 2016 SP1 Enter prise op Windows Server 2016** |**SQL Server 2016 SP1 Enter prise op Windows Server 2016** |
| **Basis beginselen** van de configuratie van virtuele machines |**Name** = cluster-FSW<br/>**Gebruikers naam** = domein Administrator<br/>**Wacht woord** = contoso! 0000<br/>**Abonnement** = uw abonnement<br/>**Resource groep** = SQL-ha-RG<br/>**Locatie** = uw Azure-locatie |**Name** = sqlserver-0<br/>**Gebruikers naam** = domein Administrator<br/>**Wacht woord** = contoso! 0000<br/>**Abonnement** = uw abonnement<br/>**Resource groep** = SQL-ha-RG<br/>**Locatie** = uw Azure-locatie |**Name** = sqlserver-1<br/>**Gebruikers naam** = domein Administrator<br/>**Wacht woord** = contoso! 0000<br/>**Abonnement** = uw abonnement<br/>**Resource groep** = SQL-ha-RG<br/>**Locatie** = uw Azure-locatie |
| Virtuele-machine configuratie **grootte** |**Grootte** = ds1\_v2 (1 vCPU, 3,5 GB) |**Grootte** = DS2\_v2 (2 vcpu's, 7 GB)</br>De omvang moet ondersteuning bieden voor SSD Storage (Premium-schijf ondersteuning). )) |**Grootte** = DS2\_v2 (2 vcpu's, 7 GB) |
| Configuratie- **instellingen** voor virtuele machines |**Opslag**: gebruik beheerde schijven.<br/>**Virtueel netwerk** = autoHAVNET<br/>**Subnet** = sqlsubnet (10.1.1.0/24)<br/>Het **open bare IP-adres** wordt automatisch gegenereerd.<br/>**Netwerk beveiligings groep** = geen<br/>**Diagnostische gegevens over bewaking** = ingeschakeld<br/>**Opslag account voor diagnostische gegevens** = een automatisch gegenereerd opslag account gebruiken<br/>**Beschikbaarheidsset** = sqlAvailabilitySet<br/> |**Opslag**: gebruik beheerde schijven.<br/>**Virtueel netwerk** = autoHAVNET<br/>**Subnet** = sqlsubnet (10.1.1.0/24)<br/>Het **open bare IP-adres** wordt automatisch gegenereerd.<br/>**Netwerk beveiligings groep** = geen<br/>**Diagnostische gegevens over bewaking** = ingeschakeld<br/>**Opslag account voor diagnostische gegevens** = een automatisch gegenereerd opslag account gebruiken<br/>**Beschikbaarheidsset** = sqlAvailabilitySet<br/> |**Opslag**: gebruik beheerde schijven.<br/>**Virtueel netwerk** = autoHAVNET<br/>**Subnet** = sqlsubnet (10.1.1.0/24)<br/>Het **open bare IP-adres** wordt automatisch gegenereerd.<br/>**Netwerk beveiligings groep** = geen<br/>**Diagnostische gegevens over bewaking** = ingeschakeld<br/>**Opslag account voor diagnostische gegevens** = een automatisch gegenereerd opslag account gebruiken<br/>**Beschikbaarheidsset** = sqlAvailabilitySet<br/> |
| Instellingen voor configuratie van virtuele machine **SQL Server** |Niet van toepassing |**SQL-connectiviteit** = privé (binnen Virtual Network)<br/>**Poort** = 1433<br/>**SQL-verificatie** = uitschakelen<br/>**Opslag configuratie** = algemeen<br/>**Automatische patching** = zondag om 2:00<br/>**Automatische back-up** = uitgeschakeld</br>**Azure Key Vault-integratie** = uitgeschakeld |**SQL-connectiviteit** = privé (binnen Virtual Network)<br/>**Poort** = 1433<br/>**SQL-verificatie** = uitschakelen<br/>**Opslag configuratie** = algemeen<br/>**Automatische patching** = zondag om 2:00<br/>**Automatische back-up** = uitgeschakeld</br>**Azure Key Vault-integratie** = uitgeschakeld |

<br/>

> [!NOTE]
> De computer grootten die hier worden voorgesteld, zijn bedoeld voor het testen van beschikbaarheids groepen in azure-Vm's. Voor de beste prestaties van werk belastingen in de productie raadpleegt u de aanbevelingen voor SQL Server computer grootten en configuratie in [Best practices voor SQL Server in azure virtual machines](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Nadat de drie Vm's volledig zijn ingericht, moet u deze toevoegen aan het **Corp.contoso.com** -domein en CORP\Install beheerders rechten verlenen aan de machines.

### <a name="joinDomain"></a>De servers toevoegen aan het domein

U kunt nu lid worden van de virtuele machines aan **Corp.contoso.com**. Voer de volgende stappen uit voor de SQL Server Vm's en de bestands share Witness-server:

1. Maak extern verbinding met de virtuele machine met **BUILTIN\DomainAdmin**.
2. Klik in **Serverbeheer**op **lokale server**.
3. Klik op de koppeling van de **werk groep** .
4. Klik in de sectie **computer naam** op **wijzigen**.
5. Schakel het selectie vakje **domein** in en typ **Corp.contoso.com** in het tekstvak. Klik op **OK**.
6. Geef in het pop-upvenster **Windows-beveiliging** de referenties op voor het standaard domein beheerders account (**CORP\DomainAdmin**) en het wacht woord (**Contoso! 0000**).
7. Wanneer het bericht ' Welkom bij het domein corp.contoso.com ' wordt weer gegeven, klikt u op **OK**.
8. Klik op **sluiten**en klik vervolgens in het pop-updialoogvenster op **nu opnieuw opstarten** .

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>De Corp\Install-gebruiker toevoegen als beheerder op elke VM van het cluster

Nadat elke virtuele machine opnieuw is opgestart als lid van het domein, voegt u **CORP\Install** toe als lid van de lokale groep Administrators.

1. Wacht totdat de virtuele machine opnieuw is opgestart en start het RDP-bestand opnieuw vanaf de primaire domein controller om u aan te melden bij **sqlserver-0** met behulp van het **CORP\DomainAdmin** -account.
   >[!TIP]
   >Zorg ervoor dat u zich aanmeldt met het domein beheerders account. In de vorige stappen gebruikt u het ingebouwde Administrator-account. Nu de server zich in het domein bevindt, gebruikt u het domein account. Geef in uw RDP-sessie *domein*\\*gebruikers naam*op.

2. Selecteer in **Serverbeheer** **extra**en klik vervolgens op **computer beheer**.
3. Vouw in het venster **computer beheer** **lokale gebruikers en groepen**uit en selecteer vervolgens **groepen**.
4. Dubbel klik op de groep **Administrators** .
5. Klik in het dialoog venster **Eigenschappen van beheerder** op de knop **toevoegen** .
6. Voer de gebruikers **CORP\Install**in en klik vervolgens op **OK**.
7. Klik op **OK** om het dialoog venster met **beheerders eigenschappen** te sluiten.
8. Herhaal de vorige stappen in **sqlserver-1** en **cluster-FSW**.

### <a name="setServiceAccount"></a>De SQL Server-service accounts instellen

Stel op elke SQL Server-VM de SQL Server-service account in. Gebruik de accounts die u hebt gemaakt tijdens het configureren van de domein accounts.

1. Open **SQL Server Configuration Manager**.
2. Klik met de rechter muisknop op de SQL Server-service en klik vervolgens op **Eigenschappen**.
3. Stel het account en het wacht woord in.
4. Herhaal deze stappen op de andere SQL Server VM.  

Voor SQL Server-beschikbaarheids groepen moet elke SQL Server VM als een domein account worden uitgevoerd.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Maak een aanmelding op elke SQL Server virtuele machine voor het installatie account

Gebruik het installatie account (CORP\install) om de beschikbaarheids groep te configureren. Dit account moet lid zijn van de vaste serverrol **sysadmin** op elke SQL Server VM. Met de volgende stappen maakt u een aanmelding voor het installatie account:

1. Maak via de Remote Desktop Protocol (RDP) verbinding met de server met behulp van het *\<machinename\>\DomainAdmin* -account.

1. Open SQL Server Management Studio en maak verbinding met het lokale exemplaar van SQL Server.

1. Klik in **objectverkenner**op **beveiliging**.

1. Klik met de rechter muisknop op **aanmeldingen**. Klik op **nieuwe aanmelding**.

1. Klik bij **Aanmelden-nieuw**op **zoeken**.

1. Klik op **locaties**.

1. Voer de netwerk referenties voor de domein beheerder in.

1. Gebruik het installatie account.

1. Stel de aanmelding in op een lid van de vaste serverrol **sysadmin** .

1. Klik op **OK**.

Herhaal de voor gaande stappen op de andere SQL Server VM.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Functies voor failover clustering toevoegen aan virtuele SQL Server Vm's

Als u functies voor Failover Clustering wilt toevoegen, voert u de volgende stappen uit op beide SQL Server Vm's:

1. Maak verbinding met de virtuele machine van SQL Server via de Remote Desktop Protocol (RDP) met behulp van het *CORP\install* -account. Open **Serverbeheer dash board**.
2. Klik op de koppeling **functies en onderdelen toevoegen** op het dash board.

    ![Serverbeheer-functies toevoegen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Selecteer **volgende** totdat u toegang hebt tot het gedeelte **Server functies** .
4. Selecteer in **functies** **failover clustering**.
5. Voeg aanvullende vereiste onderdelen toe.
6. Klik op **installeren** om de functies toe te voegen.

Herhaal de stappen op de andere SQL Server VM.

  >[!NOTE]
  > Deze stap, samen met de daad werkelijke deelname van de SQL Server Vm's aan het failovercluster, kan nu worden geautomatiseerd met [Azure SQL VM cli](virtual-machines-windows-sql-availability-group-cli.md) en [Azure Quick](virtual-machines-windows-sql-availability-group-quickstart-template.md)start-sjablonen.


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> de firewall op elke SQL Server VM configureren

Voor de oplossing moeten de volgende TCP-poorten in de firewall zijn geopend:

- **SQL Server VM**:<br/>
   Poort 1433 voor een standaard exemplaar van SQL Server.
- **Azure load balancer-test:**<br/>
   Elke beschik bare poort. Voor beelden regel matig gebruik van 59999.
- **Eind punt voor database spiegeling:** <br/>
   Elke beschik bare poort. Voor beelden regel matig gebruik van 5022.

De firewall poorten moeten op beide SQL Server Vm's zijn geopend.

De methode voor het openen van de poorten is afhankelijk van de firewall-oplossing die u gebruikt. In de volgende sectie wordt uitgelegd hoe u de poorten in Windows Firewall kunt openen. Open de vereiste poorten op elk van uw SQL Server Vm's.

### <a name="open-a-tcp-port-in-the-firewall"></a>Een TCP-poort openen in de firewall

1. Op de eerste SQL Server **Start** scherm, start u **Windows Firewall met geavanceerde beveiliging**.
2. Selecteer in het linkerdeel venster **regels voor binnenkomende verbindingen**. Klik in het rechterdeel venster op **nieuwe regel**.
3. Kies **poort**bij **regel type**.
4. Geef voor de poort **TCP** op en typ de juiste poort nummers. Zie het volgende voorbeeld:

   ![SQL-firewall](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Klik op **Volgende**.
6. Behoud op de pagina **actie** **de verbinding toestaan** geselecteerd en klik op **volgende**.
7. Accepteer de standaard instellingen op de **profiel** pagina en klik vervolgens op **volgende**.
8. Geef op de pagina **naam** een regel naam op (zoals **Azure lb probe**) in het tekstvak **naam** en klik vervolgens op **volt ooien**.

Herhaal deze stappen op de tweede SQL Server VM.

## <a name="configure-system-account-permissions"></a>Machtigingen voor systeem accounts configureren

Als u een account voor het systeem account wilt maken en de juiste machtigingen wilt verlenen, voert u de volgende stappen uit op elk SQL Server-exemplaar:

1. Maak een account voor `[NT AUTHORITY\SYSTEM]` op elk SQL Server exemplaar. Met het volgende script maakt u dit account:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Verleen de volgende machtigingen voor `[NT AUTHORITY\SYSTEM]` op elke SQL Server instantie:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Met het volgende script worden deze machtigingen verleend:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Volgende stappen

* [Een SQL Server AlwaysOn-beschikbaarheids groep maken op virtuele machines van Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
