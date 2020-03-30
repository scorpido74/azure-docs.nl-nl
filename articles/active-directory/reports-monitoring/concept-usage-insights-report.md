---
title: Rapport gebruik en inzichten | Microsoft Documenten
description: Inleiding tot het rapport gebruik en inzichten in de Azure Active Directory-portal
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008270"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Rapport gebruik en inzichten in de Azure Active Directory-portal

Met het rapport gebruik en inzichten u een toepassingsgerichte weergave van uw aanmeldingsgegevens krijgen. Antwoorden op de volgende vragen vindt u:

*   Wat zijn de best gebruikte applicaties in mijn organisatie?
*   Welke toepassingen hebben de meeste mislukte aanmeldingen? 
*   Wat zijn de belangrijkste aanmeldingsfouten voor elke toepassing?

## <a name="prerequisites"></a>Vereisten 

Als u toegang wilt krijgen tot de gegevens uit het rapport gebruik en inzichten, hebt u het:

* Een Azure AD-tenant
* Een Azure AD Premium-licentie (P1/P2) om de aanmeldingsgegevens weer te geven
* Een gebruiker in de globale beheerder, beveiligingsbeheerder, beveiligingslezer of rapportlezerrollen. Bovendien heeft elke gebruiker (niet-beheerders) toegang tot zijn eigen aanmeldingen. 

## <a name="access-the-usage-and-insights-report"></a>Toegang tot het rapport gebruik en inzichten

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
2. Selecteer de juiste map, selecteer **Vervolgens Azure Active Directory** en kies **Enterprise-toepassingen**.
3. Selecteer **in** de sectie Activiteit de optie **Gebruik& inzichten** om het rapport te openen. 

![Rapport over gebruik en inzichten weergeven](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Het rapport gebruiken

Het rapport Gebruik en inzicht toont de lijst met toepassingen met een of meer aanmeldingspogingen en stelt u in staat om te sorteren op het aantal succesvolle aanmeldingen, mislukte aanmeldingen en het slagingspercentage.

Als u onder aan de lijst op laden klikt, u aanvullende toepassingen op de pagina bekijken. U het datumbereik selecteren om alle toepassingen weer te geven die binnen het bereik zijn gebruikt.

U ook de focus instellen op een specifieke toepassing. Selecteer **aanmeldingsactiviteit weergeven** om de aanmeldingsactiviteit in de loop van de tijd voor de toepassing en de topfouten te bekijken.  

Wanneer u een dag selecteert in de grafiek met toepassingsgebruik, krijgt u een gedetailleerde lijst met de aanmeldingsactiviteiten voor de toepassing.  

![Rapport over gebruik en inzichten weergeven](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Volgende stappen

* [Aanmeldingenrapport](concept-sign-ins.md)