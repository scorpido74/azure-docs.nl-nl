---
title: Een toepassing verifiëren om toegang te krijgen tot Azure Event Hubs-bronnen
description: In dit artikel vindt u informatie over het verifiëren van een toepassing met Azure Active Directory om toegang te krijgen tot Azure Event Hubs-bronnen
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: a242da8cc98a21248c48a1b3981fa713706028ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064946"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Een toepassing verifiëren met Azure Active Directory om toegang te krijgen tot gebeurtenishubsbronnen
Microsoft Azure biedt geïntegreerd beheer van toegangsbeheer voor resources en toepassingen op basis van Azure Active Directory (Azure AD). Een belangrijk voordeel van het gebruik van Azure AD met Azure Event Hubs is dat u uw referenties niet meer in de code hoeft op te slaan. In plaats daarvan u een OAuth 2.0-toegangstoken aanvragen bij het Microsoft Identity-platform. De resourcenaam om een `https://eventhubs.azure.net/` token aan te vragen is (Voor Kafka-clients is `https://<namespace>.servicebus.windows.net`de bron om een token aan te vragen ). Azure AD verifieert de beveiligingsprincipal (een gebruiker, groep of serviceprincipal) waarop de toepassing wordt uitgevoerd. Als de verificatie slaagt, retourneert Azure AD een toegangstoken naar de toepassing en kan de toepassing vervolgens het toegangstoken gebruiken om het verzoek tot Azure Event Hubs-bronnen te autoriseren.

Wanneer een rol is toegewezen aan een Azure AD-beveiligingsprincipal, verleent Azure toegang tot deze bronnen voor die beveiligingsprincipal. Toegang kan worden beperkt tot het abonnementsniveau, de resourcegroep, de naamruimte van gebeurtenishubs of een resource eronder. Een Azure AD-beveiliging kan rollen toewijzen aan een gebruiker, een groep, een hoofdvan de toepassingsservice of een [beheerde identiteit voor Azure-resources.](../active-directory/managed-identities-azure-resources/overview.md) 

> [!NOTE]
> Een roldefinitie is een verzameling machtigingen. RBAC (Role-based access control) bepaalt hoe deze machtigingen worden afgedwongen door middel van roltoewijzing. Een roltoewijzing bestaat uit drie elementen: beveiligings-principal, roldefinitie en bereik (ook wel scope of niveau genoemd). Zie [De verschillende rollen begrijpen](../role-based-access-control/overview.md)voor meer informatie.

## <a name="built-in-roles-for-azure-event-hubs"></a>Ingebouwde rollen voor Azure Event Hubs
Azure biedt de volgende ingebouwde RBAC-rollen voor het toestaan van toegang tot gebeurtenishubsgegevens met Azure AD en OAuth:

- [Eigenaar van Azure Event Hubs:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)gebruik deze rol om volledige toegang te geven tot bronnen van Gebeurtenishubs.
- [Gegevensafzender azure-gebeurtenishubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): gebruik deze rol om de bronnen van send toegang te geven tot bronnen van gebeurtenishubs.
- [Gegevensontvanger Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): gebruik deze rol om toegang te krijgen tot bronnen van Event Hubs.   

