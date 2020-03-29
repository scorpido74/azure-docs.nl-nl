---
title: Bedreigingsinformatierapport Azure Security Center | Microsoft Docs
description: In dit document wordt uitgelegd hoe u bedreigingsinformatierapporten in Azure Security Center gebruikt tijdens een onderzoek om meer informatie over een beveiligingswaarschuwing te krijgen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: f8b4063d87fa9a89dccd42eddea644609bd6ff27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921246"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Bedreigingsinformatierapport in Azure Security Center
In dit document wordt uitgelegd hoe bedreigingsinformatierapporten in Azure Security Center u kunnen helpen meer te weten te komen over een bedreiging die een beveiligingswaarschuwing heeft gegenereerd.

## <a name="what-is-a-threat-intelligence-report"></a>Wat is een bedreigingsinformatierapport?
Beveiliging van bedreigingen in security center werkt door beveiligingsgegevens van uw Azure-bronnen, het netwerk en verbonden partneroplossingen te bewaken. Deze informatie wordt door Security Center geanalyseerd, waarbij vaak informatie uit meerdere bronnen wordt samengebracht om bedreigingen te analyseren. Zie [Hoe Azure Security Center bedreigingen detecteert en reageert](security-center-alerts-overview.md#detect-threats)voor meer informatie.

Wanneer Security Center een bedreiging detecteert, produceert het een [beveiligingswaarschuwing](security-center-managing-and-responding-alerts.md) met gedetailleerde informatie over een specifieke gebeurtenis en suggesties om het op te lossen. Om incidentresponseteams te helpen bij het onderzoeken en verhelpen van bedreigingen, bevat Security Center een rapport over bedreigingsinformatie dat informatie bevat over de bedreiging die is gedetecteerd, inclusief informatie zoals:

* De identiteit of associaties van de aanvaller (indien beschikbaar)
* De doelstellingen van de aanvaller
* Huidige en eerdere aanvalscampagnes (indien beschikbaar)
* De tactieken, gereedschappen en procedures van aanvallers
* Gekoppelde indicators of compromise (IoC) zoals URL's en bestands-hashes
* Victimologie, de prevalentie qua branche en geografische locatie, om u te helpen bepalen of uw Azure-resources risico lopen
* Informatie over risicobeperking en herstel

> [!NOTE]
> De hoeveelheid informatie in het rapport kan variëren; de gedetailleerdheid van de informatie is afhankelijk van de activiteit en de gangbaarheid van de malware.
>
>

Security Center heeft drie soorten bedreigingsrapporten, afhankelijk van het soort aanval. De beschikbare rapporten zijn:

* **Activiteitsgroeprapport**: biedt diepgaande informatie over aanvallers en hun doelstellingen en tactieken.
* **Campagnerapport**: gericht op details van een specifieke aanvalscampagne.
* **Bedreigingsoverzichtsrapport**: dekt alle onderwerpen in de voorgaande twee rapporten.

Dit soort informatie is nuttig tijdens het incident respons proces, waar er een lopend onderzoek naar de bron van de aanval, de aanvaller motivaties, en wat te doen om dit probleem vooruit te beperken begrijpen.

## <a name="how-to-access-the-threat-intelligence-report"></a>Hoe open ik het bedreigingsinformatierapport?
U kunt uw huidige waarschuwingen controleren met de tegel **Beveiligingswaarschuwingen**. Open de Azure-portal en volg de onderstaande stappen om meer details over elke waarschuwing te bekijken:

1. Op het Security Center-dashboard ziet u de tegel **Beveiligingswaarschuwingen**.
2. Klik op de tegel om de blade **Beveiligingswaarschuwingen** te openen, die meer informatie over de waarschuwingen bevat. Klik vervolgens in de beveiligingswaarschuwing waarover u meer te weten wilt komen.

    ![Beveiligingswaarschuwingen](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. In dit geval toont het **door verdachte proces uitgevoerd** blad de details over de waarschuwing zoals weergegeven in de onderstaande afbeelding:

    ![Details van beveiligingswaarschuwing](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. De hoeveelheid beschikbare informatie voor een beveiligingswaarschuwing is afhankelijk van het type waarschuwing. In het veld **RAPPORTEN** hebt u een link naar het rapport bedreigingsinformatie. Als u hierop klikt, wordt er een nieuw browservenster geopend met een PDF-bestand.

   ![Opslagselectie](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Hier kunt u de PDF voor het rapport downloaden en meer lezen over het gedetecteerde beveiligingsprobleem, waarna u op basis van de informatie actie kunt ondernemen.

## <a name="see-also"></a>Zie ook
In dit document hebt u geleerd hoe beveiligingsinformatierapporten van Azure Security Center u kunnen helpen tijdens een onderzoek naar beveiligingswaarschuwingen. Zie de volgende onderwerpen voor meer informatie over Azure Security Center:

* [Handleiding voor planning en bewerkingen van azure Security Center](security-center-planning-and-operations-guide.md). Leer de ontwerpoverwegingen kennen en leer deze in te plannen als u de overstap naar Azure Security Center wilt maken.
* [Beveiligingswaarschuwingen beheren en beantwoorden in Azure Security Center](security-center-managing-and-responding-alerts.md). Informatie over het beheren van en reageren op beveiligingswaarschuwingen.
* [Beveiligingsincidenten afhandelen in Azure Security Center](security-center-incident.md)