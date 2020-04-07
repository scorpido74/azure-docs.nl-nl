---
title: Verificatie
description: Legt uit hoe verificatie werkt
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681634"
---
# <a name="configure-authentication"></a>Verificatie configureren

Azure Remote Rendering maakt gebruik van hetzelfde verificatiemechanisme als [Azure Spatial Anchors (ASA).](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp) Clients moeten *AccountKey,* *AuthenticationToken*of *AccessToken* instellen om de REST API's met succes te bellen. *AccountKey* kan worden verkregen op het tabblad Sleutels voor het account Extern renderen op de Azure-portal. *AuthenticationToken* is een Azure AD-token, dat kan worden verkregen met behulp van de [ADAL-bibliotheek.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) *AccessToken* is een MR-token, dat kan worden verkregen van Azure Mixed Reality Security Token Service (STS).

## <a name="authentication-for-deployed-applications"></a>Verificatie voor geïmplementeerde toepassingen

 Het gebruik van accountsleutels wordt aanbevolen voor snel instappen, maar alleen tijdens de ontwikkeling/prototyping. Het wordt ten zeerste aanbevolen om uw toepassing niet naar productie te verzenden met behulp van een ingesloten accountsleutel en in plaats daarvan de op gebruikers gebaseerde of servicegebaseerde Azure AD-verificatiebenaderingen te gebruiken.

 Azure AD-verificatie wordt beschreven in de sectie [Azure AD-gebruikersverificatie](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) van de [ASA-service (Azure Spatial Anchors).](https://docs.microsoft.com/azure/spatial-anchors/)

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Om het toegangsniveau voor toepassingen, services of Azure AD-gebruikers van uw service te beheren, zijn de volgende rollen gemaakt die u zo nodig toewijzen aan uw Azure Remote Rendering-accounts:

* **Extern renderingbeheerbeheer:** biedt gebruikers conversie-, sessie-, rendering- en diagnostische mogelijkheden voor Azure Remote Rendering.
* **Externe renderingclient:** biedt gebruikers beheersessie-, rendering- en diagnostische mogelijkheden voor Azure Remote Rendering.

## <a name="next-steps"></a>Volgende stappen

* [Een account maken](create-an-account.md)
* [De Azure Frontend API's gebruiken voor verificatie](frontend-apis.md)
* [Voorbeeld PowerShell-scripts](../samples/powershell-example-scripts.md)
