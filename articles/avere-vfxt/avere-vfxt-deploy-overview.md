---
title: Implementatie overzicht-avere vFXT voor Azure
description: Overzicht van de implementatie van AVERE vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 9684b230b8790e36cd7442c65481c0c71ce185d6
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255422"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Overzicht van AVERE vFXT voor Azure-implementatie

In dit artikel vindt u een overzicht van de stappen die nodig zijn om een avere vFXT voor Azure-cluster actief te krijgen.

Er zijn verschillende taken nodig voor en nadat u het vFXT-cluster hebt gemaakt op de Azure Marketplace. Een duidelijke indruk van het begin-to-finish-proces helpt u de inspanningen te bereiken die nodig zijn. 

## <a name="deployment-steps"></a>Implementatiestappen

Nadat u [uw systeem hebt gepland](avere-vfxt-deploy-plan.md), kunt u beginnen met het maken van uw avere vFXT-cluster. 

Een Azure Resource Manager sjabloon in de Azure Marketplace verzamelt de benodigde gegevens en implementeert automatisch het hele cluster. 

Nadat het vFXT-cluster actief is, wilt u weten hoe u clients kunt verbinden en, indien nodig, hoe u uw gegevens naar de nieuwe Blob Storage-container kunt verplaatsen.  

Hier volgt een overzicht van alle stappen.

1. Vereisten configureren 

   Voordat u een VM maakt, moet u een nieuw abonnement maken voor het avere vFXT-project, het eigendom van het abonnement configureren, quota's controleren en zo nodig een verhoging aanvragen en voor waarden voor het gebruik van de avere vFXT-software accepteren. Lees [de voor bereiding voor het maken van de avere-vFXT](avere-vfxt-prereqs.md) voor gedetailleerde instructies.

1. Het avere vFXT-cluster maken 

   Gebruik Azure Marketplace om de avere vFXT voor Azure-cluster te maken. Een sjabloon verzamelt de vereiste informatie en voert scripts uit om het eind product te maken.

   Het maken van een cluster omvat de volgende stappen, die allemaal worden uitgevoerd door de Marketplace-sjabloon: 

   * Zo nodig nieuwe netwerk infrastructuur en-resource groepen maken
   * Een *cluster controller* maken  

     De cluster controller is een eenvoudige VM die zich in hetzelfde virtuele netwerk bevindt als het avere vFXT-cluster en beschikt over de aangepaste software die nodig is voor het maken en beheren van het cluster. De controller maakt de vFXT-knoop punten en vormt het cluster en maakt ook een opdracht regel interface voor het beheren van het cluster tijdens de levens duur.

     Als u tijdens de implementatie een nieuw vnet maakt, heeft uw controller een openbaar IP-adres. Dit betekent dat de controller als een Jump host kan fungeren voor het maken van verbinding met het cluster van buiten het vnet.

   * De virtuele machines van het cluster knooppunt maken

   * De virtuele machines van het cluster knooppunt configureren om het cluster te vormen

   * U kunt eventueel een nieuwe BLOB-container maken en deze configureren als back-end-opslag voor het cluster

1. Het cluster configureren 

   Maak verbinding met de configuratie-interface van AVERE vFXT (avere in het configuratie scherm) om de instellingen van het cluster aan te passen. Meld u aan voor ondersteunings bewaking en voeg uw opslag systeem toe als u een on-premises Data Center gebruikt.

   * [Access the vFXT cluster](avere-vfxt-cluster-gui.md) (Toegang tot het vFXT-cluster)
   * [Ondersteuning inschakelen](avere-vfxt-enable-support.md)
   * [Opslag configureren](avere-vfxt-add-storage.md) (indien nodig)

1. Clients koppelen

   Volg de richt lijnen in [mount the avere vFXT cluster](avere-vfxt-mount-clients.md) voor meer informatie over taak verdeling en hoe client computers het cluster moeten koppelen.

1. Gegevens toevoegen (indien nodig)

   Omdat de avere-vFXT een schaal bare cache voor meerdere clients is, is de beste manier om gegevens naar een nieuwe back-end-opslag container te verplaatsen, met een multi-client en een strategie voor het kopiëren van bestanden met meerdere threads. Lees [gegevens naar het vFXT-cluster verplaatsen](avere-vfxt-data-ingest.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Ga door met [de voor bereiding voor het maken van de avere vFXT](avere-vfxt-prereqs.md) voor het volt ooien van de voorbereidende taken voor het implementeren van de avere VFXT voor Azure. 