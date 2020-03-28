---
title: 'Zelfstudie: knooppunten toevoegen aan een Azure FXT Edge Filer-cluster'
description: Knooppunten toevoegen aan de Azure FXT Edge Filer-opslagcache
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 6251fe8f88b7db25e3c09898540e07754d72fb0d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551944"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Zelfstudie: clusterknooppunten toevoegen aan een Azure FXT Edge Filer-cluster

Er wordt een nieuw Azure FXT Edge Filer-cluster gemaakt met slechts één knooppunt. U moet nog minstens twee knooppunten toevoegen en hoge beschikbaarheid inschakelen voordat u een andere configuratie doet. 

In deze zelfstudie wordt uitgelegd hoe u clusterknooppunten toevoegt en de ha-functie (High Availability) inschakelt. 

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Knooppunten toevoegen aan het FXT-cluster
> * Hoe ha in te schakelen

De stappen in deze zelfstudie duren ongeveer 45 minuten.

Voordat u deze zelfstudie start, u de knooppunten die u wilt toevoegen en [de oorspronkelijke wachtwoorden instellen.](fxt-node-password.md) 

## <a name="1-load-the-cluster-nodes-page"></a>1. De pagina Clusterknooppunten laden

Open het Configuratiescherm van het cluster in een webbrowser en meld u aan als beheerder. (Gedetailleerde instructies staan in het overzichtsartikel onder [De pagina Instellingen openen](fxt-cluster-create.md#open-the-settings-pages).)

In het Configuratiescherm wordt het tabblad **Dashboard** weergegeven wanneer het wordt geopend. 

![Dashboard van het Configuratiescherm (eerste tabblad)](media/fxt-cluster-config/dashboard-1-node.png)

Deze afbeelding toont het dashboard van een nieuw gemaakt cluster, met één knooppunt.

## <a name="2-locate-the-node-to-add"></a>2. Zoek het knooppunt om toe te voegen

Als u knooppunten wilt toevoegen, klikt u op het tabblad **Instellingen** en kiest u de pagina **FXT-knooppunten** in de sectie **Cluster.**

![Tabblad Instellingen van het Configuratiescherm (tweede tabblad) met cluster> FXT-knooppunten geladen](media/fxt-cluster-config/settings-fxt-nodes.png)

De **FXT-knooppunten - Niet-samengevoegde** lijst toont alle niet-toegewezen FXT-knooppunten (de meeste datacenters hebben er maar een paar. Zoek de FXT-knooppunten die u aan het cluster wilt toevoegen.

> [!Tip] 
> Als u het gewenste knooppunt niet vinden in de lijst **Niet-verbonden,** controleert u of het aan deze vereisten voldoet:
> 
> * Het is ingeschakeld en heeft een [root wachtwoord ingesteld.](fxt-node-password.md)
> * Het is verbonden met een netwerk waartoe u toegang heeft. Als u VLAN's gebruikt, moet deze zich op dezelfde VLAN als het cluster bevinden.
> * Het kan worden gedetecteerd met het Bonjour-protocol. 
>
>   Sommige firewall-instellingen blokkeren de TCP/UDP-poorten die door Bonjour worden gebruikt, waardoor het FXT-besturingssysteem de knooppunten niet automatisch detecteert.
> 
> Als het knooppunt dat u wilt toevoegen niet op de lijst staat, probeert u de volgende oplossingen: 
> 
> * Klik **op de** knop Handmatig ontdekken om deze op IP-adres te vinden.
> 
> * Stel handmatig tijdelijke IP-adressen toe. Dit is zeldzaam, maar kan nodig zijn als u gelabelde VLAN's gebruikt en de knooppunten zich niet op het juiste netwerk bevinden of als uw netwerk zelf toegewezen IP-adressen niet toestaat. Volg de instructies in de verouderde versie van dit document om [handmatig een statisch IP-adres in](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html)te stellen.

De naam van het knooppunt, het IP-adres, de softwareversie en de geschiktheidsstatus worden weergegeven in de lijst. In de kolom **Status** staat doorgaans 'Wil lid worden' of wordt een systeem- of hardwareprobleem beschreven waardoor het knooppunt niet in aanmerking komt voor deelname aan het cluster.

De kolom **Handelingen** heeft knoppen waarmee u het knooppunt aan het cluster toevoegen of de software bijwerken. De updateknop installeert automatisch de softwareversie die overeenkomt met de knooppunten die al in het cluster staan.

Alle knooppunten in een cluster moeten dezelfde versie van het besturingssysteem gebruiken, maar u hoeft geen software bij te werken voordat u een knooppunt toevoegt. Nadat u op de knop **Deelnemen toestaan** hebt geklikt, controleert en installeert het proces voor deelnemen aan het clusterjoin automatisch de OS-software die overeenkomt met de versie op het cluster.

Lees [ **Cluster** > FXT-knooppunten](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) in de clusterconfiguratiehandleiding voor meer informatie over de opties op deze pagina.

## <a name="3-click-the-allow-to-join-button"></a>3. Klik op de knop 'Deelnemen toestaan' 

Klik **op**de knop Deelnemen aan * toestaan in de kolom **Acties** voor het knooppunt dat u wilt toevoegen.

Nadat u op de knop hebt geklikt, kan de status van het knooppunt veranderen omdat de software wordt bijgewerkt ter voorbereiding op het toevoegen ervan aan het cluster. 

De afbeelding hieronder toont een knooppunt dat in het proces van toetreding tot het cluster (waarschijnlijk, het is het krijgen van een OS-update voordat ze worden toegevoegd). Er worden geen knoppen weergegeven in de kolom **Acties** voor knooppunten die worden toegevoegd aan het cluster.

![een rij van de knooppunttabel met de naam van een knooppunt, IP-adres, softwareversie, het bericht 'Mag deelnemen' en een lege laatste kolom](media/fxt-cluster-config/node-join-in-process.png)

Na enkele ogenblikken moet het nieuwe knooppunt worden weergegeven in de lijst met clusterknooppunten boven aan de pagina **FXT-knooppunten.** 

Herhaal dit proces om de andere knooppunten aan uw cluster toe te voegen. U hoeft niet te wachten tot het ene knooppunt is voltooid om het cluster te sluiten voordat u een ander wordt gestart.

## <a name="enable-high-availability"></a>Hoge beschikbaarheid inschakelen

Nadat u een tweede knooppunt aan uw cluster hebt toegevoegd, ziet u mogelijk een waarschuwingsbericht op het dashboard van het configuratiescherm dat de functie met hoge beschikbaarheid niet is geconfigureerd. 

Met hoge beschikbaarheid (HA) kunnen de clusterknooppunten elkaar compenseren als er een uitvalt. HA is standaard niet ingeschakeld.

![Tabblad Dashboard met het bericht "Het cluster heeft meer dan één knooppunt, maar HA is niet ingeschakeld ..." in de tabel Voorwaarden](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Schakel HA niet in totdat u ten minste drie knooppunten in het cluster hebt.

Volg deze procedure om HA in te schakelen: 

1. De pagina **Hoge beschikbaarheid** laden in het **clustergedeelte** van het tabblad **Instellingen.**

   ![HA-configuratiepagina (Cluster > Hoge beschikbaarheid). Het selectievakje HA inschakelen staat bovenaan en de knop verzenden bevindt zich aan de onderkant.](media/fxt-cluster-config/enable-ha.png)

2. Klik op het vak met het label **HA inschakelen** en klik op **Verzenden.** 

Er verschijnt een waarschuwing op het **dashboard** om te bevestigen dat HA is ingeschakeld.

![Dashboardtabel met het bericht 'HA is nu volledig geconfigureerd'](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Volgende stappen

Nadat u alle knooppunten in uw cluster hebt toegevoegd, u de installatie voortzetten door de langetermijnopslag van uw cluster te configureren.

> [!div class="nextstepaction"]
> [Back-endopslag toevoegen en de virtuele naamruimte instellen](fxt-add-storage.md)