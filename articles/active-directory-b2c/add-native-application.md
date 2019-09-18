---
title: Een systeem eigen client toepassing toevoegen-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het toevoegen van een systeem eigen client toepassing aan uw Active Directory B2C-Tenant.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6fc953d5c6109fbc6eacbd946ecd112db4639fa5
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064594"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Een systeem eigen client toepassing toevoegen aan uw Azure Active Directory B2C-Tenant

Systeem eigen client bronnen moeten worden geregistreerd in uw Tenant voordat uw toepassing kan communiceren met Azure Active Directory B2C.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Toepassingen** en vervolgens **Toevoegen**.
2. Voer een naam in voor de toepassing. Bijvoorbeeld *nativeapp1*.
3. Selecteer voor **Web-app / web-API opnemen** **Nee**.
4. Selecteer **Systeemeigen client opnemen** **Ja**.
5. Voer voor **Omleidings-URI** een geldige omleidings-URI met een aangepast schema in. Er zijn twee belangrijke overwegingen bij het kiezen van een omleidings-URI:

    - **Uniek**: het schema van de omleidings-URI moet voor elke toepassing uniek zijn. In het voorbeeld `com.onmicrosoft.contoso.appname://redirect/path` is `com.onmicrosoft.contoso.appname` het schema. Dit patroon moet worden gevolgd. Als twee toepassingen hetzelfde schema delen, kan de gebruiker een toepassing kiezen. Als de gebruiker een foute keuze maakt, mislukt de aanmelding.
    - **Volledig**: de omleidings-URI moet een schema en een pad hebben. Het pad moet ten minste één slash na het domein bevatten. Bijvoorbeeld: `//contoso/` werkt en `//contoso` is mislukt. Zorg ervoor dat de omleidings-URI geen speciale tekens bevat, zoals onderstrepingstekens.

6. Klik op **Create**.
7. Noteer de toepassings-ID die u gebruikt wanneer u uw systeem eigen client toepassing configureert op de pagina Eigenschappen.
