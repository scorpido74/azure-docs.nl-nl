---
title: SQL Server op Windows Virtual Machines in Azure FAQ | Microsoft Documenten
description: In dit artikel vindt u antwoorden op veelgestelde vragen over het uitvoeren van SQL Server op Azure VM's.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 0d6d69b82e80ff9bc33e49302cf59766b9c2e8d4
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270822"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Veelgestelde vragen over SQL Server op virtuele Windows-machines in Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over het uitvoeren van [SQL Server op Virtuele Windows-machines in Azure.](https://azure.microsoft.com/services/virtual-machines/sql-server/)

> [!NOTE]
> Dit artikel richt zich op problemen die specifiek zijn voor SQL Server op Windows VM's. Als u SQL Server op Linux VM's uitvoert, raadpleegt u de [Veelgestelde vragen over Linux.](../../linux/sql/sql-server-linux-faq.md)

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Afbeeldingen

1. **Welke SQL Server virtuele machine galerij afbeeldingen zijn beschikbaar?** 

   Azure onderhoudt virtuele machineafbeeldingen voor alle ondersteunde grote releases van SQL Server op alle edities voor zowel Windows als Linux. Zie voor meer informatie de volledige lijst met [Windows VM-afbeeldingen](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) en [Linux VM-afbeeldingen](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Worden bestaande SQL Server-virtuele machinegalerieafbeeldingen bijgewerkt?**

   Elke twee maanden worden SQL Server-afbeeldingen in de virtuele machinegalerij bijgewerkt met de nieuwste Windows- en Linux-updates. Voor Windows-afbeeldingen omvat dit alle updates die belangrijk zijn gemarkeerd in Windows Update, inclusief belangrijke SQL Server-beveiligingsupdates en servicepacks. Voor Linux-afbeeldingen bevat dit de nieuwste systeemupdates. SQL Server cumulatieve updates worden anders behandeld voor Linux en Windows. Voor Linux zijn ook cumulatieve SQL Server-updates opgenomen in de vernieuwing. Maar op dit moment worden Windows VM's niet bijgewerkt met SQL Server of Windows Server cumulatieve updates.

1. **Kunnen sql server-afbeeldingen met virtuele machines uit de galerie worden verwijderd?**

   Ja. Azure behoudt slechts één afbeelding per grote versie en editie. Wanneer bijvoorbeeld een nieuw SQL Server-servicepack wordt uitgebracht, voegt Azure een nieuwe afbeelding toe aan de galerie voor dat servicepack. De SQL Server-afbeelding voor het vorige servicepack wordt onmiddellijk verwijderd uit de Azure-portal. Het is echter nog steeds beschikbaar voor de levering van PowerShell voor de komende drie maanden. Na drie maanden is de vorige servicepackafbeelding niet meer beschikbaar. Dit verwijderingsbeleid is ook van toepassing als een SQL Server-versie niet wordt ondersteund wanneer deze het einde van de levenscyclus bereikt.


1. **Is het mogelijk om een oudere afbeelding van SQL Server te implementeren die niet zichtbaar is in de Azure-portal?**

   Ja, met PowerShell. Zie [SQL Server-virtuele machines inrichten met Azure PowerShell](virtual-machines-windows-ps-sql-create.md)voor meer informatie over het implementeren van SQL Server VM's met PowerShell.

1. **Hoe kan ik SQL Server op Azure VM generaliseren en gebruiken om nieuwe VM's te implementeren?**

   U een Windows Server VM implementeren (zonder dat er SQL Server op is geïnstalleerd) en het [SQL sysprep-proces](/sql/database-engine/install-windows/install-sql-server-using-sysprep?view=sql-server-ver15) gebruiken om SQL Server op Azure VM (Windows) te generaliseren met de SQL Server-installatiemedia. Klanten die [softwarezekerheid](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) hebben, kunnen hun installatiemedia verkrijgen bij het [Volume Licensing Center.](https://www.microsoft.com/Licensing/servicecenter/default.aspx) Klanten die geen softwarezekerheid hebben, kunnen de installatiemedia gebruiken van een Marketplace SQL Server VM-afbeelding met de gewenste editie.

   U ook een van de SQL Server-afbeeldingen gebruiken die Azure-marktplaats vormen om SQL Server op Azure VM te generaliseren. Houd er rekening mee dat u de volgende registersleutel in de bronafbeelding moet verwijderen voordat u uw eigen afbeelding maakt. Als u dit niet doet, kan dit ertoe leiden dat de map bootstrap en/of SQL IaaS-extensie in mislukte staat worden opgeblazen.

   Pad registersleutel:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > We raden u aan alle SQL Server Azure VM's, inclusief die die welke zijn geïmplementeerd op basis van aangepaste gegeneraliseerde afbeeldingen, te [registreren bij een SQL VM-redoprovider](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash) om te voldoen aan de nalevingsvereisten en optionele functies te gebruiken, zoals geautomatiseerde patching en automatische back-ups. Hiermee u ook [het licentietype](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-ahb?tabs=azure-portal) voor elke SQL Server VM opgeven.

1. **Kan ik mijn eigen VHD gebruiken om een SQL Server VM te implementeren?**

   Ja, maar u moet vervolgens [elke SQL Server VM registreren bij de SQL Server VM-resourceprovider](virtual-machines-windows-sql-register-with-resource-provider.md) om uw SQL Server VM in de portal te beheren en functies zoals geautomatiseerde patching en automatische back-ups gebruiken.

1. **Is het mogelijk om configuraties in te stellen die niet in de virtuele machinegalerij worden weergegeven (bijvoorbeeld Windows 2008 R2 + SQL Server 2012)?**

   Nee. Voor virtuele machinegalerieafbeeldingen die SQL Server bevatten, moet u een van de meegeleverde afbeeldingen selecteren via de Azure-portal of via [PowerShell.](virtual-machines-windows-ps-sql-create.md) U hebt echter de mogelijkheid om een Windows VM te implementeren en SQL Server zelf te installeren. U moet vervolgens [uw SQL Server VM registreren bij de SQL Server VM-resourceprovider](virtual-machines-windows-sql-register-with-resource-provider.md) om uw SQL Server VM in de portal te beheren, evenals functies gebruiken zoals geautomatiseerd patchen en automatische back-ups. 


## <a name="creation"></a>Maken

1. **Hoe maak ik een virtuele Azure-machine met SQL Server?**

   De eenvoudigste methode is het maken van een virtuele machine die SQL Server bevat. Zie Een virtuele sql server inde [Azure-portal inrichten](virtual-machines-windows-portal-sql-server-provision.md)voor een zelfstudie over het aanmelden voor Azure en het maken van een SQL Server VM vanuit de portal. U een virtuele machineafbeelding selecteren die gebruikmaakt van PAY-per-Second SQL Server-licenties, of u een afbeelding gebruiken waarmee u uw eigen SQL Server-licentie meenemen. U hebt ook de mogelijkheid om SQL Server handmatig te installeren op een VM met een gratis gelicentieerde editie (Developer of Express) of door een on-premises licentie opnieuw te gebruiken. Zorg ervoor dat [u uw SQL Server VM registreert bij de SQL Server VM-resourceprovider](virtual-machines-windows-sql-register-with-resource-provider.md) om uw SQL Server VM in de portal te beheren, en gebruik functies zoals geautomatiseerd patchen en automatische back-ups. Als u uw eigen licentie meeneemt, moet u [license mobility via Software Assurance op Azure](https://azure.microsoft.com/pricing/license-mobility/)hebben. Zie [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prijsrichtlijnen voor SQL Server Azure VM's) voor meer informatie.

1. **Hoe kan ik mijn on-premises SQL Server-database migreren naar de cloud?**

   Maak eerst een Virtuele Azure-machine met een SQL Server-exemplaar. Migreer vervolgens uw on-premises databases naar die instantie. Zie Een SQL [Server-database migreren naar SQL Server in een Azure VM](virtual-machines-windows-migrate-sql.md)voor gegevensmigratiestrategieën.

## <a name="licensing"></a>Licentieverlening

1. **Hoe kan ik mijn gelicentieerd exemplaar van SQL Server installeren op een Azure-VM?**

   Er zijn drie manieren om dit te doen. Als u een enterprise agreement (EA) klant bent, u een van de [virtuele machineafbeeldingen inrichten die licenties ondersteunt,](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)die ook wel bring-your-own-license (BYOL) wordt genoemd. Als u [softwarezekerheid hebt,](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)u het [Azure Hybrid Benefit](virtual-machines-windows-sql-ahb.md) inschakelen op een bestaande PAY-as-you-go -afbeelding (PAYG). U de SQL Server-installatiemedia kopiëren naar een Windows Server-vm en SQL Server vervolgens op de VM installeren. Zorg ervoor dat u uw SQL Server VM registreert bij de [resourceprovider](virtual-machines-windows-sql-register-with-resource-provider.md) voor functies zoals portalbeheer, geautomatiseerde back-up en geautomatiseerd patchen. 

1. **Kan ik een VM wijzigen zodat mijn eigen SQL Server-licentie wordt gebruikt, wanneer de VM is gemaakt vanuit een van de Betalen per gebruik-installatiekopieën uit de galerie?**

   Ja. U eenvoudig een pay-as-you-go (PAYG) galerijafbeelding overschakelen naar bring-your-own-license (BYOL) door het [Azure Hybrid Benefit in](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)te schakelen.  Zie [Het licentiemodel voor een SQL Server VM wijzigen voor](virtual-machines-windows-sql-ahb.md)meer informatie. Momenteel is deze faciliteit alleen beschikbaar voor Public Cloud-klanten.

1. **Treedt er downtime op voor SQL Server tijdens het schakelen tussen licentiemodellen?**

   Nee. [Het wijzigen van het licentiemodel](virtual-machines-windows-sql-ahb.md) vereist geen downtime voor SQL Server, omdat de wijziging onmiddellijk van kracht is en geen herstart van de VM vereist. Om uw SQL Server VM echter te registreren bij de SQL Server VM-resourceprovider, is de [SQL IaaS-extensie](virtual-machines-windows-sql-server-agent-extension.md) een vereiste en wordt de SQL Server-extensie in de _volledige_ modus opnieuw gestart. Als de SQL IaaS-extensie moet worden geïnstalleerd, installeert u deze dan in _de lichtgewicht_ modus voor beperkte functionaliteit of installeert u deze in _de volledige_ modus tijdens een onderhoudsvenster. De SQL IaaS-extensie die in _de lichtgewicht_ modus is geïnstalleerd, kan op elk gewenst moment worden geüpgraded naar _de volledige_ modus, maar vereist een herstart van de SQL Server-service. 
   
1. **Is het mogelijk om van licentiemodel te wisselen op een SQL Server VM die is geïmplementeerd met behulp van een klassiek model?**

   Nee. Het wijzigen van het licentiemodel wordt niet ondersteund op een klassieke vm. U uw VM migreren naar het Azure Resource Manager-model en zich registreren bij de SQL Server VM-resourceprovider. Zodra de VM is geregistreerd bij de SQL Server VM-resourceprovider, zijn wijzigingen in licentiemodellen beschikbaar op de VM.

1. **Kan ik de Azure-portal gebruiken om meerdere exemplaren op dezelfde vm te beheren?**

   Nee. Portalbeheer is een functie die wordt geleverd door de SQL Server VM-resourceprovider, die is gebaseerd op de SQL Server IaaS-agent-extensie. Als zodanig gelden voor de resourceprovider dezelfde beperkingen als voor de extensie. De portal kan slechts één standaardinstantie of één benoemde instantie beheren, zolang deze correct is geconfigureerd. Zie [SQL Server IaaS-agentextensie](virtual-machines-windows-sql-server-agent-extension.md)voor meer informatie over deze beperkingen. 

1. **Kunnen CSP-abonnementen het Azure Hybrid Benefit activeren?**

   Ja, het Azure Hybrid Benefit is beschikbaar voor CSP-abonnementen. CSP-klanten moeten eerst een pay-as-you-go-afbeelding implementeren en vervolgens [het licentiemodel wijzigen](virtual-machines-windows-sql-ahb.md) in bring-your-own-license.
   
 
1. **Moet ik betalen om een SQL-server een licentie te verlenen op een Azure-VM, als deze SQL-server alleen wordt gebruikt als stand-by of om een failover uit te voeren?**

   Als u een gratis passieve licentie wilt hebben voor een standaard secundaire beschikbaarheidsgroep of failoverclusteringsinstantie, moet u voldoen aan alle volgende criteria zoals beschreven in de [productlicentievoorwaarden:](https://www.microsoft.com/licensing/product-licensing/products)

   1. U beschikt [over licentiemobiliteit](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) via [Software Assurance.](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) 
   1. De passieve SQL Server-instantie levert geen SQL Server-gegevens aan clients of voert actieve SQL Server-workloads uit. Het wordt alleen gebruikt om te synchroniseren met de primaire server en anders de passieve database in een warme stand-by status te onderhouden. Als er gegevens worden weergegeven, zoals rapporten aan clients met actieve SQL Server-workloads of andere werkzaamheden uitvoeren dan wat in de productvoorwaarden is opgegeven, moet het een exemplaar met een betaalde licentie SQL Server zijn. De volgende activiteit is toegestaan in de secundaire instantie: databaseconsistentiecontroles of CheckDB, volledige back-ups, back-ups van transactielogboeken en monitoring van gegevens over resourcegebruik. U de primaire en bijbehorende instantie voor noodherstel ook gelijktijdig uitvoeren voor korte perioden van noodhersteltests om de 90 dagen. 
   1. De actieve SQL Server-licentie valt onder Software Assurance en biedt **slechts één** passieve secundaire SQL Server-instantie, met maximaal dezelfde hoeveelheid rekenkracht als de gelicentieerde actieve server. 
   1. De secundaire SQL Server VM maakt gebruik van de [Disaster Recovery-licentie](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) in de Azure-portal.
   
1. **Wat wordt beschouwd als een passieve instantie?**

   De passieve SQL Server-instantie levert geen SQL Server-gegevens aan clients of voert actieve SQL Server-workloads uit. Het wordt alleen gebruikt om te synchroniseren met de primaire server en anders de passieve database in een warme stand-by status te onderhouden. Als er gegevens worden weergegeven, zoals rapporten aan clients met actieve SQL Server-workloads of andere werkzaamheden uitvoeren dan wat in de productvoorwaarden is opgegeven, moet het een exemplaar met een betaalde licentie SQL Server zijn. De volgende activiteit is toegestaan in de secundaire instantie: databaseconsistentiecontroles of CheckDB, volledige back-ups, back-ups van transactielogboeken en monitoring van gegevens over resourcegebruik. U de primaire en bijbehorende instantie voor noodherstel ook gelijktijdig uitvoeren voor korte perioden van noodhersteltests om de 90 dagen.
   

1. **Welke scenario's kunnen gebruik maken van het VOORDEEL Disaster Recovery (DR).**

   De [licentiegids](https://aka.ms/sql2019licenseguide) biedt scenario's waarin het disaster recovery benefit kan worden gebruikt. Raadpleeg uw productvoorwaarden en praat met uw licentiecontactpersonen of accountmanager voor meer informatie.

1. **Welke abonnementen ondersteunen het Disaster Recovery (DR)-voordeel?**

   Uitgebreide programma's die Software Assurance gelijkwaardige abonnementsrechten bieden als vast voordeel ondersteunen het DR-voordeel. Dit omvat. maar is niet beperkt tot, de Open Value (OV), Open Value Subscription (OVS), Enterprise Agreement (EA), Enterprise Subscription Agreement (EAS) en de Server and Cloud Enrollment (SCE). Raadpleeg de [productvoorwaarden](https://www.microsoft.com/licensing/product-licensing/products) en praat met uw licentiecontactpersonen of accountmanager voor meer informatie. 

   
 ## <a name="resource-provider"></a>Resourceprovider

1. **Brengt het registreren van mijn VM bij de nieuwe SQL Server VM-resourceprovider extra kosten met zich mee?**

   Nee. De SQL Server VM-bronprovider maakt alleen extra beheerbaarheid mogelijk voor SQL Server op Azure VM zonder extra kosten. 

1. **Is de SQL Server VM-resourceprovider beschikbaar voor alle klanten?**
 
   Ja, zolang de SQL Server VM is geïmplementeerd in de openbare cloud met behulp van het Resource Manager-model en niet het klassieke model. Alle andere klanten kunnen zich registreren bij de nieuwe SQL Server VM-resourceprovider. Alleen klanten met het [Software Assurance-voordeel](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) kunnen echter hun eigen licentie gebruiken door het [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) op een SQL Server VM te activeren. 

1. **Wat gebeurt er met de resourceprovider _(Microsoft.SqlVirtualMachine)_ resource als de VM-bron wordt verplaatst of verwijderd?** 

   Wanneer de Microsoft.Compute/VirtualMachine-bron wordt verwijderd of verplaatst, wordt de bijbehorende Microsoft.SqlVirtualMachine-bron op de hoogte gesteld om de bewerking asynchroon te repliceren.

1. **Wat gebeurt er met de VM als de resourceprovider _(Microsoft.SqlVirtualMachine)-bron_wordt verwijderd?**

    De Microsoft.Compute/VirtualMachine-bron wordt niet beïnvloed wanneer de Microsoft.SqlVirtualMachine-bron wordt verwijderd. De licentiewijzigingen worden echter standaard teruggezet naar de oorspronkelijke afbeeldingsbron. 

1. **Is het mogelijk om zelf geïmplementeerde SQL Server VM's te registreren bij de SQL Server VM-resourceprovider?**

    Ja. Als u SQL Server vanuit uw eigen media hebt geïmplementeerd en de SQL IaaS-extensie hebt geïnstalleerd, u uw SQL Server VM registreren bij de resourceprovider om de beheervoordelen van de SQL IaaS-extensie te krijgen. U een zelf geïmplementeerde SQL Server VM echter niet converteren naar betalen per gebruik.


   


## <a name="administration"></a>Beheer

1. **Kan ik een tweede exemplaar van SQL Server op dezelfde VM installeren? Kan ik de geïnstalleerde functies van de standaardinstantie wijzigen?**

   Ja. De SQL Server-installatiemedia bevinden zich in een map op het **C-station.** Voer **Setup.exe** vanaf die locatie uit om nieuwe SQL Server-exemplaren toe te voegen of om andere geïnstalleerde functies van SQL Server op de machine te wijzigen. Houd er rekening mee dat sommige functies, zoals Geautomatiseerde back-up, geautomatiseerde patching en Azure Key Vault Integration, alleen werken tegen de standaardinstantie of een benoemde instantie die correct is geconfigureerd (Zie vraag 3). 

1. **Kan ik het standaardexemplaar van SQL Server verwijderen?**

   Ja, maar er is een aantal overwegingen. Ten eerste kan SQL Server-gerelateerde facturering blijven plaatsvinden, afhankelijk van het licentiemodel voor de VM. Ten tweede, zoals vermeld in het vorige antwoord, zijn er functies die afhankelijk zijn van de [SQL Server IaaS Agent Extension.](virtual-machines-windows-sql-server-agent-extension.md) Als u de standaardinstantie verwijdert zonder de IaaS-extensie ook te verwijderen, blijft de extensie zoeken naar de standaardinstantie en kan er fouten in het gebeurtenislogboek worden gegenereerd. Deze fouten zijn afkomstig van de volgende twee bronnen: **Microsoft SQL Server Credential Management** en Microsoft SQL Server **IaaS Agent**. Een van de fouten lijkt mogelijk op de volgende:

      Een netwerkgerelateerde of exemplaarspecifieke fout is opgetreden bij het maken van een verbinding met SQL Server. De server wordt niet gevonden of toegang tot de server is niet mogelijk.

   Als u besluit de standaardinstantie te verwijderen, verwijdert u ook de [SQL Server IaaS-agentextensie.](virtual-machines-windows-sql-server-agent-extension.md) 

1. **Kan ik een benoemde instantie van SQL Server gebruiken met de IaaS-extensie?**
   
   Ja, als de benoemde instantie de enige instantie op de SQL Server is en als de oorspronkelijke standaardinstantie correct is [verwijderd.](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance) Als er geen standaardinstantie is en er meerdere benoemde exemplaren op één SQL Server VM zijn, wordt de SQL Server IaaS-agentextensie niet geïnstalleerd. 

1. **Kan ik SQL Server volledig verwijderen van een SQL Server VM?**

   Ja, maar er worden nog steeds kosten in rekening gebracht voor uw SQL Server VM, zoals beschreven in [prijsrichtlijnen voor SQL Server Azure VM's.](virtual-machines-windows-sql-server-pricing-guidance.md) Als u de SQL Server niet langer nodig hebt, kunt u een nieuwe virtuele machine implementeren en de gegevens en toepassingen migreren naar de nieuwe virtuele machine. Vervolgens kunt u de virtuele SQL Server-machine verwijderen.
   
## <a name="updating-and-patching"></a>Bijwerken en patchen

1. **Hoe kan ik overschakelen naar een andere versie of editie van de SQL Server in een Azure-VM?**

   Klanten kunnen hun versie of editie van SQL Server wijzigen door gebruik te maken van een installatiemedium met de gewenste versie of editie van SQL Server. Zodra de versie of editie is gewijzigd, gebruikt u de Azure-portal om de versie- of editie-eigenschap van de VM te wijzigen zodat deze nauwkeurig de facturering voor de VM aangeeft. Zie [Versie van een SQL Server VM wijzigen](virtual-machines-windows-sql-change-edition.md)voor meer informatie. Er is geen factureringsverschil voor verschillende versies van SQL Server, dus zodra de versie van SQL Server is gewijzigd, is er geen verdere actie nodig.

1. **Waar kan ik de installatiemedia krijgen om de editie of versie van SQL Server te wijzigen?**

   Klanten die [softwarezekerheid](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) hebben, kunnen hun installatiemedia verkrijgen bij het [Volume Licensing Center.](https://www.microsoft.com/Licensing/servicecenter/default.aspx) Klanten die geen softwarezekerheid hebben, kunnen de installatiemedia gebruiken van een Marketplace SQL Server VM-afbeelding met de gewenste editie.
   
1. **Hoe worden updates en servicepacks toegepast op een SQL Server VM?**

   virtuele machines geven u controle over de hostmachine, inclusief wanneer en hoe u updates wilt toepassen. Voor het besturingssysteem u handmatig windows-updates toepassen of een planningsservice inschakelen met de naam [Automated Patching.](virtual-machines-windows-sql-automated-patching.md) Automated Patching installeert alle updates die als belangrijk zijn aangeduid, met inbegrip van SQL Server-updates in deze categorie. Overige optionele updates voor SQL Server moeten handmatig worden geïnstalleerd.

1. **Kan ik mijn SQL Server 2008/ 2008 R2-exemplaar upgraden nadat ik deze heb geregistreerd bij de SQL Server VM-resourceprovider?**

   Ja. U alle installatiemedia gebruiken om de versie en editie van SQL Server te upgraden, en vervolgens u uw [SQL IaaS-extensiemodus](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)upgraden ) van _geen agent_ naar _volledig_. Hierdoor krijgt u toegang tot alle voordelen van de SQL IaaS-extensie, zoals beheerbaarheid van portals, geautomatiseerde back-ups en geautomatiseerd patchen. 

1. **Hoe krijg ik gratis uitgebreide beveiligingsupdates voor mijn ondersteuning SQL Server 2008 en SQL Server 2008 R2-exemplaren?**

   U [gratis uitgebreide beveiligingsupdates](virtual-machines-windows-sql-server-2008-eos-extend-support.md) ontvangen door uw SQL Server as-is naar een Azure SQL-virtuele machine te verplaatsen. Zie [opties voor het einde van de ondersteuning voor](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)meer informatie . 
  
   

## <a name="general"></a>Algemeen

1. **Worden SQL Server failoverclusterexemplaren (FCI) ondersteund op Azure VM's?**

   Ja. U een failoverclusterinstantie installeren met behulp van [premium bestandsshares (PFS)](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) of [opslagruimten direct (S2D)](virtual-machines-windows-portal-sql-create-failover-cluster.md) voor het subsysteem voor opslag. Premium bestandsshares bieden IOPS en doorvoercapaciteiten die voldoen aan de behoeften van veel workloads. Voor IO-intensieve workloads u overwegen om opslagruimten direct te gebruiken op basis van gemanged premium of ultra-schijven. U ook clustering- of opslagoplossingen van derden gebruiken zoals beschreven in [hoge beschikbaarheid en noodherstel voor SQL Server in Azure Virtual Machines.](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)

   > [!IMPORTANT]
   > Op dit moment wordt de _volledige_ [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) niet ondersteund voor SQL Server FCI op Azure. We raden u aan de _volledige_ extensie van VM's die deelnemen aan de FCI te verwijderen en de extensie in plaats daarvan in _de lichtgewicht_ modus te installeren. Deze extensie ondersteunt functies, zoals Automated Backup en Patching en enkele portalfuncties voor SQL Server. Deze functies werken niet voor SQL Server VM's nadat de _volledige_ agent is verwijderd.

1. **Wat is het verschil tussen SQL Server VM's en de SQL Database-service?**

   Conceptueel is sql server op een virtuele Azure-machine niet zo verschillend van het uitvoeren van SQL Server in een extern datacenter. [SQL Database](../../../sql-database/sql-database-technical-overview.md) biedt daarentegen database-as-a-service. Met SQL Database hebt u geen toegang tot de machines die uw databases hosten. Zie Een cloud [SQL Server-optie kiezen: Azure SQL (PaaS) Database of SQL Server op Azure VM's (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)voor een volledige vergelijking.

1. **Hoe installeer ik SQL Data-hulpprogramma's op mijn Azure VM?**

    Download en installeer de SQL Data-tools van [Microsoft SQL Server Data Tools - Business Intelligence voor Visual Studio 2013.](https://www.microsoft.com/download/details.aspx?id=42313)

1. **Worden gedistribueerde transacties met MSDTC ondersteund op SQL Server VM's?**
   
    Ja. Lokale DTC wordt ondersteund voor SQL Server 2016 SP2 en meer. Toepassingen moeten echter worden getest bij het gebruik van Always On-beschikbaarheidsgroepen, omdat transacties tijdens de vlucht tijdens een failover mislukken en opnieuw moeten worden geprobeerd. Geclusterde DTC is beschikbaar vanaf Windows Server 2019. 

## <a name="resources"></a>Resources

**Windows VM's:**

* [Overzicht van SQL Server op een Windows VM](virtual-machines-windows-sql-server-iaas-overview.md).
* [Een SQL Server Windows VM inrichten](virtual-machines-windows-portal-sql-server-provision.md)
* [Een database migreren naar SQL Server op een Azure VM](virtual-machines-windows-migrate-sql.md)
* [Hoge beschikbaarheid en noodherstel voor SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Aanbevolen procedures voor prestaties voor SQL Server op virtuele machines van Azure](virtual-machines-windows-sql-performance.md)
* [Toepassingspatronen en ontwikkelingsstrategieën voor SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM's:**

* [Overzicht van SQL Server op een Linux VM](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Een SQL Server Linux VM inrichten](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Veelgestelde vragen (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentatie voor SQL Server op Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
