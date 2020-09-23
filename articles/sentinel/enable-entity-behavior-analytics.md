---
title: Gebruik de analyse van entiteits gedrag om geavanceerde bedreigingen te detecteren | Microsoft Docs
description: Gedrags analyse van gebruikers en entiteiten inschakelen in azure Sentinel en gegevens bronnen configureren
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995493"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>UEBA (User and entity Behavior Analytics) inschakelen in azure Sentinel 



## <a name="prerequisites"></a>Vereisten

- Aan uw gebruiker moet de rol van **globale beheerder** of **beveiligings beheerder** zijn toegewezen in azure AD om UEBA in of uit te scha kelen, maar niet om het programma uit te voeren.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>De analyse van gebruikers-en entiteits gedrag inschakelen

1. Selecteer in het navigatie menu van de Azure-Sentinel het **item entiteit gedrag (preview-versie)**.

1. Schakel onder de kop **deze in** **op aan.**

1. Klik op de knop **gegevens bronnen selecteren** .

1. Schakel in het selectie deel venster **gegevens bron** de selectie vakjes in naast de gegevens bronnen waarvoor u UEBA wilt inschakelen en selecteer vervolgens **Toep assen**.

    > [!NOTE]
    >
    > In de onderste helft van het **selectie deel venster gegevens bron** ziet u een lijst met UEBA-ondersteunde gegevens bronnen die u nog niet hebt ingeschakeld. 
    >
    > Wanneer u UEBA hebt ingeschakeld, hebt u de optie om nieuwe gegevens bronnen te verbinden, zodat ze rechtstreeks vanuit het deel venster gegevens connector kunnen worden UEBA als deze UEBA-compatibel zijn.

1. Selecteer **Ga naar entiteit zoeken**. Hiermee gaat u naar het deel venster entiteit zoeken, dat vanaf nu wordt weer geven wanneer u **entiteit gedrag** kiest in het hoofd menu van Azure.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u UEBA (User and entity Behavior Analytics) inschakelt en configureert in azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
