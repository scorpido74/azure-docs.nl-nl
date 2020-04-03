---
title: Azure AD-gegevens over identiteitsbeveiliging verbinden met Azure Sentinel
description: Meer informatie over het verbinden van Azure AD Identity Protection-gegevens met Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: b82ddfef57efaaca0ae43750cd306a63a772b911
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616825"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Gegevens verbinden vanuit Azure AD-identiteitsbeveiliging



U logboeken van [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) naar Azure Sentinel streamen om waarschuwingen naar Azure Sentinel te streamen om dashboards te bekijken, aangepaste waarschuwingen te maken en onderzoek te verbeteren. Azure Active Directory Identity Protection biedt een geconsolideerde weergave van risicogebruikers, risicodetecties en kwetsbaarheden, met de mogelijkheid om risico's onmiddellijk te verhelpen en beleid in te stellen om toekomstige gebeurtenissen automatisch te herstellen. De service is gebaseerd op de ervaring van Microsoft om de identiteit van consumenten te beschermen en krijgt een enorme nauwkeurigheid van het signaal van meer dan 13 miljard aanmeldingen per dag. 


## <a name="prerequisites"></a>Vereisten

- U moet een [Azure Active Directory Premium P1- of P2-licentie hebben](https://azure.microsoft.com/pricing/details/active-directory/)
- Gebruiker met machtigingen voor globale beheerders of beveiligingsbeheerders


## <a name="connect-to-azure-ad-identity-protection"></a>Verbinding maken met Azure AD-identiteitsbeveiliging

Als u al Azure AD-identiteitsbeveiliging hebt, controleert u of deze is [ingeschakeld in uw netwerk.](../active-directory/identity-protection/overview-identity-protection.md)
Als Azure AD Identity Protection wordt geïmplementeerd en gegevens worden opgevraagd, kunnen de waarschuwingsgegevens eenvoudig worden gestreamd naar Azure Sentinel.


1. Selecteer in Azure Sentinel **gegevensconnectors** en klik vervolgens op de tegel **Azure AD-identiteitsbeveiliging.**

2. Klik **op Verbinding maken** om Azure AD Identity Protection-gebeurtenissen te streamen naar Azure Sentinel.


6. Als u het relevante schema wilt gebruiken in Logboekanalyse voor de Azure AD-identiteitsbeveiliging-waarschuwingen, zoekt u **naar SecurityAlert**.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure AD-identiteitsbeveiliging verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
