---
title: Azure AD Identity Protection gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Azure AD Identity Protection gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2019
ms.author: rkarlin
ms.openlocfilehash: 5d73ce12d509b2ccc534e19a27124980f3b60044
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158897"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Verbinding maken met gegevens van Azure AD Identity Protection



U kunt Logboeken van [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) naar Azure Sentinel streamen om waarschuwingen te streamen naar Azure Sentinel om Dash boards weer te geven, aangepaste waarschuwingen te maken en het onderzoek te verbeteren. Azure Active Directory Identity Protection biedt een geconsolideerde weer gave van risico gebruikers, risico detecties en beveiligings problemen, met de mogelijkheid om het risico onmiddellijk op te lossen en beleids regels in te stellen om toekomstige gebeurtenissen automatisch te herstellen. De service is gebouwd op basis van de ervaring van micro soft met het beschermen van consumenten identiteiten en het verbeteren van een enorme nauw keurigheid van het signaal van meer dan 13.000.000.000 logboeken per dag. 


## <a name="prerequisites"></a>Vereisten

- U moet een [licentie voor Azure Active Directory Premium P1 of P2](https://azure.microsoft.com/pricing/details/active-directory/) hebben
- Gebruiker met de machtigingen globale beheerder of beveiligings beheerder


## <a name="connect-to-azure-ad-identity-protection"></a>Verbinding maken met Azure AD Identity Protection

Als u Azure AD Identity Protection al hebt, controleert u of deze is [ingeschakeld in uw netwerk](../active-directory/identity-protection/overview-identity-protection.md).
Als Azure AD Identity Protection is geïmplementeerd en gegevens ophaalt, kunnen de waarschuwings gegevens eenvoudig worden gestreamd naar Azure Sentinel.


1. Selecteer in azure Sentinel **Data connectors** en klik vervolgens op de tegel **Azure AD Identity Protection** .

2. Klik op **verbinding maken** om Azure AD Identity Protection gebeurtenissen te streamen naar Azure Sentinel.


6. Als u het relevante schema in Log Analytics voor de Azure AD Identity Protection waarschuwingen wilt gebruiken, zoekt u naar **SecurityAlert**.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure AD Identity Protection kunt verbinden met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
