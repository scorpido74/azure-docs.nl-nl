---
title: Oracle-oplossingen op virtuele Azure-machines | Microsoft Documenten
description: Meer informatie over ondersteunde configuraties en beperkingen van oracle-afbeeldingen voor virtuele machines op Microsoft Azure.
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
ms.openlocfilehash: 3abc09f8c82442e3b24a9edf6ef4fb42f19dfde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806946"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle VM-afbeeldingen en hun implementatie op Microsoft Azure

Dit artikel bevat informatie over Oracle-oplossingen op basis van afbeeldingen van virtuele machines die door Oracle zijn gepubliceerd in de Azure Marketplace. Als u geïnteresseerd bent in cross-cloudapplicatieoplossingen met Oracle Cloud Infrastructure, raadpleegt u [Oracle-toepassingsoplossingen die Microsoft Azure en Oracle Cloud Infrastructure integreren.](oracle-oci-overview.md)

Voer de volgende opdracht uit om een lijst met momenteel beschikbare afbeeldingen te krijgen:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Vanaf mei 2019 zijn de volgende afbeeldingen beschikbaar:

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

Deze afbeeldingen worden beschouwd als "Bring Your Own License" en als zodanig worden er alleen reken-, opslag- en netwerkkosten in rekening gebracht die worden gemaakt door het uitvoeren van een VM.  Er wordt aangenomen dat u een goede licentie hebt om Oracle-software te gebruiken en dat u een huidige ondersteuningsovereenkomst met Oracle hebt. Oracle heeft licentiemobiliteit gegarandeerd van on-premises naar Azure. Zie de gepubliceerde [Oracle en Microsoft-notitie](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) voor meer informatie over licentiemobiliteit. 

Individuen kunnen er ook voor kiezen om hun oplossingen te baseren op een aangepaste afbeelding die ze helemaal opnieuw maken in Azure of een aangepaste afbeelding uploaden vanuit hun on-premises omgeving.

## <a name="oracle-database-vm-images"></a>VM-afbeeldingen van Oracle-database
Oracle ondersteunt het uitvoeren van Oracle Database 12.1 en hogere Standard- en Enterprise-edities in Azure op afbeeldingen van virtuele machines op basis van Oracle Linux.  Voor de beste prestaties voor productieworkloads van Oracle Database op Azure moet u de VM-afbeelding op de juiste grootte geven en Premium SSD- of Ultra SSD-beheerde schijven gebruiken. [Probeer de Oracle Database Quickstart-walkthrough](oracle-database-quick-create.md)voor instructies over het snel operationeel maken van een Oracle Database in Azure met behulp van de door Oracle gepubliceerde VM-afbeelding.

### <a name="attached-disk-configuration-options"></a>Gekoppelde schijfconfiguratieopties

