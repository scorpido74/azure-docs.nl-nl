---
title: Uw beveiligingshouding versterken met Azure Security Center | Microsoft Documenten
description: In dit artikel u uw beveiligingshouding versterken door uw resources in Azure Security Center te controleren.
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
ms.openlocfilehash: 40352b7ae1f3fb6b1178f2dfe70cdca9871c76b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603766"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>U beveiligingsstatus versterken met Azure Security Center
Dit artikel helpt u uw beveiligingshouding te versterken. Gebruik de bewakingsmogelijkheden in Azure Security Center om ervoor te zorgen dat uw bronbeveiliging zo strak mogelijk is en de naleving van het beleid te controleren.

## <a name="how-do-you-strengthen-your-security-posture"></a>Hoe versterkt u uw veiligheidshouding?
Bij het woord bewaking wordt misschien gesuggereerd dat er wordt gewacht tot een gebeurtenis plaatsvindt en dat er dan op de situatie wordt gereageerd. Het versterken van uw beveiligingshouding verwijst naar het hebben van een proactieve strategie die uw resources controleert om systemen te identificeren die niet voldoen aan de organisatienormen of best practices.

Nadat u een [beveiligingsbeleid](tutorial-security-policy.md) voor de resources van een abonnement hebt ingeschakeld, analyseert Security Center de beveiliging van uw resources om mogelijke beveiligingsproblemen op te sporen. Informatie over uw netwerkconfiguratie is onmiddellijk beschikbaar. Afhankelijk van het aantal virtuele machines en computers waarop de agent is geïnstalleerd, kan het een uur of langer duren voordat informatie is verzameld over configuratie van de VM's en de computer, zoals de status van de beveiligingsupdates en de configuratie van het besturingssysteem. U een volledige lijst met problemen en manieren bekijken om uw netwerk te verharden en risico's te herstellen in de tegel **Aanbevelingen.**

U de beveiligingsstatus van uw resources en eventuele problemen per resourcetype bekijken:

- Zie Uw machines en toepassingen beveiligen in Azure Security Center controleren als u de status van uw computerbronnen en uw apps wilt controleren en aanbevelingen wilt ontvangen voor het verbeteren van de beveiliging [ervan.](security-center-virtual-machine-protection.md)
- Zie Uw netwerk beveiligen [in Azure Security Center](security-center-network-recommendations.md) voor meer informatie om uw netwerkbronnen, zoals virtuele machines, netwerkbeveiligingsgroepen en eindpunten, te controleren en aanbevelingen te ontvangen voor het verbeteren van hun beveiliging. 
- Zie [Azure SQL-service en -gegevens beveiligen in Azure Security Center](security-center-sql-service-recommendations.md) voor meer informatie om uw gegevens en opslagbronnen, zoals SQL-servers en opslagaccounts, te controleren en aanbevelingen te ontvangen voor het verbeteren van hun beveiliging. 
- Zie Identiteit en toegang controleren [in Azure Security Center](security-center-identity-access.md) voor meer informatie om uw identiteit en toegangsbronnen, waaronder MFA- en accountmachtigingen, te controleren en aanbevelingen te ontvangen voor het verbeteren van hun beveiliging. 
- Zie Toegang tot [virtuele machines beheren met just-in-time](security-center-just-in-time.md) beheren voor meer informatie om just-in-time toegang tot uw resources te controleren. 


Zie [Beveiligingsaanbevelingen implementeren in Azure Security Center](security-center-recommendations.md) voor meer informatie over het toepassen van aanbevelingen.



![De tegel Beveiligingsstatus van de resource](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Zie ook
In dit artikel hebt u kunnen lezen hoe u de bewakingsmogelijkheden in Azure Security Center gebruikt. Zie de volgende onderwerpen voor meer informatie over Azure Security Center:

* [Beveiligingsbeleid instellen in Azure Security Center](tutorial-security-policy.md): leer hoe u beveiligingsinstellingen configureert in Azure Security Center.
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
* [Partneroplossingen controleren met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt controleren.