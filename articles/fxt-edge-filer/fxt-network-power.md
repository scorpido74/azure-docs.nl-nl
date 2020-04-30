---
title: 'Zelf studie: kabels aansluiten op een Azure FXT Edge-Bestandsr'
description: De netwerk poorten bekabelen en kracht koppelen voor Azure FXT edge-hardware
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 1e3c60fd955bd899955c46b7670acd3f088d0f86
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79239782"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Zelf studie: netwerk verbindingen maken en energie leveren aan het Azure FXT Edge-knoop punt

In deze zelf studie leert u hoe u de netwerk verbindingen kunt bekabelen voor een FXT Edge filer-hardwareprofiel van Azure.

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Het type netwerk kabel voor uw omgeving kiezen
> * Een Azure FXT Edge-knoop punt verbinden met uw datacenter netwerk
> * Kabels routeren via de arm voor kabel beheer (CMA)
> * Verbinding maken met het Gereke apparaat en de stroom inschakelen

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet de Azure FXT Edge-bestand worden geïnstalleerd in een rack met standaard apparatuur. De CMA moet worden geïnstalleerd op het knoop punt van de bestands extensie. 

## <a name="identify-ports"></a>Poorten identificeren

Identificeer de verschillende poorten op de achterkant van uw Azure FXT Edge-bestand. 
 
