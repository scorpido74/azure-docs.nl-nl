---
title: Aanvullende koppelingen over Avere vFXT voor Azure
description: Koppelingen naar aanvullende informatie over Avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78e0500057baf407f1d26afe29d9df1e525cbf3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153765"
---
# <a name="additional-documentation"></a>Aanvullende documentatie

In dit artikel vindt u links naar aanvullende documentatie over de beheerinterface van het Configuratiescherm en aanverwante onderwerpen.

## <a name="avere-cluster-documentation"></a>Documentatie van Avere-cluster

Aanvullende Avere cluster documentatie is te <https://azure.github.io/Avere/>vinden op de website op . Met deze documenten u inzicht krijgen in de mogelijkheden van het cluster en hoe u de instellingen configureren.

* De [FXT Cluster Creation Guide](<https://azure.github.io/Avere/#fxt_cluster>) is ontworpen voor clusters bestaande uit fysieke hardwareknooppunten, maar sommige informatie in het document is ook relevant voor vFXT-clusters. Met name nieuwe vFXT-clusterbeheerders kunnen profiteren van het lezen van deze secties:
  * [Als u ondersteunings- en bewakingsinstellingen aanpast,](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) wordt uitgelegd hoe u de instellingen voor het uploaden van ondersteuning aanpassen en externe bewaking inschakelen.
  * [Het configureren van VServers en Global Namespace](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) heeft informatie over het maken van een clientgerichte naamruimte.
  * [Als u DNS voor het Avere-cluster configureert,](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) wordt uitgelegd hoe u round-robin DNS configureert.
  * [Back-end storagedocumenten toevoegen](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) om kernfilers toe te voegen.

* De [clusterconfiguratiehandleiding](<https://azure.github.io/Avere/#operations>) is een volledige verwijzing naar de instellingen en opties voor een Avere-cluster. Een vFXT-cluster maakt gebruik van een subset van deze opties, maar de meeste van dezelfde configuratiepagina's zijn van toepassing.

* In [de dashboardhandleiding](<https://azure.github.io/Avere/#operations>) wordt uitgelegd hoe u de clusterbewakingsfuncties van het Configuratiescherm van Avere gebruiken.

## <a name="vfxt-creation-and-management-documentation"></a>vFXT-documentatie voor creatie en beheer

Een volledige handleiding voor het gebruik van vfxt.py, een op scripts gebaseerde hulpprogramma voor het maken en beheren van cloudcluster, wordt geleverd op GitHub: [Cloudclusterbeheer met vfxt.py.](https://github.com/Azure/AvereSDK/blob/master/docs/README.md)
