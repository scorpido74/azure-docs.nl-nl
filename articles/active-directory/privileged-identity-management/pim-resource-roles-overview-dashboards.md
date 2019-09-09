---
title: Een resource dashboard gebruiken om een toegangs beoordeling uit te voeren in PIM-Azure Active Directory | Microsoft Docs
description: Hierin wordt beschreven hoe u een resource dashboard gebruikt voor het uitvoeren van een toegangs beoordeling in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e759ba47c16617aa1783ce6fb0e324aa62ee96d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804110"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Een resource dashboard gebruiken om een toegangs beoordeling in PIM uit te voeren

U kunt een resource dashboard gebruiken om een toegangs beoordeling uit te voeren in Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Het dash board van de beheer weergave bevat drie primaire onderdelen:

- Een grafische weer gave van de activering van een resource functie.
- Twee grafieken waarin de verdeling van roltoewijzingen per toewijzings type wordt weer gegeven.
- Een gegevens gebied met betrekking tot nieuwe roltoewijzingen.

![Scherm opname van het dash board van de beheer weergave, met grafieken en grafieken](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Scherm opname van het dash board van de beheer weergave, met gegevens lijsten](media/pim-resource-roles-overview-dashboards/role-settings.png)

De grafische weer gave van de functie activeringen van de resource behandelt de afgelopen zeven dagen. Deze gegevens zijn gericht op de geselecteerde resource en weer gave-activeringen voor de meest voorkomende rollen (eigenaar, Inzender, gebruikers toegangs beheer) en voor alle rollen gecombineerd.

Rechts van de grafiek activeringen worden in twee grafieken de verdeling van roltoewijzingen per toewijzings type weer gegeven voor gebruikers en groepen. U kunt de waarde wijzigen in een percentage (of andersom) door een segment van de grafiek te selecteren.

Onder de grafieken ziet u het aantal gebruikers en groepen met nieuwe roltoewijzingen in de afgelopen 30 dagen, en een lijst met rollen gesorteerd op totaal aantal toewijzingen (in aflopende volg orde).

## <a name="next-steps"></a>Volgende stappen

- [Een toegangsbeoordeling starten voor mijn Azure AD-resourcerollen in PIM](pim-resource-roles-start-access-review.md) 
