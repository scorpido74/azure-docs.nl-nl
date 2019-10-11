---
title: Microsoft Azure-FXT Edge-cluster configuratie-knoop punten toevoegen
description: Knoop punten toevoegen aan de Azure FXT Edge-opslag cache
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 85ab9aaa3e184af7aa71a31eb3d8de1a20639c2a
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254921"
---
# <a name="tutorial-add-cluster-nodes"></a>Zelf studie: cluster knooppunten toevoegen 

Er wordt een nieuw Azure FXT Edge-bestands cluster gemaakt met slechts één knoop punt. U moet ten minste twee extra knoop punten toevoegen en hoge Beschik baarheid inschakelen voordat u een andere configuratie uitvoert. 

In deze zelf studie wordt uitgelegd hoe u cluster knooppunten toevoegt en de functie hoge Beschik baarheid (HA) inschakelt. 

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Knoop punten toevoegen aan het FXT-cluster
> * HA inschakelen

De stappen in deze zelf studie nemen ongeveer 45 minuten in beslag.

Voordat u met deze zelf studie begint, moet u de knoop punten die u wilt toevoegen, inschakelen en [de oorspronkelijke wacht woorden instellen](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. de pagina cluster knooppunten laden

Open het configuratie scherm van het cluster in een webbrowser en meld u aan als beheerder. (Gedetailleerde instructies vindt u in het artikel overzicht, onder [de pagina instellingen openen](fxt-cluster-create.md#open-the-settings-pages).)

In het configuratie scherm wordt het tabblad **dash board** weer gegeven wanneer het wordt geopend. 

![Dash board van het configuratie scherm (eerste tabblad)](media/fxt-cluster-config/dashboard-1-node.png)

Deze afbeelding toont het dash board van een nieuw gemaakt cluster, met één knoop punt.

## <a name="2-locate-the-node-to-add"></a>2. Zoek het toe te voegen knoop punt

Als u knoop punten wilt toevoegen, klikt u op het tabblad **instellingen** en kiest u de pagina **FXT-knoop punten** in de sectie **cluster** .

![Tabblad instellingen van het configuratie scherm (tweede tabblad) met cluster > FXT knoop punten geladen](media/fxt-cluster-config/settings-fxt-nodes.png)

In de **FXT-knoop punten-niet-samenvoegde** lijst worden alle niet-toegewezen FXT-knoop punten weer gegeven (de meeste data centers hebben slechts enkele. Zoek de FXT-knoop punten die u aan het cluster wilt toevoegen.

> [!Tip] 
> Als u het gewenste knoop punt niet kunt vinden in de niet- **samenvoegde** lijst, controleer dan of het voldoet aan deze vereisten:
> 
> * Deze is ingeschakeld en er is een [hoofd wachtwoord ingesteld](fxt-node-password.md).
> * Deze is verbonden met een netwerk waartoe u toegang hebt. Als u VLAN'S gebruikt, moet deze zich op hetzelfde VLAN bekomen als het cluster.
> * Het kan worden gedetecteerd met het Bonjour-protocol. 
>
>   Sommige firewall instellingen blok keren de TCP/UDP-poorten die worden gebruikt door Bonjour, waarmee wordt voor komen dat de knoop punten automatisch worden gedetecteerd door het besturings systeem FXT.
> 
> Als het knoop punt dat u wilt toevoegen, zich niet in de lijst bevindt, probeert u de volgende oplossingen: 
> 
> * Klik op de knop **detectie hand matig** om deze te zoeken op IP-adres.
> 
> * Hand matig tijdelijke IP-adressen toewijzen. Dit komt zelden voor, maar kan ook nodig zijn als u gecodeerde VLAN'S gebruikt, de knoop punten zich niet in het juiste netwerk bevinden, of als uw netwerk geen zelf-toegewezen IP-adressen toestaat. Volg de instructies in de oudere versie van dit document om [hand matig een statisch IP-adres](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html)in te stellen.

De knooppunt naam, het IP-adres, de software versie en de geschiktheids status worden weer gegeven in de lijst. Normaal gesp roken bevat de kolom **status** de tekst "wil lid worden" of wordt een systeem-of hardwareprobleem beschreven waardoor het knoop punt niet in aanmerking komt voor deelname aan het cluster.

De kolom **acties** bevat knoppen waarmee u het knoop punt kunt toevoegen aan het cluster of de software ervan kunt bijwerken. De knop bijwerken installeert automatisch de software versie die overeenkomt met de knoop punten die zich al in het cluster bekomen.

Alle knoop punten in een cluster moeten dezelfde versie van het besturings systeem gebruiken, maar u hoeft de software niet bij te werken voordat u een knoop punt toevoegt. Nadat u op de knop **toe te voegen toevoegen** hebt geklikt, controleert en installeert het cluster deelnameproces automatisch de OS-software die overeenkomt met de versie op het cluster.

Lees voor meer informatie over de opties op deze pagina [ **cluster** > **FXT-knoop punten** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) in de cluster configuratie handleiding.

## <a name="3-click-the-allow-to-join-button"></a>3. Klik op de knop toe te voegen 

Klik op de knop **toe te**voegen * in de kolom **acties** voor het knoop punt dat u wilt toevoegen.

Nadat u op de knop hebt geklikt, kan de status van het knoop punt veranderen wanneer de software wordt bijgewerkt in de voor bereiding voor het toevoegen van deze aan het cluster. 

In de onderstaande afbeelding ziet u een knoop punt dat deel uitmaakt van het cluster (waarschijnlijk wordt er een update van het besturings systeem opgehaald voordat deze wordt toegevoegd). Er worden geen knoppen weer gegeven in de kolom **acties** voor knoop punten die worden toegevoegd aan het cluster.

![Eén rij van de knooppunt tabel, met de naam van een knoop punt, het IP-adres, de software versie, het bericht ' mag worden toegevoegd ' en een lege laatste kolom](media/fxt-cluster-config/node-join-in-process.png)

Na enkele ogen blikken moet het nieuwe knoop punt worden weer gegeven in de lijst met cluster knooppunten boven aan de pagina instellingen van **FXT-knoop punten** . 

Herhaal dit proces om de andere knoop punten toe te voegen aan uw cluster. U hoeft niet te wachten tot het ene knoop punt is toegevoegd aan het cluster voordat u een ander start.

## <a name="enable-high-availability"></a>Hoge Beschik baarheid inschakelen

Nadat u een tweede knoop punt aan uw cluster hebt toegevoegd, wordt er mogelijk een waarschuwing weer gegeven in het dash board van het configuratie scherm dat de functie voor hoge Beschik baarheid niet is geconfigureerd. 

Met hoge Beschik baarheid (HA) kunnen de cluster knooppunten worden gecompenseerd als een van de knoop punten uitvalt. HA is standaard niet ingeschakeld.

![Tabblad dash board met het bericht ' het cluster heeft meer dan een knoop punt, maar HA is niet ingeschakeld... ' in de tabel voor waarden](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Schakel HA pas in als u ten minste drie knoop punten in het cluster hebt.

Volg deze procedure om HA in te scha kelen: 

1. Laad de pagina **hoge Beschik baarheid** in het gedeelte **cluster** van het tabblad **instellingen** .

   ![Configuratie pagina van HA (cluster > hoge Beschik baarheid). Het selectie vakje ' HA inschakelen ' bevindt zich bovenaan en de knop verzenden onderaan.](media/fxt-cluster-config/enable-ha.png)

2. Klik op het vak met de naam **Activeer ha** en klik op de knop **verzenden** . 

Er wordt een waarschuwing weer gegeven op het **dash board** om te bevestigen dat ha is ingeschakeld.

![Dashboard tabel met het bericht ' HA is nu volledig geconfigureerd '](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Volgende stappen

Nadat u alle knoop punten in het cluster hebt toegevoegd, kunt u door gaan met de installatie door de lange termijn opslag van uw cluster te configureren.

> [!div class="nextstepaction"]
> [Back-end-opslag toevoegen en de virtuele naam ruimte instellen](fxt-add-storage.md)