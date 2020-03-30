---
title: Beveiligingsincidenten beheren in Azure Security Center | Microsoft Documenten
description: Met dit document u Azure Security Center gebruiken om beveiligingsincidenten te beheren.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415669"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Beveiligingsincidenten beheren in Azure Security Center

Triage en het onderzoeken van beveiligingswaarschuwingen kan tijdrovend zijn voor zelfs de meest ervaren security analisten, en voor velen is het moeilijk om zelfs te weten waar te beginnen. Met behulp van [analytics](security-center-detection-capabilities.md) om de informatie tussen afzonderlijke [beveiligingswaarschuwingen](security-center-managing-and-responding-alerts.md) te verbinden kan het Beveiligingscentrum u één overzicht geven van de aanval en alle gerelateerde waarschuwingen - zo begrijpt u snel wat de aanvaller deed en welke resources betrokken zijn.

In dit onderwerp wordt uitgelegd hoe incidenten in Het Beveiligingscentrum kunnen worden gebruikt en hoe u hun waarschuwingen herstellen.

## <a name="what-is-a-security-incident"></a>Wat is een beveiligingsincident?

In Security Center is een beveiligingsincident een samenloop van alle waarschuwingen voor een resource die overeenstemt met [kill chain](alerts-reference.md#intentions)-patronen. Incidenten worden weergegeven in de lijst [Beveiligingswaarschuwingen.](security-center-managing-and-responding-alerts.md) Klik op een incident om de gerelateerde waarschuwingen te bekijken, waardoor u meer informatie krijgen over elke gebeurtenis.

## <a name="managing-security-incidents"></a>Beveiligingsincidenten beheren

1. Klik op het dashboard Beveiligingscentrum op de tegel **Beveiligingswaarschuwingen.** De incidenten en waarschuwingen worden vermeld. Merk op dat de beschrijving van een beveiligingsincident een ander pictogram heeft vergeleken met andere waarschuwingen.

    ![Beveiligingsincidenten weergeven](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Als u details wilt bekijken, klikt u op een incident. Het beveiligingsincident dat het mes **is gedetecteerd,** geeft meer details weer. De sectie **Algemene informatie** kan inzicht geven in wat de beveiligingswaarschuwing heeft geactiveerd. Het toont informatie zoals de doelbron, bron-IP-adres (indien van toepassing), als de waarschuwing nog actief is en aanbevelingen over hoe te herstellen.  

    ![Reageren op beveiligingsincidenten in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Klik op een waarschuwing om meer informatie over elke waarschuwing te verkrijgen. Het herstel dat door Security Center wordt voorgesteld, verschilt per beveiligingswaarschuwing.

   > [!NOTE]
   > Dezelfde waarschuwing kan bestaan als onderdeel van een incident, evenals om zichtbaar te zijn als een standalone alert.

    ![Meldingsdetails](./media/security-center-incident/security-center-incident-alert.png)

1. Volg de herstelstappen die voor elke waarschuwing worden gegeven.


## <a name="see-also"></a>Zie ook
In dit document hebt u kunnen lezen hoe u de mogelijkheid beveiligingsincidenten in Azure Security Center gebruikt. Zie voor gerelateerde informatie het volgende:

* [Bescherming tegen bedreigingen in Azure Security Center](threat-protection.md)
* [Beveiligingswaarschuwingen in Azure Security Center](security-center-alerts-overview.md)
* [Beveiligingswaarschuwingen beheren](security-center-managing-and-responding-alerts.md)