![Terug van een apparaat met een kabel](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Het apparaat bekabelen

* Verbind de RJ-45-poorten met de netwerk bron van uw Data Center, zoals wordt beschreven in [netwerk poorten](#network-ports).  
* U kunt de [iDRAC-poort](#idrac-port) veilig verbinden met een afzonderlijk netwerk met een beveiligde DHCP-server. 
* Gebruik de USB-poorten en de VGA-poort om een toetsen bord en monitor te verbinden met het knoop punt voor de eerste Setup. U moet het knoop punt opstarten en [een eerste wacht woord instellen](fxt-node-password.md) om de andere poorten van het knoop punt te activeren. Lees de [eerste wacht woorden instellen](fxt-node-password.md) voor meer informatie. 

In dit artikel wordt ook beschreven hoe u [netstroom](#connect-power-cables) voor het knoop punt verbindt. 

In dit artikel wordt ook uitgelegd hoe u verbinding maakt met de [seriële poort](#serial-port-only-when-necessary)van het knoop punt, indien nodig voor gespecialiseerde probleem oplossing. 

### <a name="network-ports"></a>Netwerkpoorten 

Elk Azure FXT Edge-bestands knooppunt bevat de volgende netwerk poorten: 

* Zes gegevens poorten met een hoge snelheid van 25GbE/10GbE met dubbele snelheid: 

  * Vier poorten die worden meegeleverd met twee netwerk adapters van de Dual-Port-invoeg toepassing
  * Twee poorten die door de systeemkaart mezzanine-netwerk adapter worden verschaft 

* Twee 1GbE-poorten die door de systeemkaart mezzanine-netwerk adapter worden meegeleverd 

De gegevens poorten met een hoge snelheid van 25GbE/10GbE hebben standaard SFP28-compatibele kooien. Als u optische kabels wilt gebruiken, moet u SFP28 Optical transceiver modules (niet meegeleverd) installeren.

De 1GbE-poorten hebben standaard RJ-45-connectors.

Raadpleeg de [Cavium FastlinQ 41000-serie](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)voor een volledige lijst met ondersteunde kabels, switches en transceivers.

Het type verbindingen dat voor uw systeem moet worden gebruikt, is afhankelijk van uw data centrum omgeving.

* Als u verbinding maakt met een 25GbE-netwerk, bekabelt u elk van de High-Speed gegevens poorten met een van de volgende kabel typen:

  * Optische kabel en SFP28 optische transceiver met 25GbE of Dual rate 25GbE/10GbE-mogelijkheid
  * SFP28 type 25GbE-compatibel direct attach twinaxial-kabel

* Als u verbinding maakt met een 10GbE netwerk, bekabelt u elk van de High-Speed gegevens poorten met een van de volgende: 

  * Optische kabel en SFP28 optische transceiver met GbE of Dual rate 25GbE/10GbE-mogelijkheid.
  * SFP28 type 25GbE-compatibel direct attach twinaxial-kabel
  * SFP28 type 10GbE-compatibel direct attach twinaxial-kabel

* De 1GbE-netwerk poorten worden gebruikt voor cluster beheer verkeer. Selecteer de optie **1gb-beheer netwerk gebruiken** bij het maken van het cluster als u een fysiek afzonderlijk netwerk wilt maken voor de cluster configuratie (beschreven in [het beheer netwerk configureren](fxt-cluster-create.md#configure-the-management-network)). Bekabel de poorten met een standaard-Cat5-of betere kabel zoals beschreven in de lijst met ondersteunde kabels.

  Als u van plan bent om de poorten met hoge snelheid te gebruiken voor al het verkeer, kunt u de 1GbE-poorten ongekabeld laten. Standaard worden de 1GbE-netwerk poorten niet gebruikt als er een hogere snelheids gegevens poort beschikbaar is.  

### <a name="idrac-port"></a>iDRAC-poort  

De poort met de naam iDRAC is een 1Gb-verbinding die communicatie mogelijk maakt met een RAS-controller die wordt gebruikt voor hardware-en bewakings doeleinden. FXT-software maakt gebruik van de Intelligent Platform Management Interface (IPMI) met deze controller voor probleem oplossing en herstel. U kunt de ingebouwde [iDRAC-interface](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) gebruiken om hardware te bewaken via deze poort. iDRAC en IPMI-toegang zijn standaard ingeschakeld. 

> [!Note]
> De iDRAC-poort kan het besturings systeem overs Laan en rechtstreeks communiceren met hardware op het knoop punt. 

Gebruik deze beveiligings strategieën bij het maken en configureren van de iDRAC-poort:

* Koppel iDRAC-poorten alleen aan een netwerk dat fysiek is gescheiden van het gegevens netwerk dat wordt gebruikt voor toegang tot het cluster.
* Stel een beveiligd iDRAC-beheerders wachtwoord in op elk knoop punt. U moet dit wacht woord instellen om de instructies voor het uitvoeren van de hardware te activeren in [Hardware-wacht woorden instellen](fxt-node-password.md).
* De standaard configuratie van de iDRAC-poort maakt gebruik van DHCP en IPv4 voor IP-adres toewijzing. Zorg ervoor dat uw DHCP-omgeving goed is beveiligd en dat de verbindingen tussen DHCP-clients en de DHCP-server worden beperkt. (Het configuratie scherm van het cluster bevat instellingen voor het wijzigen van de adres configuratie methode van de knoop punten nadat u het cluster hebt gemaakt.)
* Zorg ervoor dat de iDRAC-poort is ingesteld op ' exclusieve modus ' (de standaard instelling), waarmee het iDRAC/IPMI-netwerk verkeer wordt beperkt tot de toegewezen RJ-45-poort.

De iDRAC-poort vereist geen snelle netwerk verbinding.
  
### <a name="serial-port-only-when-necessary"></a>Seriële poort (alleen als dit nodig is)

In sommige gevallen kan micro soft-service en-ondersteuning u laten weten dat u een Terminal verbindt met de seriële poort van een knoop punt om een probleem op te sporen.  

De console koppelen:

1. Zoek de seriële (COM1) poort aan de achterzijde van het knoop punt FXT Edge-bestand.
1. Gebruik een nulmodemkabel om de seriële poort te verbinden met een terminal die is geconfigureerd voor ANSI-115200-8N1.
1. Meld u aan bij de-console en onderneem andere stappen als gericht door ondersteunings personeel.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Kabels routeren in de arm voor kabel beheer (CMA)

Elk Azure FXT Edge-verwerkings knooppunt wordt geleverd met een optionele arm voor kabel beheer. De CMA vereenvoudigt de route ring van kabels en biedt eenvoudiger toegang tot de achtergrond van het chassis zonder dat u kabels hoeft te verbreken. 

Volg deze instructies om de kabels via de CMA te routeren: 

1. Bundel de kabels aan de hand van de beschik bare stem afrondingen en sluit de-manden, zodat ze niet interfereren met aangrenzende systemen (1).
1. Met de CMA in de service positie routert u de kabel bundel via de binnenste en buitenste manden (2).
1. Gebruik de vooraf geïnstalleerde Hook-en loop riemen aan beide uiteinden van de manden om de kabels te beveiligen (3).
1. Draai de CMA weer op de lade (4).
1. Installeer de status indicator kabel aan de achterkant van het systeem en beveilig de kabel door deze via de CMA te routeren. Koppel het andere uiteinde van de kabel aan de hoek van de buitenste CMA-mand (5). 

   > [!CAUTION]
   > Om mogelijke schade van uitstekende kabels te voor komen, moet u elke toegestane vertraging in de status indicator kabel na het routeren van deze kabel via de CMA beveiligen. 

![Afbeelding van CMA met geïnstalleerde kabels](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  Als u de CMA niet hebt geïnstalleerd, gebruikt u de twee hooks en lussen in de Rail Kit om de kabels aan de achtergrond van uw systeem te routeren.
> 
>  1. Zoek de buitenste CMA haakjes aan de binnenkant van beide rack flenss.
>  2. Bundel de kabels voorzichtig, zodat ze de systeem connectors aan de linker-en rechter kant van elkaar halen.
>  3. Verbind de Hook en lussen door de gewerkte sleuven op de buitenste CMA haakjes aan de zijkant van het systeem om de kabel bundels te beveiligen.
> 
>     ![Kabels worden gerouteerd zonder een CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Over IP-adres vereisten

Voor hardwareprofielen in een hybride opslag cache van Azure FXT Edge-bestanden worden IP-adressen beheerd door de cluster software.

Elk knoop punt vereist ten minste één IP-adres, maar knooppunt adressen worden toegewezen wanneer knoop punten worden toegevoegd aan of verwijderd uit het cluster. 

Het totale aantal IP-adressen dat is vereist, is afhankelijk van het aantal knoop punten in de cache. 

Configureer het IP-adres bereik via de software van het configuratie scherm nadat de knoop punten zijn geïnstalleerd. Lees voor meer informatie [informatie over het verzamelen van gegevens voor het cluster](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Stroom kabels aansluiten

Elk Azure FXT Edge-Bestandsr knoop punt gebruikt twee energie-eenheden (PSUs). 

> [!TIP] 
> Als u gebruik wilt maken van de twee redundante PSUs, koppelt u elke netstroom kabel aan een PDU (energie distributie-eenheid) op een onafhankelijk vertakkings circuit.  
> 
> U kunt een UPS gebruiken om de Pdu's uit te scha kelen voor extra beveiliging. 

1. Verbind de inbegrepen stroom snoeren met de PSUs in het chassis. Zorg ervoor dat de snoeren en PSUs volledig zijn aangesloten. 
1. Koppel de netsnoeren aan de netstroom distributie-eenheden op het rack van het apparaat. Gebruik, indien mogelijk, twee afzonderlijke energie bronnen voor de twee snoeren. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Een Azure FXT Edge-knoop punt inschakelen

Als u het knoop punt wilt inschakelen, drukt u op de aan/uit-knop aan de voor kant van het systeem. De knop bevindt zich aan de rechter kant van het configuratie scherm. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Een Azure FXT Edge-knoop punt uitzetten

De knop aan/uit kan worden gebruikt om het systeem uit te scha kelen tijdens het testen en voordat het aan een cluster wordt toegevoegd. Nadat een Azure FXT Edge-knoop punt wordt gebruikt als onderdeel van een cluster, moet u echter de software van het configuratie scherm van het cluster gebruiken om de hardware af te sluiten. Lees [hoe u de hardware van Azure FXT Edge-bestanden veilig kunt uitschakelen](fxt-power-off.md) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Nadat u klaar bent met het bekabelen van de hardware, kunt u alle knoop punten inschakelen en deze initialiseren door hun hoofd wacht woorden in te stellen. 
> [!div class="nextstepaction"]
> [Initiële wacht woorden instellen](fxt-node-password.md)
