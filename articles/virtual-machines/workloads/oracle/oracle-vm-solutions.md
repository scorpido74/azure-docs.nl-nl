---
title: Oracle-oplossingen op Azure virtual machines | Microsoft Docs
description: Meer informatie over ondersteunde configuraties en beperkingen van installatie kopieën van virtuele Oracle-machines op Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 4480819a08ef9a7a4ad7257f75a94c5d10a3d312
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858573"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle-VM-installatie kopieën en hun implementatie op Microsoft Azure

In dit artikel vindt u informatie over Oracle-oplossingen op basis van installatie kopieën van virtuele machines die zijn gepubliceerd door Oracle in azure Marketplace. Als u geïnteresseerd bent in cross-Cloud toepassings oplossingen met Oracle Cloud Infrastructure, raadpleegt u [Oracle Application solutions Microsoft Azure en Oracle Cloud Infrastructure](oracle-oci-overview.md)(Engelstalig).

Voer de volgende opdracht uit om een lijst met momenteel beschik bare installatie kopieën weer te geven:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Vanaf mei 2019 zijn de volgende installatie kopieën beschikbaar:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Deze installatie kopieën worden beschouwd als ' uw eigen licentie nemen ' en als zodanig worden er alleen kosten in rekening gebracht voor berekening, opslag en netwerken die worden gemaakt door een virtuele machine uit te voeren.  Er wordt van uitgegaan dat u over een juiste licentie beschikt om Oracle-software te gebruiken en dat er een actuele ondersteunings overeenkomst is met Oracle. Oracle heeft de mobiliteit van licenties van on-premises naar Azure gegarandeerd. Raadpleeg de gepubliceerde [Oracle en micro soft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) Note voor meer informatie over License Mobility. 

Gebruikers kunnen er ook voor kiezen om hun oplossingen te baseren op een aangepaste installatie kopie die ze helemaal zelf maken in azure of een aangepaste installatie kopie uploaden vanuit hun on-premises omgeving.

