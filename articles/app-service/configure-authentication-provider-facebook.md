---
title: Facebook-verificatie configureren-Azure App Service
description: Meer informatie over het configureren van Facebook-verificatie voor uw App Service-app.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 410d769d0d9abe3a0a0f9c45e3cf67bb94ec9f4d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232071"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Uw App Service-toepassing configureren voor het gebruik van Facebook-aanmelding
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit onderwerp wordt beschreven hoe u Azure App Service kunt configureren om Facebook als verificatie provider te gebruiken.

Als u de procedure in dit onderwerp wilt volt ooien, moet u beschikken over een Facebook-account met een geverifieerd e-mail adres en een mobiel telefoon nummer. Als u een nieuw Facebook-account wilt maken, gaat u naar [Facebook.com].

## <a name="register"> </a>Uw toepassing registreren bij Facebook
1. Ga naar de website van [Facebook-ontwikkel aars] en meld u aan met de referenties van uw Facebook-account.
3. Beschrijving Als u geen Facebook-account voor ontwikkel aars hebt, klikt u op **aan de slag**en volgt u de registratie stappen.
4. Klik op **mijn apps** > **nieuwe app toevoegen**.
5. Typ in **weergave naam**een unieke naam voor uw app. Geef ook uw **contact-e-mail**op en klik vervolgens op **App-ID maken** en de beveiligings controle volt ooien. Het dash board voor ontwikkel aars voor uw nieuwe Facebook-app wordt geopend.
6. Klik op **dash board** > **Facebook-aanmelding** > **instellen** > op**Web**.
1. Klik in de linkernavigatiebalk onder Facebook- **aanmelding**op **instellingen**.
1. In **geldige OAuth**omleidings- `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` uri's typt en vervangt  *\<u de app-naam >* door de naam van uw Azure app service-app. Klik op **Wijzigingen opslaan**.
8. Klik in de linkernavigatiebalk op **instellingen** > **basis**. Klik in het veld voor de **app-geheim** op **weer geven**. Kopieer de waarden van de **App-ID** en het **app-geheim**. U kunt deze later gebruiken om uw App Service-app te configureren in Azure.
   
   > [!IMPORTANT]
   > Het app-geheim is een belang rijke beveiligings referentie. Deel dit geheim niet met iemand of distribueer het in een client toepassing.
   > 
   > 
9. Het Facebook-account dat u hebt gebruikt om de toepassing te registreren, is een beheerder van de app. Op dit moment kunnen alleen beheerders zich aanmelden bij deze toepassing. Als u andere Facebook-accounts wilt verifiëren, klikt u op **app controleren** en schakelt  **\<u uw app-naam > openbaar** in om algemene open bare toegang mogelijk te maken met Facebook-verificatie.

## <a name="secrets"> </a>Facebook-gegevens toevoegen aan uw toepassing
1. Meld u aan bij de [Azure-portal] en navigeer naar uw app service-app. Klik op **instellingen** > **verificatie/autorisatie**en controleer of **app service-verificatie** is **ingeschakeld**.
2. Klik op **Facebook**, plak de waarde van de App-ID en de geheime sleutel van de app die u eerder hebt verkregen, en Schakel eventueel de scopes in die nodig zijn voor uw toepassing en klik vervolgens op **OK**.
   
    ![][0]
   
    App Service biedt standaard verificatie, maar beperkt geen geautoriseerde toegang tot uw site-inhoud en Api's. U moet gebruikers in uw app-code autoriseren.
3. Beschrijving Als u de toegang tot uw site wilt beperken tot alleen gebruikers die zijn geverifieerd door Facebook, stelt **u actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** voor **Facebook**. Hiervoor moeten alle aanvragen worden geverifieerd en alle niet-geverifieerde aanvragen worden omgeleid naar Facebook voor verificatie.
 
> [!CAUTION]
> Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps die een openbaar beschik bare start pagina willen, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie) toestaan** voor keur, waarbij de app de aanmelding zelf hand matig start, zoals [hier](overview-authentication-authorization.md#authentication-flow)wordt beschreven.

4. Klik op **Opslaan**wanneer u klaar bent met het configureren van de verificatie.

U bent nu klaar om Facebook te gebruiken voor verificatie in uw app.

## <a name="related-content"> </a>Gerelateerde inhoud
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook-ontwikkel aars]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-portal]: https://portal.azure.com/
