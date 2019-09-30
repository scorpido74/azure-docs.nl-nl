---
title: Een web-app toevoegen-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het toevoegen van een webtoepassing aan uw Active Directory B2C-Tenant.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3114f082a402fd005797b270cfdcd7527784dbe1
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679224"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Een web-API-toepassing toevoegen aan uw Azure Active Directory B2C-Tenant

 Registreer Web-API-resources in uw Tenant, zodat ze aanvragen kunnen accepteren en hierop reageren door client toepassingen die een toegangs token aanbieden. In dit artikel wordt beschreven hoe u een toepassing registreert in Azure Active Directory B2C (Azure AD B2C).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Toepassingen** en vervolgens **Toevoegen**.
5. Voer een naam in voor de toepassing. Bijvoorbeeld *webapi1*.
6. Selecteer voor **Inclusief webtoepassing/ web-API** en **Impliciete stroom toestaan** **Ja**.
7. Voer voor de **Antwoord-URL** een eindpunt in waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. In uw productie toepassing kunt u de antwoord-URL instellen op een waarde zoals `https://localhost:44332`. Voor test doeleinden, stelt u de antwoord- `https://jwt.ms`URL in op.
8. Voer voor **App-id-URI** de id in die wordt gebruikt voor uw web-API. De volledige id-URI, inclusief het domein, wordt voor u gegenereerd. Bijvoorbeeld `https://contosotenant.onmicrosoft.com/api`.
9. Klik op **Create**.
10. Noteer op de eigenschappenpagina de toepassings-ID die u gebruikt wanneer u de webtoepassing configureert.

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Bijvoorbeeld: gebruikers van de web-API kunnen zowel lees- als schrijftoegang hebben of alleen leestoegang. In deze zelfstudie gebruikt u bereiken om lees- en schrijfmachtigingen voor de web-API te definiëren.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een app, moet u uw toepassing machtigingen geven voor de API. Bijvoorbeeld, in [Zelfstudie: Een toepassing registreren in azure Active Directory B2C](tutorial-register-applications.md), wordt er een webtoepassing gemaakt in azure AD B2C met de naam *webapp1*. U kunt deze toepassing gebruiken om de Web-API aan te roepen.

1. Selecteer **toepassingen**en selecteer vervolgens uw webtoepassing.
1. Selecteer **API-toegang** en vervolgens **Toevoegen**.
1. Selecteer *webapi1* in de vervolgkeuzelijst **API selecteren**.
1. Selecteer in de vervolg keuzelijst **bereiken selecteren** de bereiken die u eerder hebt gedefinieerd. Bijvoorbeeld: *demo. Read* en *demo. write*.
1. Selecteer **OK**.

Uw toepassing is geregistreerd voor het aanroepen van de beveiligde web-API. Een gebruiker voert een verificatie uit bij Azure AD B2C om de toepassing te kunnen gebruiken. De toepassing verkrijgt een autorisatietoekenning van Azure AD B2C voor toegang tot de beveiligde web-API.
