---
title: Overzicht van implementatie - Avere vFXT voor Azure
description: Overzicht van de implementatie van Avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78140fea74272dff6056bebfbd44ed9d55b0e1db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153680"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT voor Azure - overzicht van implementatie

In dit artikel vindt u een overzicht van de stappen die nodig zijn om een Avere vFXT voor Azure-cluster operationeel te krijgen.

Er zijn verschillende taken nodig voor en nadat u het vFXT-cluster hebt gemaakt vanuit de Azure Marketplace. Het hebben van een duidelijk gevoel van het begin-tot-finish proces zal u helpen de omvang van de inspanning die nodig is.

## <a name="deployment-steps"></a>Implementatiestappen

Na [het plannen van uw systeem](avere-vfxt-deploy-plan.md)u beginnen met het maken van uw Avere vFXT-cluster.

Een Azure Resource Manager-sjabloon in azure marketplace verzamelt de benodigde informatie en implementeert automatisch het hele cluster.

Nadat het vFXT-cluster operationeel is, zijn er nog enkele configuratiestappen die moeten worden genomen voordat u het gebruikt. Als u een nieuwe Blob-opslagcontainer hebt gemaakt, wilt u uw gegevens naar deze container verplaatsen. Als u een NAS-opslagsysteem gebruikt, moet u het toevoegen nadat het cluster is gemaakt. U wilt clients verbinden met het cluster.

Hier is een overzicht van alle stappen.

1. Vereisten configureren

   Voordat u een VM maakt, moet u een nieuw abonnement maken voor het Avere vFXT-project, het eigendom van abonnementen configureren, quota controleren en indien nodig een verhoging aanvragen en voorwaarden accepteren voor het gebruik van de Avere vFXT-software. Lees [Bereid je voor om de Avere vFXT te maken](avere-vfxt-prereqs.md) voor gedetailleerde instructies.

1. Het Avere vFXT-cluster maken

   Gebruik de Azure Marketplace om het Avere vFXT voor Azure-cluster te maken. Een sjabloon verzamelt de vereiste informatie en voert scripts uit om het eindproduct te maken.

   Bij het maken van clusteren zijn deze stappen betrokken, die allemaal worden uitgevoerd door de marketplace-sjabloon:

   * Indien nodig nieuwe netwerkinfrastructuur- en resourcegroepen maken
   * Een clustercontroller maken

     De clustercontroller is een eenvoudige VM die zich in hetzelfde virtuele netwerk bevindt als het Avere vFXT-cluster en de aangepaste software heeft die nodig is om het cluster te maken en te beheren. De controller maakt de vFXT-knooppunten en vormt het cluster, en biedt ook een command-line interface om het cluster te beheren tijdens de levensduur.

     Als u tijdens de implementatie een nieuw virtueel netwerk of subnet maakt, heeft uw controller een openbaar IP-adres. Dit betekent dat de controller kan dienen als een jump host voor het verbinden met het cluster van buiten het virtuele netwerk.

   * Vm's voor clusterknooppunten maken

   * Het cluster maken op de afzonderlijke knooppunten

   * Maak eventueel een nieuwe Blob-container en configureer deze als back-endopslag voor het cluster

   Clustercreatie wordt in detail beschreven in [Deploy the vFXT cluster](avere-vfxt-deploy.md).

1. Het cluster configureren

   Maak verbinding met de Avere vFXT-configuratie-interface (Avere Control Panel) om de instellingen van het cluster aan te passen. U zich aanmelden voor ondersteuningsbewaking en uw opslagsysteem toevoegen als u hardwareopslag of extra Blob-containers gebruikt.

   * [Access the vFXT cluster](avere-vfxt-cluster-gui.md) (Toegang tot het vFXT-cluster)
   * [Ondersteuning inschakelen](avere-vfxt-enable-support.md)
   * [Opslag configureren](avere-vfxt-add-storage.md) (indien nodig)

1. Clients koppelen

   Volg de richtlijnen in [Mount the Avere vFXT cluster](avere-vfxt-mount-clients.md) om meer te weten te komen over load balancing en hoe clientmachines het cluster moeten monteren.

1. Gegevens toevoegen (indien nodig)

   Omdat de Avere vFXT een schaalbare multi-client cache is, is de beste manier om gegevens naar een nieuwe back-end opslagcontainer te verplaatsen met een multi-client, multi-threaded bestandskopieerstrategie.

   Als u werksetgegevens moet verplaatsen naar een nieuwe Blob-container of een ander back-endopslagsysteem, volgt u de instructies in [Gegevens verplaatsen naar het vFXT-cluster.](avere-vfxt-data-ingest.md)

## <a name="next-steps"></a>Volgende stappen

Ga verder [met Voorbereiden om de Avere vFXT](avere-vfxt-prereqs.md) te maken om de vereiste taken te voltooien.
