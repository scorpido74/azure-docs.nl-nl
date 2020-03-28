---
title: 'Zelfstudie: Kabels verbinden met een Azure FXT Edge Filer'
description: De netwerkpoorten bekabelen en stroom koppelen voor Azure FXT Edge Filer-hardware
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 1e3c60fd955bd899955c46b7670acd3f088d0f86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239782"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Zelfstudie: Netwerkverbindingen maken en stroom leveren aan het Azure FXT Edge Filer-knooppunt

In deze zelfstudie leert u hoe u de netwerkverbindingen voor een Azure FXT Edge Filer-hardwareknooppunt bekabelen.

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Het type netwerkkabel voor uw omgeving kiezen
> * Een Azure FXT Edge Filer-knooppunt verbinden met uw datacenternetwerk
> * Kabels routeren via de kabelmanagementarm (CMA)
> * Stroom aansluiten op het rackte apparaat en het inschakelen

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet de Azure FXT Edge Filer worden geïnstalleerd in een rack met standaardapparatuur. De CMA moet worden geïnstalleerd op de filer node. 

## <a name="identify-ports"></a>Poorten identificeren

Identificeer de verschillende poorten op de achterkant van uw Azure FXT Edge Filer. 
 
![Achterkant van een bekabeld apparaat](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Het apparaat bekabelen

* Sluit de RJ-45-poorten aan op de netwerkbron van uw datacenter, zoals beschreven in [netwerkpoorten.](#network-ports)  
* Sluit de [iDRAC-poort](#idrac-port) veilig aan op een apart netwerk met een beveiligde DHCP-server. 
* Gebruik de USB-poorten en de VGA-poort om een toetsenbord en monitor aan te sluiten op het knooppunt voor de eerste installatie. U moet het knooppunt opstarten en [een eerste wachtwoord instellen](fxt-node-password.md) om de andere poorten van het knooppunt te activeren. Lees [De eerste wachtwoorden instellen](fxt-node-password.md) voor meer informatie. 

In dit artikel wordt ook beschreven hoe [u de wisselstroom](#connect-power-cables) voor het knooppunt aansluiten. 

In dit artikel wordt ook uitgelegd hoe u verbinding maken met de [seriële poort](#serial-port-only-when-necessary)van het knooppunt, indien nodig voor gespecialiseerde probleemoplossing. 

### <a name="network-ports"></a>Netwerkpoorten 

Elke Azure FXT Edge Filer-node bevat de volgende netwerkpoorten: 

* Zes snelle 25GbE/10GbE dual rate datapoorten: 

  * Vier poorten van twee plug-in netwerkadapters met twee poorten
  * Twee poorten die door het moederbord mezzanine netwerkadapter 

* Twee 1GbE-poorten van de moederbord mezzanine-netwerkadapter 

De snelle 25GbE/10GbE-datapoorten hebben standaard SFP28-compatibele kooien. Om optische kabels te gebruiken, moet u SFP28 optische transceivermodules installeren (niet aanwezig).

De 1GbE-poorten hebben standaard RJ-45-connectoren.

Raadpleeg de [Cavium FastlinQ 41000-serie Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)voor een volledige lijst met ondersteunde kabels, schakelaars en transceivers.

Het type verbindingen dat voor uw systeem moet worden gebruikt, is afhankelijk van uw datacenteromgeving.

* Als u verbinding maakt met een 25 GbE-netwerk, kabelt u elk van de snelle datapoorten met een van de volgende kabeltypen:

  * Optische kabel en SFP28 optische transceiver met 25GbE of dual rate 25GbE/10GbE-mogelijkheden
  * SFP28 type 25GbE-geschikte direct attach twinaxial kabel

* Als u verbinding maakt met een 10 GbE-netwerk, kabelt u elk van de snelle datapoorten met een van de volgende: 

  * Optische kabel en SFP28 optische transceiver met 10GbE of dual rate 25GbE/10GbE mogelijkheden.
  * SFP28 type 25GbE-geschikte direct attach twinaxial kabel
  * SFP28 type 10GbE-geschikte direct attach twinaxial kabel

* De 1GbE-netwerkpoorten worden gebruikt voor clusterbeheerverkeer. Schakel de optie **MGMt-netwerk van 1 Gb gebruiken** in bij het maken van het cluster als u een fysiek afzonderlijk netwerk wilt maken voor clusterconfiguratie (beschreven in Het [beheernetwerk configureren).](fxt-cluster-create.md#configure-the-management-network) Kabel de poorten met standaard Cat5 of betere kabel zoals beschreven in de lijst met ondersteunde kabels.

  U de 1GbE-poorten onbestoofd laten als u van plan bent om de hogesnelheidspoorten te gebruiken voor al het verkeer. Standaard worden de 1GbE-netwerkpoorten niet gebruikt als er een gegevenspoort met hogere snelheid beschikbaar is.  

### <a name="idrac-port"></a>iDRAC-poort  

De poort met het label iDRAC is een 1Gb-verbinding waarmee u communiceren met een externe toegangscontroller die wordt gebruikt voor hardwarebeheer en -monitoring. FXT-software maakt gebruik van de Intelligent Platform Management Interface (IPMI) met deze controller voor probleemoplossing en herstel. U de ingebouwde [iDRAC-interface](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) gebruiken om hardware via deze poort te controleren. iDRAC- en IPMI-toegang zijn standaard ingeschakeld. 

> [!Note]
> De iDRAC-poort kan het besturingssysteem omzeilen en direct communiceren met hardware op het knooppunt. 

Gebruik deze beveiligingsstrategieën bij het verbinden en configureren van de iDRAC-poort:

* Sluit alleen iDRAC-poorten aan op een netwerk dat fysiek is gescheiden van het gegevensnetwerk dat wordt gebruikt om toegang te krijgen tot het cluster.
* Stel op elk knooppunt een beveiligd wachtwoord voor iDRAC-beheerders in. U moet dit wachtwoord instellen om de hardware te activeren - volg instructies in [Hardware-wachtwoorden instellen](fxt-node-password.md).
* De standaardconfiguratie van de iDRAC-poort maakt gebruik van DHCP en IPv4 voor IP-adrestoewijzing. Zorg ervoor dat uw DHCP-omgeving goed is beveiligd en dat de verbindingen tussen DHCP-clients en de DHCP-server zijn beperkt. (Het clusterconfiguratiepaneel bevat instellingen om de adresconfiguratiemethode van de knooppunten te wijzigen nadat u het cluster hebt gemaakt.)
* Laat de iDRAC-poort instellen op "dedicated mode" (de standaardmodus), die het iDRAC/IPMI-netwerkverkeer beperkt tot de speciale RJ-45-poort.

De iDRAC-poort vereist geen snelle netwerkverbinding.
  
### <a name="serial-port-only-when-necessary"></a>Seriële poort (alleen indien nodig)

In sommige situaties kunnen Microsoft Service en Ondersteuning u vertellen een terminal aan te sluiten op de seriële poort van een knooppunt om een probleem te diagnosticeren.  

Ga als u de console wilt bevestigen:

1. Zoek de seriële (COM1)-poort aan de achterzijde van het FXT Edge Filer-knooppunt.
1. Gebruik een null-modemkabel om de seriële poort aan te sluiten op een terminal die is geconfigureerd voor ANSI-115200-8N1.
1. Meld u aan bij de console en neem andere stappen zoals aangegeven door ondersteunend personeel.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Routekabels in de kabelmanagementarm (CMA)

Elke Azure FXT Edge Filer-knooppunt wordt geleverd met een optionele kabelbeheerarm. De CMA vereenvoudigt de kabelgeleiding en biedt gemakkelijkertoegang tot de achterkant van het chassis zonder kabels los te koppelen. 

Volg deze instructies om de kabels door de CMA te leiden: 

1. Met behulp van de meegeleverde tiewraps bundel je de kabels bij het in- en uitstappen van de manden, zodat ze de aangrenzende systemen niet verstoren (1).
1. Met de CMA in de servicepositie u de kabelbundel door de binnen- en buitenmanden leiden (2).
1. Gebruik de voorgeïnstalleerde haak- en lusriemen aan weerszijden van de manden om de kabels vast te zetten (3).
1. Draai de CMA terug op zijn plaats op de lade (4).
1. Installeer de statusindicatorkabel aan de achterkant van het systeem en beveilig de kabel door deze door de CMA te leiden. Bevestig het andere uiteinde van de kabel aan de hoek van de buitenste CMA-mand (5). 

   > [!CAUTION]
   > Om mogelijke schade door uitstekende kabels te voorkomen, u elke speling in de statusindicatorkabel beveiligen nadat u deze kabel door de CMA hebt geleid. 

![Illustratie van CMA met geïnstalleerde kabels](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  Als u de CMA niet hebt geïnstalleerd, gebruikt u de twee haak- en lusriemen in de railkit om de kabels aan de achterkant van uw systeem te routeren.
> 
>  1. Zoek de buitenste CMA beugels aan de binnenzijde van beide rackflenzen.
>  2. Bundel de kabels voorzichtig en trek ze uit de buurt van de systeemconnectoren aan de linker- en rechterkant.
>  3. Rijg de haak en loop riemen door de gereedschapssleuven op de buitenste CMA beugels aan elke kant van het systeem om de kabelbundels te beveiligen.
> 
>     ![Kabels zonder CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Over IP-adresvereisten

Voor hardwareknooppunten in een hybride opslagcache van Azure FXT Edge Filer worden IP-adressen beheerd door de clustersoftware.

Voor elk knooppunt is ten minste één IP-adres vereist, maar knooppuntadressen worden toegewezen wanneer knooppunten worden toegevoegd aan of verwijderd uit het cluster. 

Het totale aantal vereiste IP-adressen is afhankelijk van het aantal knooppunten in die groep van uw cache. 

Configureer het IP-adresbereik met behulp van de software voor het Configuratiescherm nadat de knooppunten zijn geïnstalleerd. Lees Voor meer informatie informatie [voor het cluster](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Stroomkabels aansluiten

Elke Azure FXT Edge Filer-node maakt gebruik van twee voedingen (PSU's). 

> [!TIP] 
> Om gebruik te maken van de twee redundante PSU's, bevestigt u elke ac-voedingskabel aan een stroomdistributieeenheid (PDU) op een onafhankelijk filiaalcircuit.  
> 
> U een UPS gebruiken om de PDU's aan te drijven voor extra bescherming. 

1. Sluit de meegeleverde netsnoeren aan op de PSU's in het chassis. Zorg ervoor dat de snoeren en PSU's volledig zitten. 
1. Bevestig de netsnoeren aan de stroomverdeeleenheden op het materiaalrek. Gebruik indien mogelijk twee afzonderlijke stroombronnen voor de twee snoeren. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Stroom uit op een Azure FXT Edge Filer-knooppunt

Als u het knooppunt wilt aanzetten, drukt u op de aan/uit-knop aan de voorzijde van het systeem. De knop bevindt zich op het bedieningspaneel aan de rechterkant. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Een Azure FXT Edge Filer-knooppunt uitschakelen

De aan/uit-knop kan worden gebruikt om het systeem uit te schakelen tijdens het testen en voordat het aan een cluster wordt toegevoegd. Nadat echter een Azure FXT Edge Filer-knooppunt in gebruik is als onderdeel van een cluster, moet u de software van het clusterconfiguratiepaneel gebruiken om de hardware uit te schakelen. Lees [Hoe u azure FXT Edge Filer-hardware veilig uitschakelen](fxt-power-off.md) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Nadat u klaar bent met het bekrachtigen van de hardware, u elk van de knooppunten inschakelen en initialiseren door hun rootwachtwoorden in te stellen. 
> [!div class="nextstepaction"]
> [Eerste wachtwoorden instellen](fxt-node-password.md)
