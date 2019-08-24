---
title: Zelf studie-een toepassing registreren-Azure Active Directory B2C
description: Meer informatie over het registreren van een webtoepassing in Azure Active Directory B2C met behulp van de Azure Portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 651c15c8206f7956bb35520f9c5837cb0c9308f9
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980695"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Zelfstudie: Een toepassing registreren in Azure Active Directory B2C

Voordat uw [toepassingen](active-directory-b2c-apps.md) kunnen communiceren met Azure Active Directory (Azure AD) B2C, moeten ze worden geregistreerd in een Tenant die u beheert. Deze zelf studie laat zien hoe u een webtoepassing registreert met behulp van de Azure Portal.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een web-app registreren
> * Een client geheim maken

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u nog geen eigen [Azure AD B2C Tenant](tutorial-create-tenant.md)hebt gemaakt, maakt u er nu een. U kunt een bestaande Azure AD B2C-Tenant gebruiken.

## <a name="register-a-web-application"></a>Een web-app registreren

1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant door te klikken op de **map- en abonnementsfilter** in het bovenste menu en de map waarin uw tenant te kiezen.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Toepassingen** en vervolgens **Toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *webapp1*.
1. Selecteer voor **Inclusief webtoepassing/ web-API** en **Impliciete stroom toestaan** **Ja**.
1. Voer voor de **Antwoord-URL** een eindpunt in waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. U kunt de service bijvoorbeeld zo instellen dat deze lokaal luistert op `https://localhost:44316`. Als u het poort nummer nog niet kent, kunt u een waarde voor de tijdelijke aanduiding opgeven en deze later wijzigen.

    Voor test doeleinden, zoals deze zelf studie, kunt u `https://jwt.ms` deze zo instellen dat de inhoud van een token voor inspectie wordt weer gegeven. Voor deze zelf studie stelt u de antwoord- `https://jwt.ms` **URL** in op.

    De volgende beperkingen zijn van toepassing op antwoord-Url's:

    * De antwoord-URL moet beginnen met het `https`schema.
    * De antwoord-URL is hoofdletter gevoelig. Het hoofdletter gebruik moet overeenkomen met het URL-pad van de actieve toepassing. Als uw toepassing bijvoorbeeld een deel van het pad `.../abc/response-oidc`bevat, moet u niet opgeven `.../ABC/response-oidc` in de antwoord-URL. Omdat in de webbrowser paden worden beschouwd als hoofdletter gevoelig, `.../abc/response-oidc` kunnen cookies die zijn gekoppeld aan, worden uitgesloten als deze worden omgeleid naar de niet- `.../ABC/response-oidc` overeenkomende URL.

1. Klik op **maken** om de registratie van de toepassing te volt ooien.

## <a name="create-a-client-secret"></a>Een client geheim maken

Als uw toepassing een code voor een token uitwisselt, moet u een toepassings geheim maken.

1. Selecteer op de pagina **Azure AD B2C-toepassingen** de toepassing die u hebt gemaakt, bijvoorbeeld *webapp1*.
1. Selecteer **sleutels** en selecteer vervolgens **sleutel genereren**.
1. Selecteer **Opslaan** om de sleutel weer te geven. Noteer de waarde van **App-sleutel**. U gebruikt deze waarde als het toepassings geheim in de code van uw toepassing.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een web-app registreren
> * Een client geheim maken

Vervolgens leert u hoe u gebruikers stromen kunt maken om gebruikers in staat te stellen zich aan te melden, zich aan te melden en hun profielen te beheren.

> [!div class="nextstepaction"]
> [Gebruikers stromen maken in Azure Active Directory B2C >](tutorial-create-user-flows.md)
