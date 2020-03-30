---
title: Een toepassing verifiëren om toegang te krijgen tot Azure Service Bus-entiteiten
description: In dit artikel vindt u informatie over het verifiëren van een toepassing met Azure Active Directory om toegang te krijgen tot Azure Service Bus-entiteiten (wachtrijen, onderwerpen, enz.)
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259290"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Een toepassing met Azure Active Directory verifiëren en autoriseren om toegang te krijgen tot Azure Service Bus-entiteiten
Azure Service Bus ondersteunt het gebruik van Azure Active Directory (Azure AD) om aanvragen te autoriseren voor servicebusentiteiten (wachtrijen, onderwerpen, abonnementen of filters). Met Azure AD u RBAC (Role-based access control) gebruiken om machtigingen toe te kennen aan een beveiligingsprincipal, die mogelijk een hoofdvan de gebruikers-, groep- of toepassingsserviceiser. Zie [De verschillende rollen begrijpen](../role-based-access-control/overview.md)voor meer informatie over rollen en roltoewijzingen.

## <a name="overview"></a>Overzicht
Wanneer een beveiligingsprincipal (een gebruiker, groep of toepassing) probeert toegang te krijgen tot een servicebusentiteit, moet de aanvraag worden geautoriseerd. Met Azure AD is toegang tot een bron een proces in twee stappen. 

 1. Eerst wordt de identiteit van de beveiligingsprincipal geverifieerd en wordt een OAuth 2.0-token geretourneerd. De resourcenaam om een `https://servicebus.azure.net`token aan te vragen is .
 1. Vervolgens wordt het token doorgegeven als onderdeel van een verzoek aan de Service Bus-service om toegang tot de opgegeven bron te autoriseren.

De verificatiestap vereist dat een toepassingsaanvraag een OAuth 2.0-toegangstoken bevat tijdens runtime. Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure VM, een virtuele machineschaalset of een Azure-functie-app, kan deze een beheerde identiteit gebruiken om toegang te krijgen tot de bronnen. Zie [Toegang tot Azure Service Bus-bronnen verifiëren met Azure Active Directory en beheerde identiteiten voor Azure Resources voor](service-bus-managed-service-identity.md)meer informatie over het verifiëren van aanvragen van een beheerde identiteit naar Service Bus-service. 

De autorisatiestap vereist dat een of meer RBAC-rollen worden toegewezen aan de beveiligingsprincipal. Azure Service Bus biedt RBAC-rollen die sets machtigingen voor Service Bus-bronnen omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal bepalen de machtigingen die de opdrachtgever zal hebben. Zie [Ingebouwde RBAC-rollen voor Azure Service Bus voor](#built-in-rbac-roles-for-azure-service-bus)meer informatie over het toewijzen van RBAC-rollen aan Azure Service Bus. 

Native toepassingen en webtoepassingen die aanvragen indienen bij Service Bus kunnen ook worden geautoriseerd met Azure AD. In dit artikel ziet u hoe u een toegangstoken aanvraagt en deze gebruiken om aanvragen voor Service Bus-bronnen te autoriseren. 


## <a name="assigning-rbac-roles-for-access-rights"></a>RBAC-rollen toewijzen voor toegangsrechten
Azure Active Directory (Azure AD) geeft toegangsrechten voor beveiligde bronnen door middel [van rbac (role-based access control).](../role-based-access-control/overview.md) Azure Service Bus definieert een set ingebouwde RBAC-rollen die veelvoorkomende sets machtigingen omvatten die worden gebruikt om toegang te krijgen tot Service Bus-entiteiten en u ook aangepaste rollen definiëren voor toegang tot de gegevens.

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligingsprincipal, verleent Azure toegang tot deze bronnen voor die beveiligingsprincipal. Toegang kan worden beperkt tot het abonnementsniveau, de resourcegroep of de naamruimte servicebus. Een Azure AD-beveiligingsprincipal kan een gebruiker, een groep, een hoofd van de toepassingsservice of een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)zijn.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Ingebouwde RBAC-rollen voor Azure Service Bus
Voor Azure Service Bus is het beheer van naamruimten en alle gerelateerde resources via de Azure-portal en de Azure Resource Management API al beveiligd met behulp van het *RBAC-model (Role-based Access Control).* Azure biedt de onderstaande ingebouwde RBAC-rollen voor het toestaan van toegang tot een naamruimte van een ServiceBus:

