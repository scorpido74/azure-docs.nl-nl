---
title: Azure Sentinel verwijderen| Microsoft Documenten
description: Uw Azure Sentinel-exemplaar verwijderen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581681"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Azure Sentinel uit uw werkruimte verwijderen

Als u de Azure Sentinel niet meer wilt gebruiken, wordt in dit artikel uitgelegd hoe u deze uit uw werkruimte verwijderen.

## <a name="how-to-remove-azure-sentinel"></a>Azure Sentinel verwijderen

Volg dit proces om Azure Sentinel uit uw werkruimte te verwijderen:

1. Ga naar **Azure Sentinel,** gevolgd door **Instellingen**en selecteer het tabblad **Azure Sentinel verwijderen**.

1. Voordat u Azure Sentinel verwijdert, gebruikt u de selectievakjes om ons te laten weten waarom u de sentinel verwijdert.

1. Selecteer **Azure Sentinel verwijderen uit uw werkruimte**.
    
    ![De oplossing SecurityInsights verwijderen](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Wat gebeurt er achter de schermen?

Wanneer u de oplossing verwijdert, duurt het tot 48 uur voordat Azure Sentinel de eerste fase van het verwijderingsproces voltooit.

Nadat de ontkoppeling is geïdentificeerd, begint het offboarding-proces.

**De configuratie van deze connectors wordt verwijderd:**
-   Office 365

-   AWS

-   Beveiligingswaarschuwingen voor Microsoft-services (Azure ATP, Microsoft Cloud App Security inclusief Cloud Discovery Shadow IT-rapportage, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Bedreigingsinformatie

-   Algemene beveiligingslogboeken (inclusief CEF-logboeken, Barracuda en Syslog) (Als u Azure Security Center hebt, worden deze logboeken nog steeds verzameld.)

-   Windows Security Events (Als u Azure Security Center hebt, worden deze logboeken nog steeds verzameld.)

Binnen de eerste 48 uur zijn de gegevens- en analytische regels (inclusief realtime automatiseringsconfiguratie) niet langer toegankelijk of queryable in Azure Sentinel.

**Na 30 dagen worden deze bronnen verwijderd:**

-   Incidenten (inclusief metagegevens van onderzoeken)

-   Analytische regels

-   Bladwijzers

Uw playbooks, opgeslagen werkmappen, opgeslagen jachtquery's en notitieblokken worden niet verwijderd. **Sommige kunnen breken als gevolg van de verwijderde gegevens. U deze handmatig verwijderen.**

Nadat u de service hebt verwijderd, is er een respijtperiode van 30 dagen waarin u de oplossing opnieuw inschakelen en uw gegevens en analytische regels worden hersteld, maar de geconfigureerde connectors die zijn losgekoppeld, moeten opnieuw worden aangesloten.

> [!NOTE]
> Als u de oplossing verwijdert, blijft uw abonnement worden geregistreerd bij de Azure Sentinel-bronprovider. **U het handmatig verwijderen.**




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u de Azure Sentinel-service verwijderen. Als u van gedachten verandert en het opnieuw wilt installeren:
- Aan de slag [met azure sentinel instappen](quickstart-onboard.md).
