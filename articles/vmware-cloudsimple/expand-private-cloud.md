---
title: Azure VMware-oplossing uitbreiden met CloudSimple Private Cloud
description: Beschrijft hoe u een bestaande CloudSimple Private Cloud uitbreidt om capaciteit toe te voegen in een bestaand of nieuw cluster
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025296"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Een CloudSimple Private Cloud uitbreiden

CloudSimple biedt de flexibiliteit om een Private Cloud dynamisch uit te breiden. U beginnen met een kleinere configuratie en vervolgens uitbreiden als u een hogere capaciteit nodig hebt. Of u een Private Cloud maken op basis van de huidige behoeften en vervolgens uitbreiden naarmate het verbruik toeneemt.

Een Private Cloud bestaat uit een of meer vSphere-clusters. Elk cluster kan 3 tot 16 knooppunten hebben.  Wanneer u een Private Cloud uitbreidt, voegt u knooppunten toe aan het bestaande cluster of maakt u een nieuw cluster. Als u een bestaand cluster wilt uitbreiden, moeten extra knooppunten hetzelfde type (SKU) zijn als de bestaande knooppunten. Voor het maken van een nieuw cluster kunnen de knooppunten van een ander type zijn. Zie de sectie Limieten in het overzichtsartikel over private cloud van [CloudSimple](cloudsimple-private-cloud.md) voor meer informatie over private cloud-limieten.

Er wordt een private cloud gemaakt met een standaard **datacenter** op vCenter.  Elk datacenter fungeert als een top-level management entiteit.  Voor een nieuw cluster biedt CloudSimple de keuze om toe te voegen aan het bestaande datacenter of een nieuw datacenter te maken.

Als onderdeel van de nieuwe clusterconfiguratie configureert CloudSimple de VMware-infrastructuur.  De instellingen omvatten opslaginstellingen voor vSAN-schijfgroepen, VMware Hoge beschikbaarheid en Distributed Resource Scheduler (DRS).

Een Private Cloud kan meerdere keren worden uitgebreid. Uitbreiding kan alleen worden gedaan wanneer u binnen de algemene node grenzen blijft. Elke keer dat u een Private Cloud uitvouwt, voegt u toe aan het bestaande cluster of maakt u een nieuw cluster.

## <a name="before-you-begin"></a>Voordat u begint

Knooppunten moeten worden ingericht voordat u uw Private Cloud uitbreiden.  Zie [Knooppunten voor VMware-oplossing voor VMware door CloudSimple - Azure](create-nodes.md) voor meer informatie over het inrichten van knooppunten.  Voor het maken van een nieuw cluster moet u ten minste drie beschikbare knooppunten van dezelfde SKU hebben.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="expand-a-private-cloud"></a>Een privécloud uitbreiden

1. [Toegang tot de CloudSimple-portal](access-cloudsimple-portal.md).

2. Open de pagina **Resources** en selecteer de Private Cloud waarvoor u wilt uitbreiden.

3. Klik in het samengevatte gedeelte op **Uitvouwen**.

    ![Private cloud uitbreiden](media/resources-expand-private-cloud.png)

4. Kies of u uw bestaande cluster wilt uitbreiden of een nieuw vSphere-cluster wilt maken. Als u wijzigingen aanbrengt, wordt de overzichtsinformatie op de pagina bijgewerkt.

    * Als u uw bestaande cluster wilt uitbreiden, klikt u op **Bestaand cluster uitvouwen**. Selecteer het cluster dat u wilt uitbreiden en voer het aantal knooppunten in dat u wilt toevoegen. Elk cluster kan maximaal 16 knooppunten hebben.
    * Als u een nieuw cluster wilt toevoegen, klikt u op **Nieuw cluster maken**. Voer een naam in voor het cluster. Selecteer een bestaand datacenter of voer een naam in om een nieuw datacenter te maken. Kies het knooppunttype. U een ander knooppunttype kiezen bij het maken van een nieuw vSphere-cluster, maar niet bij het uitbreiden van een bestaand vSphere-cluster. Selecteer het aantal knooppunten. Elk nieuw cluster moet ten minste drie knooppunten hebben.

    ![Private cloud uitbreiden - knooppunten toevoegen](media/resources-expand-private-cloud-add-nodes.png)

5. Klik **op Verzenden** om de privécloud uit te vouwen.

## <a name="next-steps"></a>Volgende stappen

* [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* Meer informatie over [Private Clouds](cloudsimple-private-cloud.md)