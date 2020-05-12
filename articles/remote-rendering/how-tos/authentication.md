---
title: Verificatie
description: Geeft uitleg over de werking van verificatie
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.custom: has-adal-ref
ms.openlocfilehash: 34874e01b526a4d88ff4402edb0c3771c75a3726
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195304"
---
# <a name="configure-authentication"></a>Verificatie configureren

Azure remote rendering maakt gebruik van hetzelfde authenticatie mechanisme als [Azure spatiale ankers (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Clients moeten *AccountKey*, *AuthenticationToken*of *AccessToken* instellen om de rest-api's te kunnen aanroepen. *AccountKey* kan worden verkregen op het tabblad sleutels voor het externe rendering-account op de Azure Portal. *AuthenticationToken* is een Azure AD-token, dat kan worden verkregen met behulp van de [ADAL-bibliotheek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* is een Mr-token dat kan worden verkregen door de Azure Mixed Reality-beveiligings token service (STS).

## <a name="authentication-for-deployed-applications"></a>Verificatie voor geïmplementeerde toepassingen

 Het gebruik van account sleutels wordt aanbevolen voor een snelle on-boarding, maar tijdens ontwikkeling/prototypen. Het wordt ten zeerste aanbevolen om uw toepassing niet naar productie te verzenden met behulp van een Inge sloten account sleutel. in plaats daarvan wordt gebruikgemaakt van de door de gebruiker of service gebaseerde Azure AD-verificatie methoden.

 Azure AD-verificatie wordt beschreven in het gedeelte [Azure AD-gebruikers verificatie](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) van de [Azure spatiale ankers (ASA)-](https://docs.microsoft.com/azure/spatial-anchors/) service.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Om het niveau van toegang te beheren dat wordt verleend aan toepassingen, services of Azure AD-gebruikers van uw service, zijn de volgende rollen gemaakt zodat u deze indien nodig kunt toewijzen aan uw Azure-accounts voor externe Rendering:

* **Externe rendering-beheerder**: biedt gebruikers de mogelijkheid om de mogelijkheden voor het omzetten van sessies, rendering en diagnose te beheren voor externe rendering van Azure.
* **Client voor externe rendering**: biedt gebruikers de mogelijkheid voor het beheren van sessies, rendering en diagnose voor de externe rendering van Azure.

## <a name="next-steps"></a>Volgende stappen

* [Een account maken](create-an-account.md)
* [De Azure frontend-Api's gebruiken voor verificatie](frontend-apis.md)
* [PowerShell-voorbeeldscripts](../samples/powershell-example-scripts.md)
