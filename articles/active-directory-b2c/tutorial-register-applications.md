---
title: 'Zelfstudie: Een toepassing registreren'
titleSuffix: Azure AD B2C
description: Meer informatie over het registreren van een webtoepassing in Azure Active Directory B2C met behulp van de Azure-portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a688f5e75f7513d0ea4308b751f87f75a2c9510a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183088"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Zelfstudie: Een toepassing registreren in Azure Active Directory B2C

Voordat uw [toepassingen](application-types.md) kunnen communiceren met Azure Active Directory B2C (Azure AD B2C), moeten ze zijn geregistreerd in een tenant die u beheert. In deze zelfstudie ziet u hoe u een webtoepassing registreert met behulp van de Azure-portal.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een web-app registreren
> * Een klantgeheim maken

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u nog geen eigen [Azure AD B2C-tenant](tutorial-create-tenant.md)hebt gemaakt, maakt u er nu een. U een bestaande Azure AD B2C-tenant gebruiken.

## <a name="register-a-web-application"></a>Een web-app registreren

Als u een toepassing wilt registreren in uw Azure AD B2C-tenant, u de huidige **ervaring met toepassingen** of onze nieuwe uniforme **app-registratie (Preview)-ervaring** gebruiken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het pictogram **Directory + Abonnement** op de werkbalk van de portal en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Zoek en selecteer **Azure AD B2C**in de Azure-portal .
1. Selecteer **Toepassingen**en selecteer **Toevoegen**.
1. Voer een naam in voor de toepassing. *Webapp1*bijvoorbeeld .
1. Selecteer voor **Inclusief webtoepassing/ web-API** en **Impliciete stroom toestaan****Ja**.
1. Voer voor de **Antwoord-URL** een eindpunt in waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. U het bijvoorbeeld instellen om `https://localhost:44316`lokaal te luisteren op. Als u het poortnummer nog niet kent, u een tijdelijke aanduidingswaarde invoeren en deze later wijzigen.

    Voor testdoeleinden zoals deze zelfstudie `https://jwt.ms` u instellen op welke de inhoud van een token wordt weergegeven voor inspectie. Stel voor deze zelfstudie `https://jwt.ms`de URL van het **antwoord** in op .

    De volgende beperkingen zijn van toepassing op het beantwoorden van URL's:

    * De url van het `https`antwoord moet beginnen met de regeling .
    * De antwoord-URL is hoofdlettergevoelig. De case moet overeenkomen met het geval van het URL-pad van uw lopende toepassing. Als uw toepassing bijvoorbeeld als onderdeel `.../abc/response-oidc`van het `.../ABC/response-oidc` pad wordt weergegeven, geeft u dit niet op in de antwoord-URL. Omdat de webbrowser paden als hoofdlettergevoelig behandelt, `.../abc/response-oidc` kunnen cookies die zijn gekoppeld aan `.../ABC/response-oidc` cookies worden uitgesloten als ze worden doorgestuurd naar de URL die niet op de plaats van de aanvraag is gekoppeld.

1. Selecteer **Maken** om de toepassingsregistratie te voltooien.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het pictogram **Directory + Abonnement** op de werkbalk van de portal en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Zoek en selecteer **Azure AD B2C**in de Azure-portal .
1. Selecteer **App-registraties (Voorbeeld)** en selecteer **Vervolgens Nieuwe registratie**.
1. Voer een **naam** voor de toepassing in. *Webapp1*bijvoorbeeld .
1. Selecteer **Accounts in een organisatiemap of een identiteitsprovider.**
1. Selecteer **onder URI omleiden** **en**voer het `https://jwt.ms` tekstvak URL in.

    De omleiding uri is het eindpunt waarnaar de gebruiker wordt verzonden door de autorisatieserver (Azure AD B2C, in dit geval) na het voltooien van de interactie met de gebruiker, en waarnaar een toegangstoken of autorisatiecode wordt verzonden na een succesvolle autorisatie. In een productietoepassing is dit meestal een openbaar toegankelijk eindpunt waarbij `https://contoso.com/auth-response`uw app wordt uitgevoerd, zoals . Voor testdoeleinden zoals deze zelfstudie `https://jwt.ms`kunt u deze instellen op een in Microsoft eigendom van een webtoepassing die de gedecodeerde inhoud van een token weergeeft (de inhoud van het token verlaat nooit uw browser). Tijdens app-ontwikkeling u het eindpunt toevoegen waar uw `https://localhost:5000`toepassing lokaal luistert, zoals. U op elk gewenst moment omleidings-URI's toevoegen en wijzigen in uw geregistreerde toepassingen.

    De volgende beperkingen zijn van toepassing op omleiding van URI's:

    * De url van het `https`antwoord moet beginnen met de regeling .
    * De antwoord-URL is hoofdlettergevoelig. De case moet overeenkomen met het geval van het URL-pad van uw lopende toepassing. Als uw toepassing bijvoorbeeld als onderdeel `.../abc/response-oidc`van het `.../ABC/response-oidc` pad wordt weergegeven, geeft u dit niet op in de antwoord-URL. Omdat de webbrowser paden als hoofdlettergevoelig behandelt, `.../abc/response-oidc` kunnen cookies die zijn gekoppeld aan `.../ABC/response-oidc` cookies worden uitgesloten als ze worden doorgestuurd naar de URL die niet op de plaats van de aanvraag is gekoppeld.

1. Schakel **onder Machtigingen**het selectievakje *Beheerderstoestemming verlenen voor openid en offline_access machtigingen* in.
1. Selecteer **Registreren**.

Zodra de registratie van de aanvraag is voltooid, schakelt u de impliciete subsidiestroom in:

1. Selecteer onder **Beheren**de optie **Verificatie**.
1. Selecteer **Probeer de nieuwe ervaring** uit (indien weergegeven).
1. Schakel **onder Impliciete subsidie**de selectievakjes **Access-tokens** en **ID-tokens** in.
1. Selecteer **Opslaan**.

* * *

## <a name="create-a-client-secret"></a>Een klantgeheim maken

Als uw aanvraag een autorisatiecode voor een toegangstoken omwisselt, moet u een toepassingsgeheim maken.

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Selecteer op de pagina **Azure AD B2C - Toepassingen** de toepassing die u hebt gemaakt, bijvoorbeeld *webapp1*.
1. Selecteer **Toetsen** en selecteer **Vervolgens Toets genereren**.
1. Selecteer **Opslaan** om de sleutel weer te geven. Noteer de waarde van **App-sleutel**. U gebruikt deze waarde als het toepassingsgeheim in de code van uw toepassing.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Selecteer in de pagina **Azure AD B2C - App-registraties (Preview)** de toepassing die u hebt gemaakt, bijvoorbeeld *webapp1*.
1. Selecteer **onder Beheren**de optie Certificaten & **geheimen**.
1. Selecteer **Nieuw clientgeheim**.
1. Voer een beschrijving in voor het clientgeheim in het vak **Beschrijving.** Bijvoorbeeld *clientsecret1*.
1. Selecteer **onder Verloopt**een duur waarvoor het geheim geldig is en selecteer Vervolgens **Toevoegen**.
1. Leg de **waarde**van het geheim vast. U gebruikt deze waarde als het toepassingsgeheim in de code van uw toepassing.

* * *

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een web-app registreren
> * Een klantgeheim maken

Leer vervolgens hoe u gebruikersstromen maakt om uw gebruikers in staat te stellen zich aan te melden, zich aan te melden en hun profielen te beheren.

> [!div class="nextstepaction"]
> [Gebruikersstromen maken in Azure Active Directory B2C->](tutorial-create-user-flows.md)
