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
ms.openlocfilehash: 10e1081c7a91c65c21ffcb3da66930fb7c6e73bb
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603444"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Bedreigingsinformatierapport in Azure Security Center
In dit document wordt uitgelegd hoe bedreigingsinformatierapporten in Azure Security Center u kunnen helpen meer te weten te komen over een bedreiging die een beveiligingswaarschuwing heeft gegenereerd.

## <a name="what-is-a-threat-intelligence-report"></a>Wat is een bedreigingsinformatierapport?
Het detecteren van bedreigingen van Security Center werkt volgens het verzamelen van beveiligingsgegevens van uw Azure-resources, het netwerk en verbonden partneroplossingen. Deze informatie wordt door Security Center geanalyseerd, waarbij vaak informatie uit meerdere bronnen wordt samengebracht om bedreigingen te analyseren. Zie [How Azure Security Center detecteert en reageert op bedreigingen](security-center-alerts-overview.md#detect-threats)voor meer informatie.

Wanneer Security Center een bedreiging detecteert, produceert het een [beveiligingswaarschuwing](security-center-managing-and-responding-alerts.md) met gedetailleerde informatie over een specifieke gebeurtenis en suggesties om het op te lossen. Ter ondersteuning van incident response teams, het onderzoeken en oplossen van bedreigingen, Security Center bevat een Threat Intelligence-rapport dat informatie bevat over de gedetecteerde bedreiging, inclusief informatie zoals:

* De identiteit of associaties van de aanvaller (indien beschikbaar)
* De doelstellingen van de aanvaller
* Huidige en eerdere aanvalscampagnes (indien beschikbaar)
* Tactieken, hulpprogram ma's en procedures van aanvallers
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

Dit type informatie is nuttig tijdens het respons proces van een incident, waarbij een voortdurend onderzoek wordt gedaan om de bron van de aanval, de motivatie van de aanvaller en wat te doen om het probleem te verhelpen.

## <a name="how-to-access-the-threat-intelligence-report"></a>Hoe open ik het bedreigingsinformatierapport?
U kunt uw huidige waarschuwingen controleren met de tegel **Beveiligingswaarschuwingen**. Open de Azure Portal en voer de volgende stappen uit om meer informatie over elke waarschuwing weer te geven:

1. Op het Security Center-dashboard ziet u de tegel **Beveiligingswaarschuwingen**.
2. Klik op de tegel om de blade **Beveiligingswaarschuwingen** te openen, die meer informatie over de waarschuwingen bevat. Klik vervolgens in de beveiligingswaarschuwing waarover u meer te weten wilt komen.

    ![Beveiligingswaarschuwingen](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. In dit geval toont de Blade **verdacht proces** de details over de waarschuwing, zoals wordt weer gegeven in de afbeelding hieronder:

    ![Details van beveiligingswaarschuwing](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. De hoeveelheid beschikbare informatie voor een beveiligingswaarschuwing is afhankelijk van het type waarschuwing. In het veld **rapporten** hebt u een koppeling naar het rapport Threat Intelligence. Als u hierop klikt, wordt er een nieuw browservenster geopend met een PDF-bestand.

   ![Opslagselectie](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Hier kunt u de PDF voor het rapport downloaden en meer lezen over het gedetecteerde beveiligingsprobleem, waarna u op basis van de informatie actie kunt ondernemen.

## <a name="see-also"></a>Zie ook
In dit document hebt u geleerd hoe beveiligingsinformatierapporten van Azure Security Center u kunnen helpen tijdens een onderzoek naar beveiligingswaarschuwingen. Zie de volgende onderwerpen voor meer informatie over het Azure Beveiligingscentrum:

* [Plannings- en bedieningsgids voor Azure Security Center](security-center-planning-and-operations-guide.md). Leer de ontwerpoverwegingen kennen en leer deze in te plannen als u de overstap naar Azure Security Center wilt maken.
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md). Informatie over het beheren van en reageren op beveiligingswaarschuwingen.
* [Beveiligingsincidenten afhandelen in Azure Security Center](security-center-incident.md)