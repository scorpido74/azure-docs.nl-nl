---
title: Aanbevelingen herstellen in Azure Security Center | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u aanbevelingen in Azure Security Center herstelt om uw resources te beschermen en te voldoen aan het beveiligings beleid.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 4bad3227e08c0fbe0d280967e45bbef9d477e1b3
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569132"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Aanbevelingen oplossen in Azure Security Center

Aanbevelingen bieden suggesties voor een betere beveiliging van uw resources. U implementeert een aanbeveling door de herstels tappen te volgen die worden beschreven in de aanbeveling.

## <a name="remediation-steps"></a>Herstels tappen <a name="remediation-steps"></a>

Nadat u alle aanbevelingen hebt bekeken, moet u bepalen welke u het eerst wilt herstellen. U wordt aangeraden de effect op een [beveiligde Score](security-center-recommendations.md#monitor-recommendations) te gebruiken om te helpen bepalen wat u eerst moet doen.

1. Klik in de lijst op de aanbeveling.

1. Volg de instructies in de sectie **herstel stappen** . Elke aanbeveling heeft een eigen set instructies. De volgende scherm afbeelding toont herbemiddelings stappen voor het configureren van toepassingen zodat alleen verkeer via HTTPS wordt toegestaan.

    ![Details van aanbeveling](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Zodra dit is voltooid, wordt een melding weer gegeven dat u wordt geïnformeerd als het herstel is geslaagd.

## <a name="quick-fix-remediation"></a>Snel herstel oplossen<a name="one-click"></a>

Met snelle oplossing kunt u snel een aanbeveling op meerdere resources herstellen. Het is alleen beschikbaar voor specifieke aanbevelingen. Snelle oplossing vereenvoudigt herstel en stelt u in staat snel uw beveiligde score te verhogen en de beveiliging van uw omgeving te verbeteren.

Voor het implementeren van herstel met snelle oplossingen:

1. In de lijst met aanbevelingen waarvoor de **snelle oplossing** is geïnstalleerd. Label, klikt u op de aanbeveling.

    [![Selecteer snelle oplossing.](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Op het tabblad **beschadigde bronnen** selecteert u de resources waarvoor u de aanbeveling wilt implementeren en klikt u op **herstellen**.

    > [!NOTE]
    > Sommige van de vermelde bronnen zijn mogelijk uitgeschakeld, omdat u niet over de juiste machtigingen beschikt om deze te wijzigen.

1. In het bevestigings venster leest u de details van het herstel en de implicaties.

    ![Snel oplossen](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > De implicaties worden weer gegeven in het grijze vak in het venster **resources herstellen** dat wordt geopend nadat u op **herstellen**hebt geklikt. Ze geven een lijst weer met de wijzigingen die worden aangebracht wanneer u doorgaat met het oplossen van problemen met snel herstel.

1. Voer indien nodig de relevante para meters in en goed keuring van het herstel.

    > [!NOTE]
    > Het kan enkele minuten duren voordat het herstel is voltooid om de resources te bekijken op het tabblad onterecht **resources** . Controleer het [activiteiten logboek](#activity-log)om de herstel acties weer te geven.

1. Zodra dit is voltooid, wordt een melding weer gegeven dat u wordt geïnformeerd als het herstel is geslaagd.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Snel herstel logboek registratie in het activiteiten logboek <a name="activity-log"></a>

Voor de herstel bewerking wordt gebruikgemaakt van een sjabloon implementatie of REST PATCH-API-aanroep om de configuratie van de bron toe te passen. Deze bewerkingen worden geregistreerd in het [Azure-activiteiten logboek](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u aanbevelingen in Security Center kunt herstellen. Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md) -meer informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen en-resource groepen.
* [Beveiligingsstatus controleren in Azure Security Center](security-center-monitoring.md): meer informatie over het controleren van de status van uw Azure-resources.
