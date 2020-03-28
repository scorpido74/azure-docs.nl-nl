---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9367719b1a1050ce8abf36804b3d0d0d58c7484b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183368"
---
Web-API-resources moeten worden geregistreerd in uw tenant voordat deze in staat zijn om beveiligde resourceaanvragen door clienttoepassingen die een toegangstoken aanbieden, kunnen accepteren en erop kunnen reageren.

Als u een toepassing wilt registreren in uw Azure AD B2C-tenant, u de huidige **ervaring met toepassingen** of onze nieuwe uniforme **app-registratie (Preview)-ervaring** gebruiken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **Toepassingen**en selecteer **Toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *webapi1*.
1. Selecteer **Ja**voor **Web App / Web API.**
1. Selecteer Voor **Impliciete stroom toestaan**de optie **Ja**.
1. Voer voor de **Antwoord-URL** een eindpunt in waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. In deze zelfstudie wordt het voorbeeld lokaal uitgevoerd en luistert dit op `https://localhost:5000`.
1. Voeg voor **App ID URI**een API-eindpunt-id toe aan de uri die wordt weergegeven. Voer voor deze `api`zelfstudie in, zodat `https://contosob2c.onmicrosoft.com/api`de volledige URI vergelijkbaar is met .
1. Selecteer **Maken**.
1. De **toepassings-id opnemen** voor gebruik in een latere stap.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **App-registraties (Voorbeeld)** en selecteer **Vervolgens Nieuwe registratie**.
1. Voer een **naam** voor de toepassing in. Bijvoorbeeld *webapi1*.
1. Selecteer **onder Uri omleiden** **en**voer een eindpunt in waarin Azure AD B2C alle tokens die uw toepassing aanvraagt, moet retourneren. In deze zelfstudie wordt het voorbeeld lokaal uitgevoerd en luistert dit op `http://localhost:5000`.
1. Selecteer **Registreren**.
1. Neem de **toepassings-id (client) op** voor gebruik in een latere stap.

Schakel vervolgens de impliciete subsidiestroom in:

1. Selecteer onder **Beheren**de optie **Verificatie**.
1. Selecteer **Probeer de nieuwe ervaring** uit (indien weergegeven).
1. Schakel **onder Impliciete subsidie**de selectievakjes **Access-tokens** en **ID-tokens** in.
1. Selecteer **Opslaan**.