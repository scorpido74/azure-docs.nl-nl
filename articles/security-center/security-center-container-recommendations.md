---
title: Aanbevelingen voor containers in Azure Security Center | Microsoft Docs
description: In dit document worden de Azure Security Center aanbevelingen voor het beveiligen van uw containers uitgelegd.
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
ms.openlocfilehash: 96efe5271b91f87e30ba26aabe69a0de76e1a791
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202391"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Aanbevelingen voor Azure Security Center container begrijpen

Bij het migreren van uw op-toepassingen voor het uitvoeren van bedrijfskritische, container eigen Cloud toepassingen in productie, kunt u profiteren van de functies van containers, waaronder eenvoudige en snelle implementatie en update. Naarmate het aantal geïmplementeerde containers blijft toenemen, moeten er beveiligings oplossingen aanwezig zijn om u inzicht te geven in de beveiligings status van uw containers en om ze te beschermen tegen bedreigingen.

Azure Security Center biedt de volgende mogelijkheden om u te helpen bij het beveiligen van uw containers:

- **Zicht baarheid van containers die worden gehost op IaaS Linux-machines**<br>Op het tabblad containers in Azure Security Center worden alle virtuele machines weer gegeven die zijn geïmplementeerd met docker. Bij het verkennen van de beveiligings problemen op een virtuele machine biedt Security Center aanvullende informatie met betrekking tot de containers op de machine, zoals docker-versie en het aantal installatie kopieën die op de host worden uitgevoerd.

    ![tabblad container](./media/security-center-container-recommendations/docker-recommendation.png)


- **Aanbevelingen voor beveiliging op basis van CIS-benchmark test voor docker**<br>Security Center scant uw Docker-configuraties en geeft u inzicht in onjuiste configuraties door een lijst op te stellen met alle mislukte regels die zijn beoordeeld. Security Center biedt richt lijnen die u helpen bij het snel oplossen van deze problemen en het besparen van tijd. Docker-configuraties worden doorlopend beoordeeld door Security Center en u ziet steeds wat de actuele status is.

    ![tabblad container](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Realtime detectie van container bedreigingen**<br> Security Center biedt in realtime detectie van bedreigingen voor uw containers op Linux-machines met gecontroleerde onderdelen. De waarschuwingen identificeren verschillende verdachte docker-activiteiten, zoals het maken van een geprivilegieerde container op de host, een indicatie van een SSH-server (Secure Shell) die binnen een docker-container wordt uitgevoerd of het gebruik van crypto-Miners. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

    ![tabblad container](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Aanbevelingen
Gebruik de onderstaande tabellen als referentie om inzicht te krijgen in de beschik bare containers die worden gehost op IaaS Linux-machines en de beveiligings evaluatie van hun docker-configuraties.

| Aanbeveling | Description | Herstel |
| --- | --- | --- |
|Beveiligings problemen in container beveiligings configuraties oplossen |Beveiligings problemen in container beveiligings configuraties oplossen op basis van aanbevolen procedures voor de configuratie.| Beveiligings problemen oplossen in de container beveiligings configuraties:<br>1. Bekijk de lijst met mislukte regels.<br>2. Corrigeer elke regel volgens de opgegeven instructies.|


## <a name="next-steps"></a>Volgende stappen
Zie de volgende onderwerpen voor meer informatie over aanbevelingen die betrekking hebben op andere typen Azure-resources:

* [Identiteit en toegang bewaken in Azure Security Center](security-center-identity-access.md)
* [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)
* [Beveiligen van uw Azure SQL-service in Azure Security Center](security-center-sql-service-recommendations.md)

Zie de volgende onderwerpen voor meer informatie over het Beveiligingscentrum:

* [Uw machines en toepassingen beveiligen in Azure Security Center](security-center-virtual-machine-protection.md)
* [Setting security policies in Azure Security Center](tutorial-security-policy.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
* [Azure Security Center FAQ](security-center-faq.md) (Veelgestelde vragen over Azure Security Center): raadpleeg veelgestelde vragen over het gebruik van de service.

