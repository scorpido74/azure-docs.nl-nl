---
title: SAP HANA-beschikbaarheid binnen één Azure-regio | Microsoft Documenten
description: Beschrijft SAP HANA-bewerkingen op Azure native VM's in één Azure-regio.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ef7161e653ec582708f242b67c643d960d75e27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255463"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA-beschikbaarheid binnen één Azure-regio
In dit artikel worden verschillende beschikbaarheidsscenario's binnen één Azure-regio beschreven. Azure heeft vele regio's, verspreid over de hele wereld. Zie [Azure-regio's](https://azure.microsoft.com/regions/)voor de lijst met Azure-regio's . Voor het implementeren van SAP HANA op VM's binnen één Azure-regio biedt Microsoft implementatie van één VM met een HANA-exemplaar. Voor een grotere beschikbaarheid u twee VM's met twee HANA-exemplaren implementeren binnen een [Azure-beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) die HANA-systeemreplicatie gebruikt voor beschikbaarheid. 

Momenteel biedt Azure [Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview)aan. In dit artikel worden beschikbaarheidszones niet in detail beschreven. Het bevat echter een algemene discussie over het gebruik van beschikbaarheidssets versus beschikbaarheidszones.

Azure-regio's waar beschikbaarheidszones worden aangeboden, hebben meerdere datacenters. De datacenters zijn onafhankelijk in de levering van stroombron, koeling en netwerk. De reden voor het aanbieden van verschillende zones binnen één Azure-regio is het implementeren van toepassingen in twee of drie beschikbaarheidszones die worden aangeboden. Als u alle zones, problemen in stroom en netwerken implementeert die slechts betrekking hebben op één Azure Availability Zone-infrastructuur, is uw toepassingsimplementatie binnen een Azure-regio nog steeds functioneel. Er kan enige verminderde capaciteit optreden. Vm's in één zone kunnen bijvoorbeeld verloren gaan, maar VM's in de andere twee zones zijn nog steeds actief. 
 
Een Azure Availability Set is een logische groeperingsmogelijkheid waarmee u ervoor zorgt dat de VM-resources die u binnen de beschikbaarheidsset plaatst, van elkaar worden geïsoleerd wanneer ze worden geïmplementeerd in een Azure-datacenter. Azure zorgt ervoor dat de VM's die u in een beschikbaarheidsset plaatst, op meerdere fysieke servers, rekenrekken, opslageenheden en netwerkswitches worden uitgevoerd. In sommige Azure-documentatie wordt deze configuratie aangeduid als plaatsingen in verschillende [update- en foutdomeinen.](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) Deze plaatsingen bevinden zich meestal in een Azure-datacenter. Ervan uitgaande dat stroombron- en netwerkproblemen van invloed zijn op het datacenter dat u implementeert, wordt al uw capaciteit in één Azure-regio beïnvloed.

De plaatsing van datacenters die Azure Availability Zones vertegenwoordigen, is een compromis tussen het leveren van aanvaardbare netwerklatentie tussen services die in verschillende zones worden geïmplementeerd en een afstand tussen datacenters. Natuurrampen zouden idealiter geen invloed hebben op de stroomvoorziening, de netwerkvoorziening en de infrastructuur voor alle beschikbaarheidszones in deze regio. Zoals echter monumentale natuurrampen hebben aangetoond, bieden beschikbaarheidszones mogelijk niet altijd de beschikbaarheid die u binnen één regio wilt. Denk aan orkaan Maria die het eiland Puerto Rico op 20 september 2017 trof. De orkaan veroorzaakte een bijna 100 procent black-out op het 90-mijl-brede eiland.

## <a name="single-vm-scenario"></a>Scenario met één VM

In een enkel VM-scenario maakt u een Azure VM voor het SAP HANA-exemplaar. U gebruikt Azure Premium Storage om de schijf van het besturingssysteem en al uw gegevensschijven te hosten. De Azure-uptime SLA van 99,9 procent en de SLA's van andere Azure-componenten zijn voldoende om uw beschikbaarheidsSLA's voor uw klanten te vervullen. In dit scenario hoeft u geen gebruik te maken van een Azure Availability Set voor VM's waarop de DBMS-laag wordt uitgevoerd. In dit scenario vertrouwt u op twee verschillende functies:

- Azure VM automatisch opnieuw opstarten (ook wel Azure-servicehealing genoemd)
- SAP HANA automatisch opnieuw opstarten

Azure VM automatisch opnieuw opstarten, of service healing, is een functionaliteit in Azure die werkt op twee niveaus:

- De Azure-serverhost controleert de status van een VM die op de serverhost wordt gehost.
- De Azure-structuurcontroller controleert de status en beschikbaarheid van de serverhost.

Een statuscontrolefunctionaliteit bewaakt de status van elke VM die wordt gehost op een Azure-serverhost. Als een VM in een niet-gezonde status valt, kan een reboot van de VM worden gestart door de Azure-hostagent die de status van de VM controleert. De fabriccontroller controleert de status van de host door veel verschillende parameters te controleren die problemen met de hosthardware kunnen aangeven. Het controleert ook op de toegankelijkheid van de host via het netwerk. Een indicatie van problemen met de host kan leiden tot de volgende gebeurtenissen:

- Als de host een slechte status signaleert, wordt een herstart van de host en een herstart van de VM's die op de host werden uitgevoerd, geactiveerd.
- Als de host niet in een gezonde toestand is na een succesvolle reboot, wordt een herschikking van de VM's gestart die oorspronkelijk op het nu ongezonde knooppunt op een gezonde hostserver stonden. In dit geval is de oorspronkelijke host gemarkeerd als niet gezond. Het zal niet worden gebruikt voor verdere implementaties totdat het is gewist of vervangen.
- Als de ongezonde host problemen heeft tijdens het herstartproces, wordt een onmiddellijke herstart van de VM's op een gezonde host geactiveerd. 

Met de host- en VM-monitoring van Azure worden Azure VM's die hostproblemen ervaren, automatisch opnieuw gestart op een gezonde Azure-host. 

>[!IMPORTANT]
>Azure service healing zal niet opnieuw Linux VM's waar de gast OS is in een kernel paniek staat. De standaardinstellingen van de veelgebruikte Linux-releases worden niet automatisch opnieuw gestart met VM's of server waar de Linux-kernel in paniek staat. In plaats daarvan voorziet de standaard om het besturingssysteem in kernel paniekstatus te houden om een kernel debugger te kunnen koppelen om te analyseren. Azure eert dat gedrag door een VM niet automatisch opnieuw op te starten met het gastbesturingssysteem in een dergelijke status. Veronderstelling is dat dergelijke gebeurtenissen uiterst zeldzaam zijn. U het standaardgedrag overschrijven om een herstart van de vm in te schakelen. Als u het standaardgedrag wilt wijzigen, schakelt u de parameter 'kernel.panic' in /etc/sysctl.conf in. De tijd die u voor deze parameter instelt, is binnen enkele seconden. Aanbevolen waarden zijn 20-30 seconden te wachten voordat de herstart via deze parameter wordt geactiveerd. Zie <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>ook .

De tweede functie waarop u in dit scenario vertrouwt, is het feit dat de HANA-service die wordt uitgevoerd in een opnieuw gestarte VM automatisch wordt gestart nadat de VM opnieuw is opgestart. U [hana service auto-herstart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) via de waakhond diensten van de verschillende HANA diensten.

U dit single-VM-scenario verbeteren door een koud failoverknooppunt toe te voegen aan een SAP HANA-configuratie. In de SAP HANA-documentatie wordt deze instelling [host auto-failover](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html)genoemd. Deze configuratie kan zinvol zijn in een on-premises implementatiesituatie waarin de serverhardware beperkt is en u een knooppunt voor één server wijdt als het host-auto-failover-knooppunt voor een set productiehosts. Maar in Azure, waar de onderliggende infrastructuur van Azure een gezonde doelserver biedt voor een succesvolle vm-herstart, heeft het geen zin om SAP HANA-host automatisch failover te implementeren. Vanwege Azure-servicehealing is er geen referentiearchitectuur die een stand-by-knooppunt voor HANA-hostauto-failover voorziet. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Speciaal geval van SAP HANA scale-out configuraties in Azure
Hoge beschikbaarheid voor SAP HANA scale-out configuraties is afhankelijk van service healing van Azure VM's en de herstart van de SAP HANA instantie als de VM is up and running again. Hoge beschikbaarheid architecturen op basis van HANA System Replication zullen worden geïntroduceerd op een later tijdstip. 


## <a name="availability-scenarios-for-two-different-vms"></a>Beschikbaarheidsscenario's voor twee verschillende VM's

Als u twee Azure VM's gebruikt binnen een Azure Availability Set, u de uptime tussen deze twee VM's verhogen als ze in een Azure Availability Set binnen één Azure-regio worden geplaatst. De basisinstelling in Azure ziet eruit als volgt:

![Diagram van twee VM's met alle lagen](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Om de verschillende beschikbaarheidsscenario's te illustreren, worden enkele lagen in het diagram weggelaten. Het diagram toont alleen lagen met VM's, hosts, Beschikbaarheidssets en Azure-gebieden. Azure Virtual Network-exemplaren, resourcegroepen en abonnementen spelen geen rol in de scenario's die in deze sectie worden beschreven.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Back-ups repliceren naar een tweede virtuele machine

Een van de meest rudimentaire opstellingen is het gebruik van back-ups. In het bijzonder u transactielogboekback-ups laten verzenden van de ene VM naar een andere Azure VM. U het azure-opslagtype kiezen. In deze installatie bent u verantwoordelijk voor het scripten van de kopie van geplande back-ups die worden uitgevoerd op de eerste VM naar de tweede VM. Als u de tweede VM-exemplaren moet gebruiken, moet u de back-ups voor volledige, incrementele/differentieelen en transactielogboeken herstellen tot het punt dat u nodig hebt. 

De architectuur ziet eruit als:

![Diagram van twee VM's met opslagreplicatie](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Deze setup is niet goed geschikt voor het bereiken van grote Recovery Point Objective (RPO) en Recovery Time Objective (RTO) tijden. RTO tijden vooral zou lijden als gevolg van de noodzaak om volledig te herstellen van de volledige database met behulp van de gekopieerde back-ups. Deze instelling is echter handig voor het herstellen van onbedoelde verwijdering van gegevens op de belangrijkste instanties. Met deze instelling u op elk gewenst moment herstellen naar een bepaald tijdstip, de gegevens extraheren en de verwijderde gegevens importeren in uw hoofdinstantie. Daarom is het misschien zinvol om een back-up methode te gebruiken in combinatie met andere functionaliteit met hoge beschikbaarheid. 

Terwijl back-ups worden gekopieerd, u mogelijk een kleinere vm gebruiken dan de hoofdvm waarop de SAP HANA-instantie wordt uitgevoerd. Houd er rekening mee dat u een kleiner aantal VHD's koppelen aan kleinere VM's. Zie [Grootte voor virtuele Linux-machines in Azure voor](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)informatie over de limieten van afzonderlijke VM-typen.

### <a name="sap-hana-system-replication-without-automatic-failover"></a>SAP HANA-systeemreplicatie zonder automatische failover

De scenario's die in deze sectie worden beschreven, maken gebruik van SAP HANA-systeemreplicatie. Zie [Systeemreplicatie](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)voor de SAP-documentatie . Scenario's zonder automatische failover zijn niet gebruikelijk voor configuraties binnen één Azure-gebied. Een configuratie zonder automatische failover, hoewel het vermijden van een Pacemaker setup, verplicht u om te controleren en failover handmatig. Aangezien dit ook inspanningen en inspanningen vergt, vertrouwen de meeste klanten in plaats daarvan op Azure-servicehealing. Er zijn enkele randgevallen waarin deze configuratie kan helpen in termen van foutscenario's. Of, in sommige gevallen, een klant zou willen meer efficiëntie te realiseren.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>SAP HANA-systeemreplicatie zonder automatische failover en zonder gegevensvoorladen

In dit scenario gebruikt u SAP HANA-systeemreplicatie om gegevens synchroon te verplaatsen om een RPO van 0 te bereiken. Aan de andere kant heb je een lang genoeg RTO dat je niet nodig hebt, hetzij failover of gegevens preloading in de HANA instantie cache. In dit geval is het mogelijk om verdere economie in uw configuratie te bereiken door de volgende acties te ondernemen:

- Voer een andere SAP HANA-instantie uit in de tweede VM. De SAP HANA-instantie in de tweede VM neemt het grootste deel van het geheugen van de virtuele machine. In het geval van een failover naar de tweede VM, moet u de lopende SAP HANA-instantie met de gegevens volledig geladen in de tweede VM afsluiten, zodat de gerepliceerde gegevens kunnen worden geladen in de cache van de beoogde HANA-instantie in de tweede VM.
- Gebruik een kleinere VM-grootte op de tweede vm. Als er een failover optreedt, hebt u een extra stap voor de handmatige failover. In deze stap wijzigt u het formaat van de vm op de grootte van de bron-vm. 
 
Het scenario ziet eruit als volgt:

![Diagram van twee VM's met opslagreplicatie](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Zelfs als u geen vooraf laden van gegevens gebruikt in het HANA-systeemreplicatiedoel, hebt u ten minste 64 GB geheugen nodig. Je hebt naast 64 GB ook voldoende geheugen nodig om de rowstore-gegevens in het geheugen van de doelinstantie te bewaren.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>SAP HANA-systeemreplicatie zonder automatische failover en met gegevensvoorladen

In dit scenario worden gegevens die in de tweede VM naar de HANA-instantie worden gerepliceerd, vooraf geladen. Dit elimineert de twee voordelen van het niet vooraf laden van gegevens. In dit geval u geen ander SAP HANA-systeem op de tweede VM uitvoeren. U ook geen kleiner VM-formaat gebruiken. Vandaar dat klanten dit scenario zelden implementeren.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>SAP HANA-systeemreplicatie met automatische failover

In de standaard configuratie van de beschikbaarheid binnen één Azure-regio hebben twee Azure VM's met SLES Linux een failovercluster gedefinieerd. Het SLES Linux cluster is gebaseerd op het [Pacemaker](/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) framework, in combinatie met een [STONITH](/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) apparaat. 

Vanuit SAP HANA-perspectief wordt de gebruikte replicatiemodus gesynchroniseerd en wordt een automatische failover geconfigureerd. In de tweede VM fungeert het SAP HANA-exemplaar als een hot standby-knooppunt. Het stand-byknooppunt ontvangt een synchrone stroom van wijzigingsrecords van de primaire SAP HANA-instantie. Aangezien transacties worden gepleegd door de toepassing op het primaire knooppunt van de HANA, wacht het primaire HANA-knooppunt om de commit aan de toepassing te bevestigen totdat het secundaire SAP HANA-knooppunt bevestigt dat het de commit-record heeft ontvangen. SAP HANA biedt twee synchrone replicatiemodi. Zie de [SAP-artikel Replicatiemodi voor SAP HANA-systeemreplicatie voor](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html)meer informatie en voor een beschrijving van de verschillen tussen deze twee synchrone replicatiemodi.

De algehele configuratie ziet eruit als volgt:

![Diagram van twee VM's met opslagreplicatie en failover](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

U deze oplossing kiezen omdat u hiermee een RPO=0 en een lage RTO bereiken. Configureer de SAP HANA-clientconnectiviteit zodat de SAP HANA-clients het virtuele IP-adres gebruiken om verbinding te maken met de HANA-systeemreplicatieconfiguratie. Een dergelijke configuratie elimineert de noodzaak om de toepassing opnieuw te configureren als er een failover optreedt naar het secundaire knooppunt. In dit scenario moeten de Azure VM SKU's voor de primaire en secundaire VM's hetzelfde zijn.

## <a name="next-steps"></a>Volgende stappen

Zie voor stapsgewijze richtlijnen voor het instellen van deze configuraties in Azure:

- [SAP HANA-systeemreplicatie instellen in Azure VM's](sap-hana-high-availability.md)
- [Hoge beschikbaarheid voor SAP HANA door gebruik te maken van systeemreplicatie](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Zie voor meer informatie over de beschikbaarheid van SAP HANA in Azure-regio's:

- [BESCHIKBAARHEID VAN SAP HANA in Azure-regio's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

