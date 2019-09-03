---
title: Verificatie van micro soft-account configureren-Azure App Service
description: Meer informatie over het configureren van micro soft-account verificatie voor uw App Service-app.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 0832c1e5f10cdb8e1d7a2edbb88162230ab13401
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233079"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Uw App Service-toepassing configureren voor het gebruik van aanmelding met een micro soft-account
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit onderwerp wordt beschreven hoe u Azure App Service kunt configureren voor gebruik van micro soft-account als verificatie provider. 

## <a name="register-microsoft-account"> </a>Uw app registreren bij micro soft-account
1. Meld u aan bij de [Azure-portal]en navigeer naar uw toepassing. 

<!-- Copy your **URL**, which you will use later to configure your app with Microsoft Account. -->
1. Ga naar [**app-registraties**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)en meld u aan met uw Microsoft-account, indien gewenst.

1. Klik op **nieuwe registratie**en typ vervolgens een naam voor de toepassing.

1. In omleidings- **uri's**selecteert u **Web**en `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`vervolgens typt u. *Vervang\<app-domain-name >* door de domein naam van uw app.  Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. 

   > [!NOTE]
   > Gebruik het HTTPS-schema in de URL.

1. Selecteer **registreren**. 

1. Kopieer de **toepassings-id (client)** . U hebt deze later nodig. 
   
7. Selecteer op de linkernavigatiebalk van de nieuwe app-registratie de optie **Certificaten & geheimen** > **Nieuw client geheim**. Geef een beschrijving op, selecteer de geldigheids duur en selecteer **toevoegen**.

1. Kopieer de waarde die wordt weer gegeven op de pagina **certificaten & geheimen** . Zodra u de pagina verlaat, wordt deze niet meer weer gegeven.

    > [!IMPORTANT]
    > Het wacht woord is een belang rijke beveiligings referentie. Deel het wacht woord niet met iemand of distribueer het in een client toepassing.

## <a name="secrets"> </a>Micro soft-account gegevens toevoegen aan uw app service-toepassing
1. Navigeer in het [Azure-portal]naar uw toepassing. Klik vanuit de linkernavigatiebalk op **verificatie/autorisatie**.

2. Als de functie voor verificatie/autorisatie niet is ingeschakeld, selecteert u **aan**.

3. Selecteer onder **verificatie providers**de optie **micro soft-account**. Plak de client-ID en het client geheim die u eerder hebt verkregen en schakel desgewenst de scopes in die voor uw toepassing nodig zijn. Klik vervolgens op **OK**.

    App Service biedt standaard verificatie, maar beperkt geen geautoriseerde toegang tot uw site-inhoud en Api's. U moet gebruikers in uw app-code autoriseren.

4. Beschrijving Als u de toegang tot Microsoft-account gebruikers wilt beperken, stelt **u een actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** voor **aanmelding bij het micro soft-account**. Hiervoor moeten alle aanvragen worden geverifieerd en alle niet-geverifieerde aanvragen worden omgeleid naar Microsoft-account voor authenticatie.

> [!NOTE]
> Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps die een openbaar beschik bare start pagina willen, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie) toestaan** voor keur, waarbij de app de aanmelding zelf hand matig start, zoals [hier](overview-authentication-authorization.md#authentication-flow)wordt beschreven.

5. Klik op **Opslaan**.

U bent nu klaar om micro soft-account te gebruiken voor verificatie in uw app.

## <a name="related-content"> </a>Gerelateerde inhoud
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-portal]: https://portal.azure.com/
