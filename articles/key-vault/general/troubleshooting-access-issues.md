---
title: Problemen met het toegangsbeleid voor Azure Key Vault oplossen
description: Problemen met het toegangsbeleid voor Azure Key Vault oplossen
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.openlocfilehash: f775a0e1dcb82afc9a458ce4ee608a52ec06dc4c
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135909"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Problemen met het toegangsbeleid voor Azure Key Vault oplossen

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Hoe kan ik vaststellen hoe en wanneer sleutelkluizen worden geopend?
Nadat u een of meer sleutelkluizen hebt gemaakt, wilt u wellicht controleren hoe en wanneer uw sleutelkluizen toegankelijk zijn en voor wie. U kunt dit doen door logboekregistratie in te schakelen voor Azure Key Vault. [Lees meer](https://docs.microsoft.com/azure/key-vault/general/logging) voor een stapsgewijze handleiding voor het inschakelen van logboekregistratie.

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Hoe kan ik de beschikbaarheid van de kluis, servicelatentieperioden of andere metrische gegevens voor de sleutelkluis bewaken?
Wanneer u begint met het schalen van uw service, neemt het aantal aanvragen toe dat naar uw sleutelkluis wordt verzonden. Dit kan de latentie van uw aanvragen verhogen en er in uitzonderlijke gevallen voor zorgen dat uw aanvragen worden beperkt, waardoor de prestaties van uw service worden beïnvloed. U kunt de metrische gegevens van de prestaties van de sleutelkluis controleren en waarschuwingen ontvangen voor specifieke drempelwaarden. [Lees meer](https://docs.microsoft.com/azure/key-vault/general/alert) voor een stapsgewijze handleiding voor het configureren van bewaking.

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Hoe kan ik toegangsbeheer per sleutelkluisobject toewijzen? 
De beschikbaarheid van de functie voor toegangsbeheer per geheim/sleutel/certificaat wordt hier vermeld. [Meer informatie](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/.32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Hoe kan ik Key Vault-verificatie bieden met een toegangsbeheerbeleid?
De eenvoudigste manier om een cloudtoepassing toegang te verlenen tot Key Vault, is met een beheerde identiteit. Zie [Een door App Service beheerde identiteit gebruiken om toegang te krijgen tot Azure Key Vault]( https://docs.microsoft.com/azure/key-vault/general/managed-identity) voor meer informatie.
Als u een on-premises toepassing maakt, lokale ontwikkeling uitvoert of op een andere manier geen beheerde identiteit kunt gebruiken, kunt u in plaats daarvan handmatig een service-principal registreren en toegang verlenen tot uw sleutelkluis met behulp van een toegangsbeheerbeleid. [Meer informatie](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps).


### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Hoe kan ik de AD-groep toegang geven tot de sleutelkluis?
Geef de AD-groep toegang tot uw sleutelkluis met behulp van de Azure CLI-opdracht az keyvault set-policy of de Azure PowerShell-cmdlet Set-AzKeyVaultAccessPolicy. Zie voor voorbeelden [De toepassing, Azure Active Directory-groep of gebruiker toegang geven tot uw sleutelkluis](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps#give-the-principal-access-to-your-key-vault).

De toepassing moet ook ten minste één IAM-rol (Identiteits- en toegangsbeheer) hebben die is toegewezen aan de sleutelkluis. Anders kan de toepassing zich niet aanmelden en mislukt deze door onvoldoende toegangsrechten tot het abonnement. De tokens van Azure Active Directory-groepen met beheerde identiteiten hebben mogelijk 8 uur nodig om te vernieuwen en effectief te worden.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Hoe kan ik Key Vault opnieuw implementeren met een ARM-sjabloon zonder bestaand toegangsbeleid te verwijderen?
Op dit moment wordt elk toegangsbeleid in Key Vault verwijderd en vervangen door het toegangsbeleid in de ARM-sjabloon als u Key Vault opnieuw implementeert met de ARM-sjabloon. Er is geen incrementele optie voor Key Vault-toegangsbeleid. Als u het toegangsbeleid in Key Vault wilt behouden, moet u het bestaande toegangsbeleid lezen in Key Vault en de ARM-sjabloon vullen met die beleidsregels om eventuele toegangsstoringen te voor komen.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Aanbevolen stappen voor probleemoplossing voor de volgende fouttypen
* HTTP 401: Niet-geverifieerde aanvraag - [Probleemoplossingsstappen](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-401-unauthenticated-request)
* HTTP 403: Onvoldoende machtigingen - [Probleemoplossingsstappen](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-403-insufficient-permissions)
* HTTP 429: Te veel aanvragen - [Probleemoplossingsstappen](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-429-too-many-requests)
* Controleer of u toegangsmachtiging voor verwijderen hebt voor de sleutelkluis: [Toegangsbeleid van Key Vault](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)
* Als u een probleem hebt met de verificatie voor de sleutelkluis in code, gebruikt u [Authentication SDK](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html) (Verificatie-SDK)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Wat zijn de aanbevolen procedures die ik moet implementeren als de sleutelkluis wordt beperkt?
Volg de aanbevolen procedures die [hier](https://docs.microsoft.com/azure/key-vault/general/overview-throttling#how-to-throttle-your-app-in-response-to-service-limits) worden beschreven

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het oplossen van problemen met sleutelkluisverificatie. [Handleiding voor het oplossen van problemen met Key Vault](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