- [Eigenaar van Azure Service Bus-gegevens:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)hiermee u gegevenstoegang tot naamruimte van servicebus en de entiteiten daarvan (wachtrijen, onderwerpen, abonnementen en filters)
- [Azure Service Bus Data Sender](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Gebruik deze rol om verzendtoegang te geven tot servicebusnaamruimte en de entiteiten daarvan.
- [Azure Service Bus Data Receiver](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Gebruik deze rol om toegang te krijgen tot servicebusnaamruimte en de entiteiten ervan. 

## <a name="resource-scope"></a>Resourcebereik 
Voordat u een RBAC-rol toewijst aan een beveiligingsprincipal, bepaalt u de toegangsruimte die de beveiligingsprincipal moet hebben. Best practices dicteren dat het altijd het beste is om alleen de smalst mogelijke ruimte te verlenen.

In de volgende lijst worden de niveaus beschreven waarop u toegang tot servicebusbronnen openen, te beginnen met het kleinste bereik:

- **Wachtrij,** **onderwerp**of **abonnement**: Roltoewijzing is van toepassing op de specifieke entiteit ServiceBus. Momenteel biedt de Azure-portal geen ondersteuning voor het toewijzen van gebruikers/groepen/beheerde identiteiten aan RBAC-rollen servicebus op abonnementsniveau. 
- **Servicebusnaamruimte**: Roltoewijzing omvat de volledige topologie van Service Bus onder de naamruimte en aan de aan deze servicegroep gekoppelde consumentengroep.
- **Resourcegroep**: Roltoewijzing is van toepassing op alle servicebusbronnen onder de resourcegroep.
- **Abonnement**: Roltoewijzing is van toepassing op alle servicebusbronnen in alle resourcegroepen in het abonnement.

> [!NOTE]
> Houd er rekening mee dat het tot vijf minuten kan duren voordat RBAC-roltoewijzingen worden uitgevoerd. 

Zie [Roldefinities begrijpen](../role-based-access-control/role-definitions.md#management-and-data-operations)voor meer informatie over hoe ingebouwde rollen worden gedefinieerd. Zie Aangepaste rollen maken voor [Azure Role-Based Access Control](../role-based-access-control/custom-roles.md)voor informatie over het maken van aangepaste RBAC-rollen.


## <a name="assign-rbac-roles-using-the-azure-portal"></a>RBAC-rollen toewijzen via de Azure-portal  
Zie [dit artikel](..//role-based-access-control/role-assignments-portal.md)voor meer informatie over het beheren van toegang tot Azure-resources met RBAC en de Azure-portal. 

Nadat u het juiste bereik voor een roltoewijzing hebt bepaald, navigeert u naar die bron in de Azure-portal. Geef de Instellingen voor toegangsbeheer (IAM) voor de resource weer en volg deze instructies om roltoewijzingen te beheren:

> [!NOTE]
> In de onderstaande stappen wordt een rol aan de naamruimte van uw servicebus toegeschreven. U dezelfde stappen volgen om een rol toe te wijzen aan andere ondersteunde scopes (resourcegroep, abonnement, enz.).

1. Navigeer in de [Azure-portal](https://portal.azure.com/)naar de naamruimte van uw servicebus. Selecteer **Toegangsbeheer (IAM)** in het linkermenu om toegangsbeheerinstellingen voor de naamruimte weer te geven. Als u een naamruimte voor servicebus wilt maken, volgt u de instructies uit dit artikel: [Naamruimte voor servicebusberichten maken](service-bus-create-namespace-portal.md).

    ![Toegangsbeheer selecteren in het linkermenu](./media/authenticate-application/select-access-control-menu.png)
1. Selecteer het tabblad **Toewijzingen van rollen** om de lijst met roltoewijzingen weer te geven. Selecteer de knop **Toevoegen** op de werkbalk en selecteer **Roltoewijzing toevoegen**. 

    ![Knop Toevoegen op de werkbalk](./media/authenticate-application/role-assignments-add-button.png)
1. Ga op de pagina **Roltoewijzing toevoegen** de volgende stappen uit:
    1. Selecteer de **rol Servicebus** die u wilt toewijzen. 
    1. Zoek naar de **beveiligingsprincipal** (gebruiker, groep, serviceprincipal) waaraan u de rol wilt toewijzen.
    1. Selecteer **Opslaan** om de roltoewijzing op te slaan. 

        ![Rol toewijzen aan een gebruiker](./media/authenticate-application/assign-role-to-user.png)
    4. De identiteit aan wie u de rol hebt toegewezen, wordt weergegeven onder die rol. In de volgende afbeelding ziet u bijvoorbeeld dat Azure-gebruikers zich in de rol Azure Service Bus Data Owner bevinden. 
        
        ![Gebruiker in de lijst](./media/authenticate-application/user-in-list.png)

U vergelijkbare stappen uitvoeren om een rol toe te wijzen die is toegewezen aan een resourcegroep of een abonnement. Zodra u de rol en de reikwijdte ervan definieert, u dit gedrag testen met de [voorbeelden op GitHub.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)


## <a name="authenticate-from-an-application"></a>Verifiëren vanuit een toepassing
Een belangrijk voordeel van het gebruik van Azure AD met Service Bus is dat uw referenties niet langer in uw code hoeven te worden opgeslagen. In plaats daarvan u een OAuth 2.0-toegangstoken aanvragen bij het identiteitsplatform van Microsoft. Azure AD verifieert de beveiligingsprincipal (een gebruiker, een groep of serviceprincipal) waarop de toepassing wordt uitgevoerd. Als verificatie slaagt, retourneert Azure AD het toegangstoken naar de toepassing en kan de toepassing vervolgens het toegangstoken gebruiken om aanvragen voor Azure Service Bus te autoriseren.

In de volgende secties ziet u hoe u uw native toepassing of webtoepassing configureert voor verificatie met Microsoft-identiteitsplatform 2.0. Zie [Microsoft Identity Platform (v2.0) overzicht voor](../active-directory/develop/v2-overview.md)meer informatie over Microsoft identity platform 2.0.

Zie [Toegang tot Azure Active Directory-webtoepassingen autoriseren met behulp van de OAuth 2.0-codesubsidiestroom](../active-directory/develop/v2-oauth2-auth-code-flow.md)voor een overzicht van de oauth 2.0-codesubsidiestroom.

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Uw toepassing registreren bij een Azure AD-tenant
De eerste stap in het gebruik van Azure AD om Service Bus-entiteiten te autoriseren, is het registreren van uw clienttoepassing met een Azure AD-tenant van de [Azure-portal.](https://portal.azure.com/) Wanneer u uw clientaanvraag registreert, geeft u informatie over de toepassing aan AD. Azure AD biedt vervolgens een client-id (ook wel een toepassings-id genoemd) die u gebruiken om uw toepassing te koppelen aan Azure AD-runtime. Zie [Hoofdobjecten voor toepassingen en services in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)voor meer informatie over de client-id. 

In de volgende afbeeldingen worden stappen weergegeven voor het registreren van een webtoepassing:

![Een toepassing registreren](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Als u uw aanvraag registreert als een native toepassing, u een geldige URI opgeven voor de Omleiding URI. Voor native toepassingen hoeft deze waarde geen echte URL te zijn. Voor webtoepassingen moet de omleidinguri een geldige URI zijn, omdat hiermee de URL wordt opgegeven waaraan tokens worden verstrekt.

Nadat u uw aanvraag hebt geregistreerd, ziet u de id van de **toepassing (client)** onder **Instellingen:**

![Toepassings-ID van de geregistreerde aanvraag](./media/authenticate-application/application-id.png)

Zie Toepassingen integreren met Azure Active [Directory](../active-directory/develop/quickstart-v2-register-an-app.md)voor meer informatie over het registreren van een toepassing bij Azure AD.

> [!IMPORTANT]
> Noteer de **TenantId** en de **ApplicationId**. U hebt deze waarden nodig om de toepassing uit te voeren.

### <a name="create-a-client-secret"></a>Een klantgeheim maken   
De toepassing heeft een geheim van de klant nodig om zijn identiteit te bewijzen bij het aanvragen van een token. Voer deze stappen uit om het clientgeheim toe te voegen.

1. Navigeer naar uw app-registratie in de Azure-portal als u nog niet op de pagina bent.
1. Selecteer **Certificaten & geheimen** in het linkermenu.
1. Selecteer **onder Klantgeheimen** **Nieuw klantgeheim** om een nieuw geheim te maken.

    ![Nieuw klantgeheim - knop](./media/authenticate-application/new-client-secret-button.png)
1. Geef een beschrijving voor het geheim op en kies het gewenste verloopinterval en selecteer **Toevoegen**.

    ![Geheime pagina voor client toevoegen](./media/authenticate-application/add-client-secret-page.png)
1. Kopieer onmiddellijk de waarde van het nieuwe geheim naar een veilige locatie. De vulwaarde wordt slechts één keer aan u weergegeven.

    ![Clientgeheim](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Machtigingen voor de API servicebus
Als uw toepassing een consoletoepassing is, moet u een native toepassing registreren en **API-machtigingen voor Microsoft.ServiceBus** toevoegen aan de **ingestelde machtigingen.** Native applications hebben ook een **redirect-URI** in Azure AD nodig, die als id fungeert; de URI hoeft geen netwerkbestemming te zijn. Gebruik `https://servicebus.microsoft.com` voor dit voorbeeld, omdat de voorbeeldcode al die URI gebruikt.

### <a name="client-libraries-for-token-acquisition"></a>Clientbibliotheken voor tokenacquisitie  
Zodra u uw toepassing hebt geregistreerd en machtigingen hebt verleend voor het verzenden/ontvangen van gegevens in Azure Service Bus, u code toevoegen aan uw toepassing om een beveiligingsprincipal te verifiëren en OAuth 2.0-token te verkrijgen. Als u het token wilt verifiëren en aanschaffen, u een van de verificatiebibliotheken van het [Microsoft-identiteitsplatform](../active-directory/develop/reference-v2-libraries.md) of een andere open-sourcebibliotheek gebruiken die OpenID of Connect 1.0 ondersteunt. Uw toepassing kan vervolgens het toegangstoken gebruiken om een aanvraag voor Azure Service Bus te autoriseren.

Zie het gedeelte [Scenario's](https://aka.ms/msal-net-scenarios) van de Microsoft Authentication Library [(MSAL) voor .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub-repository voor een lijst met scenario's waarvoor tokens worden ondersteund.

## <a name="sample-on-github"></a>Voorbeeld op GitHub
Zie het volgende voorbeeld op GitHub: [Access control voor de functie van basisbeheer voor servicebus.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl) 

Gebruik de optie **Client Secret Login,** niet de optie **Interactieve gebruiker inloggen.** Wanneer u de clientgeheime optie gebruikt, ziet u geen pop-upvenster. De toepassing maakt gebruik van de tenant-id en app-id voor verificatie. 

### <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Voordat u het voorbeeld uitvoeren, bewerkt u het bestand **app.config** en stelt u, afhankelijk van uw scenario, de volgende waarden in:

- `tenantId`: Ingesteld op **tenantid-waarde.**
- `clientId`: Ingesteld op **applicationid-waarde.**
- `clientSecret`: Als u zich wilt aanmelden met het clientgeheim, maakt u het in Azure AD. Gebruik ook een web-app of API in plaats van een native app. Voeg ook de app toe onder **Toegangsbeheer (IAM)** in de naamruimte die u eerder hebt gemaakt.
- `serviceBusNamespaceFQDN`: Stel de volledige DNS-naam in van de nieuw gemaakte servicebusnaamruimte; bijvoorbeeld . `example.servicebus.windows.net`
- `queueName`: Stel in op de naam van de wachtrij die u hebt gemaakt.
- De omleidings-URI die u in uw app in de vorige stappen hebt opgegeven.

Wanneer u de consoletoepassing uitvoert, wordt u gevraagd een scenario te selecteren. Selecteer **Interactieve gebruikersaanmelding** door het nummer te typen en op ENTER te drukken. De applicatie geeft een inlogvenster weer, vraagt uw toestemming om toegang te krijgen tot Service Bus en gebruikt de service vervolgens om het scenario voor verzenden/ontvangen met behulp van de inlogidentiteit te doorlopen.


## <a name="next-steps"></a>Volgende stappen
- Zie [Wat is role-based access control (RBAC)](../role-based-access-control/overview.md)voor meer informatie over RBAC?
- Zie de volgende artikelen voor meer informatie over het toewijzen en beheren van RBAC-roltoewijzingen met Azure PowerShell, Azure CLI of de REST API:
    - [Beheer op rollen gebaseerde toegangscontrole (RBAC) met Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Functiegebaseerde toegangscontrole (RBAC) beheren met Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Functiegebaseerde toegangscontrole (RBAC) beheren met de REST API](../role-based-access-control/role-assignments-rest.md)
    - [Functiegebaseerde toegangscontrole (RBAC) beheren met Azure Resource Manager-sjablonen](../role-based-access-control/role-assignments-template.md)

Zie de volgende onderwerpen voor meer informatie over de Service Bus-berichtenservice

- [Service Bus RBAC monsters](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
- [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
- [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
