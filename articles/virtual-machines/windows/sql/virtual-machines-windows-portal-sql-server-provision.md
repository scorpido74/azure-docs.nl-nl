---
title: Een virtuele machine inrichten met Azure Portal
description: In deze hand leiding worden de opties beschreven voor het maken van virtuele machines met Windows SQL Server 2017 in de Azure Portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 68fda45038da48660da0c29787b3a86e00d9b129
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033592"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Een virtuele machine met Windows SQL Server inrichten in de Azure Portal

Deze hand leiding bevat informatie over de verschillende opties die beschikbaar zijn wanneer u een virtuele machine van Windows SQL Server maakt in de Azure Portal. In dit artikel komen meer configuratie opties aan bod dan de Snelstartgids voor de [SQL Server virtuele machine](quickstart-sql-vm-create-portal.md), die meer door één mogelijke inrichtings taak gaat. 

Gebruik deze hand leiding om uw eigen SQL Server-VM te maken. Of gebruik deze als referentie voor de beschik bare opties in de Azure Portal.

> [!TIP]
> Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](virtual-machines-windows-sql-server-iaas-faq.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a id="select"></a>Installatie kopieën van de galerie met virtuele machines SQL Server

Wanneer u een SQL Server virtuele machine maakt, kunt u een van de verschillende vooraf geconfigureerde installatie kopieën selecteren in de galerie met virtuele machines. De volgende stappen laten zien hoe u een van de SQL Server 2017-installatie kopieën kunt selecteren.

1. Selecteer **Azure SQL** in het linkermenu van de Azure Portal. Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens Azure SQL in het zoekvak. Beschrijving Selecteer de ster naast **Azure SQL** om deze te favoriet en voeg deze toe als een item in de linkernavigatiebalk. 
1. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie weer geven door **Details weer geven**te selecteren. 
1. Typ `2017` in het zoekvak van de SQL Server-installatie kopie op de tegel **SQL virtual machines** en selecteer vervolgens **gratis SQL Server licentie: SQL Server 2017 Developer op Windows Server 2016** uit de vervolg keuzelijst. 


   ![SQL VM-installatie kopie selecteren](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > De Developer-editie wordt in deze walkthrough gebruikt omdat het een volledig functionele, gratis versie van SQL Server voor ontwikkel tests is. U betaalt alleen voor de kosten van het uitvoeren van de virtuele machine. U kunt echter kiezen welke installatie kopieën u in deze walkthrough wilt gebruiken. Zie [SQL Server Windows virtual machines Overview](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)(Engelstalig) voor een beschrijving van de beschik bare installatie kopieën.

   > [!TIP]
   > De licentie kosten voor SQL Server zijn opgenomen in de prijzen per seconde van de virtuele machine die u maakt, en is afhankelijk van editie en kernen. SQL Server Developer Edition is echter gratis voor ontwikkeling/testen (niet voor productie doeleinden) en SQL Express is gratis voor lichte werk belastingen (minder dan 1 GB geheugen, minder dan 10 GB opslag ruimte). U kunt ook uw eigen licentie (BYOL) meenemen en alleen betalen voor de virtuele machine. De namen van de installatiekopieën worden voorafgegaan door {BYOL}. 
   >
   > Zie [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prijsrichtlijnen voor SQL Server Azure VM's) voor meer informatie over deze opties.


1. Selecteer **Maken**.


## <a name="1-configure-basic-settings"></a>1. basis instellingen configureren


Geef op het tabblad **basis beginselen** de volgende informatie op:

* Controleer onder **Project Details**of het juiste abonnement is geselecteerd. 
*  In de sectie **resource groep** selecteert u een bestaande resource groep in de lijst of kiest u **nieuwe maken** om een nieuwe resource groep te maken. Een resourcegroep is een verzameling verwante resources in Azure (virtuele machines, opslagaccounts, virtuele netwerken enz.). 

    ![Abonnement](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Het is een goed idee om een nieuwe resourcegroep te maken als u het gebruik van SQL Server in Azure alleen wilt testen of hier meer over te weten wilt komen. Als u klaar bent met testen, verwijdert u gewoon de resourcegroep. De virtuele machine en alle resources die aan de resourcegroep zijn gekoppeld, worden dan automatisch verwijderd. Zie voor meer informatie over resourcegroepen [Overzicht van Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).


* Onder **Details van exemplaar**:
    1. Voer een unieke **naam voor de virtuele machine**in.  
    1. Kies een locatie voor uw **regio**. 
    1. Voor het doel van deze hand leiding, behoud de **beschikbaarheids opties** ingesteld op _geen infra structuur-redundantie vereist_. Zie [Beschik baarheid](../../windows/availability.md)voor meer informatie over beschikbaarheids opties. 
    1. Selecteer in de lijst met **installatie kopieën** _gratis SQL Server licentie: SQL Server 2017 Developer op Windows Server 2016_.  
    1. Kies voor het wijzigen van de **grootte** voor de **grootte** van de virtuele machine en selecteer de **a2 Basic** -aanbieding. Zorg ervoor dat u uw resources opschoont nadat u klaar bent met deze om te voor komen dat er onverwachte kosten in rekening worden gebracht. Zie [Performance best practices for SQL Server in Azure Virtual Machines (Best practices voor optimale prestaties van SQL Server in Azure Virtual Machines)](virtual-machines-windows-sql-performance.md) voor de aanbevolen machinegrootten en configuratie voor productieworkloads.

    ![Exemplaar Details](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> De geschatte maandelijkse kosten die worden weergegeven in het venster **Grootte kiezen**, zijn niet inclusief de kosten voor SQL Server-licentieverlening. Deze schatting is alleen de kosten van de virtuele machine. Voor de Express-en Developer-edities van SQL Server is deze schatting de totale geschatte kosten. Zie de [pagina met prijzen voor virtuele Windows-machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) en selecteer de gewenste editie van SQL server. Zie ook de [richt lijnen voor prijzen voor SQL Server Azure-vm's](virtual-machines-windows-sql-server-pricing-guidance.md) en- [grootten voor virtuele machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Geef onder **Administrator-account**een gebruikers naam en wacht woord op. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Administrator-account](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* Onder **Binnenkomende poort regels**kiest u **geselecteerde poorten toestaan** en selecteert u vervolgens **RDP (3389)** in de vervolg keuzelijst. 

   ![Regels voor binnenkomende poort](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. optionele functies configureren

### <a name="disks"></a>Disks

Configureer uw schijf opties op het tabblad **schijven** . 

* Selecteer onder **type besturingssysteem schijf**het type schijf dat u wilt voor het besturings systeem in de vervolg keuzelijst. Premium wordt aanbevolen voor productie systemen, maar is niet beschikbaar voor een basis-VM. Als u Premium-SSD wilt gebruiken, wijzigt u de grootte van de virtuele machine. 
* Onder **Geavanceerd**selecteert u **ja** onder gebruik **Managed disks**.

   > [!NOTE]
   > Microsoft raadt Managed Disks aan voor SQL Server. Managed Disks verwerken de opslag achter de schermen. Bovendien distribueert Azure de opslagresources zodat voldoende redundantie wordt geboden wanneer virtuele machines met Managed Disks zich in dezelfde beschikbaarheidsset bevinden. Zie [overzicht van Azure Managed disks](../managed-disks-overview.md)voor meer informatie. Zie [Managed Disks gebruiken schijven voor virtuele machines in een beschikbaarheidsset](../manage-availability.md) voor meer informatie over Managed Disks in een beschikbaarheidsset.

![SQL-VM-schijf instellingen](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Netwerken

Configureer uw netwerk opties op het tabblad **netwerk** . 

* Maak een nieuw **virtueel netwerk**of gebruik een bestaand vNet voor uw SQL Server-VM. Wijs ook een **subnet** toe. 

* Onder **NIC-netwerk beveiligings groep**selecteert u een basis beveiligings groep of de geavanceerde beveiligings groep. Als u de optie basis selecteert, kunt u binnenkomende poorten voor de SQL Server VM selecteren (dezelfde waarden die zijn geconfigureerd op het tabblad **basis** ). Als u de optie Geavanceerd selecteert, kunt u een bestaande netwerk beveiligings groep kiezen of een nieuwe maken. 

* U kunt andere wijzigingen aanbrengen in de netwerk instellingen of de standaard waarden blijven gebruiken.

![SQL VM-netwerk instellingen](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Bewaking

Configureer bewaking en automatisch afsluiten op het tabblad **bewaking** . 

* Azure schakelt **Diagnostische gegevens over opstarten** standaard in met hetzelfde opslag account dat is opgegeven voor de virtuele machine. U kunt deze instellingen hier wijzigen en ook **Diagnostische gegevens van het besturings systeem**inschakelen. 
* U kunt ook de door het **systeem toegewezen beheerde identiteit** inschakelen en automatisch **Afsluiten** op dit tabblad. 

![Instellingen voor SQL-VM-beheer](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Configureer de SQL Server instellingen

Configureer op het tabblad **SQL Server instellingen** specifieke instellingen en optimalisaties voor SQL Server. De instellingen die u kunt configureren voor SQL Server omvatten het volgende:

| Instelling |
| --- |
| [Connectiviteit](#connectivity) |
| [Verificatie](#authentication) |
| [Integratie van Azure Key Vault](#azure-key-vault-integration) |
| [Opslagconfiguratie](#storage-configuration) |
| [Automatisch patch toepassen](#automated-patching) |
| [Automatische back-up](#automated-backup) |
| [R Services (geavanceerde analyse)](#r-services-advanced-analytics) |


### <a name="connectivity"></a>Connectiviteit

Geef onder **SQL-connectiviteit** het gewenste type toegang tot het SQL Server-exemplaar op deze virtuele machine op. Voor de doel einden van deze walkthrough selecteert u **openbaar (Internet)** om verbindingen toe te staan met SQL Server van computers of services op internet. Als deze optie is geselecteerd, configureert Azure automatisch de firewall en de netwerk beveiligings groep om verkeer op de geselecteerde poort toe te staan.

> [!TIP]
> Standaard gebruikt SQL Server een bekende poort, namelijk **1433**. Wijzig de poort in het vorige dialoogvenster om een andere poort, zoals 1401, te gebruiken voor betere beveiliging. Als u de poort wijzigt, moet u via die poort verbinding maken via alle client hulpprogramma's, zoals SSMS.

![SQL VM-beveiliging](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Voor verbinding met SQL Server via internet moet u ook SQL Server-verificatie inschakelen. Dit wordt beschreven in het volgende gedeelte.

Als u de mogelijkheid om via internet verbinding te maken met de database-engine liever niet inschakelt, kiest u een van de volgende opties:

* **Lokaal (alleen binnen VM)** om alleen verbindingen met SQL Server vanuit de virtuele machine toe te staan.
* **Privé (binnen virtueel netwerk)** om verbindingen met SQL Server toe te staan vanaf machines of services in hetzelfde virtuele netwerk.

In het algemeen kunt u de beveiliging verbeteren door te kiezen voor de meest beperkende connectiviteit die voor uw scenario mogelijk is. Alle opties kunnen echter worden beveiligd via regels van de netwerkbeveiligingsgroep en SQL/Windows-verificatie. U kunt de netwerkbeveiligingsgroep bewerken nadat de virtuele machine is gemaakt. Zie [Veiligheidsoverwegingen voor SQL Server in virtuele Azure-machines](virtual-machines-windows-sql-security.md) voor meer informatie.



### <a name="authentication"></a>Authentication

Als u SQL Server-verificatie nodig hebt, klikt u op **inschakelen** onder **SQL-verificatie** op het tabblad **SQL Server instellingen** .

![SQL Server-verificatie](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Als u van plan bent SQL Server via internet (de open bare connectiviteits optie) te gebruiken, moet u hier SQL-verificatie inschakelen. Voor openbare toegang tot de SQL Server is het gebruik van SQL-verificatie vereist.

Als u SQL Server-verificatie inschakelt, geeft u een **Aanmeldingsnaam** en een **Wachtwoord** op. Deze aanmeldings naam is geconfigureerd als een SQL Server verificatie aanmelding en een lid van de vaste serverrol **sysadmin** . Zie [Een verificatiemodus kiezen](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) voor meer informatie over de verificatiemodi.

Als u SQL Server-verificatie niet inschakelt, kunt u het lokale beheerdersaccount op de virtuele machine gebruiken om verbinding te maken met het SQL Server-exemplaar.


### <a name="azure-key-vault-integration"></a>Integratie van Azure Sleutelkluis

Als u beveiligings geheimen wilt opslaan in azure voor versleuteling, selecteert u **SQL Server instellingen**en schuift u omlaag naar **Azure Key kluis-integratie**. Selecteer **inschakelen** en vul de aangevraagde informatie in. 

![Integratie van Azure Sleutelkluis](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

De volgende tabel bevat de vereiste parameters voor het configureren van de Azure Sleutelkluis-integratie.

| PARAMETER | BESCHRIJVING | VOORBEELD |
| --- | --- | --- |
| **Key Vault-URL** |De locatie van de sleutelkluis. |https:\/-contosokeyvault.vault.azure.net/ |
| **Principal-naam** |Principal-naam voor de Azure Active Directory-service. Deze naam wordt ook wel aangeduid als de Client-ID. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Principal-geheim** |Principal-geheim voor de Azure Active Directory-service. Dit geheim wordt ook wel aangeduid als het Clientgeheim. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Referentienaam** |**Referentienaam**: de Azure Sleutelkluis-integratie maakt een referentie in SQL Server, zodat de virtuele machine toegang heeft tot de sleutelkluis. Kies een naam voor deze referentie. |mycred1 |

Zie voor meer informatie [Integratie van Azure Key Vault configureren voor SQL Server op Azure-VM's](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Opslagconfiguratie

Selecteer op het tabblad **SQL Server instellingen** onder **opslag configuratie**de optie **configuratie wijzigen** om de configuratie pagina prestaties geoptimaliseerd voor opslag te openen en de opslag vereisten op te geven.

![Configuratie van SQL-VM-opslag](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selecteer onder **Opslag geoptimaliseerd voor** een van de volgende opties:

* **Algemeen** is de standaardinstelling en ondersteunt de meeste workloads.
* **Transactionele verwerking** optimaliseert de opslag voor traditionele OLTP-workloads van data bases.
* **Gegevensopslag** optimaliseert de opslag voor analyse- en rapportageworkloads.

![Configuratie van SQL-VM-opslag](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

U kunt ervoor kiezen de waarden standaard te laten staan of u kunt de opslag topologie hand matig aanpassen aan uw IOPS-behoeften. Zie [opslag configuratie](virtual-machines-windows-sql-server-storage-configuration.md)voor meer informatie. 

### <a name="sql-server-license"></a>SQL Server licentie
Als u een Software Assurance-klant bent, kunt u de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) gebruiken om uw eigen SQL Server licentie te nemen en op te slaan op resources. 

![SQL VM-licentie](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatisch patchen

**Automatisch patchen** is standaard ingeschakeld. Met automatisch patchen kan Azure automatisch een patch uitvoeren voor SQL Server en het besturingssysteem. Geef een dag van de week, een tijd en een periode op voor een onderhoudssessie. Azure voert de patch uit tijdens deze onderhoudssessie. Het onderhoudsschema maakt voor de tijd gebruik van de landinstellingen van de virtuele machine. Als u niet wilt dat Azure een automatische patch uitvoert voor SQL Server en het besturingssysteem, klikt u op **Uitschakelen**.  

![Automatische patching van SQL-VM](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Zie voor meer informatie [Automatisch patchen voor SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Automatische back-up

Schakel automatische databaseback-ups in voor alle databases onder **Automatische back-up**. Automatische back-up is standaard uitgeschakeld.

Als u automatische back-up van SQL inschakelt, kunt u de volgende instellingen configureren:

* De retentieperiode (dagen) voor back-ups
* Het opslagaccount dat voor back-ups moet worden gebruikt
* Versleutelingsoptie en wachtwoord voor back-ups
* Back-up maken van systeemdatabases
* Back-upschema configureren

Voor het versleutelen van de back-up klikt u op **Inschakelen**. Geef het **Wachtwoord** op. Azure maakt een certificaat voor het versleutelen van de back-ups en gebruikt het opgegeven wachtwoord om dit certificaat te beschermen. Standaard wordt het schema automatisch ingesteld, maar u kunt een hand matige planning maken door **hand matig**te selecteren. 

![Automatische back-ups van SQL-VM](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Zie voor meer informatie [Automatische back-up voor SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).


### <a name="r-services-advanced-analytics"></a>R Services (geavanceerde analyse)

U hebt de mogelijkheid om SQL Server R Services in te scha kelen [(geavanceerde analyse)](/sql/advanced-analytics/r/sql-server-r-services/). Met deze optie kunt u geavanceerde analyses gebruiken met SQL Server 2017. Selecteer **inschakelen** in het venster **SQL Server instellingen** .


## <a name="4-review--create"></a>4. controleren en maken

Bekijk op het tabblad **controleren en maken** de samen vatting en selecteer **maken** om SQL Server, resource groep en resources te maken die zijn opgegeven voor deze virtuele machine.

U kunt de implementatie bewaken vanuit Azure Portal. Met de knop **Meldingen** boven aan het scherm kunt u de algemene status van de implementatie weergeven.

> [!NOTE]
> Om u een idee te geven van de implementatietijden, heb ik een virtuele SQL-machine voor de regio US - oost geïmplementeerd met standaardinstellingen. Deze testimplementatie nam in totaal ongeveer 12 minuten in beslag. Afhankelijk van uw regio en de geselecteerde instellingen bent u mogelijk meer of minder tijd kwijt aan de implementatie.

## <a id="remotedesktop"></a> De virtuele machine openen via Extern bureaublad

Doorloop de volgende stappen om via Extern bureaublad verbinding te maken met de virtuele SQL Server-machine:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Nadat u verbinding hebt gemaakt met de virtuele SQL Server-machine, kunt u SQL Server Management Studio starten en verbinding maken met Windows-verificatie met behulp van de lokale beheerdersreferenties. Als u SQL Server-verificatie inschakelt, kunt u ook verbinding maken met SQL-verificatie. Dit kan met behulp van de SQL-aanmeldingsnaam en het wachtwoord die u hebt geconfigureerd tijdens het inrichten.

Met toegang tot de machine kunt u rechtstreeks de instellingen voor de machine en de SQL Server wijzigen op basis van uw vereisten. U kunt bijvoorbeeld de firewallinstellingen configureren of de SQL Server-configuratie-instellingen wijzigen.

## <a id="connect"></a> Op afstand verbinding maken met SQL Server

In dit scenario hebt u **open bare** toegang geselecteerd voor de virtuele machine en **SQL Server-verificatie**. Door deze instellingen is de virtuele machine automatisch geconfigureerd voor het toestaan van SQL Server-verbindingen vanaf elke client via internet (ervan uitgaande dat deze beschikken over de juiste SQL-aanmeldgegevens).

> [!NOTE]
> Als u tijdens het inrichten niet de optie Openbaar hebt geselecteerd, kunt u de instellingen voor SQL-verbindingen achteraf wijzigen via de portal. Zie [Change your SQL connectivity settings](virtual-machines-windows-sql-connect.md#change) (SQL-verbindingsinstellingen wijzigen) voor meer informatie.

In de volgende secties ziet u hoe u via Internet verbinding maakt met uw SQL Server VM-exemplaar.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > In dit voor beeld wordt gebruikgemaakt van de algemene poort 1433. Deze waarde moet echter worden gewijzigd als er een andere poort (zoals 1401) is opgegeven tijdens de implementatie van de SQL Server VM. 


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van SQL Server in Azure [SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) en de [Veelgestelde vragen](virtual-machines-windows-sql-server-iaas-faq.md).
