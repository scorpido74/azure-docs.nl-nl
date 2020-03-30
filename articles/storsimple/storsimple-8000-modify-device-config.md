---
title: De configuratie van de StorSimple 8000-serie wijzigen | Microsoft Documenten
description: Beschrijft hoe u de StorSimple Device Manager-service gebruiken om een StorSimple-apparaat opnieuw te configureren dat al is geïmplementeerd.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 774f5a73a5fc30352698c0af0c279fbbe488c480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267688"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Gebruik de StorSimple Device Manager-service om de configuratie van uw StorSimple-apparaat te wijzigen

## <a name="overview"></a>Overzicht

De sectie **Apparaatinstellingen azure-portal** in het **pagina-instellingen** bevat alle apparaatparameters die u opnieuw configureren op een StorSimple-apparaat dat wordt beheerd door een StorSimple-apparaatbeheerservice. In deze zelfstudie wordt uitgelegd hoe u het **mes Instellingen** gebruiken om de volgende taken op apparaatniveau uit te voeren:

* Apparaatvriendelijke naam wijzigen
* Instellingen voor apparaattijd wijzigen
* Een secundaire DNS toewijzen
* Netwerkinterfaces wijzigen
* IP-knopverwisselingen of opnieuw toewijzen

## <a name="modify-device-friendly-name"></a>Apparaatvriendelijke naam wijzigen

U de Azure-portal gebruiken om de naam van het apparaat te wijzigen en deze een unieke vriendelijke naam naar keuze toe te wijzen. Gebruik het mes **algemene instellingen** op uw apparaat om de apparaatvriendelijke naam te wijzigen. De vriendelijke naam kan alle tekens bevatten en kan maximaal 64 tekens lang zijn.

> [!NOTE] 
> U de apparaatnaam alleen wijzigen in de Azure-portal voordat de apparaatconfiguratie is voltooid. Zodra de minimale apparaatinstelling is voltooid, u de naam van het apparaat niet meer wijzigen.

