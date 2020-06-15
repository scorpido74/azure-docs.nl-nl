---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c594dbab51c46c382c21b4d87595cd7322f6036
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84464992"
---
Ga naar het opslagaccount waarvoor u het openbare eindpunt wilt beperken tot bepaalde virtuele netwerken. Selecteer in de inhoudsopgave voor het opslagaccount **Firewalls en virtuele netwerken**. 

Selecteer bovenaan de pagina het keuzerondje **Geselecteerde netwerken**. Hierdoor komen er een aantal instellingen beschikbaar voor het beperken van de toegang tot het openbare eindpunt. Klik op **+ Bestaand virtueel netwerk toevoegen** om het specifieke virtuele netwerk te selecteren dat toegang mag hebben tot het opslagaccount via het openbare eindpunt. Hiervoor moet u een virtueel netwerk en een subnet selecteren voor dat virtuele netwerk. 

Selecteer **Vertrouwde Microsoft-services toegang geven tot dit serviceaccount** om vertrouwde Microsoft-services, zoals Azure File Sync, toegang te geven tot het opslagaccount.

[![Schermopname van de blade Firewalls en virtuele netwerken waarop een bepaald virtueel netwerk toegang heeft gekregen tot het opslagaccount via het openbare eindpunt](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)