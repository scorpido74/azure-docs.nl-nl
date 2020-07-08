---
title: Beheerde identiteiten in de Azure signalerings service
description: Meer informatie over hoe beheerde identiteiten werken in de Azure signalerings service en hoe u een beheerde identiteit gebruikt in serverloze scenario's.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: abe7503e7eb73d533ae901af21de001960173fb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559403"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Beheerde identiteiten voor de Azure signalerings service

In dit artikel wordt beschreven hoe u een beheerde identiteit voor de Azure signalerings service maakt en hoe u deze kunt gebruiken in serverloze scenario's.

> [!Important] 
> De Azure signalerings service kan slechts één beheerde identiteit ondersteunen. Dit betekent dat u een door het systeem toegewezen identiteit of een door de gebruiker toegewezen identiteit kunt toevoegen. 

## <a name="add-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen

Als u een beheerde identiteit wilt instellen in de Azure Portal, maakt u eerst een service-exemplaar van Azure signalering en schakelt u de functie in.

1. Maak een exemplaar van de Azure Signalr-service in de portal zoals u dat gewend bent. Blader ernaar in de portal.

2. Selecteer **identiteit**.

4. Schakel op het tabblad **systeem toegewezen** de optie **status** in **op aan**. Selecteer **Opslaan**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Een door het systeem toegewezen identiteit toevoegen aan de portal":::

## <a name="add-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit toevoegen

Voor het maken van een exemplaar van de Azure signalerings service met een door de gebruiker toegewezen identiteit moet u de identiteit maken en vervolgens de resource-id toevoegen aan uw service.

1. Maak een door de gebruiker toegewezen beheerde identiteits bron op basis van [deze instructies](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Maak een exemplaar van de Azure Signalr-service in de portal zoals u dat gewend bent. Blader ernaar in de portal.

3. Selecteer **identiteit**.

4. Selecteer **toevoegen**op het tabblad door de **gebruiker toegewezen** .

5. Zoek naar de identiteit die u eerder hebt gemaakt en selecteer deze. Selecteer **Toevoegen**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Een door de gebruiker toegewezen identiteit toevoegen aan de portal":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Een beheerde identiteit gebruiken in serverloze scenario's

De Azure signalerings service is een volledig beheerde service. u kunt dus geen beheerde identiteit gebruiken om tokens hand matig op te halen. In plaats daarvan gebruikt de Azure signalerings service de beheerde identiteit die u hebt ingesteld om een toegangs token op te halen. De service stelt vervolgens het toegangs token in op een `Authorization` header in een upstream-aanvraag in serverloze scenario's.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Beheerde identiteits verificatie inschakelen in de upstream-instellingen

1. Een door het systeem toegewezen identiteit of door de gebruiker toegewezen identiteit toevoegen.

2. Configureer de instellingen voor de upstream en gebruik **ManagedIdentity** als **verificatie** -instellingen. Zie [upstream-instellingen](concept-upstream.md)voor meer informatie over het maken van upstream-instellingen met verificatie.

3. In de instellingen voor beheerde identiteits verificatie voor **resource**kunt u de doel resource opgeven. De resource wordt een `aud` claim in het verkregen toegangs token, dat kan worden gebruikt als onderdeel van validatie in de upstream-eind punten. De bron kan een van de volgende zijn:
    - Leeg
    - Toepassings-ID (client) van de Service-Principal
    - De URI van de toepassings-ID van de Service-Principal
    - [Resource-ID van een Azure-service](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Als u een toegangs token door uzelf in uw service valideert, kunt u een van de resource-indelingen kiezen. Zorg ervoor dat de **bron** waarde in **verificatie** -instellingen en de validatie consistent zijn. Als u gebruikmaakt van op rollen gebaseerd toegangs beheer (RBAC) voor een gegevenslaag, moet u de resource gebruiken die door de service provider wordt aangevraagd.

### <a name="validate-access-tokens"></a>Toegangs tokens valideren

Het token in de `Authorization` header is een [toegangs token van micro soft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens).

Als u toegangs tokens wilt valideren, moet uw app ook de doel groep en de handtekening tokens valideren. Deze moeten worden gevalideerd aan de hand van de waarden in het detectie document voor OpenID Connect. Zie bijvoorbeeld de [Tenant-onafhankelijke versie van het document](https://login.microsoftonline.com/common/.well-known/openid-configuration).

De Azure Active Directory (Azure AD) middleware heeft ingebouwde mogelijkheden voor het valideren van toegangs tokens. U kunt door onze voor [beelden](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) bladeren om er een te vinden in de taal van uw keuze.

We bieden bibliotheken en code voorbeelden die laten zien hoe u de token validatie kunt afhandelen. Er zijn ook verschillende open source-partner bibliotheken beschikbaar voor de validatie van de JSON Web Token (JWT). Er is ten minste één optie voor bijna elk platform en de taal. Zie [verificatie bibliotheken voor micro soft-identiteits platform](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries)voor meer informatie over Azure AD-verificatie bibliotheken en code voorbeelden.

## <a name="next-steps"></a>Volgende stappen

- [Ontwikkeling en configuratie van Azure Functions met Azure SignalR Service](signalr-concept-serverless-development-config.md)
