---
title: Uw beveiligings postuur versterken met Azure Security Center | Microsoft Docs
description: Dit artikel helpt u bij het versterken van uw beveiligings postuur door uw resources te controleren in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: d18258d62267c931a39947611dda038ff6e65205
ms.sourcegitcommit: a8928136b49362448e992a297db1072ee322b7fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84718696"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>U beveiligingsstatus versterken met Azure Security Center
Dit artikel helpt u bij het versterken van uw beveiligings postuur. Gebruik de bewakings mogelijkheden in Azure Security Center om ervoor te zorgen dat uw resource beveiliging zo nauw keurig mogelijk is en de naleving van het beleid kan bewaken.

## <a name="how-do-you-strengthen-your-security-posture"></a>Hoe breidt u uw beveiligings postuur uit?
Bij het woord bewaking wordt misschien gesuggereerd dat er wordt gewacht tot een gebeurtenis plaatsvindt en dat er dan op de situatie wordt gereageerd. Het versterken van uw beveiligings postuur verwijst naar een proactieve strategie waarmee uw resources worden gecontroleerd om systemen te identificeren die niet voldoen aan de bedrijfs normen of aanbevolen procedures.

Nadat u een [beveiligingsbeleid](tutorial-security-policy.md) voor de resources van een abonnement hebt ingeschakeld, analyseert Security Center de beveiliging van uw resources om mogelijke beveiligingsproblemen op te sporen. Informatie over uw netwerkconfiguratie is onmiddellijk beschikbaar. Afhankelijk van het aantal virtuele machines en computers waarop de agent is geïnstalleerd, kan het een uur of langer duren voordat informatie is verzameld over configuratie van de VM's en de computer, zoals de status van de beveiligingsupdates en de configuratie van het besturingssysteem. U kunt een volledige lijst met problemen en manieren weer geven om uw netwerk te beveiligen en risico's te herstellen in de tegel **aanbevelingen** .

U kunt de beveiligings status van uw resources en eventuele problemen per resource type bekijken:

- Zie [uw computers en toepassingen beschermen in azure Security Center](security-center-virtual-machine-protection.md) om de status van uw computer bronnen en uw apps te controleren en aanbevelingen te ontvangen voor het verbeteren van de beveiliging.
- Als u uw netwerk bronnen, zoals virtuele machines, netwerk beveiligings groepen en-eind punten, wilt bewaken en aanbevelingen wilt ontvangen voor het verbeteren van de beveiliging, raadpleegt u [uw netwerk beveiligen in azure Security Center](security-center-network-recommendations.md) voor meer informatie. 
- Als u uw gegevens en opslag resources, zoals SQL-servers en opslag accounts, wilt bewaken en aanbevelingen wilt ontvangen voor het verbeteren van de beveiliging, raadpleegt u [Azure SQL-service en-gegevens in azure Security Center beveiligen](security-center-sql-service-recommendations.md) voor meer informatie. 
- Als u uw identiteits-en toegangs bronnen wilt bewaken, met inbegrip van MFA-en account machtigingen, en aanbevelingen wilt ontvangen voor het verbeteren van de beveiliging, raadpleegt u [identiteit en toegang controleren in azure Security Center](security-center-identity-access.md) voor meer informatie. 
- Zie [toegang tot virtuele machines beheren met Just-in-time](security-center-just-in-time.md) voor meer informatie om de just-in-time toegang tot uw resources te controleren.


Zie [Beveiligingsaanbevelingen implementeren in Azure Security Center](security-center-recommendations.md) voor meer informatie over het toepassen van aanbevelingen.



![De tegel Beveiligingsstatus van de resource](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Zie ook
In dit artikel hebt u kunnen lezen hoe u de bewakingsmogelijkheden in Azure Security Center gebruikt. Zie de volgende onderwerpen voor meer informatie over Azure Security Center:

* [Beveiligingsbeleid instellen in Azure Security Center](tutorial-security-policy.md): leer hoe u beveiligingsinstellingen configureert in Azure Security Center.
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
* [Partneroplossingen controleren met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt controleren.