![Apparaatnaam in algemene instellingen](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Aan een StorSimple-apparaat dat is verbonden met de StorSimple Device Manager-service, wordt een standaardnaam toegewezen. De standaardnaam geeft doorgaans het serienummer van het apparaat weer. Een standaardapparaatnaam van 15 tekens lang, zoals 8600-SHX0991003G44HT, geeft bijvoorbeeld het volgende aan:

* **8600** – Geeft het apparaatmodel aan.
* **SHX** – Geeft de productielocatie aan.
* **0991003** - Geeft een specifiek product aan.
* **G44HT**- De laatste 5 cijfers worden verhoogd om unieke serienummers te maken. Dit is mogelijk geen sequentiële set.

## <a name="modify-device-description"></a>Apparaatbeschrijving wijzigen

Gebruik het blad **Algemene instellingen** op uw apparaat om de apparaatbeschrijving te wijzigen.

![Apparaatbeschrijving in algemene instellingen](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Een apparaatbeschrijving helpt meestal bij het identificeren van de eigenaar en de fysieke locatie van het apparaat. Het beschrijvingsveld moet minder dan 256 tekens bevatten.

## <a name="modify-time-settings"></a>Tijdinstellingen wijzigen

Uw apparaat moet de tijd synchroniseren om te verifiëren met uw cloudopslagserviceprovider. Gebruik het blad **Algemene instellingen** op uw apparaat om de instellingen voor apparaattijd te wijzigen.

![Apparaatbeschrijving in algemene instellingen](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Selecteer uw tijdzone in de vervolgkeuzelijst. U maximaal twee NTP-servers (Network Time Protocol) opgeven:

 - **Primaire NTP-server** - De configuratie is vereist en is opgegeven wanneer u Windows PowerShell voor StorSimple gebruikt om uw apparaat te configureren. U de standaard Windows **Server-time.windows.com** opgeven als uw NTP-server. U de primaire NTP-serverconfiguratie bekijken via de Azure-portal, maar u moet de Windows PowerShell-interface gebruiken om deze te wijzigen. Gebruik `Set-HcsNTPClientServerAddress` de cmdlet om de primaire NTP-server van uw apparaat te wijzigen. Ga voor meer informatie naar syntaxis voor de cmdlet [Set-HcsNTPClientServerAddress.](https://technet.microsoft.com/library/dn688138.aspx)

- **Secundaire NTP-server** - De configuratie is optioneel. U de portal gebruiken om een secundaire NTP-server te configureren.

Wanneer u de NTP-server configureert, moet u ervoor zorgen dat het NTP-verkeer door uw netwerk naar het internet kan gaan. Wanneer u een openbare NTP-server opgeeft, moet u ervoor zorgen dat uw netwerkfirewalls en andere beveiligingsapparaten zijn geconfigureerd om NTP-verkeer van en naar het externe netwerk te laten reizen. Als bidirectioneel NTP-verkeer niet is toegestaan, moet u een interne NTP-server gebruiken (een Windows-domeincontroller biedt deze functie). Als uw apparaat de tijd niet kan synchroniseren, kan het mogelijk niet communiceren met uw cloudopslagprovider.

Als u een lijst met openbare NTP-servers wilt bekijken, gaat u naar het [NTP-serversweb.](https://support.ntp.org/bin/view/Servers/WebHome)

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Wat gebeurt er als het apparaat in een andere tijdzone wordt geïmplementeerd?

Als het apparaat in een andere tijdzone wordt geïmplementeerd, wordt de tijdzone van het apparaat gewijzigd. Aangezien alle back-upbeleidsregels de tijdzone van het apparaat gebruiken, wordt het back-upbeleid automatisch aangepast aan de nieuwe tijdzone. Er is geen tussenkomst van de gebruiker is vereist.

## <a name="modify-dns-settings"></a>DNS-instellingen wijzigen

Er wordt een DNS-server gebruikt wanneer uw apparaat probeert te communiceren met uw cloudopslagserviceprovider. Gebruik het blade **netwerkinstellingen** op uw apparaat om de geconfigureerde DNS-instellingen weer te geven en te wijzigen. 

![DNS-instellingen in netwerkinstellingen](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Voor een hoge beschikbaarheid moet u zowel de primaire als de secundaire DNS-servers configureren tijdens de eerste apparaatimplementatie.

**Primaire DNS-server** - U gebruikt de Windows PowerShell voor StorSimple om eerst de primaire DNS-server op te geven tijdens de eerste installatie. U de primaire DNS-server alleen opnieuw configureren via de Windows PowerShell-interface. Gebruik `Set-HcsDNSClientServerAddress` de cmdlet om de primaire DNS-server van uw apparaat te wijzigen. Ga voor meer informatie naar syntaxis voor de cmdlet [Set-HcsDNSClientServerAddress.](https://technet.microsoft.com/library/dn688138.aspx)

**Secundaire DNS-server** - Als u `Set-HcsDNSClientServerAddress` de secundaire DNS-server wilt wijzigen, gebruikt u de cmdlet in de Windows PowerShell-interface van het apparaat of het blade **netwerkinstellingen** van uw StorSimple-apparaat in de Azure-portal.

Voer de volgende stappen uit om de secundaire DNS-server in azure-portal te wijzigen.

1. Ga naar uw StorSimple-apparaatbeheerservice. Selecteer en klik in de lijst met apparaten op uw apparaat.

2. Ga **in** het blad Instellingen naar **Apparaatinstellingen > Netwerk**. Hiermee wordt het mes **netwerkinstellingen** geopend. Klik op de tegel **DNS-instellingen.** Wijzig het IP-adres van de secundaire DNS-server.

    ![Ip-adres van secundaire DNS-server wijzigen](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Klik op de opdrachtbalk op **Opslaan** en klik op **OK**wanneer u om bevestiging wordt gevraagd.

    ![Wijzigingen opslaan en bevestigen](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Netwerkinterfaces wijzigen

Uw apparaat heeft zes netwerkinterfaces voor apparaten, waarvan er vier 1 GbE zijn en twee van die 10 GbE. Deze interfaces worden aangeduid als DATA 0 – DATA 5. DATA 0, DATA 1, DATA 4 en DATA 5 zijn 1 GbE, terwijl DATA 2 en DATA 3 10 GbE-netwerkinterfaces zijn.

Gebruik het blad **Netwerkinstellingen** om elk van de te gebruiken interfaces te configureren.

![Netwerkinterfaces configureren via netwerkinstellingen](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Om een hoge beschikbaarheid te garanderen, raden we u aan ten minste twee iSCSI-interfaces en twee interfaces met cloudfunctionaliteit op uw apparaat te hebben. Wij raden u aan, maar vereisen niet dat ongebruikte interfaces worden uitgeschakeld.

Voor elke netwerkinterface worden de volgende parameters weergegeven:

* **Snelheid** – Geen door de gebruiker configureerbare parameter. DATA 0, DATA 1, DATA 4 en DATA 5 zijn altijd 1 GbE, terwijl DATA 2 en DATA 3 10 GbE-interfaces zijn.
  
  > [!NOTE]
  > Snelheid en duplex zijn altijd automatisch onderhandeld. Jumbo frames worden niet ondersteund.
  
* **Interfacestatus** : een interface kan worden ingeschakeld of uitgeschakeld. Als dit is ingeschakeld, probeert het apparaat de interface te gebruiken. We raden u aan alleen interfaces die zijn aangesloten op het netwerk en worden gebruikt, in te schakelen. Schakel alle interfaces uit die u niet gebruikt.
* **Interfacetype** : met deze parameter u iSCSI-verkeer isoleren van cloudopslagverkeer. Deze parameter kan een van de volgende parameters zijn:
  
  * **Cloud ingeschakeld** – wanneer ingeschakeld, zal het apparaat deze interface gebruiken om te communiceren met de cloud.
  * **iSCSI ingeschakeld** – wanneer ingeschakeld, gebruikt het apparaat deze interface om te communiceren met de iSCSI-host.
    
    We raden u aan iSCSI-verkeer te isoleren van cloudopslagverkeer. Houd er ook rekening mee dat als uw host zich binnen hetzelfde subnet bevindt als uw apparaat, u geen gateway hoeft toe te wijzen. Als uw host zich echter in een ander subnet bevindt dan uw apparaat, moet u een gateway toewijzen.
* **IP-adres** : wanneer u een van de netwerkinterfaces configureert, moet u een virtueel IP-adres (VIP) configureren. Dit kan IPv4 of IPv6 of beide zijn. Zowel de IPv4- als de IPv6-adresfamilies worden ondersteund voor de netwerkinterfaces van het apparaat. Geef bij het gebruik van IPv4 een 32-bits IP-adres *(xxx.xxx.xxx.xxx)* op in puntdecimale notatie. Wanneer u IPv6 gebruikt, levert u gewoon een 4-cijferig voorvoegsel en wordt een 128-bits adres automatisch gegenereerd voor de netwerkinterface van uw apparaat op basis van dat voorvoegsel.
* **Subnet** – Dit verwijst naar het subnetmasker en is geconfigureerd via de Windows PowerShell-interface.
* **Gateway** : dit is de standaardgateway die door deze interface moet worden gebruikt wanneer deze probeert te communiceren met knooppunten die zich niet binnen dezelfde IP-adresruimte bevinden (subnet). De standaardgateway moet zich in dezelfde adresruimte (subnet) bevinden als het IP-adres van de interface, zoals bepaald door het subnetmasker.
* **Vast IP-adres** : dit veld is alleen beschikbaar terwijl u de DATA 0-interface configureert. Voor bewerkingen zoals updates of het oplossen van problemen met het apparaat moet u mogelijk rechtstreeks verbinding maken met de apparaatcontroller. Het vaste IP-adres kan worden gebruikt om toegang te krijgen tot zowel de actieve als de passieve controller op uw apparaat.

> [!NOTE]
> * Controleer de interfacesnelheid en duplex op de schakelaar waarmee elke apparaatinterface is verbonden, om een goede werking te garanderen. Switch-interfaces moeten onderhandelen met of geconfigureerd worden voor Gigabit Ethernet (1000 Mbps) en full-duplex zijn. Interfaces die met lagere snelheden of in half-duplex werken, zullen leiden tot prestatieproblemen.
> * Om onderbrekingen en downtime tot een minimum te beperken, raden we u aan portfast in te schakelen op elk van de switchpoorten waarmee de iSCSI-netwerkinterface van uw apparaat verbinding maakt. Dit zal ervoor zorgen dat de netwerkconnectiviteit snel kan worden vastgesteld in het geval van een failover.

### <a name="configure-data-0"></a>GEGEVENS 0 configureren

DATA 0 is standaard cloudingeschakeld. Bij het configureren van DATA 0 moet u ook twee vaste IP-adressen configureren, één voor elke controller. Deze vaste IP-adressen kunnen worden gebruikt om rechtstreeks toegang te krijgen tot de apparaatcontrollers en zijn handig wanneer u updates op het apparaat installeert, om garbage collection goed te laten werken of wanneer u toegang krijgt tot de controllers met het oog op het oplossen van problemen.

U de vaste IP-controllers opnieuw configureren via het DATA 0-instellingenblad.

![Netwerkinterface configureren - GEGEVENS 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> De vaste IP-adressen voor de controller worden gebruikt voor het onderhoud van de updates aan het apparaat en voor ruimteterugwinningsalgoritmen (garbage collection) om goed te werken. Daarom moeten de vaste IP-adressen routeerbaar zijn en verbinding kunnen maken met internet.

### <a name="configure-data-1---data-5"></a>GEGEVENS 1 configureren - GEGEVENS 5

Voor DATA 1 - DATA 5-netwerkinterfaces u alle netwerkinstellingen configureren zoals weergegeven in de volgende schermafbeelding:

![Netwerkinterfaces DATA 1 - DATA 5 configureren](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>IP-knopverwisselingen of opnieuw toewijzen

Als momenteel een netwerkinterface op de controller een VIP toegewezen krijgt die in gebruik is (door hetzelfde apparaat of een ander apparaat in het netwerk), mislukt de controller. Als u VIP's verwisselt of opnieuw toewijst voor een apparaatnetwerkinterface, moet u een goede procedure volgen omdat u een dubbele IP-situatie maken.

Voer de volgende stappen uit om de VIP's voor een van de netwerkinterfaces te verwisselen of opnieuw toe te wijzen:

#### <a name="to-reassign-ips"></a>IP's opnieuw toewijzen

1. Maak het IP-adres voor beide interfaces wissen.
2. Nadat de IP-adressen zijn gewist, wijst u de nieuwe IP-adressen toe aan de desbetreffende interfaces.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [configureren van MPIO voor uw StorSimple-apparaat.](storsimple-8000-configure-mpio-windows-server.md)
* Meer informatie over het [gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

