---
title: Open de Privécloud van Azure VMware Solutions (AVS)
description: Hierin wordt beschreven hoe u een bestaande AVS-Privécloud uitbreidt om capaciteit toe te voegen aan een bestaand of nieuw cluster
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3286b7537056a6c2f282533aa629ebbe47612690
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025296"
---
# <a name="expand-an-avs-private-cloud"></a>Een Privécloud met een persoonlijke Cloud uitbreiden

AVS biedt de flexibiliteit voor het dynamisch uitbreiden van een Privécloud. U kunt beginnen met een kleinere configuratie en vervolgens uitbreiden naarmate u meer capaciteit nodig hebt. U kunt ook een Privécloud maken op basis van huidige behoeften en vervolgens uitvouwen naarmate het verbruik groeit.

Een automatische AVS-Cloud bestaat uit een of meer vSphere-clusters. Elk cluster kan 3 tot 16 knoop punten bevatten. Wanneer u een nieuwe AVS-Cloud uitbreidt, voegt u knoop punten toe aan het bestaande cluster of maakt u een nieuw cluster. Als u een bestaand cluster wilt uitbreiden, moeten extra knoop punten van hetzelfde type (SKU) zijn als de bestaande knoop punten. Voor het maken van een nieuw cluster kunnen de knoop punten van een ander type zijn. Zie de sectie limieten in het [overzichts](cloudsimple-private-cloud.md) artikel van de AVS-privécloud voor meer informatie over de limieten voor de privécloud in de Cloud.

Een automatische AVS-Cloud wordt gemaakt met een standaard **datacenter** op vCenter. Elk Data Center fungeert als een beheer entiteit op het hoogste niveau. Voor een nieuw cluster biedt AVS de keuze van toevoegen aan het bestaande Data Center of het maken van een nieuw Data Center.

Als onderdeel van de nieuwe cluster configuratie, configureert AVS de VMware-infra structuur. De instellingen omvatten opslag instellingen voor vSAN-schijf groepen, VMware-hoge Beschik baarheid en gedistribueerde resource planner (DRS).

Een automatische AVS-Cloud kan meerdere keren worden uitgebreid. Uitbrei ding kan alleen worden uitgevoerd als u binnen de algehele knooppunt limieten blijft. Telkens wanneer u een nieuwe AVS-Cloud uitbreidt, voegt u deze toe aan het bestaande cluster of maakt u een nieuw.

## <a name="before-you-begin"></a>Voordat u begint

Knoop punten moeten worden ingericht voordat u de Privécloud van uw AVS kunt uitbreiden. Zie voor meer informatie over het inrichten van knoop punten [richt knooppunten inrichten voor VMware-oplossing door AVS-Azure-](create-nodes.md) artikel. Voor het maken van een nieuw cluster moeten er ten minste drie beschik bare knoop punten van dezelfde SKU zijn.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-an-avs-private-cloud"></a>Een Privécloud met een persoonlijke Cloud uitbreiden

1. [Toegang tot de AVS-Portal](access-cloudsimple-portal.md).

2. Open de pagina **resources** en selecteer de privécloud die u wilt uitbreiden.

3. Klik in de sectie samen vatting op **uitvouwen**.

    ![De persoonlijke cloud van AVS uitbreiden](media/resources-expand-private-cloud.png)

4. Kies of u uw bestaande cluster wilt uitbreiden of een nieuw vSphere-cluster wilt maken. Wanneer u wijzigingen aanbrengt, wordt de samenvattings informatie op de pagina bijgewerkt.

    * Als u uw bestaande cluster wilt uitbreiden, klikt u op **bestaand cluster uitvouwen**. Selecteer het cluster dat u wilt uitbreiden en voer het aantal knoop punten in dat u wilt toevoegen. Elk cluster kan Maxi maal 16 knoop punten bevatten.
    * Klik op **Nieuw cluster maken**om een nieuw cluster toe te voegen. Voer een naam in voor het cluster. Selecteer een bestaand Data Center of voer een naam in om een nieuw Data Center te maken. Kies het knooppunt type. U kunt een ander type knoop punt kiezen bij het maken van een nieuw vSphere-cluster, maar niet bij het uitbreiden van een bestaand vSphere-cluster. Selecteer het aantal knoop punten. Elk nieuw cluster moet ten minste drie knoop punten hebben.

    ![Open de automatische AVS-Cloud en voeg knoop punten toe](media/resources-expand-private-cloud-add-nodes.png)

5. Klik op **verzenden** om de automatische AVS-Cloud uit te vouwen.

## <a name="next-steps"></a>Volgende stappen

* [VMware-Vm's in azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* Meer informatie over [persoonlijke Clouds van AVS](cloudsimple-private-cloud.md)