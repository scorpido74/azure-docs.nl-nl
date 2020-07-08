---
title: Registratie instellen en aanmelden met een Facebook-account
titleSuffix: Azure AD B2C
description: Bied u de mogelijkheid om u aan te melden bij klanten met Facebook-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 394af855b7968d38bf99afada246ae661dc0459a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85549477"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een Facebook-account met Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Een Facebook-toepassing maken

Als u een Facebook-account wilt gebruiken als een [ID-provider](authorization-code-flow.md) in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw Tenant waarmee deze wordt vertegenwoordigd. Als u nog geen Facebook-account hebt, kunt u zich aanmelden bij [https://www.facebook.com/](https://www.facebook.com/) .

1. Meld u aan bij [Facebook voor ontwikkel aars](https://developers.facebook.com/) met de referenties van uw Facebook-account.
1. Als u dit nog niet hebt gedaan, moet u zich registreren als Facebook-ontwikkelaar. U doet dit door aan de rechter bovenhoek van de pagina aan de **slag te gaan** , het beleid van Facebook te accepteren en de registratie stappen te volt ooien.
1. Selecteer **mijn apps** en maak vervolgens een **app**.
1. Voer een **weergave naam** in en geef een geldig **e-mail adres voor de contact persoon op**.
1. Selecteer **App-ID maken**. Hiervoor moet u mogelijk het Facebook-platform beleid accepteren en een online beveiligings controle volt ooien.
1. Selecteer **instellingen**  >  **basis**.
1. Kies bijvoorbeeld een **categorie** `Business and Pages` . Deze waarde is vereist voor Facebook, maar wordt niet gebruikt voor Azure AD B2C.
1. Selecteer onder aan de pagina **platform toevoegen**en selecteer vervolgens **website**.
1. Voer in **site-URL**het adres van uw website in, bijvoorbeeld `https://contoso.com` . Voer een URL in voor de URL van het **Privacybeleid**, bijvoorbeeld `http://www.contoso.com/privacy` . De beleids-URL is een pagina die u kunt onderhouden voor het verstrekken van privacy-informatie voor uw toepassing.
1. Selecteer **wijzigingen opslaan**.
1. Kopieer de waarde van de **App-ID**aan de bovenkant van de pagina.
1. Selecteer **weer geven** en kopieer de waarde van het **app-geheim**. U kunt beide gebruiken om Facebook te configureren als een id-provider in uw Tenant. **App-geheim** is een belang rijke beveiligings referentie.
1. Selecteer het plus teken naast **producten**en selecteer vervolgens **instellen** onder **Facebook-aanmelding**.
1. Selecteer onder **Facebook-aanmelding**de optie **instellingen**.
1. Voer in **geldige OAuth omleidings-uri's**in `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Vervang door `your-tenant-name` de naam van uw Tenant. Selecteer **wijzigingen opslaan** onder aan de pagina.
1. Als u uw Facebook-toepassing beschikbaar wilt maken voor Azure AD B2C, selecteert u in de rechter bovenhoek van de pagina de status kiezer en schakelt u deze **in** om de toepassing openbaar te maken en vervolgens **Switch modus**te selecteren.  Op dit moment moet de status worden gewijzigd van **ontwikkeling** naar **Live**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Een Facebook-account configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **Facebook**.
1. Voer een **naam**in. Bijvoorbeeld *Facebook*.
1. Voer voor de **client-id**de app-id in van de Facebook-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim**het app-geheim in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.
