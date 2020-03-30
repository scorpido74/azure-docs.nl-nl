---
title: Een web-API-toepassing toevoegen - Azure Active Directory B2C | Microsoft Documenten
description: Meer informatie over het toevoegen van een web-API-toepassing aan uw Active Directory B2C-tenant.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e6dbf3d6fd5a43ab2d075c193c5bc589dc3566a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190174"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Een web-API-toepassing toevoegen aan uw Azure Active Directory B2C-tenant

 Registreer webAPI-bronnen in uw tenant, zodat ze aanvragen kunnen accepteren en beantwoorden door clienttoepassingen die een toegangstoken presenteren. In dit artikel ziet u hoe u een web-API registreert in Azure Active Directory B2C (Azure AD B2C).

Als u een toepassing wilt registreren in uw Azure AD B2C-tenant, u de huidige **ervaring met toepassingen** of onze nieuwe uniforme **app-registratie (Preview)-ervaring** gebruiken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw tenant.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Toepassingen**en selecteer **Toevoegen**.
5. Voer een naam in voor de toepassing. Bijvoorbeeld *webapi1*.
6. Selecteer voor **Inclusief webtoepassing/ web-API** en **Impliciete stroom toestaan****Ja**.
7. Voer voor de **Antwoord-URL** een eindpunt in waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. In uw productietoepassing u de antwoord-URL `https://localhost:44332`instellen op een waarde zoals. Stel de antwoord-URL in `https://jwt.ms`op .
8. Voer voor **App-id-URI** de id in die wordt gebruikt voor uw web-API. De volledige id-URI, inclusief het domein, wordt voor u gegenereerd. Bijvoorbeeld `https://contosotenant.onmicrosoft.com/api`.
9. Klik **op Maken**.
10. Noteer op de eigenschappenpagina de toepassings-id die u gebruikt wanneer u de web-app configureert.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **App-registraties (Voorbeeld)** en selecteer **Vervolgens Nieuwe registratie**.
1. Voer een **naam** voor de toepassing in. Bijvoorbeeld *webapi1*.
1. Selecteer **onder Uri omleiden** **en**voer een eindpunt in waarin Azure AD B2C alle tokens die uw toepassing aanvraagt, moet retourneren. In een productietoepassing u de omleidings-URI instellen als `https://localhost:5000`. Tijdens het ontwikkelen of testen `https://jwt.ms`kunt u deze instellen op een in Microsoft eigendom van een microsoft-webtoepassing die de gedecodeerde inhoud van een token weergeeft (de inhoud van het token verlaat nooit uw browser). U op elk gewenst moment omleidings-URI's toevoegen en wijzigen in uw geregistreerde toepassingen.
1. Selecteer **Registreren**.
1. Neem de **toepassings-id (client) op** voor gebruik in de code van uw web-API.

Als u een toepassing hebt die de impliciete subsidiestroom implementeert, bijvoorbeeld een op JavaScript gebaseerde toepassing met één pagina (SPA), u de stroom inschakelen door de volgende stappen te volgen:

1. Selecteer onder **Beheren**de optie **Verificatie**.
1. Selecteer **Probeer de nieuwe ervaring** uit (indien weergegeven).
1. Schakel **onder Impliciete subsidie**de selectievakjes **Access-tokens** en **ID-tokens** in.
1. Selecteer **Opslaan**.

* * *

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Bijvoorbeeld: gebruikers van de web-API kunnen zowel lees- als schrijftoegang hebben of alleen leestoegang. In deze zelfstudie gebruikt u bereiken om lees- en schrijfmachtigingen voor de web-API te definiëren.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een app, moet u uw toepassing machtigingen geven voor de API. In [Zelfstudie: Een toepassing registreren in Azure Active Directory B2C,](tutorial-register-applications.md)wordt een webtoepassing met de naam *webapp1* geregistreerd in Azure AD B2C. U deze toepassing gebruiken om de web-API aan te roepen.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Uw toepassing is geregistreerd voor het aanroepen van de beveiligde web-API. Een gebruiker voert een verificatie uit bij Azure AD B2C om de toepassing te kunnen gebruiken. De toepassing verkrijgt een autorisatietoekenning van Azure AD B2C voor toegang tot de beveiligde web-API.