## <a name="oracle-database-vm-images"></a>VM-installatie kopieën van Oracle data base
Oracle biedt ondersteuning voor het uitvoeren van Oracle DB 12,1 en hogere Standard-en Enter prise-edities in azure op installatie kopieën van virtuele machines op basis van Oracle Linux.  Voor de beste prestaties voor productie werkbelastingen van Oracle DB op Azure, moet u de grootte van de VM-installatie kopie op de juiste wijze aanpassen en Premium-SSD of Ultra-SSD Managed Disks gebruiken. Meer informatie over hoe u snel een Oracle DB in azure kunt krijgen met behulp van de Oracle-installatie kopie van het gepubliceerde virtuele systeem, [kunt u Oracle DB Snelstartgids](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opties voor de schijf configuratie zijn gekoppeld

Gekoppelde schijven zijn afhankelijk van de Azure Blob Storage-service. Elke standaard schijf kan een theoretisch maximum van ongeveer 500 invoer/uitvoer-bewerkingen per seconde (IOPS) zijn. Onze Premium Disk-aanbieding verdient de voor keur voor hoogwaardige data bases en kan Maxi maal 5000 IOps per schijf belasten. U kunt één schijf gebruiken als deze voldoet aan de prestatie behoeften. U kunt de daad werkelijke IOPS-prestaties echter verbeteren als u meerdere gekoppelde schijven gebruikt, database gegevens verspreid over hen en vervolgens Oracle Automatic Storage Management (ASM) gebruikt. Zie [overzicht van automatische opslag van Oracle](https://www.oracle.com/technetwork/database/index-100339.html) voor meer informatie over Oracle asm. Zie voor een voor beeld van het installeren en configureren van Oracle ASM op een Linux Azure-VM de zelf studie [Oracle Automated Storage Management installeren en configureren](configure-oracle-asm.md) .

### <a name="shared-storage-configuration-options"></a>Configuratie opties voor gedeelde opslag

Azure NetApp Files is ontworpen om te voldoen aan de belangrijkste vereisten voor het uitvoeren van hoogwaardige workloads zoals data bases in de Cloud, en biedt;
- De Azure native gedeelde NFS-opslag service voor het uitvoeren van Oracle-workloads via de native NFS-client van de VM of Oracle dNFS
- Schaal bare prestatie lagen die het werkelijke bereik van IOPS-aanvragen weer spie gelen
- Lage latentie
- Hoge Beschik baarheid, hoge duurzaamheid en beheer baarheid op schaal, meestal vereist door bedrijfs werkbelastingen die essentieel zijn voor bedrijven (zoals SAP en Oracle)
- Snelle en efficiënte back-up en herstel om de meest agressieve RTO-en RPO-SLA te halen

Deze mogelijkheden zijn mogelijk omdat Azure NetApp Files is gebaseerd op NetApp® ONTAP® alle-Flash systemen die worden uitgevoerd in een Azure Data Center-omgeving, als een systeem eigen Azure-service. Het resultaat is een ideale database opslag technologie die net als andere opties voor Azure Storage kan worden ingericht en verbruikt. Zie [Azure NetApp files-documentatie](https://docs.microsoft.com/azure/azure-netapp-files/) voor meer informatie over het implementeren en gebruiken van Azure NetApp files NFS-volumes. Zie de [Best practices-hand leiding voor Oracle in azure met behulp van Azure NetApp files](https://www.netapp.com/us/media/tr-4780.pdf) voor Best Practice aanbevelingen voor het werken met een Oracle-data base op Azure NetApp files.


## <a name="licensing-oracle-database--software-on-azure"></a>Licentie Oracle Database & software op Azure
Microsoft Azure is een geautoriseerde cloud omgeving voor het uitvoeren van Oracle Database. De Oracle core factor Table is niet van toepassing op Oracle-data bases in de Cloud. Bij het gebruik van Vm's met Hyper-Threading-technologie die is ingeschakeld voor Enter prise Edition-data bases, telt u twee Vcpu's als gelijkwaardig aan één Oracle-processor licentie als hyperthreading is ingeschakeld (zoals vermeld in het beleids document). De details van het beleid vindt u [hier](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf).
Oracle-data bases vereisen over het algemeen meer geheugen en IO. Daarom worden [Vm's geoptimaliseerd voor geheugen](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes-memory) aanbevolen voor deze werk belastingen. Om uw workloads verder te optimaliseren, worden [beperkte kern vcpu's](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/constrained-vcpu) aanbevolen voor Oracle DB workloads die hoge geheugen, opslag ruimte en I/O-band breedte nodig hebben, maar geen hoog aantal kernen.

Wanneer Oracle-software en-workloads van on-premises naar Microsoft Azure worden gemigreerd, biedt Oracle licentie mobiliteit zoals vermeld in de [Veelgestelde vragen over Oracle op Azure](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle RAC is ontworpen om het mislukken van één knoop punt in een on-premises cluster configuratie met meerdere knoop punten te verminderen. Het is afhankelijk van twee on-premises technologieën die niet standaard zijn voor het maken van open bare Cloud omgevingen: netwerk multi cast en gedeelde schijven. Als uw database oplossing Oracle RAC vereist in azure, hebt u de software van derden nodig om deze technologieën in te scha kelen. Zie de [pagina FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)voor meer informatie over Oracle RAC.

## <a name="high-availability-and-disaster-recovery-considerations"></a>Overwegingen voor hoge Beschik baarheid en herstel na nood gevallen
Wanneer u Oracle-data bases in azure gebruikt, bent u verantwoordelijk voor het implementeren van een oplossing voor hoge Beschik baarheid en herstel na nood gevallen om uitval tijd te voor komen. 

Hoge Beschik baarheid en herstel na nood gevallen voor Oracle Database Enterprise Edition (zonder vertrouwen op Oracle RAC) kunnen worden bereikt op Azure met behulp van [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)of [Oracle Golden Gate](https://www.oracle.com/technetwork/middleware/goldengate), met twee data bases op twee afzonderlijke virtuele apparaten. Beide virtuele machines moeten zich in hetzelfde [virtuele netwerk](https://azure.microsoft.com/documentation/services/virtual-network/) bevinden om ervoor te zorgen dat ze toegang hebben tot elkaar via het permanente IP-adres van de privé.  Daarnaast is het raadzaam om de virtuele machines in dezelfde beschikbaarheidsset te plaatsen zodat Azure deze kan plaatsen in afzonderlijke fout domeinen en upgrade domeinen. Als u geo-redundantie wilt instellen, stelt u de twee data bases in voor replicatie tussen twee verschillende regio's en verbindt u de twee exemplaren met een VPN Gateway.

Met de zelf studie [implementeert u Oracle Data Guard in azure](configure-oracle-dataguard.md) met behulp van de basis installatie procedure van Azure.  

Met Oracle Data Guard kan hoge Beschik baarheid worden bereikt met een primaire data base in één virtuele machine, een secundaire (standby)-data base in een andere virtuele machine en een eenrichtings replicatie tussen deze. Het resultaat is lees toegang tot de kopie van de data base. Met Oracle Golden Gate kunt u bidirectionele replicatie tussen de twee data bases configureren. Zie de documentatie over [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) en [Golden Gate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) op de Oracle-website voor meer informatie over het instellen van een oplossing voor hoge Beschik baarheid voor uw data bases met behulp van deze hulpprogram ma's. Als u lees-/schrijftoegang tot de kopie van de data base nodig hebt, kunt u [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)gebruiken.

In de zelf studie wordt [Oracle Golden Gate in azure geïmplementeerd](configure-oracle-golden-gate.md) , wordt u begeleid bij de basis installatie procedure van Azure.

Naast een oplossing van HA en DR in azure, moet u een back-upstrategie hebben om uw data base te herstellen. In de zelf studie [back-up en herstellen van een Oracle database](oracle-backup-recovery.md) wordt u begeleid bij de basis procedure voor het instellen van een consistente back-up.


## <a name="support-for-jd-edwards"></a>Ondersteuning voor JD Edwards
Volgens Oracle-ondersteunings nota [doc id 2178595,1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), JD Edwards EnterpriseOne versie 9,2 en hoger, worden ondersteund in **een open bare Cloud** die `Minimum Technical Requirements` voldoet aan hun specifieke (MTR).  U moet aangepaste installatie kopieën maken die voldoen aan de MTR-specificaties voor de compatibiliteit van besturings systemen en software toepassingen. 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Installatie kopieën van virtuele Oracle WebLogic-Server

* **Clustering wordt alleen ondersteund in de Enter prise-editie.** U hebt een licentie voor het gebruik van WebLogic-clustering alleen wanneer u de Enter prise-editie van WebLogic Server gebruikt. Gebruik clustering niet met WebLogic Server Standard Edition.
* **UDP-multi cast wordt niet ondersteund.** Azure ondersteunt UDP-Unicasting, maar geen multi Casting of Broadcasting. WebLogic-Server kan gebruikmaken van de mogelijkheden van Azure UDP-unicast. Voor de beste resultaten die gebruikmaken van UDP-unicast, wordt aangeraden de grootte van het WebLogic-cluster statisch te houden of te blijven werken met niet meer dan 10 beheerde servers.
* **WebLogic-Server verwacht dat open bare en particuliere poorten hetzelfde zijn voor T3-toegang (bijvoorbeeld wanneer u Enter prise JavaBeans gebruikt).** Overweeg een scenario met meerdere lagen waarbij een EJB-toepassing wordt uitgevoerd op een WebLogic-Server cluster dat bestaat uit twee of meer virtuele machines, in een virtueel netwerk met de naam *SLWLS*. De laag van de client bevindt zich in een ander subnet in hetzelfde virtuele netwerk, met een eenvoudig Java-programma dat probeert EJB aan te roepen in de service laag. Omdat het nodig is om de service laag te verdelen, moet een openbaar eind punt met gelijke taak verdeling worden gemaakt voor de virtuele machines in het WebLogic-Server cluster. Als de particuliere poort die u opgeeft, niet overeenkomt met de open bare poort (bijvoorbeeld 7006:7008), treedt er een fout op, zoals de volgende:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Dit komt doordat voor een externe T3-toegang WebLogic-Server de load balancer poort en de WebLogic beheerde server poort hetzelfde moeten hebben. In het bovenstaande geval opent de client poort 7006 (de load balancer poort) en luistert de beheerde server naar 7008 (de particuliere poort). Deze beperking is alleen van toepassing op T3-toegang, niet op HTTP.

   Als u dit probleem wilt voor komen, gebruikt u een van de volgende tijdelijke oplossingen:

  * Gebruik dezelfde persoonlijke en open bare poort nummers voor eind punten met gelijke taak verdeling die specifiek zijn voor T3-toegang.
  * Neem de volgende JVM-para meter op bij het starten van de WebLogic-Server:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Zie KB-artikel **860340,1** op <https://support.oracle.com>voor meer informatie.

* **Beperkingen voor dynamische clustering en taak verdeling.** Stel dat u een dynamisch cluster wilt gebruiken in de WebLogic-Server en dit beschikbaar wilt maken via één openbaar eind punt met gelijke taak verdeling in Azure. U kunt dit doen als u een vast poort nummer gebruikt voor elk van de beheerde servers (niet dynamisch toegewezen vanuit een bereik) en niet meer beheerde servers start dan de computers die door de beheerder worden bijgehouden. Dat wil zeggen dat er niet meer dan één beheerde server per virtuele machine is. Als uw configuratie resulteert in meer WebLogic-servers die worden gestart dan er virtuele machines zijn (dat wil zeggen, waarbij meerdere WebLogic-Server instanties dezelfde virtuele machine delen), is het niet mogelijk om meer dan een van de exemplaren van WebLogic-servers te gebruiken binden aan een opgegeven poort nummer. De andere op die virtuele machine mislukken.

   Als u de-beheer server zodanig configureert dat er automatisch unieke poort nummers worden toegewezen aan de beheerde servers, is taak verdeling niet mogelijk omdat Azure geen toewijzing van een enkele open bare poort aan meerdere persoonlijke poorten ondersteunt, zoals vereist voor dit configuratie.
* **Meerdere exemplaren van de WebLogic-Server op een virtuele machine.** Afhankelijk van de vereisten van uw implementatie kunt u overwegen om meerdere exemplaren van WebLogic-Server op dezelfde virtuele machine uit te voeren als de virtuele machine groot genoeg is. U kunt bijvoorbeeld op een virtuele machine met een gemiddelde grootte, die twee kernen bevat, kiezen om twee exemplaren van de WebLogic-Server uit te voeren. We raden u echter aan om afzonderlijke punten van storingen in uw architectuur te voor komen. Dit is bijvoorbeeld het geval als u slechts één virtuele machine met meerdere exemplaren van WebLogic Server hebt gebruikt. Het gebruik van ten minste twee virtuele machines kan een betere benadering zijn en elke virtuele machine kan vervolgens meerdere exemplaren van de WebLogic-Server uitvoeren. Elk exemplaar van de WebLogic-Server kan nog steeds deel uitmaken van hetzelfde cluster. Het is momenteel niet mogelijk om Azure te gebruiken voor het laden van eind punten die worden weer gegeven door dergelijke WebLogic-Server implementaties binnen dezelfde virtuele machine, omdat Azure load balancer vereist dat de servers met gelijke taak verdeling worden verdeeld over unieke virtuele machines.

## <a name="oracle-jdk-virtual-machine-images"></a>Installatie kopieën voor virtuele Oracle JDK-machines
* **JDK 6 en 7 nieuwste updates.** We raden u aan om de meest recente, ondersteunde versie van Java (momenteel Java 8) te gebruiken, maar er zijn ook JDK 6-en 7-installatie kopieën beschikbaar. Dit is bedoeld voor oudere toepassingen die nog niet gereed zijn om te worden bijgewerkt naar JDK 8. Hoewel updates voor eerdere JDK-installatie kopieën mogelijk niet meer beschikbaar zijn voor het grote publiek, gezien de micro soft-samen werking met Oracle, zijn de installatie kopieën van JDK 6 en 7 van Azure bedoeld voor een meer recente niet-open bare update die normaal gesp roken wordt aangeboden door Oracle naar alleen een SELECT-groep van Oracle-ondersteunde klanten. Nieuwe versies van de JDK-installatie kopieën worden na verloop van tijd beschikbaar gemaakt met bijgewerkte releases van JDK 6 en 7.

   De JDK die beschikbaar zijn in de installatie kopieën van JDK 6 en 7 en de virtuele machines en installatie kopieën die hiervan zijn afgeleid, kunnen alleen worden gebruikt in Azure.
* **64-bits JDK.** De installatie kopieën van de virtuele machine van Oracle WebLogic Server en de installatie kopieën van de virtuele machine van Oracle JDK die door Azure worden meegeleverd, bevatten de 64-bits versies van zowel Windows Server als de JDK.

## <a name="next-steps"></a>Volgende stappen
U hebt nu een overzicht van de huidige Oracle-oplossingen op basis van installatie kopieën van virtuele machines in Microsoft Azure. De volgende stap is het implementeren van uw eerste Oracle-data base in Azure.

> [!div class="nextstepaction"]
> [Een Oracle-data base maken in azure](oracle-database-quick-create.md)
