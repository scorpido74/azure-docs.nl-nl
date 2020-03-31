---
title: E-mailverificatie uitschakelen tijdens het aanmelden van klanten
titleSuffix: Azure AD B2C
description: Meer informatie over het uitschakelen van e-mailverificatie tijdens het aanmelden van klanten in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 369b4e13baa344a71a51b358ef810d1a66b4b6ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126746"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>E-mailverificatie uitschakelen tijdens het aanmelden van klanten in Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Volg de volgende stappen om e-mailverificatie uit te schakelen:

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Gebruik het **filter Directory + abonnement** in het bovenste menu om de map te selecteren die uw Azure AD B2C-tenant bevat.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **Gebruikersstromen**.
1. Selecteer de gebruikersstroom waarvoor u e-mailverificatie wilt uitschakelen. Bijvoorbeeld *B2C_1_signinsignup*.
1. Selecteer **Pagina-indelingen**.
1. Selecteer **Aanmeldingspagina Lokaal account**selecteren .
1. Selecteer **E-mailadres**onder **Gebruikerskenmerken**.
1. Selecteer **nee**in de vervolgkeuzelijst **Verificatie vereist** .
1. Selecteer **Opslaan**. Verificatie via e-mail is nu uitgeschakeld voor deze gebruikersstroom.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [aanpassen van de gebruikersinterface in Azure Active Directory B2C](customize-ui-overview.md)

