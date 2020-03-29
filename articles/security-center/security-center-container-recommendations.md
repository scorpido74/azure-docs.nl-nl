---
title: Containeraanbevelingen in Azure Security Center | Microsoft Documenten
description: In dit document worden de aanbevelingen van het Azure Security Center uitgelegd voor het beveiligen van uw containers.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: memildin
ms.openlocfilehash: b1e1402f58b103570d6a98a5f9a01c8168abf749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77912357"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Aanbevelingen van Azure Security Center voor containers

Terwijl u uw monoliettoepassingen migreert om bedrijfskritieke, containergestuurde cloud-native toepassingen in productie uit te voeren, u profiteren van de functies van containers, waaronder eenvoudige en snelle implementatie en update. Naarmate het aantal ingezette containers blijft toenemen, moeten er beveiligingsoplossingen zijn om u inzicht te geven in de veiligheidsstatus van uw containers en hen te beschermen tegen bedreigingen.

Azure Security Center biedt de volgende mogelijkheden om u te helpen uw containers te beveiligen:

- **Inzicht in containers gehost op IaaS Linux machines**<br>In Azure Security Center worden op het tabblad Containers alle virtuele machines weergegeven die met Docker zijn geïmplementeerd. Bij het verkennen van de beveiligingsproblemen op een virtuele machine biedt Security Center aanvullende informatie met betrekking tot de containers op de machine, zoals docker-versie en het aantal afbeeldingen dat op de host wordt uitgevoerd.

    ![tabblad container](./media/security-center-container-recommendations/docker-recommendation.png)


- **Beveiligingsaanbevelingen op basis van CIS-benchmark voor Docker**<br>Security Center scant uw Docker-configuraties en geeft u inzicht in onjuiste configuraties door een lijst op te stellen met alle mislukte regels die zijn beoordeeld. Security Center biedt richtlijnen om u te helpen deze problemen snel op te lossen en tijd te besparen. Docker-configuraties worden doorlopend beoordeeld door Security Center en u ziet steeds wat de actuele status is.

    ![tabblad container](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Real-time bescherming tegen containerbedreigingen**<br> Security Center biedt real-time bescherming tegen bedreigingen voor uw containers op Linux-machines met auditd-component. De waarschuwingen identificeren verschillende verdachte Docker-activiteiten, zoals het maken van een bevoorrechte container op host, een indicatie van Secure Shell (SSH) server die in een Docker-container wordt uitgevoerd of het gebruik van crypto-miners. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

    ![tabblad container](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Aanbevelingen
Gebruik de onderstaande tabellen als referentie om u te helpen inzicht te krijgen in de beschikbare containers die worden gehost op IaaS Linux-machines en de beveiligingsbeoordeling van hun Docker-configuraties.

| Aanbeveling | Beschrijving | Herstel |
| --- | --- | --- |
|Kwetsbaarheden in containerbeveiligingsconfiguraties oplossen |Verwerk kwetsbaarheden in containerbeveiligingsconfiguraties op basis van aanbevolen procedures voor configuratie.| Ga als bedoeld als het gaat om het oplossen van kwetsbaarheden in de beveiligingsconfiguraties van containers:<br>1. Bekijk de lijst met mislukte regels.<br>2. Bevestig elke regel volgens de opgegeven instructies.|


## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor meer informatie over aanbevelingen die van toepassing zijn op andere Azure-brontypen:

* [Identiteit en toegang bewaken in Azure Security Center](security-center-identity-access.md)
* [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)
* [Uw Azure SQL-service beveiligen in Azure Security Center](security-center-sql-service-recommendations.md)

Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Uw machines en toepassingen beveiligen in Azure Security Center](security-center-virtual-machine-protection.md)
* [Setting security policies in Azure Security Center](tutorial-security-policy.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Beveiligingswaarschuwingen beheren en beantwoorden in Azure Security Center:](security-center-managing-and-responding-alerts.md) meer informatie over het beheren en reageren op beveiligingswaarschuwingen.