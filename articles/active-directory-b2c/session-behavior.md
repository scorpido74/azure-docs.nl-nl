---
title: Gedrag van sessies configureren-Azure Active Directory B2C | Microsoft Docs
description: Het gedrag van sessies in Azure Active Directory B2C configureren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed10a9258590f8f9037a574bd0322a82dd309a5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385243"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Sessie gedrag in Azure Active Directory B2C configureren

Met [eenmalige aanmelding (SSO)-sessie](session-overview.md) beheer in Azure Active Directory B2C (Azure AD B2C) kan een beheerder de interactie met een gebruiker beheren nadat de gebruiker al is geverifieerd. De beheerder kan bijvoorbeeld bepalen of de selectie van id-providers wordt weer gegeven of dat de account gegevens opnieuw moeten worden ingevoerd. In dit artikel wordt beschreven hoe u de SSO-instellingen voor Azure AD B2C kunt configureren.

## <a name="session-behavior-properties"></a>Eigenschappen van sessie gedrag

U kunt de volgende eigenschappen gebruiken voor het beheren van sessies van webtoepassingen:

- **Levens duur van de web-app (minuten)** : de levens duur van de Azure AD B2C's-sessie cookie die is opgeslagen in de browser van de gebruiker wanneer de verificatie is geslaagd.
    - Standaard instelling = 1440 minuten.
    - Mini maal (inclusief) = 15 minuten.
    - Maximum (inclusief) = 1440 minuten.
- **Sessietime-out van web-app** -het [type sessie verloop](session-overview.md#session-expiry-type), *Rolling*of *absoluut*. 
- **Configuratie van eenmalige aanmelding** : het [sessie bereik](session-overview.md#session-scope) van het gedrag van eenmalige aanmelding (SSO) over meerdere apps en gebruikers stromen in uw Azure AD B2C-Tenant. 


## <a name="configure-the-properties"></a>De eigenschappen configureren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Azure AD B2C Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **gebruikers stromen (beleid)**.
5. Open de gebruikers stroom die u eerder hebt gemaakt.
6. Selecteer **Eigenschappen**.
7. Configureer de duur van de **Web-app-sessie (minuten)**, **time-out voor de web-app sessie**, **configuratie van eenmalige aanmelding**en **vereisen een id-token in afmeldings aanvragen** , indien nodig.

    ![Instellingen voor de eigenschappen van sessie gedrag in de Azure Portal](./media/session-behavior/session-behavior.png)

8. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure AD B2C sessie](session-overview.md).