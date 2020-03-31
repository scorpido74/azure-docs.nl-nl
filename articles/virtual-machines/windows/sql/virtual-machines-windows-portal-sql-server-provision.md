---
title: Virtuele machine inrichten met Azure-portal
description: In deze handleiding worden uw opties beschreven voor het maken van virtuele Windows Server 2017-machines in de Azure-portal.
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
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 1fdf776570b6f10a363fb98dfe343387d86219d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249787"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Een virtuele Windows SQL Server-machine inrichten in de Azure-portal

In deze handleiding vindt u informatie over de verschillende opties die beschikbaar zijn wanneer u een virtuele Windows Server-machine maakt in de Azure-portal. Dit artikel bevat meer configuratieopties dan de [SQL Server VM quickstart](quickstart-sql-vm-create-portal.md), die meer gaat door middel van een mogelijke inrichting taak. 

Gebruik deze handleiding om uw eigen SQL Server VM te maken. Of gebruik het als referentie voor de beschikbare opties in de Azure-portal.

> [!TIP]
> Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](virtual-machines-windows-sql-server-iaas-faq.md).

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a>Virtuele sql Server-galerieafbeeldingen

Wanneer u een virtuele SQL Server-machine maakt, u een van de verschillende vooraf geconfigureerde afbeeldingen selecteren in de virtuele machinegalerie. De volgende stappen laten zien hoe u een van de SQL Server 2017-afbeeldingen selecteert.

