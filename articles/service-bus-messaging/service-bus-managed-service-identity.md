---
title: Beheerde identiteiten voor Azure-resources met Service Bus
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruiken om toegang te krijgen met Azure Service Bus-entiteiten (wachtrijen, onderwerpen en abonnementen).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 89de6bf80d14ec77fe6b1f98b6e1d15c6e573fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756280"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Azure Service Bus-bronnen
[Beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) is een cross-Azure-functie waarmee u een veilige identiteit maken die is gekoppeld aan de implementatie waaronder uw toepassingscode wordt uitgevoerd. U die identiteit vervolgens koppelen aan access-control-rollen die aangepaste machtigingen verlenen voor toegang tot specifieke Azure-bronnen die uw toepassing nodig heeft.

Met beheerde identiteiten beheert het Azure-platform deze runtime-identiteit. U hoeft toegangssleutels niet op te slaan en te beveiligen in uw toepassingscode of -configuratie, noch voor de identiteit zelf, noch voor de resources die u moet openen. Een Service Bus-client-app die wordt uitgevoerd in een Azure App Service-toepassing of in een virtuele machine met ingeschakelde beheerde entiteiten voor Azure-resources-ondersteuning, hoeft geen SAS-regels en -sleutels of andere toegangstokens te verwerken. De client-app heeft alleen het eindpuntadres van de naamruimte servicebusberichten nodig. Wanneer de app verbinding maakt, bindt Service Bus de context van de beheerde entiteit aan de client in een bewerking die later in dit artikel in een voorbeeld wordt weergegeven. Zodra deze is gekoppeld aan een beheerde identiteit, kan uw Service Bus-client alle geautoriseerde bewerkingen uitvoeren. Autorisatie wordt verleend door een beheerde entiteit te koppelen aan servicebusrollen. 

## <a name="overview"></a>Overzicht
Wanneer een beveiligingsprincipal (een gebruiker, groep of toepassing) probeert toegang te krijgen tot een servicebusentiteit, moet de aanvraag worden geautoriseerd. Met Azure AD is toegang tot een bron een proces in twee stappen. 

 1. Eerst wordt de identiteit van de beveiligingsprincipal geverifieerd en wordt een OAuth 2.0-token geretourneerd. De resourcenaam om een `https://servicebus.azure.net`token aan te vragen is .
 1. Vervolgens wordt het token doorgegeven als onderdeel van een verzoek aan de Service Bus-service om toegang tot de opgegeven bron te autoriseren.

De verificatiestap vereist dat een toepassingsaanvraag een OAuth 2.0-toegangstoken bevat tijdens runtime. Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure VM, een virtuele machineschaalset of een Azure-functie-app, kan deze een beheerde identiteit gebruiken om toegang te krijgen tot de bronnen. 

