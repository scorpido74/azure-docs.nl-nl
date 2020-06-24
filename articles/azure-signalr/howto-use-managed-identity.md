---
title: Beheerde identiteiten in de Azure signalerings service
description: Meer informatie over hoe beheerde identiteiten werken in de Azure signalerings service, hoe u configureert voor het gebruik van beheerde identiteiten in serverloze scenario's.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: 8e122be74d623c7f1ea5576a5fe527dfa538d1d1
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988607"
---
# <a name="how-to-use-managed-identities-for-azure-signalr-service"></a>Beheerde identiteiten gebruiken voor de Azure signalerings service

In dit onderwerp wordt beschreven hoe u een beheerde identiteit voor de Azure signalerings service maakt en hoe u deze kunt gebruiken in serverloze scenario's.

> [!Important] 
> De Azure signalerings service kan slechts één beheerde identiteit ondersteunen. Dit betekent dat u een door het systeem toegewezen identiteit of een door de gebruiker toegewezen identiteit kunt toevoegen. 

## <a name="add-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u een beheerde identiteit in de portal wilt instellen, maakt u eerst een signalerings service als normaal en schakelt u de functie in.

1. Maak een signalerings service in de portal zoals u dat gewend bent. Navigeer ernaar in de portal.

2. Selecteer **identiteit**.

4. Schakel op het tabblad **systeem toegewezen** de optie **status** in **op aan**. Klik op **Opslaan**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Door het systeem toegewezen identiteit toevoegen aan de portal":::

## <a name="add-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit toevoegen

Als u een signalerings service met een door de gebruiker toegewezen identiteit wilt maken, moet u de identiteit maken en vervolgens de resource-id toevoegen aan uw service.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Eerst moet u een door de gebruiker toegewezen id-resource maken.

1. Maak een door de gebruiker toegewezen beheerde identiteits bron op basis van [deze instructies](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Maak een signalerings service in de portal zoals u dat gewend bent. Navigeer ernaar in de portal.

3. Selecteer **identiteit**.

4. Klik op het tabblad **toegewezen door gebruiker** op **toevoegen**.

5. Zoek de identiteit die u eerder hebt gemaakt en selecteer deze. Klik op **Add**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Identiteit van de gebruiker assigened toevoegen in de portal":::

## <a name="use-managed-identity-in-serverless-scenarios"></a>Beheerde identiteit gebruiken in serverloze scenario's

Als de signaal service is een volledig beheerde service, kunt u geen beheerde identiteit gebruiken om tokens hand matig op te halen. In plaats daarvan gebruikt de signalerings service de beheerde identiteit die u hebt ingesteld voor het verkrijgen van toegangs token en `Authorization` het instellen van een header in een upstream-aanvraag in serverloze scenario's.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Beheerde identiteits verificatie inschakelen in de upstream-instellingen

1. Een door het systeem toegewezen identiteit of door de gebruiker toegewezen identiteit toevoegen

2. Configureer de instellingen voor de upstream en gebruik *ManagedIdentity* als *verificatie* -instellingen. Meer informatie over het maken van upstream-instellingen met verificatie via [upstream-instellingen](concept-upstream.md#create-upstream-settings).

3. In de instellingen voor beheerde identiteits verificatie kunt u de doel *resource*opgeven. De *resource* wordt `aud` claim in het verkregen toegangs token, dat kan worden gebruikt als onderdeel van validatie in uw upstream-eind punten. De *bron* kan een van de volgende zijn:
    - Leeg
    - Toepassings-ID (client) van de Service-Principal
    - De URI van de toepassings-ID van de Service-Principal
    - Resource-Id's van Azure-Services, Zie [resource-id van Azure-Services](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Als u het toegangs token zelf in uw service valideert, kunt u een van de *resource* -indelingen kiezen. Zo lang u ervoor moet zorgen dat de *bron* in de *verificatie* -instellingen en de validatie consistent zijn. Als u gebruikmaakt van het data-vlak RBAC, moet u de *resource* gebruiken die door de service provider wordt aangevraagd.

### <a name="validate-access-token"></a>Toegangs token valideren

Het token in de `Authorization` header is een [toegangs token van micro soft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens).

Als u toegangs tokens wilt valideren, moet uw app ook de doel groep en de handtekening tokens valideren. Deze moeten worden gevalideerd aan de hand van de waarden in het detectie document voor OpenID Connect. De Tenant-onafhankelijke versie van het document bevindt zich bijvoorbeeld in [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) .

De Azure AD-middleware heeft ingebouwde mogelijkheden voor het valideren van toegangs tokens, en u kunt bladeren door onze voor [beelden](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) om er een te vinden in de taal van uw keuze.

We bieden bibliotheken en code voorbeelden die laten zien hoe u de token validatie kunt afhandelen. De onderstaande informatie is bedoeld voor degenen die het onderliggende proces willen begrijpen. Er zijn ook verschillende open source-bibliotheken van derden beschikbaar voor JWT-validatie-er is ten minste één optie voor bijna elk platform en elke taal. Zie [v 2.0-verificatie bibliotheken](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries)voor meer informatie over Azure AD-verificatie bibliotheken en code voorbeelden.

## <a name="next-steps"></a>Volgende stappen

- [Ontwikkeling en configuratie van Azure Functions met Azure SignalR Service](signalr-concept-serverless-development-config.md)