1. Selecteer **Azure SQL** in het linkermenu van de Azure-portal. Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en voeg deze toe als een item in de linkernavigatie. 
1. Selecteer **+ Toevoegen** om de **optiepagina SQL-implementatie selecteren** te openen. U aanvullende informatie bekijken door **Details weergeven te selecteren.** 
1. Typ `2017` in het zoekvak SQL Server-afbeelding op de tegel **SQL-virtuele machines** en selecteer **vervolgens Gratis SQL Server-licentie: SQL Server 2017-ontwikkelaar op Windows Server 2016** in de vervolgkeuzelijst. 


   ![SQL VM-afbeelding selecteren](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > De Developer-editie wordt gebruikt in deze walkthrough omdat het een volledige, gratis editie van SQL Server is voor ontwikkelingstests. U betaalt alleen voor de kosten van het uitvoeren van de virtuele machine. U bent echter vrij om een van de afbeeldingen te kiezen om te gebruiken in deze walkthrough. Zie het overzicht van [SQL Server Windows Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)voor een beschrijving van beschikbare afbeeldingen.

   > [!TIP]
   > Licentiekosten voor SQL Server worden opgenomen in de prijzen per seconde van de VM die u maakt en variëren per editie en cores. Sql Server Developer edition is echter gratis voor ontwikkeling/testen (niet voor productie) en SQL Express is gratis voor lichtgewicht workloads (minder dan 1 GB geheugen, minder dan 10 GB opslag). U ook brengen-uw eigen-licentie (BYOL) en betalen alleen voor de VM. De namen van de installatiekopieën worden voorafgegaan door {BYOL}. 
   >
   > Zie [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prijsrichtlijnen voor SQL Server Azure VM's) voor meer informatie over deze opties.


1. Selecteer **Maken**.


## <a name="1-configure-basic-settings"></a>1. Basisinstellingen configureren


Geef op het tabblad **Basisbeginselen** de volgende informatie op:

* Controleer **onder Projectdetails**of het juiste abonnement is geselecteerd. 
*  Selecteer in de sectie **Resourcegroep** een bestaande resourcegroep in de lijst of kies **Nieuw maken** om een nieuwe resourcegroep te maken. Een resourcegroep is een verzameling verwante resources in Azure (virtuele machines, opslagaccounts, virtuele netwerken enz.). 

    ![Abonnement](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Het is een goed idee om een nieuwe resourcegroep te maken als u het gebruik van SQL Server in Azure alleen wilt testen of hier meer over te weten wilt komen. Als u klaar bent met testen, verwijdert u gewoon de resourcegroep. De virtuele machine en alle resources die aan de resourcegroep zijn gekoppeld, worden dan automatisch verwijderd. Zie voor meer informatie over resourcegroepen [Overzicht van Azure Resource Manager](../../../azure-resource-manager/management/overview.md).


* Onder **Bijzonderheden van de instantie**:
    1. Voer een unieke **naam van de virtuele machine**in.  
    1. Kies een locatie voor uw **regio.** 
    1. Voor de toepassing van deze handleiding moet u **beschikbaarheidsopties** die zijn ingesteld op _geen redundantie van de infrastructuur, laten_vallen . Zie [Beschikbaarheid](../../windows/availability.md)voor meer informatie over beschikbaarheidsopties. 
    1. Selecteer in de lijst **Met afbeeldingen** de optie Gratis _SQL Server-licentie: SQL Server 2017-ontwikkelaar op Windows Server 2016_.  
    1. Kies voor **Wijziging van de grootte** voor de **grootte** van de virtuele machine en selecteer het **A2 Basic-aanbod.** Zorg ervoor dat u uw resources opschonen zodra u klaar bent met hen om onverwachte kosten te voorkomen. Zie [Performance best practices for SQL Server in Azure Virtual Machines (Best practices voor optimale prestaties van SQL Server in Azure Virtual Machines)](virtual-machines-windows-sql-performance.md) voor de aanbevolen machinegrootten en configuratie voor productieworkloads.

    ![Instantiedetails](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> De geschatte maandelijkse kosten die worden weergegeven in het venster **Grootte kiezen**, zijn niet inclusief de kosten voor SQL Server-licentieverlening. Deze schatting is de kosten van de VM alleen. Voor de Express- en Developer-edities van SQL Server is deze schatting de totale geschatte kosten. Zie de [pagina met prijzen voor virtuele Windows-machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) en selecteer de gewenste editie van SQL server. Zie ook de [richtlijnen voor prijzen voor SQL Server Azure VM's](virtual-machines-windows-sql-server-pricing-guidance.md) en [-formaten voor virtuele machines.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* Geef **onder Administrator-account**een gebruikersnaam en een wachtwoord op. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Administrator-account](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* Kies onder **Inkomende poortregels**De optie **Geselecteerde poorten toestaan** en selecteer vervolgens **RDP (3389)** in de vervolgkeuzelijst. 

   ![Regels voor binnenkomende poort](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Optionele functies configureren

### <a name="disks"></a>Disks

Configureer op het tabblad **Schijven** de schijfopties. 

* Selecteer **onder het type OS-schijf**het gewenste type schijf voor uw besturingssysteem in de vervolgkeuzelijst. Premium wordt aanbevolen voor productiesystemen, maar is niet beschikbaar voor een Basic VM. Als u Premium SSD wilt gebruiken, wijzigt u de grootte van de virtuele machine. 
* Selecteer **Onder Geavanceerd**de optie **Ja** onder **Beheerde schijven**gebruiken .

   > [!NOTE]
   > Microsoft raadt Managed Disks aan voor SQL Server. Managed Disks verwerken de opslag achter de schermen. Bovendien distribueert Azure de opslagresources zodat voldoende redundantie wordt geboden wanneer virtuele machines met Managed Disks zich in dezelfde beschikbaarheidsset bevinden. Zie [Overzicht van Azure Managed Disks voor](../managed-disks-overview.md)meer informatie. Zie [Managed Disks gebruiken schijven voor virtuele machines in een beschikbaarheidsset](../manage-availability.md) voor meer informatie over Managed Disks in een beschikbaarheidsset.

![SQL VM-schijfinstellingen](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Netwerken

Configureer op het tabblad **Netwerken** uw netwerkopties. 

* Maak een nieuw **virtueel netwerk**of gebruik een bestaande vNet voor uw SQL Server VM. Wijs ook een **subnet** aan. 

* Selecteer onder **NIC-netwerkbeveiligingsgroep**een basisbeveiligingsgroep of de geavanceerde beveiligingsgroep. Als u de basisoptie kiest, u binnenkomende poorten selecteren voor de SQL Server VM (dezelfde waarden die zijn geconfigureerd op het tabblad **Basis).** Als u de geavanceerde optie selecteert, u een bestaande netwerkbeveiligingsgroep kiezen of een nieuwe groep maken. 

* U andere wijzigingen aanbrengen in de netwerkinstellingen of de standaardwaarden behouden.

![SQL VM-netwerkinstellingen](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Bewaking

Configureer op het tabblad **Controle** de controle en automatische afsluiting. 

* Azure maakt **standaard opstartdiagnostiek** mogelijk met hetzelfde opslagaccount dat is aangewezen voor de virtuele machine. U deze instellingen hier wijzigen en **os-gastdiagnostiek**inschakelen. 
* U **systeemtoegewezen beheerde identiteit** en **automatisch afsluiten** ook op dit tabblad inschakelen. 

![SQL VM-beheerinstellingen](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. SQL Server-instellingen configureren

Configureer op het tabblad **SQL Server-instellingen** specifieke instellingen en optimalisaties voor SQL Server. De instellingen die u voor SQL Server configureren, zijn onder andere:

| Instelling |
| --- |
| [Connectiviteit](#connectivity) |
| [Verificatie](#authentication) |
| [Azure Key Vault-integratie](#azure-key-vault-integration) |
| [Opslagconfiguratie](#storage-configuration) |
| [Geautomatiseerd patchen](#automated-patching) |
| [Geautomatiseerde back-up](#automated-backup) |
| [Machine Learning Services](#machine-learning-services) |


### <a name="connectivity"></a>Connectiviteit

Geef onder **SQL-connectiviteit** het gewenste type toegang tot het SQL Server-exemplaar op deze virtuele machine op. Selecteer voor deze walkthrough **Public (internet)** om verbindingen met SQL Server toe te staan vanaf machines of services op internet. Als deze optie is geselecteerd, configureert Azure automatisch de firewall en de netwerkbeveiligingsgroep om verkeer op de geselecteerde poort toe te staan.

> [!TIP]
> Standaard gebruikt SQL Server een bekende poort, namelijk **1433**. Wijzig de poort in het vorige dialoogvenster om een andere poort, zoals 1401, te gebruiken voor betere beveiliging. Als u de poort wijzigt, moet u verbinding maken via die poort via clienthulpprogramma's, zoals SSMS.

![SQL VM-beveiliging](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Voor verbinding met SQL Server via internet moet u ook SQL Server-verificatie inschakelen. Dit wordt beschreven in het volgende gedeelte.

Als u de mogelijkheid om via internet verbinding te maken met de database-engine liever niet inschakelt, kiest u een van de volgende opties:

* **Lokaal (alleen binnen VM)** om alleen verbindingen met SQL Server vanuit de virtuele machine toe te staan.
* **Privé (binnen virtueel netwerk)** om verbindingen met SQL Server toe te staan vanaf machines of services in hetzelfde virtuele netwerk.

In het algemeen kunt u de beveiliging verbeteren door te kiezen voor de meest beperkende connectiviteit die voor uw scenario mogelijk is. Alle opties kunnen echter worden beveiligd via regels van de netwerkbeveiligingsgroep en SQL/Windows-verificatie. U kunt de netwerkbeveiligingsgroep bewerken nadat de virtuele machine is gemaakt. Zie [Veiligheidsoverwegingen voor SQL Server in virtuele Azure-machines](virtual-machines-windows-sql-security.md) voor meer informatie.



### <a name="authentication"></a>Authentication

Als u SQL Server-verificatie vereist, klikt u op **Inschakelen** onder **SQL-verificatie** op het tabblad **SQL Server-instellingen.**

![SQL Server-verificatie](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Als u sql-server via internet wilt openen (de optie Openbare connectiviteit), moet u SQL-verificatie hier inschakelen. Voor openbare toegang tot de SQL Server is het gebruik van SQL-verificatie vereist.

Als u SQL Server-verificatie inschakelt, geeft u een **Aanmeldingsnaam** en een **Wachtwoord** op. Deze inlognaam is geconfigureerd als inlogfunctie voor SQL Server-verificatie en lid van de vaste serverrol **van sysadmin.** Zie [Een verificatiemodus kiezen](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) voor meer informatie over de verificatiemodi.

Als u SQL Server-verificatie niet inschakelt, kunt u het lokale beheerdersaccount op de virtuele machine gebruiken om verbinding te maken met het SQL Server-exemplaar.


### <a name="azure-key-vault-integration"></a>Integratie van Azure Sleutelkluis

Als u beveiligingsgeheimen in Azure wilt opslaan voor versleuteling, selecteert u **SQL Server-instellingen**en schuift u omlaag naar **Azure-sleutelkluisintegratie.** Selecteer De gevraagde informatie **inschakelen** en invullen. 

![Integratie van Azure Sleutelkluis](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

De volgende tabel bevat de vereiste parameters voor het configureren van de Azure Sleutelkluis-integratie.

| PARAMETER | BESCHRIJVING | VOORBEELD |
| --- | --- | --- |
| **Sleutelkluis-URL** |De locatie van de sleutelkluis. |https:\//contosokeyvault.vault.azure.net/ |
| **Principal-naam** |Principal-naam voor de Azure Active Directory-service. Deze naam wordt ook wel aangeduid als de Client-ID. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Principal-geheim** |Principal-geheim voor de Azure Active Directory-service. Dit geheim wordt ook wel aangeduid als het Clientgeheim. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Referentienaam** |**Referentienaam**: de Azure Sleutelkluis-integratie maakt een referentie in SQL Server, zodat de virtuele machine toegang heeft tot de sleutelkluis. Kies een naam voor deze referentie. |mycred1 |

Zie voor meer informatie [Integratie van Azure Sleutelkluis configureren voor SQL Server op Azure-VM's](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Opslagconfiguratie

Selecteer op het tabblad **SQL Server-instellingen** onder **Opslagconfiguratie**de optie **Configuratie wijzigen** om de pagina Prestatiegeoptimaliseerde opslagconfiguratie te openen en geef de opslagvereisten op.

![SQL VM-opslagconfiguratie](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selecteer onder **Opslag geoptimaliseerd voor** een van de volgende opties:

* **Algemeen** is de standaardinstelling en ondersteunt de meeste workloads.
* **Transactionele verwerking** optimaliseert de opslag voor traditionele DATABASE OLTP-workloads.
* **Gegevensopslag** optimaliseert de opslag voor analyse- en rapportageworkloads.

![SQL VM-opslagconfiguratie](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

U ervoor kiezen om de waarden standaard achter te laten, of u de opslagtopologie handmatig aanpassen aan uw IOPS-behoeften. Zie [opslagconfiguratie](virtual-machines-windows-sql-server-storage-configuration.md)voor meer informatie. 

### <a name="sql-server-license"></a>SQL Server-licentie
Als u een Software Assurance-klant bent, u het [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) gebruiken om uw eigen SQL Server-licentie mee te nemen en te besparen op resources. 

![SQL VM-licentie](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatisch patchen

**Automatisch patchen** is standaard ingeschakeld. Met automatisch patchen kan Azure automatisch een patch uitvoeren voor SQL Server en het besturingssysteem. Geef een dag van de week, een tijd en een periode op voor een onderhoudssessie. Azure voert de patch uit tijdens deze onderhoudssessie. Het onderhoudsschema maakt voor de tijd gebruik van de landinstellingen van de virtuele machine. Als u niet wilt dat Azure een automatische patch uitvoert voor SQL Server en het besturingssysteem, klikt u op **Uitschakelen**.  

![SQL VM geautomatiseerd patchen](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Zie voor meer informatie [Automatisch patchen voor SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Automatische back-up

Schakel automatische databaseback-ups in voor alle databases onder **Automatische back-up**. Automatische back-up is standaard uitgeschakeld.

Als u automatische back-up van SQL inschakelt, kunt u de volgende instellingen configureren:

* De retentieperiode (dagen) voor back-ups
* Het opslagaccount dat voor back-ups moet worden gebruikt
* Versleutelingsoptie en wachtwoord voor back-ups
* Back-up maken van systeemdatabases
* Back-upschema configureren

Voor het versleutelen van de back-up klikt u op **Inschakelen**. Geef het **Wachtwoord** op. Azure maakt een certificaat voor het versleutelen van de back-ups en gebruikt het opgegeven wachtwoord om dit certificaat te beschermen. Standaard wordt de planning automatisch ingesteld, maar u een handmatigschema maken door **Handmatig te**selecteren. 

![SQL VM geautomatiseerde back-ups](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Zie voor meer informatie [Automatische back-up voor SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).


### <a name="machine-learning-services"></a>Machine Learning Services

U hebt de mogelijkheid om [Machine Learning Services](/sql/advanced-analytics/)in te schakelen. Met deze optie u machine learning gebruiken met Python en R in SQL Server 2017. Selecteer **Inschakelen** in het venster **SQL Server-instellingen.**


## <a name="4-review--create"></a>4. Review + maken

Controleer op het tabblad **Controleren + maken** de samenvatting en selecteer **Maken** om SQL Server, resourcegroep en resources te maken die voor deze vm zijn opgegeven.

U kunt de implementatie bewaken vanuit Azure Portal. Met de knop **Meldingen** boven aan het scherm kunt u de algemene status van de implementatie weergeven.

> [!NOTE]
> Om u een idee te geven van de implementatietijden, heb ik een virtuele SQL-machine voor de regio VS - oost geïmplementeerd met standaardinstellingen. Deze testimplementatie nam in totaal ongeveer 12 minuten in beslag. Afhankelijk van uw regio en de geselecteerde instellingen bent u mogelijk meer of minder tijd kwijt aan de implementatie.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a>De VM openen met Extern bureaublad

Doorloop de volgende stappen om via Extern bureaublad verbinding te maken met de virtuele SQL Server-machine:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Nadat u verbinding hebt gemaakt met de virtuele SQL Server-machine, kunt u SQL Server Management Studio starten en verbinding maken met Windows-verificatie met behulp van de lokale beheerdersreferenties. Als u SQL Server-verificatie inschakelt, kunt u ook verbinding maken met SQL-verificatie. Dit kan met behulp van de SQL-aanmeldingsnaam en het wachtwoord die u hebt geconfigureerd tijdens het inrichten.

Met toegang tot de machine kunt u rechtstreeks de instellingen voor de machine en de SQL Server wijzigen op basis van uw vereisten. U kunt bijvoorbeeld de firewallinstellingen configureren of de SQL Server-configuratie-instellingen wijzigen.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a>Verbinding maken met SQL Server op afstand

In deze walkthrough hebt u **Openbare** toegang voor de virtuele machine en **SQL Server-verificatie**geselecteerd. Door deze instellingen is de virtuele machine automatisch geconfigureerd voor het toestaan van SQL Server-verbindingen vanaf elke client via internet (ervan uitgaande dat deze beschikken over de juiste SQL-aanmeldgegevens).

> [!NOTE]
> Als u tijdens het inrichten niet de optie Openbaar hebt geselecteerd, kunt u de instellingen voor SQL-verbindingen achteraf wijzigen via de portal. Zie [Change your SQL connectivity settings](virtual-machines-windows-sql-connect.md#change) (SQL-verbindingsinstellingen wijzigen) voor meer informatie.

In de volgende secties ziet u hoe u via internet verbinding maken met uw SQL Server VM-exemplaar.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > In dit voorbeeld wordt gebruik gemaakte van de gemeenschappelijke poort 1433. Deze waarde moet echter worden gewijzigd als een andere poort (zoals 1401) is opgegeven tijdens de implementatie van de SQL Server VM. 


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van SQL Server in Azure [SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) en de [Veelgestelde vragen](virtual-machines-windows-sql-server-iaas-faq.md).
