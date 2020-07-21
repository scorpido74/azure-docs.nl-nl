---
title: 'Zelf studie: een toepassing registreren'
titleSuffix: Azure AD B2C
description: Meer informatie over het registreren van een webtoepassing in Azure Active Directory B2C met behulp van de Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ac8baf0e2aa9555efb6d6b4f39787d9a7bf9508
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499899"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>Zelf studie: een webtoepassing registreren in Azure Active Directory B2C

Voordat uw [toepassingen](application-types.md) kunnen communiceren met Azure Active Directory B2C (Azure AD B2C), moeten ze zijn geregistreerd in een Tenant die u beheert. Deze zelf studie laat zien hoe u een webtoepassing registreert met behulp van de Azure Portal.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een web-app registreren
> * Een clientgeheim maken

Als u in plaats daarvan een systeem eigen app gebruikt (bijvoorbeeld iOS, Android, mobiel & bureau blad), leert u [hoe u een systeem eigen client toepassing kunt registreren](add-native-application.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u nog geen eigen [Azure AD B2C Tenant](tutorial-create-tenant.md)hebt gemaakt, maakt u er nu een. U kunt een bestaande Azure AD B2C-Tenant gebruiken.

## <a name="register-a-web-application"></a>Een web-app registreren

Als u een toepassing wilt registreren in de Azure AD B2C-tenant, kunt u de nieuwe uniforme ervaring voor **App-registraties** of de verouderde ervaring **Toepassingen (verouderd)** gebruiken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Zoek in het Azure Portal naar en selecteer **Azure AD B2C**.
1. Selecteer **App-registraties** en selecteer vervolgens **Nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *webapp1*.
1. Onder **ondersteunde account typen**selecteert u **accounts in elke organisatie Directory of een id-provider. Voor het verifiëren van gebruikers met Azure AD B2C**.
1. Onder **omleidings-URI**selecteert u **Web**en voert u `https://jwt.ms` in het tekstvak URL in.

    De omleidings-URI is het eind punt waarnaar de gebruiker wordt verzonden door de autorisatie server (Azure AD B2C in dit geval) nadat de interactie met de gebruiker is voltooid en aan welke toegangs token of autorisatie code wordt verzonden wanneer de autorisatie is geslaagd. In een productie toepassing is dit doorgaans een openbaar toegankelijk eind punt waarop uw app wordt uitgevoerd, zoals `https://contoso.com/auth-response` . Voor test doeleinden als deze zelf studie kunt u deze instellen op `https://jwt.ms` een webtoepassing van micro soft die de gedecodeerde inhoud van een token weergeeft (de inhoud van het token verlaat uw browser nooit). Tijdens het ontwikkelen van apps kunt u het eind punt toevoegen waar uw toepassing lokaal luistert, zoals `https://localhost:5000` . U kunt op elk gewenst moment omleidings-Uri's toevoegen en wijzigen in uw geregistreerde toepassingen.

    De volgende beperkingen zijn van toepassing op omleidings-Uri's:

    * De antwoord-URL moet beginnen met het schema `https` .
    * De antwoord-URL is hoofdletter gevoelig. Het hoofdletter gebruik moet overeenkomen met het URL-pad van de actieve toepassing. Als uw toepassing bijvoorbeeld een deel van het pad bevat `.../abc/response-oidc` , moet u niet opgeven `.../ABC/response-oidc` in de antwoord-URL. Omdat in de webbrowser paden worden beschouwd als hoofdletter gevoelig, kunnen cookies die zijn gekoppeld aan, `.../abc/response-oidc` worden uitgesloten als deze worden omgeleid naar de niet-overeenkomende `.../ABC/response-oidc` URL.

1. Selecteer in **Machtigingen** het selectievakje *Beheerdersgoedkeuring verlenen aan machtigingen van OpenID en offline_access*.
1. Selecteer **Registreren**.

Zodra de registratie van de toepassing is voltooid, schakelt u de impliciete toekennings stroom in:

1. Selecteer **Verificatie** onder **Beheren**.
1. Schakel onder **Impliciete toekenning** de selectievakjes **Toegangstokens** en **Id-tokens** in.
1. Selecteer **Opslaan**.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Zoek in het Azure Portal naar en selecteer **Azure AD B2C**.
1. Selecteer **Toepassingen (verouderd)** , en selecteer vervolgens **Toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *webapp1*.
1. Selecteer voor **Inclusief webtoepassing/ web-API** en **Impliciete stroom toestaan****Ja**.
1. Voer voor de **Antwoord-URL** een eindpunt in waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. U kunt de service bijvoorbeeld zo instellen dat deze lokaal luistert op `https://localhost:44316` . Als u het poort nummer nog niet kent, kunt u een waarde voor de tijdelijke aanduiding opgeven en deze later wijzigen.

    Voor test doeleinden, zoals deze zelf studie, kunt u deze zo instellen dat `https://jwt.ms` de inhoud van een token voor inspectie wordt weer gegeven. Voor deze zelf studie stelt u de **antwoord-URL** in op `https://jwt.ms` .

    De volgende beperkingen zijn van toepassing op antwoord-Url's:

    * De antwoord-URL moet beginnen met het schema `https` .
    * De antwoord-URL is hoofdletter gevoelig. Het hoofdletter gebruik moet overeenkomen met het URL-pad van de actieve toepassing. Als uw toepassing bijvoorbeeld een deel van het pad bevat `.../abc/response-oidc` , moet u niet opgeven `.../ABC/response-oidc` in de antwoord-URL. Omdat in de webbrowser paden worden beschouwd als hoofdletter gevoelig, kunnen cookies die zijn gekoppeld aan, `.../abc/response-oidc` worden uitgesloten als deze worden omgeleid naar de niet-overeenkomende `.../ABC/response-oidc` URL.

1. Selecteer **maken** om de registratie van de toepassing te volt ooien.

* * *

## <a name="create-a-client-secret"></a>Een clientgeheim maken

Als uw toepassing een autorisatie code voor een toegangs token uitwisselt, moet u een toepassings geheim maken.


#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Selecteer op de pagina **Azure AD B2C-app-registraties** de toepassing die u hebt gemaakt, bijvoorbeeld *webapp1*.
1. Selecteer onder **beheren**de optie **certificaten & geheimen**.
1. Selecteer **Nieuw clientgeheim**.
1. Voer in het vak **Beschrijving** een beschrijving voor het client geheim in. Bijvoorbeeld *clientsecret1*.
1. Onder **verlopen**selecteert u een duur waarvoor het geheim geldig is en selecteert u vervolgens **toevoegen**.
1. Noteer de **waarde**van het geheim. U gebruikt deze waarde als het toepassings geheim in de code van uw toepassing.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Selecteer op de pagina **Azure AD B2C-toepassingen** de toepassing die u hebt gemaakt, bijvoorbeeld *webapp1*.
1. Selecteer **sleutels** en selecteer vervolgens **sleutel genereren**.
1. Selecteer **Opslaan** om de sleutel weer te geven. Noteer de waarde van **App-sleutel**. U gebruikt deze waarde als het toepassings geheim in de code van uw toepassing.

* * *

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een web-app registreren
> * Een clientgeheim maken

Vervolgens leert u hoe u gebruikers stromen kunt maken om gebruikers in staat te stellen zich aan te melden, zich aan te melden en hun profielen te beheren.

> [!div class="nextstepaction"]
> [Gebruikers stromen maken in Azure Active Directory B2C >](tutorial-create-user-flows.md)
