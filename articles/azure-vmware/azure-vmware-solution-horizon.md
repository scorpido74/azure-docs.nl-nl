---
title: Distributie op Azure VMware-oplossing implementeren
description: Meer informatie over het implementeren van VMware horizon op de Azure VMware-oplossing.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 60207b0ed9e1df805ac667752b55f14a693ec25c
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91492472"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Distributie op Azure VMware-oplossing implementeren 

>[!NOTE]
>Dit document is gericht op het VMware horizon-product. Dit is vroeger bekend als Horizony 7 voordat de product naam verandert van Horizon 7 naar horizon. Horizon is een andere oplossing dan een horizon-Cloud op Azure, hoewel er een aantal gedeelde onderdelen zijn. De belangrijkste voor delen van de Azure VMware-oplossing zijn een eenvoudigere schaal methode en het beheer van VMware Cloud Foundation is geïntegreerd in de Azure Portal.

[VMware Horizon](https://www.vmware.com/products/horizon.html)® is een virtueel bureau blad-en toepassings platform dat wordt uitgevoerd in het Data Center en biedt eenvoudig en gecentraliseerd beheer. Het levert virtuele Bureau bladen en toepassingen aan eind gebruikers op elk apparaat, waar dan ook. Met horizon kunt u verbindingen maken en brokeren met virtuele Windows-Bureau bladen, virtuele Linux-Bureau bladen, door Extern bureaublad server (RDS) gehoste toepassingen, Desk tops en fysieke machines.

Hier Best Eden we aandacht aan het implementeren van Horizon in azure VMware-oplossing. Voor algemene informatie over VMware horizon raadpleegt u de horizon productie documentatie:

* [Wat is VMware horizon?](https://www.vmware.com/products/horizon.html)

* [Meer informatie over VMware horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Referentie architectuur van Horizon](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Met de inleiding over de Azure VMware-oplossing zijn er nu twee VDI-oplossingen (Virtual Desktop Infrastructure) op het Azure-platform. In het volgende diagram vindt u een overzicht van de belangrijkste verschillen op hoog niveau.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="De verschillen tussen de periode van de Azure VMware-oplossing en de horizon-Cloud op Azure" border="false":::

Horizon 2006 en latere versies op de regel van de horizon-versie 8 bieden ondersteuning voor zowel on-premises implementatie als implementatie van Azure VMware-oplossingen. Er zijn een paar van de functies die on-premises maar niet in de Azure VMware-oplossing worden ondersteund. Aanvullende producten in het Horizon-ecosysteem worden ook ondersteund. Zie [pariteit en interoperabiliteit van functies](https://kb.vmware.com/s/article/80850)voor voor meer informatie.

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Een periode implementeren in een hybride Cloud

U kunt horizon in een hybride cloud omgeving implementeren wanneer u pod-architectuur (CPA) van de Cloud gebruikt om on-premises data centers en Azure-data centers te verbinden. CPA wordt doorgaans gebruikt om uw implementatie te verg Roten, een hybride Cloud te bouwen en redundantie te bieden voor bedrijfs continuïteit en herstel na nood gevallen. Zie voor een uitgebreide bespreking van de richt lijnen voor de continuïteit van VMware-horizon, [bestaande horizon-7-omgevingen uitbreiden](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CPA is geen uitgerekte implementatie. elke horizon Pod is DISTINCT en alle verbindings servers die deel uitmaken van elk van de afzonderlijke peulen, moeten zich op één locatie bevinden en op hetzelfde broadcast-domein worden uitgevoerd vanuit een netwerk perspectief.

Net zoals on-premises of persoonlijk Data Center, kan horizon worden geïmplementeerd in een privécloud van Azure VMware-oplossingen. In de volgende secties worden de belangrijkste verschillen besproken in de implementatie van Horizon-premises en de Azure VMware-oplossing.

De privécloud van Azure is conceptueel hetzelfde als de VMware-SDDC, een term die meestal in de horizon-documentatie wordt gebruikt. In de rest van dit document worden de voor waarden van de Azure Private Cloud en de VMware SDDC-uitwisselbaar gebruikt.

De horizon-Cloud connector is vereist voor Horizon-Azure VMware-oplossing voor het beheren van abonnements licenties. Cloud connector kan worden geïmplementeerd in azure Virtual Network naast de andere horizon-verbinding servers.

>[!IMPORTANT]
>De ondersteuning voor Horizon besturings vlak voor Horizon op Azure VMware-oplossing is nog niet beschikbaar. Zorg ervoor dat u de VHD-versie van de horizon-Cloud Connector downloadt.

## <a name="vcenter-cloud-admin-role"></a>rol van vCenter-Cloud beheerder

Omdat de Azure VMware-oplossing een SDDC-service is en Azure de levens cyclus van de SDDC op de Azure VMware-oplossing beheert, wordt het vCenter-machtigings model voor de Azure VMware-oplossing beperkt door het ontwerp.

Klanten moeten de rol van Cloud beheerder gebruiken, die een beperkt aantal vCenter-machtigingen heeft. Het product is gewijzigd in samen werking met de rol van Cloud beheerder in de Azure VMware-oplossing, met name:

* Het inrichten van Instant klonen is gewijzigd om te worden uitgevoerd op de Azure VMware-oplossing.

* Er is een specifiek vSAN-beleid (VMware_Horizon) gemaakt op de Azure VMware-oplossing om met de horizon te werken. deze moet beschikbaar zijn en worden gebruikt in de SDDCs die voor de horizon worden geïmplementeerd.

* vSphere Lees cache (CBRC), ook wel bekend als weer geven, is uitgeschakeld wanneer het wordt uitgevoerd op de Azure VMware-oplossing.

>[!IMPORTANT]
>CBRC mag niet opnieuw worden ingeschakeld.

>[!NOTE]
>Met Azure VMware-oplossing worden specifieke instellingen voor de horizon automatisch geconfigureerd zolang u horizon 2006 (ook wel horizon 8) en hoger op de vertakking van de horizon-8 implementeert en selecteert u de optie **Azure** in het installatie programma van de horizon-verbinding server.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Distributie architectuur van de Azure VMware-oplossing

Een typische horizon architectuur ontwerp maakt gebruik van een pod-en blokkerings strategie. Een blok is één vCenter, terwijl meerdere blokken gecombineerd een pod maken. Een horizon-Pod is een organisatie-eenheid, bepaald door de limieten voor Horizon taal schalen. Elke horizon-Pod heeft een afzonderlijke beheer Portal, en daarom is een standaard ontwerp procedure het aantal peulen te minimaliseren.

Elke Cloud heeft een eigen schema voor netwerk connectiviteit. In combi natie met VMware SDDC Networking/NSX Edge biedt de Azure VMware-oplossing voor netwerk connectiviteit unieke vereisten voor het implementeren van Horizon land dat verschilt van on-premises.

Elke Azure Private Cloud/SDDC is geschikt voor het afhandelen van 4.000-bureau blad-of app-sessies, waarbij het volgende van toepassing is:

* Het werk belasting verkeer wordt uitgelijnd met dat van het LoginVSI-taak werknemers profiel.

* Alleen protocol verkeer wordt beschouwd, geen gebruikers gegevens.

* NSX Edge is geconfigureerd om groot te zijn.

>[!NOTE]
>Uw werkbelasting profiel en de behoeften kunnen afwijken en daarom kunnen de resultaten variëren op basis van uw use-case. Volumes met gebruikers gegevens kunnen de schaal limieten in de context van uw werk belasting verlagen. Grootte en plan uw implementatie dienovereenkomstig. Zie de richt lijnen voor het aanpassen van de [grootte van Azure VMware Solution-hosts voor Horizon-implementaties](#size-azure-vmware-solution-hosts-for-horizon-deployments) voor meer informatie.

Gezien de maximum limiet voor Azure Private Cloud/SDDC, raden we u aan een implementatie architectuur te gebruiken waarin de horizon-verbindings servers en VMware Unified Access gateways (UAGs) worden uitgevoerd binnen de Azure-Virtual Network. Hierdoor worden alle Azure Private Cloud-SDDC in een blok omgezet. Op die manier maximaliseert u de schaal baarheid van de horizon op de Azure VMware-oplossing.

De verbinding van Azure Virtual Network met Azure private clouds/SDDCs moet worden geconfigureerd met ExpressPath Fast Path. In het volgende diagram ziet u een eenvoudige implementatie van Horizon pod.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="De verschillen tussen de periode van de Azure VMware-oplossing en de horizon-Cloud op Azure" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Netwerk connectiviteit voor schaal horizon op Azure VMware-oplossing

In deze sectie wordt de netwerk architectuur op hoog niveau voor het schalen van Horizon taal op Azure VMware-oplossingen beschreven met enkele veelvoorkomende implementatie voorbeelden. De focus is hier specifiek op essentiële netwerk elementen.

### <a name="single-horizon-pd-on-azure-vmware-solution"></a>Eén horizon-pd op Azure VMware-oplossing

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="De verschillen tussen de periode van de Azure VMware-oplossing en de horizon-Cloud op Azure" border="false":::

Eén horizon Pod is het meest rechtse implementatie scenario. In dit voor beeld kiest u dat u slechts één Horizon pod in de regio VS Oost wilt implementeren. Omdat elke privécloud/SDDC wordt geschat om ongeveer het verkeer van 4.000-bureaublad sessies te verwerken, kunt u de implementatie van Maxi maal drie privé Clouds/SDDCs plannen.

Daarom kunt u in dit voor beeld, in combi natie met de virtuele machines van de horizon infrastructuur (Vm's) die zijn geïmplementeerd in azure Virtual Network, de 12.000-sessies per horizon-pod bereiken op basis van uw werk belasting en gegevens behoeften. De verbinding tussen elke privécloud en SDDC met Azure Virtual Network is ExpressRoute snel pad, zodat er geen Oost-West-verkeer tussen persoonlijke Clouds nodig is.

Voor beelden van belang rijke hypo Thesen voor dit basis implementatie zijn:

* U hebt geen on-premises horizon pod die u wilt verbinden met deze nieuwe pod met behulp van Cloud pod Architecture (CPA).

* Eind gebruikers maken verbinding met hun virtuele Bureau bladen via internet (versus verbinding maken via een on-premises Data Center).

In dit eenvoudige voor beeld kunt u uw AD-domein controller in azure Virtual Network koppelen met uw on-premises Active Directory via VPN of een ExpressRoute-circuit.

Een variant in het basis voorbeeld dat wordt besproken, is mogelijk om connectiviteit te ondersteunen voor on-premises resources. Dit kunnen gebruikers zijn die toegang hebben tot Desk tops en virtueel-bureaublad toepassings verkeer genereren of verbinding maken met een on-premises horizon pod met behulp van CPA.

In het onderstaande diagram ziet u hoe u dit kunt doen.Als u uw bedrijfs netwerk wilt verbinden met de Azure-Virtual Network, hebt u een ExpressRoute nodig.U moet ook een verbinding maken tussen uw bedrijfs netwerk en elk van de privécloud-SDDCs met behulp van Global Reach, waarmee de verbinding van de SDDC met de ExpressRoute-en on-premises resources mogelijk is.

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="De verschillen tussen de periode van de Azure VMware-oplossing en de horizon-Cloud op Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Meerdere horizon-peulen voor Azure VMware-oplossing in meerdere regio's

Voor een ander horizon pod-voor beeld bekijken we een voor beeld waarin het schalen van de schaal over meerdere peulen wordt weer gegeven.In dit voor beeld implementeert u twee verschillende regio's en federeren ze met behulp van CPA.De netwerk configuratie is vergelijkbaar met het vorige voor beeld, met een aantal aanvullende cross-regionale koppelingen. 

U moet de Azure-Virtual Network in elke regio verbinden met de privécloud/SDDCs in de andere regio, waardoor de horizon-verbindings servers die deel uitmaken van de CPA-Federatie, verbinding kunnen maken met alle Bureau bladen onder beheer.Door extra persoonlijke Clouds/SDDCs toe te voegen aan deze configuratie kunt u in totaal 24.000-sessies schalen. 

Hoewel in dit voor beeld meerdere regio's worden weer gegeven, is hetzelfde principe van toepassing als u twee verschillende Horizons wilt implementeren in dezelfde regio. Houd er rekening mee dat u ervoor moet zorgen dat de tweede horizon pod wordt geïmplementeerd in een *afzonderlijke Azure-Virtual Network*.Ten slotte, op dezelfde manier als in het vorige voor beeld van één Pod, kunt u uw bedrijfs netwerk en on-premises pod koppelen aan dit voor beeld van een multi-pod/regio met behulp van klant ExpressRoute en Global Reach.

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="De verschillen tussen de periode van de Azure VMware-oplossing en de horizon-Cloud op Azure" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Grootte van Azure VMware Solution-hosts voor Horizon-implementaties 

De schaal methodologie van een host die wordt uitgevoerd in de Azure VMware-oplossing is eenvoudiger dan horizon-premises, omdat het exemplaar van de Azure VMware Solution host is gestandaardiseerd. Nauw keuriger grootte van de host helpt bij het bepalen van het aantal hosts dat nodig is voor de ondersteuning van uw VDI-vereisten en is een centrale manier om de kosten per Desktop te bepalen.

### <a name="azure-vmware-solution-host-instance"></a>Azure VMware Solution host-instantie

* PowerEdge R640-server-DSS beperkt

* 36 kernen \@ 2,3 GHz

* 576 GB RAM-GEHEUGEN

* HBA330 12 Gbps SAS HBA-controller (niet-RAID)

* 1,92 TB SSD SATA mix 6 Gbps 512 2.5 in Hot-pluggeen-station, 3 DWPD, 10512 TBW

* Intel 1,6 TB, NVMe, gemengd gebruik Express Flash, 2,5 SFF-station, U. 2, P4600 met transporteur

* 2 vSAN-schijf groepen: 1,6 x 4 (1.92 TB)

### <a name="horizon-sizing-inputs"></a>Invoer van Horizon formaat

Bekijk het volgende voor uw geplande workload:

* Aantal gelijktijdige Bureau bladen

* Vereist vCPU per bureau blad

* Vereist vRAM per Desktop

* Vereiste opslag per Desktop

Over het algemeen zijn VDI-implementaties ofwel CPU-of RAM-geheugen, aangezien deze factoren de grootte van de host bepalen. Laten we het volgende voor beeld volgen voor een LoginVSI van de werk belasting, gevalideerd met prestatie tests:

* 2.000 gelijktijdige Desktop implementatie

* 2vCPU per bureau blad.

* 4 GB vRAM per bureau blad.

* 50 GB aan opslag per Desktop

In dit voor beeld wordt het totale aantal hosts gemeten tot 18, wat resulteert in een VM-per-host-densiteit van 111.

>[!IMPORTANT]
>De werk belasting van klanten is afhankelijk van dit voor beeld van een LoginVSI-kennis werker. Als onderdeel van het plannen van uw implementatie, werkt u met uw VMware EUC-SEs voor uw specifieke grootte-en prestatie behoeften. Zorg ervoor dat u uw eigen prestatie tests uitvoert met behulp van de werkelijke geplande werk belasting voordat u de grootte van de host afrondt en dienovereenkomstig bijwerkt.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Horizonal op Azure VMware-oplossings licenties 

Er zijn vier onderdelen voor de totale kosten van het uitvoeren van een Azure VMware-oplossing. 

### <a name="azure-vmware-solution-capacity-cost"></a>Capaciteits kosten van Azure VMware-oplossing

Zie de pagina met [prijzen voor Azure VMware-oplossingen](https://azure.microsoft.com/pricing/details/azure-vmware/) voor meer informatie over de prijzen.

### <a name="horizon-licensing-cost"></a>Kosten van Horizon-licentie verlening

Er zijn twee beschik bare licenties voor gebruik met de Azure VMware-oplossing. Dit kan gelijktijdige gebruiker (CCU) of een benoemde gebruiker (NU) zijn:

* Licentie voor een horizon-abonnement

* De licentie voor een universele abonnement

Als er slechts een distributie op Azure VMware-oplossing voor de nabije toekomst wordt geïmplementeerd, gebruikt u de licentie voor het gebruik van een horizon-abonnement.

Als u beide horizon op de Azure VMware-oplossing en on-premises implementeert, net als bij een gebruiks aanvraag voor herstel na nood gevallen, kiest u de licentie voor een Horizonal universele abonnement. De universele licentie is een hogere prijs omdat deze een vSphere-licentie bevat voor on-premises implementatie.

Werk samen met uw VMware EUC-verkoop team om de Cloud kosten te bepalen op basis van uw behoeften.

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Kosten van de VM-Vm's in azure Virtual Network

Op basis van de standaard implementatie architectuur bestaan de VM-Vm's uit de verbindings servers, UAGs, app-volume managers en worden geïmplementeerd in de Azure-Virtual Network van de klant. Extra Azure native instanties zijn vereist ter ondersteuning van de services voor hoge Beschik baarheid (HA), micro soft SQL of micro soft Active Directory (AD) in Azure. Hier volgt een lijst met Azure-exemplaren op basis van een voor beeld van een implementatie van 2.000-desktop. 

| Onderdeel van de horizon-infra structuur | Azure-exemplaar | Aantal exemplaren vereist (voor 2.000-Desk tops)    | Opmerking  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| Verbindings server                | D4sv3          | 2       | *Inclusief 1 exemplaar voor HA*             |    
| UAG                              | F2sv2          | 2       | *Inclusief 1 exemplaar voor HA*             |
| App-volume beheer              | D4sv3          | 2       | *Inclusief 1 exemplaar voor HA*             |
| Cloud connector                  | D4sv3          | 1       |                                          |
| AD-controller                    | D4sv3          | 2       | *Optie voor het gebruik van MSFT AD service op Azure* |
| MS-SQL Database                  | D4sv3          | 2       | *Optie voor het gebruik van SQL service op Azure*     |
| Windows-bestands share               | D4sv3          |         | *Optioneel*                               |

De kosten voor de VM van de infra structuur zijn \$ 0,36 per gebruiker per maand voor de 2.000-desktop implementatie in bovenstaand voor beeld. In dit voor beeld wordt gebruikgemaakt van de prijzen voor US Oost Azure instance vanaf juni 2020. Uw prijzen kunnen variëren, afhankelijk van de regio, opties geselecteerd en timing.