> [!IMPORTANT]
> In onze preview-release worden ondersteuning gegeven voor het toevoegen van toegangsrechten voor gebeurtenishubs aan de rol Eigenaar of Bijdrager. De toegangsrechten voor gegevens voor de rol Eigenaar en inzender worden echter niet langer gerespecteerd. Als u de rol Eigenaar of inzender gebruikt, schakelt u over naar de rol Azure Event Hubs Data Owner.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>RBAC-rollen toewijzen via de Azure-portal  
Zie [dit artikel](..//role-based-access-control/role-assignments-portal.md)voor meer informatie over het beheren van toegang tot Azure-resources met RBAC en de Azure-portal. 

Nadat u het juiste bereik voor een roltoewijzing hebt bepaald, navigeert u naar die bron in de Azure-portal. Geef de Instellingen voor toegangsbeheer (IAM) voor de resource weer en volg deze instructies om roltoewijzingen te beheren:

> [!NOTE]
> In de onderstaande stappen wordt een rol toegewezen aan uw gebeurtenishub onder de naamruimten van gebeurtenishubs, maar u dezelfde stappen volgen om een rol toe te wijzen die wordt toegewezen aan een gebeurtenishubsbron.

1. Navigeer in de [Azure-portal](https://portal.azure.com/)naar de naamruimte van uw gebeurtenishubs.
2. Selecteer **op** de pagina Overzicht de gebeurtenishub waarvoor u een rol wilt toewijzen.

    ![Selecteer uw gebeurtenishub](./media/authenticate-application/select-event-hub.png)
1. Selecteer **Toegangsbeheer (IAM)** om toegangsbeheerinstellingen voor de gebeurtenishub weer te geven. 
1. Selecteer het tabblad **Toewijzingen van rollen** om de lijst met roltoewijzingen weer te geven. Selecteer de knop **Toevoegen** op de werkbalk en selecteer **Roltoewijzing toevoegen**. 

    ![Knop Toevoegen op de werkbalk](./media/authenticate-application/role-assignments-add-button.png)
1. Ga op de pagina **Roltoewijzing toevoegen** de volgende stappen uit:
    1. Selecteer de **rol Gebeurtenishubs** die u wilt toewijzen. 
    1. Zoek naar de **beveiligingsprincipal** (gebruiker, groep, serviceprincipal) waaraan u de rol wilt toewijzen.
    1. Selecteer **Opslaan** om de roltoewijzing op te slaan. 

        ![Rol toewijzen aan een gebruiker](./media/authenticate-application/assign-role-to-user.png)
    4. De identiteit aan wie u de rol hebt toegewezen, wordt weergegeven onder die rol. In de volgende afbeelding ziet u bijvoorbeeld dat Azure-gebruikers zich in de rol Azure Event Hubs Data Owner bevinden. 
        
        ![Gebruiker in de lijst](./media/authenticate-application/user-in-list.png)

U vergelijkbare stappen uitvoeren om een rol toe te wijzen die is toegewezen aan naamruimte van gebeurtenishubs, resourcegroep of abonnement. Zodra u de rol en het bereik ervan hebt gedefinieerd, u dit gedrag testen met voorbeelden [op deze GitHub-locatie.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)


## <a name="authenticate-from-an-application"></a>Verifiëren vanuit een toepassing
Een belangrijk voordeel van het gebruik van Azure AD met Event Hubs is dat uw referenties niet langer in uw code hoeven te worden opgeslagen. In plaats daarvan u een OAuth 2.0-toegangstoken aanvragen bij het identiteitsplatform van Microsoft. Azure AD verifieert de beveiligingsprincipal (een gebruiker, een groep of serviceprincipal) waarop de toepassing wordt uitgevoerd. Als verificatie slaagt, retourneert Azure AD het toegangstoken naar de toepassing en kan de toepassing vervolgens het toegangstoken gebruiken om aanvragen voor Azure Event Hubs te autoriseren.

In de volgende secties ziet u hoe u uw native toepassing of webtoepassing configureert voor verificatie met Microsoft-identiteitsplatform 2.0. Zie [Microsoft Identity Platform (v2.0) overzicht voor](../active-directory/develop/v2-overview.md)meer informatie over Microsoft identity platform 2.0.

Zie [Toegang tot Azure Active Directory-webtoepassingen autoriseren met behulp van de OAuth 2.0-codesubsidiestroom](../active-directory/develop/v2-oauth2-auth-code-flow.md)voor een overzicht van de oauth 2.0-codesubsidiestroom.

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Uw toepassing registreren bij een Azure AD-tenant
De eerste stap in het gebruik van Azure AD om gebeurtenishubsresources te autoriseren, is het registreren van uw clienttoepassing met een Azure AD-tenant van de [Azure-portal.](https://portal.azure.com/) Wanneer u uw clientaanvraag registreert, geeft u informatie over de toepassing aan AD. Azure AD biedt vervolgens een client-id (ook wel een toepassings-id genoemd) die u gebruiken om uw toepassing te koppelen aan Azure AD-runtime. Zie [Hoofdobjecten voor toepassingen en services in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)voor meer informatie over de client-id. 

In de volgende afbeeldingen worden stappen weergegeven voor het registreren van een webtoepassing:

![Een toepassing registreren](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Als u uw aanvraag registreert als een native toepassing, u een geldige URI opgeven voor de Omleiding URI. Voor native toepassingen hoeft deze waarde geen echte URL te zijn. Voor webtoepassingen moet de omleidinguri een geldige URI zijn, omdat hiermee de URL wordt opgegeven waaraan tokens worden verstrekt.

Nadat u uw aanvraag hebt geregistreerd, ziet u de id van de **toepassing (client)** onder **Instellingen:**

![Toepassings-ID van de geregistreerde aanvraag](./media/authenticate-application/application-id.png)

Zie Toepassingen integreren met Azure Active [Directory](../active-directory/develop/quickstart-v2-register-an-app.md)voor meer informatie over het registreren van een toepassing bij Azure AD.


### <a name="create-a-client-secret"></a>Een klantgeheim maken   
De toepassing heeft een geheim van de klant nodig om zijn identiteit te bewijzen bij het aanvragen van een token. Voer deze stappen uit om het clientgeheim toe te voegen.

1. Navigeer naar uw app-registratie in de Azure-portal.
1. Selecteer de instelling **Certificaten & geheimen.**
1. Selecteer **onder Klantgeheimen** **Nieuw klantgeheim** om een nieuw geheim te maken.
1. Geef een beschrijving voor het geheim en kies het gewenste verloopinterval.
1. Kopieer onmiddellijk de waarde van het nieuwe geheim naar een veilige locatie. De vulwaarde wordt slechts één keer aan u weergegeven.

    ![Clientgeheim](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>Clientbibliotheken voor tokenacquisitie  
Zodra u uw toepassing hebt geregistreerd en machtigingen hebt verleend voor het verzenden/ontvangen van gegevens in Azure Event Hubs, u code toevoegen aan uw toepassing om een beveiligingsprincipal te verifiëren en OAuth 2.0-token te verkrijgen. Als u het token wilt verifiëren en aanschaffen, u een van de verificatiebibliotheken van het [Microsoft-identiteitsplatform](../active-directory/develop/reference-v2-libraries.md) of een andere open-sourcebibliotheek gebruiken die OpenID of Connect 1.0 ondersteunt. Uw toepassing kan vervolgens het toegangstoken gebruiken om een aanvraag voor Azure Event Hubs te autoriseren.

Zie het gedeelte [Scenario's](https://aka.ms/msal-net-scenarios) van de Microsoft Authentication Library [(MSAL) voor .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub-repository voor een lijst met scenario's waarvoor tokens worden ondersteund.

## <a name="samples"></a>Voorbeelden
- [Voorbeelden van Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Deze voorbeelden maken gebruik van de oude **Microsoft.Azure.EventHubs-bibliotheek,** maar u deze eenvoudig bijwerken naar de nieuwste **Azure.Messaging.EventHubs-bibliotheek.** Zie de [handleiding voor het migreren van Microsoft.Azure.EventHubs naar Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)als u het voorbeeld wilt verplaatsen van het gebruik van de oude bibliotheek naar de nieuwe bibliotheek.
- [Azure.Messaging.EventHubs-voorbeelden](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Dit voorbeeld is bijgewerkt om de nieuwste **Azure.Messaging.EventHubs-bibliotheek** te gebruiken.

## <a name="next-steps"></a>Volgende stappen
- Zie [Wat is role-based access control (RBAC)](../role-based-access-control/overview.md)voor meer informatie over RBAC?
- Zie de volgende artikelen voor meer informatie over het toewijzen en beheren van RBAC-roltoewijzingen met Azure PowerShell, Azure CLI of de REST API:
    - [Beheer op rollen gebaseerde toegangscontrole (RBAC) met Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Functiegebaseerde toegangscontrole (RBAC) beheren met Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Functiegebaseerde toegangscontrole (RBAC) beheren met de REST API](../role-based-access-control/role-assignments-rest.md)
    - [Functiegebaseerde toegangscontrole (RBAC) beheren met Azure Resource Manager-sjablonen](../role-based-access-control/role-assignments-template.md)

Zie de volgende gerelateerde artikelen:
- [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot gebeurtenishubsbronnen](authenticate-managed-identity.md)
- [Aanvragen voor Azure Event Hubs verifiëren met behulp van gedeelde toegangshandtekeningen](authenticate-shared-access-signature.md)
- [Toegang tot gebeurtenishubsresources autoriseren met Azure Active Directory](authorize-access-azure-active-directory.md)
- [Toegang tot bronnen van gebeurtenishubs autoriseren met behulp van gedeelde toegangshandtekeningen](authorize-access-shared-access-signature.md)

