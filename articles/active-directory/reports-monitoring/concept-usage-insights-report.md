---
title: Het rapport gebruik en inzichten | Microsoft Docs
description: Inleiding tot het rapport over gebruik en inzichten in de Azure Active Directory Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b3db86137207ae726c7befc393f62590fd1456d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74008270"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Het rapport gebruik en inzichten in de Azure Active Directory Portal

Met het rapport gebruik en inzichten kunt u een toepassings gerichte weer gave van uw aanmeldings gegevens verkrijgen. U vindt antwoorden op de volgende vragen:

*   Wat zijn de meestgebruikte toepassingen in mijn organisatie?
*   Welke toepassingen hebben de meeste mislukte aanmeldingen? 
*   Wat zijn de belangrijkste aanmeldings fouten voor elke toepassing?

## <a name="prerequisites"></a>Vereisten 

Voor toegang tot de gegevens in het rapport gebruik en inzichten hebt u het volgende nodig:

* Een Azure AD-Tenant
* Een Azure AD Premium-licentie (P1/P2) voor het weer geven van de aanmeldings gegevens
* Een gebruiker in de rollen globale beheerder, beveiligings beheerder, beveiligings lezer of rapport lezers. Daarnaast hebben alle gebruikers (niet-beheerders) toegang tot hun eigen aanmeldingen. 

## <a name="access-the-usage-and-insights-report"></a>Het rapport gebruik en inzichten openen

1. Navigeer naar [Azure Portal](https://portal.azure.com).
2. Selecteer de juiste map, selecteer **Azure Active Directory** en kies **bedrijfs toepassingen**.
3. Selecteer in de sectie **activiteit** **gebruik & inzichten** om het rapport te openen. 

![Rapport over gebruik en inzichten weergeven](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Het rapport gebruiken

In het rapport gebruik en inzichten ziet u een lijst met toepassingen met een of meer aanmeldings pogingen, en kunt u sorteren op het aantal geslaagde aanmeldingen, mislukte aanmeldingen en het succes percentage.

Als u onder aan de lijst op laden klikt, kunt u aanvullende toepassingen op de pagina bekijken. U kunt het datum bereik selecteren om alle toepassingen weer te geven die binnen het bereik zijn gebruikt.

U kunt ook de focus instellen voor een specifieke toepassing. Selecteer **aanmeldings activiteit weer geven** om de aanmeldings activiteit in de loop van de tijd voor de toepassing en de belangrijkste fouten te bekijken.  

Wanneer u een dag selecteert in de grafiek voor het gebruik van de toepassing, krijgt u een gedetailleerde lijst van de aanmeldings activiteiten voor de toepassing.  

![Rapport over gebruik en inzichten weergeven](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Volgende stappen

* [Aanmeldingenrapport](concept-sign-ins.md)