---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74005561"
---
In dit artikel worden richtlijnen voor het bouwen van krachtige toepassingen met Azure Premium Storage. U de instructies in dit document gebruiken in combinatie met aanbevolen procedures voor prestaties die van toepassing zijn op technologieën die door uw toepassing worden gebruikt. Om de richtlijnen te illustreren, hebben we SQL Server gebruikt die op Premium Storage wordt uitgevoerd als voorbeeld in dit document.

Hoewel we prestatiescenario's voor de opslaglaag in dit artikel aanpakken, moet u de toepassingslaag optimaliseren. Als u bijvoorbeeld een SharePoint Farm host op Azure Premium Storage, u de SQL Server-voorbeelden uit dit artikel gebruiken om de databaseserver te optimaliseren. Optimaliseer bovendien de webserver en toepassingsserver van de SharePoint Farm om de meeste prestaties te krijgen.

In dit artikel u de volgende veelgestelde vragen beantwoorden over het optimaliseren van de toepassingsprestaties op Azure Premium Storage,

* Hoe meet je de prestaties van je toepassing?  
* Waarom zie je geen verwachte hoge prestaties?  
* Welke factoren beïnvloeden uw toepassingsprestaties op Premium Storage?  
* Hoe beïnvloeden deze factoren de prestaties van uw toepassing op Premium Storage?  
* Hoe u optimaliseren voor IOPS, Bandbreedte en Latency?  

We hebben deze richtlijnen speciaal voor Premium Storage verstrekt, omdat workloads die op Premium Storage worden uitgevoerd, zeer prestatiegevoelig zijn. Waar nodig hebben we voorbeelden gegeven. U een aantal van deze richtlijnen ook toepassen op toepassingen die worden uitgevoerd op IaaS VM's met standaardopslagschijven.