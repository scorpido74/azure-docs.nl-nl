---
title: Gebruikersaccounts beheren in Azure API Management | Microsoft Documenten
description: Meer informatie over het maken of uitnodigen van gebruikers in Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 672f145b638fa6b85f7139f2edb4fc41bb2855d9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260969"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Gebruikersaccounts in Azure API Management beheren

In API Management zijn ontwikkelaars de gebruikers van de API's die u blootstelt met API Management. In deze handleiding ziet u hoe u ontwikkelaars maken en uitnodigen om de API's en producten die u voor hen beschikbaar stelt met uw API-beheerexemplaar te gebruiken. Zie de documentatie van de [gebruikersentiteit](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/user) in de API [Management REST-verwijzing](/rest/api/apimanagement/) voor informatie over het programmatisch beheren van gebruikersaccounts.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Vereisten

Taken voltooien in dit artikel: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-new-developer"></a><a name="create-developer"> </a>Een nieuwe ontwikkelaar maken

Als u een nieuwe gebruiker wilt toevoegen, voert u de stappen in deze sectie uit:

1. Selecteer het tabblad **Gebruikers** links van het scherm.
2. Druk op **+Toevoegen**.
3. Voer de juiste informatie in voor de gebruiker.
4. Klik op **Toevoegen**.

    ![Een nieuwe gebruiker toevoegen](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Nieuw gemaakte ontwikkelaarsaccounts zijn standaard **Actief**en zijn gekoppeld aan de groep **Ontwikkelaars.** Ontwikkelaarsaccounts die zich in een **actieve** status bevinden, kunnen worden gebruikt om toegang te krijgen tot alle API's waarvoor ze abonnementen hebben. Zie [Groepen koppelen aan ontwikkelaars][How to associate groups with developers]als u de nieuw gemaakte ontwikkelaar wilt koppelen aan extra groepen.

## <a name="invite-a-developer"></a><a name="invite-developer"> </a>Een ontwikkelaar uitnodigen
Als u een ontwikkelaar wilt uitnodigen, voert u de stappen in deze sectie uit:

1. Selecteer het tabblad **Gebruikers** links van het scherm.
2. Druk op **+Uitnodigen**.

Er wordt een bevestigingsbericht weergegeven, maar de nieuw uitgenodigde ontwikkelaar wordt pas in de lijst weergegeven nadat hij de uitnodiging heeft geaccepteerd. 

Wanneer een ontwikkelaar wordt uitgenodigd, wordt een e-mail naar de ontwikkelaar verzonden. Deze e-mail wordt gegenereerd met behulp van een sjabloon en is aanpasbaar. Zie [E-mailsjablonen configureren voor][Configure email templates]meer informatie.

Zodra de uitnodiging is geaccepteerd, wordt het account actief.

## <a name="deactivate-or-reactivate-a-developer-account"></a><a name="block-developer"> </a> Een ontwikkelaarsaccount deactiveren of opnieuw activeren

Standaard zijn nieuw gemaakte of uitgenodigde ontwikkelaarsaccounts **Actief**. Als u een ontwikkelaarsaccount wilt deactiveren, klikt u op **Blokkeren**. Als u een geblokkeerd ontwikkelaarsaccount opnieuw wilt activeren, klikt u op **Activeren**. Een geblokkeerd ontwikkelaarsaccount heeft geen toegang tot de ontwikkelaarsportal of kan geen API's aanroepen. Als u een gebruikersaccount wilt verwijderen, klikt u op **Verwijderen**.

Als u een gebruiker wilt blokkeren, voert u de volgende stappen uit.

1. Selecteer het tabblad **Gebruikers** links van het scherm.
2. Klik op de gebruiker die u wilt blokkeren.
3. Druk op **Blok**.

## <a name="reset-a-user-password"></a>Een gebruikerswachtwoord opnieuw instellen

Zie de documentatie van de gebruikersentiteit in de [API-verwijzing API-api voor API-beheer](/rest/api/apimanagement/) om programmatisch met gebruikersaccounts te werken. Als u een wachtwoord voor een gebruikersaccount wilt resetten naar een specifieke waarde, u de [bewerking Een gebruiker](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) bijwerken gebruiken en het gewenste wachtwoord opgeven.

## <a name="next-steps"></a><a name="next-steps"> </a>Volgende stappen
Zodra een ontwikkelaarsaccount is gemaakt, u het koppelen aan rollen en het abonneren op producten en API's. Zie [Groepen maken en gebruiken][How to create and use groups]voor meer informatie.

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