Gekoppelde schijven zijn afhankelijk van de Azure Blob-opslagservice. Elke standaardschijf is geschikt voor een theoretisch maximum van ongeveer 500 input/outputbewerkingen per seconde (IOPS). Ons premium schijfaanbod heeft de voorkeur voor krachtige databaseworkloads en kan tot 5000 IOps per schijf bereiken. U één schijf gebruiken als dat aan uw prestatiebehoeften voldoet. U echter de effectieve IOPS-prestaties verbeteren als u meerdere gekoppelde schijven gebruikt, databasegegevens over deze schijven verspreidt en vervolgens Oracle Automatic Storage Management (ASM) gebruikt. Zie [het overzicht van Oracle Automatic Storage](https://www.oracle.com/technetwork/database/index-100339.html) voor meer Oracle ASM-specifieke informatie. Zie de zelfstudie voor [het installeren en configureren van Oracle Automated Storage Management](configure-oracle-asm.md) voor een voorbeeld van het installeren en configureren van Oracle ASM op een Linux Azure VM.

### <a name="shared-storage-configuration-options"></a>Configuratieopties voor gedeelde opslag

Azure NetApp Files is ontworpen om te voldoen aan de kernvereisten van het uitvoeren van krachtige workloads zoals databases in de cloud en biedt;
- Azure native shared NFS-opslagservice voor het uitvoeren van Oracle-workloads via VM-native NFS-client of Oracle dNFS
- Schaalbare prestatieniveaus die aansluiten bij de echte iops-eisen
- Lage latentie
- Hoge beschikbaarheid, hoge duurzaamheid en beheerbaarheid op schaal, meestal gevraagd door bedrijfskritieke bedrijfsworkloads (zoals SAP en Oracle)
- Snelle en efficiënte back-up en herstel, om de meest agressieve RTO en RPO SLA's te bereiken

Deze mogelijkheden zijn mogelijk omdat Azure NetApp Files is gebaseerd op NetApp® ONTAP® all-flash-systemen die worden uitgevoerd binnen de Azure-datacenteromgeving – als Azure Native-service. Het resultaat is een ideale databaseopslagtechnologie die net als andere Azure-opslagopties kan worden ingericht en verbruikt. Zie [Azure NetApp Files-documentatie](https://docs.microsoft.com/azure/azure-netapp-files/) voor meer informatie over het implementeren en openen van NFS-volumes van Azure NetApp Files. Zie [Oracle op Azure Deployment Best Practice Guide Using Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf) for best practice recommendations for operating an Oracle database on Azure NetApp Files.


## <a name="licensing-oracle-database--software-on-azure"></a>Oracle Database & software op Azure licentieverlening
Microsoft Azure is een geautoriseerde cloudomgeving voor het uitvoeren van Oracle Database. De Oracle Core Factor-tabel is niet van toepassing bij het verlenen van licenties voor Oracle-databases in de cloud. Tel in plaats daarvan bij het gebruik van VM's met Hyper Threading Technology ingeschakeld voor Enterprise Edition-databases twee vCPU's als gelijkwaardig aan één Oracle Processor-licentie als hyperthreading is ingeschakeld (zoals in het beleidsdocument staat). De beleidsdetails vindt u [hier.](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)
Oracle-databases vereisen over het algemeen een hoger geheugen en IO. Daarom wordt [geheugengeoptimaliseerde VM's](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) aanbevolen voor deze workloads. Om uw workloads verder te optimaliseren, worden [beperkte Core-vCPU's](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu) aanbevolen voor Oracle Database-workloads waarvoor een hoge geheugen-, opslag- en I/O-bandbreedte nodig is, maar geen hoog aantal cores.

Wanneer Oracle-software en workloads worden gemigreerd van on-premises naar Microsoft Azure, biedt Oracle licentiemobiliteit zoals vermeld in de [Oracle on Azure FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle Real Application Cluster (Oracle RAC) is ontworpen om het falen van één knooppunt in een on-premises clusterconfiguratie met meerdere nodes te beperken. Het is gebaseerd op twee on-premises technologieën die niet afkomstig zijn van hyper-scale public cloud omgevingen: netwerk multi-cast en gedeelde schijf. Als voor uw databaseoplossing Oracle RAC in Azure is vereist, hebt u software van derden nodig om deze technologieën in te schakelen. Zie de [FlashGrid SkyCluster-pagina](https://www.flashgrid.io/oracle-rac-in-azure/)voor meer informatie over Oracle RAC.

## <a name="high-availability-and-disaster-recovery-considerations"></a>Overwegingen met hoge beschikbaarheid en noodherstel
Wanneer u Oracle-databases in Azure gebruikt, bent u verantwoordelijk voor de implementatie van een oplossing voor hoge beschikbaarheid en noodherstel om downtime te voorkomen. 

Hoge beschikbaarheid en noodherstel voor Oracle Database Enterprise Edition (zonder te vertrouwen op Oracle RAC) kan worden bereikt op Azure met behulp van [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)of Oracle [GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), met twee databases op twee afzonderlijke virtuele machines. Beide virtuele machines moeten zich in hetzelfde [virtuele netwerk bevinden](https://azure.microsoft.com/documentation/services/virtual-network/) om ervoor te zorgen dat ze toegang tot elkaar hebben via het privé-permanente IP-adres.  Daarnaast raden we aan om de virtuele machines in dezelfde beschikbaarheidsset te plaatsen, zodat Azure ze in afzonderlijke foutdomeinen kan plaatsen en domeinen upgraden. Als u georedundantie wilt hebben, stelt u de twee databases in om te repliceren tussen twee verschillende regio's en de twee instanties te verbinden met een VPN-gateway.

De zelfstudie [Implementeren Oracle Data Guard op Azure](configure-oracle-dataguard.md) leidt u door de basisinstallatieprocedure op Azure.  

Met Oracle Data Guard kan een hoge beschikbaarheid worden bereikt met een primaire database in één virtuele machine, een secundaire (stand-by)database in een andere virtuele machine en eenrichtingsreplicatie die ertussen is ingesteld. Het resultaat is leestoegang tot de kopie van de database. Met Oracle GoldenGate u bidirectionele replicatie tussen de twee databases configureren. Zie [Active Data Guard-](https://www.oracle.com/database/technologies/high-availability/dataguard.html) en [GoldenGate-documentatie](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) op de website van Oracle voor meer informatie over het instellen van een oplossing met hoge beschikbaarheid voor uw databases met behulp van deze tools. Als u lees-schrijftoegang nodig hebt tot de kopie van de database, u [Oracle Active Data Guard gebruiken.](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)

De zelfstudie [Implementeren Oracle GoldenGate op Azure](configure-oracle-golden-gate.md) leidt u door de basisinstallatieprocedure op Azure.

Naast het hebben van een HA- en DR-oplossing die is ontworpen in Azure, moet u ook een back-upstrategie hebben om uw database te herstellen. De [zelfstudie Back-up en herstel van een Oracle Database](oracle-backup-recovery.md) leidt u door de basisprocedure voor het opzetten van een consistente back-up.


## <a name="support-for-jd-edwards"></a>Ondersteuning voor JD Edwards
Volgens Oracle Support note [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)worden JD Edwards EnterpriseOne-versies 9.2 en `Minimum Technical Requirements` hoger ondersteund op elke openbare **cloudaanbieding** die voldoet aan hun specifieke (MTR).  U moet aangepaste afbeeldingen maken die voldoen aan hun MTR-specificaties voor compatibiliteit met os- en softwaretoepassingen. 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Afbeeldingen van virtuele machines van Oracle WebLogic Server

* **Clustering wordt alleen ondersteund op Enterprise Edition.** U hebt een licentie om WebLogic-clustering alleen te gebruiken wanneer u de Enterprise Edition van Oracle WebLogic Server gebruikt. Gebruik geen clustering met Oracle WebLogic Server Standard Edition.
* **UDP multicast wordt niet ondersteund.** Azure ondersteunt UDP unicasting, maar niet multicasting of broadcasting. Oracle WebLogic Server kan rekenen op Azure UDP unicast-mogelijkheden. Voor de beste resultaten die afhankelijk zijn van UDP unicast, raden we aan dat de weblogic-clustergrootte statisch wordt gehouden of wordt bewaard met niet meer dan 10 beheerde servers.
* **Oracle WebLogic Server verwacht dat openbare en private poorten hetzelfde zijn voor T3-toegang (bijvoorbeeld bij het gebruik van Enterprise JavaBeans).** Overweeg een scenario met meerdere lagen waarin een EJB-toepassing (Service Layer) wordt uitgevoerd op een Oracle WebLogic Server-cluster bestaande uit twee of meer VM's, in een virtueel netwerk met de naam *SLWLS.* De clientlaag bevindt zich in een ander subnet in hetzelfde virtuele netwerk en voert een eenvoudig Java-programma uit dat EJB in de servicelaag probeert te bellen. Omdat het noodzakelijk is om de balans van de servicelaag te laden, moet er een openbaar eindpunt voor de belasting worden gemaakt voor de virtuele machines in het Oracle WebLogic Server-cluster. Als de privépoort die u opgeeft, verschilt van de openbare poort (bijvoorbeeld 7006:7008), treedt een fout op zoals de volgende:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Oracle WebLogic Server verwacht namelijk dat voor elke externe T3-toegang de load balancer-poort en de door WebLogic beheerde serverpoort hetzelfde zullen zijn. In het voorgaande geval heeft de client toegang tot poort 7006 (de load balancer-poort) en luistert de beheerde server op 7008 (de privépoort). Deze beperking is alleen van toepassing op T3-toegang, niet voor HTTP.

   Gebruik een van de volgende tijdelijke oplossingen om dit probleem te voorkomen:

  * Gebruik dezelfde privé- en openbare poortnummers voor laadgebalanceerde eindpunten die zijn toegewezen aan T3-toegang.
  * Neem de volgende PARAMETER JVM op bij het starten van Oracle WebLogic Server:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Zie KB-artikel **860340.1** op <https://support.oracle.com>.

* **Dynamische clustering- en taakverdelingsbeperkingen.** Stel dat u een dynamisch cluster in Oracle WebLogic Server wilt gebruiken en deze wilt blootleggen via één openbaar eindpunt voor de belasting in Azure. Dit kan worden gedaan zolang u een vast poortnummer gebruikt voor elk van de beheerde servers (niet dynamisch toegewezen vanuit een bereik) en niet meer beheerde servers start dan er machines zijn die de beheerder bijhoudt. Dat wil zeggen, er is niet meer dan een beheerde server per virtuele machine). Als uw configuratie resulteert in meer Oracle WebLogic-servers die worden gestart dan virtuele machines (dat wil zeggen, waarbij meerdere Oracle WebLogic Server-exemplaren dezelfde virtuele machine delen), is het niet mogelijk voor meer dan één van die gevallen van Oracle WebLogic Servers te binden aan een bepaald poortnummer. De anderen op die virtuele machine falen.

   Als u de beheerserver configureert om automatisch unieke poortnummers toe te wijzen aan de beheerde servers, is taakverdeling niet mogelijk omdat Azure geen ondersteuning biedt voor toewijzing van één openbare poort naar meerdere privépoorten, zoals hiervoor nodig zou zijn Configuratie.
* **Meerdere exemplaren van Oracle WebLogic Server op een virtuele machine.** Afhankelijk van de vereisten van uw implementatie u overwegen meerdere exemplaren van Oracle WebLogic Server op dezelfde virtuele machine uit te voeren als de virtuele machine groot genoeg is. Op een virtuele machine van middelgrote grootte, die twee kernen bevat, u er bijvoorbeeld voor kiezen om twee exemplaren van Oracle WebLogic Server uit te voeren. We raden u echter nog steeds aan om geen enkele storingspunten in uw architectuur in te voeren, wat het geval zou zijn als u slechts één virtuele machine gebruikt die meerdere exemplaren van Oracle WebLogic Server uitvoert. Het gebruik van ten minste twee virtuele machines kan een betere aanpak zijn en elke virtuele machine kan vervolgens meerdere exemplaren van Oracle WebLogic Server uitvoeren. Elk exemplaar van Oracle WebLogic Server kan nog steeds deel uitmaken van hetzelfde cluster. Het is momenteel echter niet mogelijk om Azure te gebruiken voor load-balance endpoints die worden blootgesteld door dergelijke Oracle WebLogic Server-implementaties binnen dezelfde virtuele machine, omdat Azure load balancer vereist dat de load-balanced servers worden verdeeld over unieke virtuele machines.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK virtuele machine beelden
* **JDK 6 en 7 laatste updates.** Hoewel we het gebruik van de nieuwste openbare, ondersteunde versie van Java (momenteel Java 8) aanbevelen, maakt Azure ook JDK 6- en 7-afbeeldingen beschikbaar. Dit is bedoeld voor oudere toepassingen die nog niet klaar zijn om te worden geüpgraded naar JDK 8. Hoewel updates van eerdere JDK-afbeeldingen mogelijk niet meer beschikbaar zijn voor het grote publiek, zijn de JDK 6- en 7-afbeeldingen van Azure, gezien de Microsoft-samenwerking met Oracle, bedoeld om een recentere niet-openbare update te bevatten die normaal gesproken door Oracle wordt aangeboden aan slechts een selecte groep van de ondersteunde klanten van Oracle. Nieuwe versies van de JDK-afbeeldingen zullen na verloop van tijd beschikbaar worden gesteld met bijgewerkte versies van JDK 6 en 7.

   De JDK beschikbaar in de JDK 6 en 7 beelden, en de virtuele machines en afbeeldingen afgeleid van hen, kan alleen worden gebruikt binnen Azure.
* **64-bits JDK.** De virtuele machineafbeeldingen van Oracle WebLogic Server en de oracle JDK-afbeeldingen van de virtuele machine die door Azure worden geleverd, bevatten de 64-bits versies van zowel Windows Server als de JDK.

## <a name="next-steps"></a>Volgende stappen
U hebt nu een overzicht van de huidige Oracle-oplossingen op basis van afbeeldingen van virtuele machines in Microsoft Azure. Uw volgende stap is het implementeren van uw eerste Oracle-database op Azure.

> [!div class="nextstepaction"]
> [Een Oracle-database maken op Azure](oracle-database-quick-create.md)