De autorisatiestap vereist dat een of meer RBAC-rollen worden toegewezen aan de beveiligingsprincipal. Azure Service Bus biedt RBAC-rollen die sets machtigingen voor Service Bus-bronnen omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal bepalen de machtigingen die de opdrachtgever zal hebben. Zie [Ingebouwde RBAC-rollen voor Azure Service Bus voor](#built-in-rbac-roles-for-azure-service-bus)meer informatie over het toewijzen van RBAC-rollen aan Azure Service Bus. 

Native toepassingen en webtoepassingen die aanvragen indienen bij Service Bus kunnen ook worden geautoriseerd met Azure AD. In dit artikel ziet u hoe u een toegangstoken aanvraagt en deze gebruiken om aanvragen voor Service Bus-bronnen te autoriseren. 


## <a name="assigning-rbac-roles-for-access-rights"></a>RBAC-rollen toewijzen voor toegangsrechten
Azure Active Directory (Azure AD) geeft toegangsrechten voor beveiligde bronnen door middel [van rbac (role-based access control).](../role-based-access-control/overview.md) Azure Service Bus definieert een set ingebouwde RBAC-rollen die veelvoorkomende sets machtigingen omvatten die worden gebruikt om toegang te krijgen tot Service Bus-entiteiten en u ook aangepaste rollen definiëren voor toegang tot de gegevens.

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligingsprincipal, verleent Azure toegang tot deze bronnen voor die beveiligingsprincipal. Toegang kan worden beperkt tot het abonnementsniveau, de resourcegroep of de naamruimte servicebus. Een Azure AD-beveiligingsprincipal kan een gebruiker, een groep, een hoofd van de toepassingsservice of een beheerde identiteit voor Azure-resources zijn.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Ingebouwde RBAC-rollen voor Azure Service Bus
Voor Azure Service Bus is het beheer van naamruimten en alle gerelateerde resources via de Azure-portal en de Azure Resource Management API al beveiligd met behulp van het *RBAC-model (Role-based Access Control).* Azure biedt de onderstaande ingebouwde RBAC-rollen voor het toestaan van toegang tot een naamruimte van een ServiceBus:

- [Eigenaar van Azure Service Bus-gegevens:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)hiermee u gegevenstoegang tot naamruimte van servicebus en de entiteiten daarvan (wachtrijen, onderwerpen, abonnementen en filters)
- [Azure Service Bus Data Sender](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Gebruik deze rol om verzendtoegang te geven tot servicebusnaamruimte en de entiteiten daarvan.
- [Azure Service Bus Data Receiver](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Gebruik deze rol om toegang te krijgen tot servicebusnaamruimte en de entiteiten ervan. 

## <a name="resource-scope"></a>Resourcebereik 
Voordat u een RBAC-rol toewijst aan een beveiligingsprincipal, bepaalt u de toegangsruimte die de beveiligingsprincipal moet hebben. Best practices dicteren dat het altijd het beste is om alleen de smalst mogelijke ruimte te verlenen.

In de volgende lijst worden de niveaus beschreven waarop u toegang tot servicebusbronnen openen, te beginnen met het kleinste bereik:

- **Wachtrij,** **onderwerp**of **abonnement**: Roltoewijzing is van toepassing op de specifieke entiteit ServiceBus. Momenteel biedt de Azure-portal geen ondersteuning voor het toewijzen van gebruikers/groepen/beheerde identiteiten aan RBAC-rollen servicebus op abonnementsniveau. Hier vindt u een voorbeeld van het gebruik van de opdracht Azure CLI: [az-role-assignment-create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) om een identiteit toe te wijzen aan een RBAC-rol servicebus: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Servicebusnaamruimte**: Roltoewijzing omvat de volledige topologie van Service Bus onder de naamruimte en aan de aan deze servicegroep gekoppelde consumentengroep.
- **Resourcegroep**: Roltoewijzing is van toepassing op alle servicebusbronnen onder de resourcegroep.
- **Abonnement**: Roltoewijzing is van toepassing op alle servicebusbronnen in alle resourcegroepen in het abonnement.

> [!NOTE]
> Houd er rekening mee dat het tot vijf minuten kan duren voordat RBAC-roltoewijzingen worden uitgevoerd. 

Zie [Roldefinities begrijpen](../role-based-access-control/role-definitions.md#management-and-data-operations)voor meer informatie over hoe ingebouwde rollen worden gedefinieerd. Zie Aangepaste rollen maken voor [Azure Role-Based Access Control](../role-based-access-control/custom-roles.md)voor informatie over het maken van aangepaste RBAC-rollen.

## <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten inschakelen op een virtuele machine
Voordat u beheerde identiteiten voor Azure Resources gebruiken om Service Bus-bronnen van uw VM te autoriseren, moet u eerst beheerde identiteiten voor Azure Resources op de VM inschakelen. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure Resources:

- [Azure-portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-clientbibliotheken](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Machtigingen verlenen aan een beheerde identiteit in Azure AD
Als u een aanvraag voor de Service Bus-service wilt autoriseren vanuit een beheerde identiteit in uw toepassing, configureert u eerst RBAC-instellingen (Role-based access control) voor die beheerde identiteit. Azure Service Bus definieert RBAC-rollen die machtigingen bevatten voor het verzenden en lezen vanuit Service Bus. Wanneer de RBAC-rol is toegewezen aan een beheerde identiteit, krijgt de beheerde identiteit toegang tot servicebusentiteiten op het juiste bereik.

Zie [Verifiëren en autoriseren met Azure Active Directory voor toegang tot Service Bus-bronnen voor](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus)meer informatie over het toewijzen van RBAC-rollen.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Servicebus gebruiken met beheerde identiteiten voor Azure-resources
Als u Service Bus met beheerde identiteitwilt gebruiken, moet u de identiteit van de rol en het juiste bereik toewijzen. De procedure in deze sectie maakt gebruik van een eenvoudige toepassing die wordt uitgevoerd onder een beheerde identiteit en toegang tot Service Bus-bronnen.

Hier gebruiken we een voorbeeldvan een webtoepassing die wordt gehost in [Azure App Service.](https://azure.microsoft.com/services/app-service/) Zie [Een web-app voor ASP.NET Core maken in Azure](../app-service/app-service-web-get-started-dotnet.md) voor stapsgewijze instructies voor het maken van een webtoepassing.

Voer de volgende stappen uit nadat de toepassing is gemaakt: 

1. Ga naar **Instellingen** en selecteer **Identiteit**. 
1. Selecteer de **status** aan **.** 
1. Selecteer **Opslaan** om de instelling op te slaan. 

    ![Beheerde identiteit voor een web-app](./media/service-bus-managed-service-identity/identity-web-app.png)

Nadat u deze instelling hebt ingeschakeld, wordt een nieuwe service-identiteit gemaakt in uw Azure Active Directory (Azure AD) en geconfigureerd in de App Service-host.

Wijs deze serviceidentiteit nu toe aan een rol in het vereiste bereik in uw Service Bus-bronnen.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>RBAC-rollen toewijzen met de Azure-portal
Als u een rol wilt toewijzen aan een naamruimte van een servicebus, navigeert u naar de naamruimte in de Azure-portal. Geef de Instellingen voor Toegangsbeheer (IAM) voor de resource weer en volg deze instructies om roltoewijzingen te beheren:

> [!NOTE]
> In de volgende stappen wordt een functie voor service-identiteit aan de naamruimten van uw servicebus toegeschreven. U dezelfde stappen volgen om een rol toe te wijzen aan andere ondersteunde scopes (resourcegroep en abonnement). 
> 
> [Maak een naamruimte voor ServiceBusBerichten](service-bus-create-namespace-portal.md) als u deze niet hebt. 

1. Navigeer in de Azure-portal naar de naamruimte van uw ServiceBus en geef het **overzicht** voor de naamruimte weer. 
1. Selecteer **Toegangsbeheer (IAM)** in het linkermenu om toegangsbeheerinstellingen voor de naamruimte van de servicebus weer te geven.
1.  Selecteer het tabblad **Toewijzingen van rollen** om de lijst met roltoewijzingen weer te geven.
3.  Selecteer **Toevoegen** om een nieuwe rol toe te voegen.
4.  Selecteer op de pagina **Roltoewijzing toevoegen** de Azure Service Bus-rollen die u wilt toewijzen. Zoek vervolgens naar de serviceidentiteit die u had geregistreerd om de rol toe te wijzen.
    
    ![Pagina Roltoewijzing toevoegen](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Selecteer **Opslaan**. De identiteit aan wie u de rol hebt toegewezen, wordt weergegeven onder die rol. In de volgende afbeelding ziet u bijvoorbeeld dat de service-identiteit de eigenaar van Azure Service Bus Data heeft.
    
    ![Identiteit toegewezen aan een rol](./media/service-bus-managed-service-identity/role-assigned.png)

Zodra u de rol hebt toegewezen, heeft de webtoepassing toegang tot de servicebusentiteiten onder het gedefinieerde bereik. 

### <a name="run-the-app"></a>De app uitvoeren

Wijzig nu de standaardpagina van de ASP.NET-toepassing die u hebt gemaakt. U de webtoepassingscode gebruiken vanuit [deze GitHub-repository.](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)  

De pagina Default.aspx is uw bestemmingspagina. De code is te vinden in het Default.aspx.cs bestand. Het resultaat is een minimale webapplicatie met een paar invoervelden en met **verzend-** en **ontvangstknoppen** die verbinding maken met Service Bus om berichten te verzenden of te ontvangen.

Houd er rekening mee hoe het object [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) is geïnitialiseerd. In plaats van de SAS-tokenprovider (Shared Access Token) te gebruiken, `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` maakt de code een tokenprovider voor de beheerde identiteit met de oproep. Als zodanig zijn er geen geheimen te behouden en te gebruiken. De stroom van de beheerde identiteitscontext naar Service Bus en de autorisatiehandshake worden automatisch afgehandeld door de tokenprovider. Het is een eenvoudiger model dan het gebruik van SAS.

Nadat u deze wijzigingen hebt aangebracht, publiceert en voert u de toepassing uit. U eenvoudig de juiste publicatiegegevens verkrijgen door een publicatieprofiel te downloaden en vervolgens te importeren in Visual Studio:

![Publicatieprofiel opdoen](./media/service-bus-managed-service-identity/msi3.png)
 
Als u berichten wilt verzenden of ontvangen, voert u de naam van de naamruimte en de naam van de entiteit die u hebt gemaakt in. Klik vervolgens op **Verzenden** of **ontvangen.**


> [!NOTE]
> - De beheerde identiteit werkt alleen binnen de Azure-omgeving, in App-services, Azure VM's en schaalsets. Voor .NET-toepassingen biedt de Microsoft.Azure.Services.AppAuthentication-bibliotheek, die wordt gebruikt door het NuGet-pakket servicebus, een abstractie over dit protocol en ondersteunt het een lokale ontwikkelingservaring. Met deze bibliotheek u uw code ook lokaal testen op uw ontwikkelingsmachine met behulp van uw gebruikersaccount van Visual Studio, Azure CLI 2.0 of Active Directory Integrated Authentication. Zie [Service-to-service-verificatie naar Azure Key Vault met .NET](../key-vault/service-to-service-authentication.md)voor meer informatie over lokale ontwikkelingsopties met deze bibliotheek.  
> 
> - Momenteel werken beheerde identiteiten niet met implementatiesleuven van App-service.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over berichten over Service Bus:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
