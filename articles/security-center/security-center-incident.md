---
title: Beveiligings incidenten beheren in Azure Security Center | Microsoft Docs
description: Dit document helpt u bij het gebruik van Azure Security Center voor het beheren van beveiligings incidenten.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415669"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Beveiligings incidenten beheren in Azure Security Center

Het sorteren en onderzoeken van beveiligings waarschuwingen kan enige tijd in beslag nemen voor zelfs de meest ervaren beveiligings analisten, en in veel gevallen is het lastig om zelfs te weten waar u moet beginnen. Met behulp van [analytics](security-center-detection-capabilities.md) om de informatie tussen afzonderlijke [beveiligingswaarschuwingen](security-center-managing-and-responding-alerts.md) te verbinden kan het Beveiligingscentrum u één overzicht geven van de aanval en alle gerelateerde waarschuwingen - zo begrijpt u snel wat de aanvaller deed en welke resources betrokken zijn.

In dit onderwerp vindt u informatie over incidenten in Security Center en hoe u de waarschuwingen herstelt.

## <a name="what-is-a-security-incident"></a>Wat is een beveiligingsincident?

In Security Center is een beveiligingsincident een samenloop van alle waarschuwingen voor een resource die overeenstemt met [kill chain](alerts-reference.md#intentions)-patronen. Incidenten worden weer gegeven in de lijst met [beveiligings waarschuwingen](security-center-managing-and-responding-alerts.md) . Klik op een incident om de gerelateerde waarschuwingen weer te geven, waarmee u meer informatie over elk exemplaar kunt verkrijgen.

## <a name="managing-security-incidents"></a>Beveiligingsincidenten beheren

1. Klik op het Security Center dash board op de tegel **beveiligings waarschuwingen** . De incidenten en waarschuwingen worden weer gegeven. Merk op dat de beschrijving van een beveiligingsincident een ander pictogram heeft vergeleken met andere waarschuwingen.

    ![Beveiligings incidenten weer geven](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Als u details wilt weer geven, klikt u op een incident. Op de Blade **beveiligings incident gedetecteerd** wordt meer informatie weer gegeven. De sectie **algemene informatie** kan inzicht bieden in wat de beveiligings waarschuwing heeft geactiveerd. Hierin worden gegevens weer gegeven, zoals de doel resource, het bron-IP-adres (indien van toepassing), als de waarschuwing nog steeds actief is en aanbevelingen voor het oplossen van problemen.  

    ![Reageren op beveiligings incidenten in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Klik op een waarschuwing om meer informatie over elke waarschuwing te krijgen. Het herstel dat door Security Center wordt voorgesteld, verschilt per beveiligingswaarschuwing.

   > [!NOTE]
   > Dezelfde waarschuwing kan bestaan als onderdeel van een incident, en als een zelfstandige waarschuwing worden weer gegeven.

    ![Meldingsdetails](./media/security-center-incident/security-center-incident-alert.png)

1. Volg de stappen voor herstel die zijn opgegeven voor elke waarschuwing.


## <a name="see-also"></a>Zie ook
In dit document hebt u kunnen lezen hoe u de mogelijkheid beveiligingsincidenten in Azure Security Center gebruikt. Zie het volgende voor meer informatie:

* [Bescherming tegen bedreiging in Azure Security Center](threat-protection.md)
* [Beveiligingswaarschuwingen in Azure Security Center](security-center-alerts-overview.md)
* [Beveiligingswaarschuwingen beheren](security-center-managing-and-responding-alerts.md)