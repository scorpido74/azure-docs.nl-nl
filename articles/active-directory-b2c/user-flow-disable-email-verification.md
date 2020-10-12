---
title: E-mail verificatie uitschakelen tijdens het aanmelden van de klant
titleSuffix: Azure AD B2C
description: Meer informatie over het uitschakelen van e-mail verificatie tijdens het aanmelden van een klant in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10613bd2d6219272248f882e45ae1c33d2cc9d61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85384206"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>E-mail verificatie uitschakelen tijdens het aanmelden van de klant in Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Volg deze stappen om de e-mail verificatie uit te scha kelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Gebruik het filter voor **adres lijst en abonnementen** in het bovenste menu om de map te selecteren die uw Azure AD B2C Tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **gebruikers stromen**.
1. Selecteer de gebruikers stroom waarvoor u de e-mail verificatie wilt uitschakelen. Bijvoorbeeld *B2C_1_signinsignup*.
1. Selecteer **pagina-indelingen**.
1. Selecteer de **pagina voor het registreren van een lokaal account**.
1. Onder **gebruikers kenmerken**selecteert u **e-mail adres**.
1. Selecteer in de vervolg keuzelijst **verificatie vereist** de optie **Nee**.
1. Selecteer **Opslaan**. E-mail verificatie is nu uitgeschakeld voor deze gebruikers stroom.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het aanpassen van de gebruikers interface in azure Active Directory B2C](customize-ui-overview.md)

