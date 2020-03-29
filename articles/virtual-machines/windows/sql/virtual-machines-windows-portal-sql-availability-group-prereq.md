---
title: 'Zelfstudie: Groep vereisten voor beschikbaarheid'
description: In deze zelfstudie ziet u hoe u de vereisten configureert voor het maken van een SQL Server Always On-beschikbaarheidsgroep op Azure VM's.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: c80a4c07e0649612b4c024cac79833c4b730f55e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060174"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Vereisten voor het maken van beschikbaarheidsgroepen altijd op SQL Server op virtuele Azure-machines

In deze zelfstudie ziet u hoe u de vereisten voor het maken van een [SQL Server Always On-beschikbaarheidsgroep op Virtuele Azure-machines (VM's) kunt](virtual-machines-windows-portal-sql-availability-group-tutorial.md)voltooien. Wanneer u de vereisten hebt voltooid, beschikt u over een domeincontroller, twee SQL Server VM's en een witness-server in één brongroep.

**Tijdsschatting**: Het kan een paar uur duren voordat de vereisten zijn voltooid. Veel van deze tijd wordt besteed aan het maken van virtuele machines.

In het volgende diagram ziet u wat u in de zelfstudie inbouwt.

![Beschikbaarheidsgroep](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Documentatie over beschikbaarheidsgroepen controleren

In deze zelfstudie wordt ervan uitgegaan dat u een basiskennis hebt van SQL Server Always On-beschikbaarheidsgroepen. Zie [Overzicht van SQL Server (Always On Availability Groups)](https://msdn.microsoft.com/library/ff877884.aspx)als u niet bekend bent met deze technologie.


## <a name="create-an-azure-account"></a>Een Azure-account maken
U hebt een Azure-account nodig. U [een gratis Azure-account openen](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) of voordelen voor Visual [Studio-abonnees activeren.](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits)

## <a name="create-a-resource-group"></a>Een resourcegroep maken
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik **+** hier om een nieuw object in de portal te maken.

   ![Nieuw object](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Typ **resourcegroep** in het zoekvenster **Marketplace.**

   ![Resourcegroep](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Klik **op Resourcegroep**.
5. Klik **op Maken**.
6. Typ onder De naam van de **resourcegroep**een naam voor de resourcegroep. Typ bijvoorbeeld **sql-ha-rg**.
7. Als u meerdere Azure-abonnementen hebt, controleert u of het abonnement het Azure-abonnement is waarin u de beschikbaarheidsgroep wilt maken.
8. Selecteer een locatie. De locatie is het Azure-gebied waar u de beschikbaarheidsgroep wilt maken. In dit artikel worden alle resources op één Azure-locatie gebouwd.
9. Controleer of **Vastmaken aan dashboard** is ingeschakeld. Met deze optionele instelling wordt een snelkoppeling voor de brongroep op het Azure-portaldashboard geplaatst.

   ![Resourcegroep](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Klik op **Maken** om de resourcegroep te maken.

Azure maakt de brongroep en maakt een snelkoppeling naar de resourcegroep in de portal.

## <a name="create-the-network-and-subnets"></a>Het netwerk en de subnetten maken
De volgende stap is het maken van de netwerken en subnetten in de Azure-brongroep.

De oplossing maakt gebruik van één virtueel netwerk met twee subnetten. Het [overzicht van het virtuele netwerk](../../../virtual-network/virtual-networks-overview.md) bevat meer informatie over netwerken in Azure.

Ga als het gaat om het virtuele netwerk:

1. Klik in de Azure-portal in uw brongroep op **+ Toevoegen**. 

   ![Nieuw item](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Zoeken naar **virtueel netwerk**.

     ![Virtueel netwerk zoeken](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Klik **op Virtueel netwerk**.
4. Klik in het **virtuele netwerk**op het implementatiemodel **Resourcebeheer** en klik vervolgens op **Maken**.

    In de volgende tabel worden de instellingen voor het virtuele netwerk weergegeven:

   | **Veld** | Waarde |
   | --- | --- |
   | **Naam** |autoHAVNET (autoHAVNET) |
   | **Adresruimte** |10.33.0.0/24 |
   | **Subnetnaam** |Beheerder |
   | **Subnetadresbereik** |10.33.0.0/29 |
   | **Abonnement** |Geef het abonnement op dat u wilt gebruiken. **Het abonnement** is leeg als u slechts één abonnement hebt. |
   | **Resourcegroep** |Kies **Bestaand gebruiken** en kies de naam van de resourcegroep. |
   | **Locatie** |Geef de Azure-locatie op. |

   Uw adresruimte en subnetadresbereik kunnen afwijken van de tabel. Afhankelijk van uw abonnement stelt de portal een beschikbare adresruimte en een bijbehorend subnetadresbereik voor. Als er niet voldoende adresruimte beschikbaar is, gebruik dan een ander abonnement.

   In het voorbeeld wordt de subnetnaam **Beheerder gebruikt**. Dit subnet is voor de domeincontrollers.

5. Klik **op Maken**.

   ![Het virtuele netwerk configureren](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure brengt u terug naar het portaldashboard en waarschuwt u wanneer het nieuwe netwerk wordt gemaakt.

### <a name="create-a-second-subnet"></a>Een tweede subnet maken
Het nieuwe virtuele netwerk heeft een subnet, genaamd **Admin**. De domeincontrollers gebruiken dit subnet. De SQL Server VM's gebruiken een tweede subnet met de naam **SQL**. Ga als volgt te werk om dit subnet te configureren:

1. Klik op uw dashboard op de resourcegroep die u hebt gemaakt, **SQL-HA-RG.** Zoek het netwerk in de resourcegroep onder **Resources**.

    Als **SQL-HA-RG** niet zichtbaar is, zoekt u deze door op **Resourcegroepen** te klikken en te filteren op de naam van de brongroep.
2. Klik **op autoHAVNET** in de lijst met bronnen. 
3. Selecteer **autoHAVNET** subnetten onder Instellingen onder **Subnetten**. **Settings**

    Let op het subnet dat u al hebt gemaakt.

   ![Het virtuele netwerk configureren](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Maak een tweede subnet. Klik **op + Subnet**.
6. Configureer op **Subnet toevoegen**het subnet door **sqlsubnet** onder **Naam**te typen . Azure geeft automatisch een geldig **adresbereik op.** Controleer of dit adresbereik ten minste 10 adressen heeft. In een productieomgeving hebt u mogelijk meer adressen nodig.
7. Klik op **OK**.

    ![Het virtuele netwerk configureren](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

In de volgende tabel worden de netwerkconfiguratie-instellingen samengevat:

| **Veld** | Waarde |
| --- | --- |
| **Naam** |**autoHAVNET (autoHAVNET)** |
| **Adresruimte** |Deze waarde is afhankelijk van de beschikbare adresruimten in uw abonnement. Een typische waarde is 10.0.0.0/16. |
| **Subnetnaam** |**beheerder** |
| **Subnetadresbereik** |Deze waarde is afhankelijk van de beschikbare adresbereiken in uw abonnement. Een typische waarde is 10.0.0.0/24. |
| **Subnetnaam** |**sqlsubnet** |
| **Subnetadresbereik** |Deze waarde is afhankelijk van de beschikbare adresbereiken in uw abonnement. Een typische waarde is 10.0.1.0/24. |
| **Abonnement** |Geef het abonnement op dat u wilt gebruiken. |
| **Resourcegroep** |**SQL-HA-RG** |
| **Locatie** |Geef dezelfde locatie op die u voor de resourcegroep hebt gekozen. |

## <a name="create-availability-sets"></a>Beschikbaarheidssets maken

Voordat u virtuele machines maakt, moet u beschikbaarheidssets maken. Beschikbaarheidssets verminderen de uitvaltijd voor geplande of ongeplande onderhoudsgebeurtenissen. Een Azure-beschikbaarheidsset is een logische groep resources die Azure plaatst op fysieke foutdomeinen en updatedomeinen. Een foutdomein zorgt ervoor dat de leden van de beschikbaarheidsset afzonderlijke stroom- en netwerkbronnen hebben. Een updatedomein zorgt ervoor dat leden van de beschikbaarheidsset niet tegelijkertijd worden neergehaald voor onderhoud. Zie voor meer informatie [De beschikbaarheid van virtuele machines beheren](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Je hebt twee beschikbaarheidssets nodig. Een daarvan is voor de domeincontrollers. De tweede is voor de SQL Server VM's.

Als u een beschikbaarheidsset wilt maken, gaat u naar de resourcegroep en klikt u op **Toevoegen**. Filter de resultaten door **beschikbaarheidsset te**typen . Klik op **Beschikbaarheidsset** in de resultaten en klik vervolgens op **Maken**.

Configureer twee beschikbaarheidssets op basis van de parameters in de volgende tabel:

| **Veld** | Beschikbaarheidsset voor domeincontroller | SQL Server-beschikbaarheidsset |
| --- | --- | --- |
| **Naam** |advertentiebeschikbaarheidsset |sqlavailabilityset |
| **Resourcegroep** |SQL-HA-RG |SQL-HA-RG |
| **Foutdomeinen** |3 |3 |
| **Updatedomeinen** |5 |3 |

Nadat u de beschikbaarheidssets hebt gemaakt, keert u terug naar de brongroep in de Azure-portal.

## <a name="create-domain-controllers"></a>Domeincontrollers maken
Nadat u de netwerk-, subnetten- en beschikbaarheidssets hebt gemaakt, u de virtuele machines voor de domeincontrollers maken.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Virtuele machines maken voor de domeincontrollers
Als u de domeincontrollers wilt maken en configureren, gaat u terug naar de **SQL-HA-RG-brongroep.**

1. Klik op**toevoegen**. 
2. Typ **Windows Server 2016-datacenter**.
3. Klik op **Windows Server 2016 Datacenter**. Controleer in **Windows Server 2016-datacenter**of het implementatiemodel **Resourcebeheer**is en klik vervolgens op **Maken**. 

Herhaal de voorgaande stappen om twee virtuele machines te maken. Noem de twee virtuele machines:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > De **virtuele virtuele virtuele machine ad-secondary-dc** is optioneel om active directory-domeinservices met hoge beschikbaarheid te bieden.
  >
  >

In de volgende tabel worden de instellingen voor deze twee machines weergegeven:

| **Veld** | Waarde |
| --- | --- |
| **Naam** |Eerste domeincontroller: *ad-primary-dc*.</br>Tweede domeincontroller *ad-secondary-dc*. |
| **VM-schijftype** |SSD |
| **Gebruikersnaam** |Domeinbeheerder |
| **Wachtwoord** |Contoso!0000 |
| **Abonnement** |*Uw abonnement* |
| **Resourcegroep** |SQL-HA-RG |
| **Locatie** |*Uw locatie* |
| **Grootte** |DS1_V2 |
| **Opslag** | **Beheerde schijven** - **gebruiken Ja** |
| **Virtueel netwerk** |autoHAVNET (autoHAVNET) |
| **Subnet** |beheerder |
| **Openbaar IP-adres** |*Dezelfde naam als de VM* |
| **Netwerkbeveiligingsgroep** |*Dezelfde naam als de VM* |
| **Beschikbaarheidsset** |advertentiebeschikbaarheidsset </br>**Foutdomeinen**:2 </br>**Domeinen bijwerken**:2|
| **Diagnostiek** |Ingeschakeld |
| **Opslagaccount voor diagnose** |*Automatisch gemaakt* |

   >[!IMPORTANT]
   >U een virtuele machine alleen in een beschikbaarheidsset plaatsen wanneer u deze maakt. U de beschikbaarheidsset niet wijzigen nadat een virtuele machine is gemaakt. Zie [De beschikbaarheid van virtuele machines beheren](../manage-availability.md).

Azure maakt de virtuele machines.

Configureer de domeincontroller nadat de virtuele machines zijn gemaakt.

### <a name="configure-the-domain-controller"></a>De domeincontroller configureren
Configureer in de volgende stappen de **ad-primary-dc-machine** als domeincontroller voor corp.contoso.com.

1. Open in de portal de **SQL-HA-RG-brongroep** en selecteer de **ad-primary-dc-machine.** Klik op **ad-primary-dc**op **Verbinding maken** om een RDP-bestand te openen voor externe bureaubladtoegang.

    ![Verbinding maken met een virtuele machine](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Meld u aan met uw geconfigureerde beheerdersaccount (**\DomainAdmin**) en wachtwoord (**Contoso!0000**).
3. Het dashboard **Serverbeheer** moet standaard worden weergegeven.
4. Klik **op** de koppeling Rollen en functies toevoegen op het dashboard.

    ![Serverbeheer - Rollen toevoegen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Selecteer **Volgende** totdat u de sectie **Serverrollen hebt** geopend.
6. Selecteer de rollen **Active Directory Domain Services** en DNS **Server.** Wanneer u wordt gevraagd, voegt u eventuele extra functies toe die door deze rollen vereist zijn.

   > [!NOTE]
   > Windows waarschuwt u dat er geen statisch IP-adres is. Als u de configuratie test, klikt u op **Doorgaan**. Stel voor productiescenario's het IP-adres in op statisch in de Azure-portal of [gebruik PowerShell om het statische IP-adres van de domeincontrollermachine in te stellen.](../../../virtual-network/virtual-networks-reserved-private-ip.md)
   >
   >

    ![Dialoogvenster Rollen toevoegen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Klik **op Volgende** totdat u de sectie **Bevestiging** hebt bereikt. Schakel het selectievakje **De doelserver automatisch opnieuw starten in indien nodig.**
8. Klik **op Installeren**.
9. Nadat de functies zijn voltooid, gaat u terug naar het dashboard **Serverbeheer.**
10. Selecteer de nieuwe **AD DS-optie** in het linkerdeelvenster.
11. Klik op de koppeling **Meer** op de gele waarschuwingsbalk.

    ![AD DS-dialoogvenster op de VM DNS-server](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. Klik in de kolom **Actie** van het dialoogvenster **Alle servertaakgegevens** op **Deze server promoten naar een domeincontroller**.
13. Gebruik in de **wizard Configuratie van Active Directory Domain Services**de volgende waarden:

    | **Pagina** | Instelling |
    | --- | --- |
    | **Implementatieconfiguratie** |**Een nieuw forest toevoegen**<br/> **Hoofddomeinnaam** = corp.contoso.com |
    | **Domeincontrolleropties** |**DSRM-wachtwoord** = Contoso!0000<br/>**Wachtwoord bevestigen** = Contoso!0000 |
14. Klik **op Volgende** om door de andere pagina's in de wizard te gaan. Controleer **op de** pagina Vereistencontrole of u het volgende bericht ziet: **Alle vereistecontroles zijn geslaagd**. U alle toepasselijke waarschuwingsberichten bekijken, maar het is mogelijk om door te gaan met de installatie.
15. Klik **op Installeren**. De **virtuele ad-primary-dc-machine** wordt automatisch opnieuw opgestart.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Let op het IP-adres van de primaire domeincontroller

Gebruik de primaire domeincontroller voor DNS. Let op het IP-adres van de primaire domeincontroller.

Een manier om het primaire IP-adres van de domeincontroller te krijgen, is via de Azure-portal.

1. Open de brongroep op de Azure-portal.

2. Klik op de primaire domeincontroller.

3. Klik op de primaire domeincontroller op **Netwerkinterfaces**.

![Netwerkinterfaces](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Let op het privé-IP-adres voor deze server.

### <a name="configure-the-virtual-network-dns"></a>Dns-netwerk configureren
Nadat u de eerste domeincontroller hebt gemaakt en DNS op de eerste server hebt ingeschakeld, configureert u het virtuele netwerk om deze server voor DNS te gebruiken.

1. Klik in de Azure-portal op het virtuele netwerk.

2. Klik **onder Instellingen**op **DNS-server**.

3. Klik **op Aangepast**en typ het privé-IP-adres van de primaire domeincontroller.

4. Klik op **Opslaan**.

### <a name="configure-the-second-domain-controller"></a>De tweede domeincontroller configureren
Nadat de primaire domeincontroller opnieuw is opgestart, u de tweede domeincontroller configureren. Deze optionele stap is voor hoge beschikbaarheid. Volg de volgende stappen om de tweede domeincontroller te configureren:

1. Open in de portal de **SQL-HA-RG-brongroep** en selecteer de **ad-secondary-dc-machine.** Klik op **ad-secondary-dc**op **Verbinding maken** om een RDP-bestand te openen voor externe bureaubladtoegang.
2. Meld u aan bij de VM met uw geconfigureerde beheerdersaccount **(BUILTIN\DomainAdmin)** en wachtwoord **(Contoso!0000).**
3. Wijzig het gewenste DNS-serveradres in het adres van de domeincontroller.
4. Klik in **netwerk- en deelcentrum**op de netwerkinterface.
   ![Netwerkinterface](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Klik op **Eigenschappen**.
6. Selecteer **Internet Protocol Versie 4 (TCP/IPv4)** en klik op **Eigenschappen**.
7. Selecteer **De volgende DNS-serveradressen gebruiken** en geef het adres op van de primaire domeincontroller in de **voorkeursDNS-server.**
8. Klik op **OK**en sluit **vervolgens** om de wijzigingen door te voeren. U nu lid worden van de VM **om**corp.contoso.com.

   >[!IMPORTANT]
   >Als u de verbinding met uw extern bureaublad verliest nadat u de DNS-instelling hebt gewijzigd, gaat u naar de Azure-portal en start u de virtuele machine opnieuw.

9. Open **Serverbeheerdashboard**van het extern bureaublad naar de secundaire domeincontroller.
10. Klik **op** de koppeling Rollen en functies toevoegen op het dashboard.

    ![Serverbeheer - Rollen toevoegen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Selecteer **Volgende** totdat u de sectie **Serverrollen hebt** geopend.
12. Selecteer de rollen **Active Directory Domain Services** en DNS **Server.** Wanneer u wordt gevraagd, voegt u eventuele extra functies toe die door deze rollen vereist zijn.
13. Nadat de functies zijn voltooid, gaat u terug naar het dashboard **Serverbeheer.**
14. Selecteer de nieuwe **AD DS-optie** in het linkerdeelvenster.
15. Klik op de koppeling **Meer** op de gele waarschuwingsbalk.
16. Klik in de kolom **Actie** van het dialoogvenster **Alle servertaakgegevens** op **Deze server promoten naar een domeincontroller**.
17. Selecteer **onder Implementatieconfiguratie**de optie **Een domeincontroller toevoegen aan een bestaand domein**.
    ![Implementatieconfiguratie](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Klik **op Selecteren**.
19. Maak verbinding met het beheerdersaccount **(CORP. CONTOSO.COM\domainadmin**) en wachtwoord (**Contoso!0000**).
20. Klik in **Een domein selecteren in het forest**op uw domein en klik vervolgens op **OK**.
21. Gebruik **in domeincontrolleropties**de standaardwaarden en stel een DSRM-wachtwoord in.

    >[!NOTE]
    >De pagina **DNS-opties** kan u waarschuwen dat er geen delegatie voor deze DNS-server kan worden gemaakt. U deze waarschuwing negeren in niet-productieomgevingen.
22. Klik **op Volgende** totdat het dialoogvenster de controle Vereiste **voorwaarden** heeft bereikt. Klik vervolgens op **Installeren**.

Nadat de server de configuratiewijzigingen heeft voltooid, start u de server opnieuw op.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Het privé-IP-adres toevoegen aan de tweede domeincontroller aan de VPN DNS-server

Wijzig in de Azure-portal onder virtueel netwerk de DNS-server om het IP-adres van de secundaire domeincontroller op te nemen. Met deze instelling kan de DNS-serviceredundantie worden ingesteld.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a>De domeinaccounts configureren

In de volgende stappen configureert u de Active Directory-accounts. In de volgende tabel worden de rekeningen weergegeven:

| |Installatieaccount<br/> |sqlserver-0 <br/>SQL Server- en SQL Agent-serviceaccount |sqlserver-1<br/>SQL Server- en SQL Agent-serviceaccount
| --- | --- | --- | ---
|**Voornaam** |Installeren |SQLSvc1 SQLSvc1 | SQLSvc2 SQLSvc2
|**SamAccountName van gebruiker** |Installeren |SQLSvc1 SQLSvc1 | SQLSvc2 SQLSvc2

Gebruik de volgende stappen om elk account te maken.

1. Meld u aan bij de **advertentie-primaire-dc-machine.**
2. Selecteer In **Serverbeheer**de optie **Extra**en klik vervolgens op **Active Directory-beheercentrum**.   
3. Selecteer **corp (lokaal)** in het linkerdeelvenster.
4. **Selecteer**nieuw en klik vervolgens op **Gebruiker**in het rechter deelvenster **Taken** .
   ![Active Directory-beheercentrum](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Stel voor elk account een complex wachtwoord in.<br/> Stel voor niet-productieomgevingen het gebruikersaccount in om nooit te verlopen.

5. Klik op **OK** om de gebruiker te maken.
6. Herhaal de voorgaande stappen voor elk van de drie accounts.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>De vereiste machtigingen verlenen aan het installatieaccount
1. Selecteer in het **Active Directory-beheercentrum** **corp (lokaal)** in het linkerdeelvenster. Klik vervolgens in het deelvenster **Taken** met de rechterkant op **Eigenschappen**.

    ![CORP-gebruikerseigenschappen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Selecteer **Extensies**en klik op de knop **Geavanceerd** op het tabblad **Beveiliging.**
3. Klik in het dialoogvenster **Geavanceerde beveiligingsinstellingen voor corp** op **Toevoegen**.
4. Klik **op Een hoofd selecteren,** zoek naar **CORP\Installeren**en klik vervolgens op **OK**.
5. Schakel het selectievakje **Alle eigenschappen lezen** in.

6. Schakel het selectievakje **Computerobjecten maken** in.

     ![Corp-gebruikersmachtigingen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Klik op **OK** en klik vervolgens nogmaals op **OK**. Sluit het **venster van de corp** eigenschappen.

Nu u active directory en de gebruikersobjecten hebt geconfigureerd, maakt u twee SQL Server VM's en een VM voor witnessserver. Sluit je dan aan bij alle drie het domein.

## <a name="create-sql-server-vms"></a>SQL Server VM's maken

Maak drie extra virtuele machines. De oplossing vereist twee virtuele machines met SQL Server-exemplaren. Een derde virtuele machine zal als getuige functioneren. Windows Server 2016 kan een [cloudgetuige](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)gebruiken, maar voor consistentie met eerdere besturingssystemen maakt dit document gebruik van een virtuele machine voor een getuige.  

Voordat u verder gaat met de volgende ontwerpbeslissingen.

* **Opslag - Beheerde schijven met Azure**

   Gebruik Azure Managed Disks voor de opslag van virtuele machines. Microsoft raadt Beheerschijven voor virtuele SQL Server-machines aan. Managed Disks verwerken de opslag achter de schermen. Bovendien distribueert Azure de opslagresources zodat voldoende redundantie wordt geboden wanneer virtuele machines met Managed Disks zich in dezelfde beschikbaarheidsset bevinden. Zie [Azure Managed Disks overview](../managed-disks-overview.md) (Overzicht van Azure Managed Disks) voor meer informatie. Zie [Beheerde schijven gebruiken voor VM's gebruiken voor VM's in een beschikbaarheidsset](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)voor specifieke gegevens over beheerde schijven in een beschikbaarheidsset.

* **Netwerk - Privé-IP-adressen in productie**

   Voor de virtuele machines maakt deze zelfstudie gebruik van openbare IP-adressen. Een openbaar IP-adres maakt externe verbinding rechtstreeks met de virtuele machine via het internet mogelijk - het maakt configuratiestappen eenvoudiger. In productieomgevingen raadt Microsoft alleen privé-IP-adressen aan om de kwetsbaarheidsvoetafdruk van de VM-bron van SQL Server-instantie te verkleinen.

### <a name="create-and-configure-the-sql-server-vms"></a>De SQL Server VM's maken en configureren
Maak vervolgens drie VM's- twee SQL Server VM's en een VM voor een extra clusterknooppunt. Als u elk van de VM's wilt maken, gaat u terug naar de **SQL-HA-RG-brongroep,** klikt u op **Toevoegen,** zoeken naar het juiste galerijitem, klikt u op **Virtuele machine**en klikt u vervolgens op **Uit galerij**. Gebruik de informatie in de volgende tabel om u te helpen de VM's te maken:


| Pagina | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Het juiste galerijitem selecteren |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise op Windows Server 2016** |**SQL Server 2016 SP1 Enterprise op Windows Server 2016** |
| Basisprincipes voor **virtuele machineconfiguratie** |**Naam** = cluster-fsw<br/>**Gebruikersnaam** = Domeinbeheerder<br/>**Wachtwoord** = Contoso!0000<br/>**Abonnement** = Uw abonnement<br/>**Resourcegroep** = SQL-HA-RG<br/>**Locatie** = Uw azure-locatie |**Naam** = sqlserver-0<br/>**Gebruikersnaam** = Domeinbeheerder<br/>**Wachtwoord** = Contoso!0000<br/>**Abonnement** = Uw abonnement<br/>**Resourcegroep** = SQL-HA-RG<br/>**Locatie** = Uw azure-locatie |**Naam** = sqlserver-1<br/>**Gebruikersnaam** = Domeinbeheerder<br/>**Wachtwoord** = Contoso!0000<br/>**Abonnement** = Uw abonnement<br/>**Resourcegroep** = SQL-HA-RG<br/>**Locatie** = Uw azure-locatie |
| Grootte van virtuele **machineconfiguratie** |**GROOTTE** = DS1\_V2 (1 vCPU, 3,5 GB) |**GROOTTE** = DS2\_V2 (2 vCPU's, 7 GB)</br>De grootte moet SSD-opslag ondersteunen (Premium-schijfondersteuning. )) |**GROOTTE** = DS2\_V2 (2 vCPU's, 7 GB) |
| **Configuratie-instellingen voor** virtuele machines |**Opslag:** beheerde schijven gebruiken.<br/>**Virtueel netwerk** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Openbaar IP-adres** wordt automatisch gegenereerd.<br/>**Netwerkbeveiligingsgroep** = Geen<br/>**Diagnostische controle** = ingeschakeld<br/>**Diagnoseopslagaccount** = Gebruik een automatisch gegenereerd opslagaccount<br/>**Beschikbaarheidsset** = sqlAvailabilitySet<br/> |**Opslag:** beheerde schijven gebruiken.<br/>**Virtueel netwerk** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Openbaar IP-adres** wordt automatisch gegenereerd.<br/>**Netwerkbeveiligingsgroep** = Geen<br/>**Diagnostische controle** = ingeschakeld<br/>**Diagnoseopslagaccount** = Gebruik een automatisch gegenereerd opslagaccount<br/>**Beschikbaarheidsset** = sqlAvailabilitySet<br/> |**Opslag:** beheerde schijven gebruiken.<br/>**Virtueel netwerk** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Openbaar IP-adres** wordt automatisch gegenereerd.<br/>**Netwerkbeveiligingsgroep** = Geen<br/>**Diagnostische controle** = ingeschakeld<br/>**Diagnoseopslagaccount** = Gebruik een automatisch gegenereerd opslagaccount<br/>**Beschikbaarheidsset** = sqlAvailabilitySet<br/> |
| SQL **Server-instellingen** voor virtuele machineconfiguratie |Niet van toepassing |**SQL-connectiviteit** = Privé (binnen virtueel netwerk)<br/>**Poort** = 1433<br/>**SQL-verificatie** = uitschakelen<br/>**Opslagconfiguratie** = Algemeen<br/>**Geautomatiseerd patchen** = zondag om 14:00<br/>**Automatische back-up** = uitgeschakeld</br>**Azure Key Vault-integratie** = Uitgeschakeld |**SQL-connectiviteit** = Privé (binnen virtueel netwerk)<br/>**Poort** = 1433<br/>**SQL-verificatie** = uitschakelen<br/>**Opslagconfiguratie** = Algemeen<br/>**Geautomatiseerd patchen** = zondag om 14:00<br/>**Automatische back-up** = uitgeschakeld</br>**Azure Key Vault-integratie** = Uitgeschakeld |

<br/>

> [!NOTE]
> De hier voorgestelde machineformaten zijn bedoeld voor het testen van beschikbaarheidsgroepen in Azure VM's. Zie de aanbevelingen voor SQL Server-machineformaten en -configuratie in [Aanbevolen procedures voor SQL Server in Azure-virtuele machines voor](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)de beste prestaties op het gebied van productieworkloads.
>
>

Nadat de drie VM's volledig zijn ingericht, moet u ze aansluiten bij het **domein corp.contoso.com** en CORP\Installeer administratieve rechten op de machines verlenen.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>De servers aansluiten bij het domein

Je nu lid worden van de VM's om te **corp.contoso.com.** Volg de volgende stappen voor zowel de SQL Server VM's als de file share witness-server:

1. Maak op afstand verbinding met de virtuele machine met **BUILTIN\DomainAdmin**.
2. Klik in **Serverbeheer**op **Lokale server**.
3. Klik op de koppeling **WERKGROEP.**
4. Klik in de sectie **Computernaam** op **Wijzigen**.
5. Schakel het selectievakje **Domein** in en typ **corp.contoso.com** in het tekstvak. Klik op **OK**.
6. Geef in het pop-updialoogvenster **Windows Security** de referenties op voor het standaard domeinbeheerdersaccount **(CORP\DomainAdmin)** en het wachtwoord (**Contoso!0000**).
7. Wanneer u het bericht 'Welkom in het corp.contoso.com domein' ziet, klikt u op **OK**.
8. Klik **op Sluiten**en klik vervolgens op Nu opnieuw **starten** in het pop-updialoogvenster.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>De Corp\Installatiegebruiker toevoegen als beheerder op elke cluster-VM

Nadat elke virtuele machine opnieuw is opgestart als lid van het domein, voegt u **CORP\Installeren toe** als lid van de groep lokale beheerders.

1. Wacht tot de VM opnieuw is opgestart en start het RDP-bestand opnieuw vanaf de primaire domeincontroller om u aan te melden bij **sqlserver-0** met behulp van het **CORP\DomainAdmin-account.**
   >[!TIP]
   >Zorg ervoor dat u zich aanmeldt met het domeinbeheerdersaccount. In de vorige stappen gebruikte u het INGEBOUWDE beheerdersaccount. Nu de server zich in het domein bevindt, gebruikt u het domeinaccount. Geef in uw RDP-sessie de*gebruikersnaam* *DOMAIN*\\op.

2. Selecteer In **Serverbeheer**de optie **Extra**en klik vervolgens op **Computerbeheer**.
3. Vouw in het venster **Computerbeheer** **lokale gebruikers en groepen**uit en selecteer Vervolgens **Groepen**.
4. Dubbelklik op de groep **Administrators.**
5. Klik **in** het dialoogvenster Eigenschappen van beheerders op de knop **Toevoegen.**
6. Voer de gebruiker **CORP\Install in**en klik op **OK**.
7. Klik op **OK** om het dialoogvenster **Beheerderseigenschappen** te sluiten.
8. Herhaal de vorige stappen op **sqlserver-1** en **cluster-fsw**.

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>De SQL Server-serviceaccounts instellen

Stel op elke SQL Server VM het SQL Server-serviceaccount in. Gebruik de accounts die u hebt gemaakt toen u de domeinaccounts configureerde.

1. Open **SQL Server Configuration Manager**.
2. Klik met de rechtermuisknop op de SQL Server-service en klik vervolgens op **Eigenschappen**.
3. Stel het account en wachtwoord in.
4. Herhaal deze stappen op de andere SQL Server VM.  

Voor SQL Server-beschikbaarheidsgroepen moet elke SQL Server VM als domeinaccount worden uitgevoerd.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Een aanmelding maken op elke SQL Server VM voor het installatieaccount

Gebruik het installatieaccount (CORP\install) om de beschikbaarheidsgroep te configureren. Dit account moet lid zijn van de vaste serverrol **van sysadmin** op elke SQL Server VM. De volgende stappen maken een aanmelding voor het installatieaccount:

1. Maak verbinding met de server via het Extern bureaublad-protocol (RDP) met het * \<MachineName\>\DomainAdmin-account.*

1. Open SQL Server Management Studio en maak verbinding met de lokale instantie van SQL Server.

1. Klik in **Object Explorer**op **Beveiliging**.

1. Klik met de rechtermuisknop **op Aanmeldingen**. Klik **op Nieuwe aanmelding**.

1. Klik **bij Aanmelding - Nieuw**op **Zoeken**.

1. Klik **op Locaties**.

1. Voer de netwerkreferenties van de domeinbeheerder in.

1. Gebruik het installatieaccount.

1. Stel de aanmelding in als lid van de vaste serverrol **van sysadmin.**

1. Klik op **OK**.

Herhaal de voorgaande stappen op de andere SQL Server VM.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Failoverclustering-functies toevoegen aan beide SQL Server VM's

Ga als volgt te werk om failoverclustering-functies toe te voegen:

1. Maak verbinding met de virtuele SQL Server-machine via het Extern bureaublad-protocol (RDP) met het *CORP\install-account.* Dashboard **serverbeheer openen**.
2. Klik **op** de koppeling Rollen en functies toevoegen op het dashboard.

    ![Serverbeheer - Rollen toevoegen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Selecteer **Volgende** totdat u de sectie **Serveronderdelen hebt** geopend.
4. Selecteer **failoverclustering**in **Functies**.
5. Voeg eventuele extra vereiste functies toe.
6. Klik **op Installeren** om de functies toe te voegen.

Herhaal de stappen op de andere SQL Server VM.

  >[!NOTE]
  > Deze stap, samen met het daadwerkelijk samenvoegen van de SQL Server VM's naar het failovercluster, kan nu worden geautomatiseerd met [Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) en Azure [Quickstart-sjablonen.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall">De firewall configureren op elke SQL Server VM

De oplossing vereist dat de volgende TCP-poorten in de firewall worden geopend:

- **SQL Server VM**:<br/>
   Poort 1433 voor een standaardexemplaar van SQL Server.
- **Azure load balancer probe:**<br/>
   Elke beschikbare poort. Voorbeelden maken vaak gebruik van 59999.
- **Eindpunt voor databasespiegelen:** <br/>
   Elke beschikbare poort. Voorbeelden maken vaak gebruik van 5022.

De firewallpoorten moeten op beide SQL Server VM's geopend zijn.

De methode voor het openen van de poorten is afhankelijk van de firewall-oplossing die u gebruikt. In de volgende sectie wordt uitgelegd hoe u de poorten in Windows Firewall opent. Open de vereiste poorten op elk van uw SQL Server VM's.

### <a name="open-a-tcp-port-in-the-firewall"></a>Een TCP-poort openen in de firewall

1. Start Op het eerste STARTSCHERM **van** SQL Server Windows Firewall met **geavanceerde beveiliging.**
2. Selecteer **binnenkomende regels in**het linkerdeelvenster . Klik in het rechterdeelvenster op **Nieuwe regel**.
3. Kies **Poort** **voor Regeltype**.
4. Geef voor de poort **TCP** op en typ de juiste poortnummers. Zie het volgende voorbeeld:

   ![SQL-firewall](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Klik op **Volgende**.
6. Houd op de **actiepagina** **De geselecteerde verbinding toestaan** en klik vervolgens op **Volgende**.
7. Accepteer **op** de pagina Profiel de standaardinstellingen en klik op **Volgende**.
8. Geef op de pagina **Naam** een regelnaam op (zoals **Azure LB Probe)** in het tekstvak **Naam** en klik op **Voltooien**.

Herhaal deze stappen op de tweede SQL Server VM.

## <a name="configure-system-account-permissions"></a>Systeemaccountmachtigingen configureren

Voer de volgende stappen uit voor elk SQL Server-exemplaar om een account voor het systeemaccount aan te maken en de juiste machtigingen toe te kennen:

1. Maak een `[NT AUTHORITY\SYSTEM]` account aan voor elk SQL Server-exemplaar. In het volgende script wordt dit account gemaakt:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. De volgende machtigingen `[NT AUTHORITY\SYSTEM]` verlenen aan elke SQL Server-instantie:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   In het volgende script worden deze machtigingen toegekend:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Volgende stappen

* [Een SQL Server Always On-beschikbaarheidsgroep maken op virtuele Azure-machines](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
