---
title: Azure AD-App beperken tot een set gebruikers | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het beperken van de toegang tot uw apps die zijn geregistreerd in azure AD voor een geselecteerde groep gebruikers.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cccd2df334828c0b8103e4da2ffcd8549673b69c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696993"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users"></a>Procedure: uw Azure AD-App beperken tot een set gebruikers

Toepassingen die zijn geregistreerd in een Azure Active Directory-Tenant (Azure AD), zijn standaard beschikbaar voor alle gebruikers van de Tenant die zijn geverifieerd.

In het geval van een [multi tenant](howto-convert-app-to-be-multi-tenant.md) -app kunnen alle gebruikers in de Azure AD-Tenant waar deze app is ingericht, toegang krijgen tot deze toepassing zodra ze zijn geverifieerd in hun respectieve Tenant.

Tenant beheerders en ontwikkel aars hebben vaak vereisten waarbij een app moet worden beperkt tot een bepaalde groep gebruikers. Ontwikkel aars kunnen hetzelfde doen met behulp van populaire verificatie patronen als op rollen gebaseerd Access Control (RBAC), maar deze benadering vereist een aanzienlijke hoeveelheid werk voor een deel van de ontwikkelaar.

Met Azure AD kunnen Tenant beheerders en ontwikkel aars een app beperken tot een specifieke set gebruikers of beveiligings groepen in de Tenant.

## <a name="supported-app-configurations"></a>Ondersteunde app-configuraties

De optie om een app te beperken tot een specifieke set gebruikers of beveiligings groepen in een Tenant werkt met de volgende typen toepassingen:

- Toepassingen die zijn geconfigureerd voor federatieve eenmalige aanmelding met op SAML gebaseerde verificatie
- Toepassings proxy toepassingen die gebruikmaken van pre-authenticatie van Azure AD
- Toepassingen die rechtstreeks zijn gebouwd op het Azure AD-toepassings platform die gebruikmaken van OAuth 2.0/OpenID Connect Connect-verificatie nadat een gebruiker of beheerder heeft ingestemd met die toepassing.

     > [!NOTE]
     > Deze functie is alleen beschikbaar voor web-app/Web-API en bedrijfs toepassingen. Apps die zijn geregistreerd als [systeem eigen](quickstart-v1-integrate-apps-with-azure-ad.md) , kunnen niet worden beperkt tot een set gebruikers of beveiligings groepen in de Tenant.

## <a name="update-the-app-to-enable-user-assignment"></a>De app bijwerken om de gebruikers toewijzing in te scha kelen

Er zijn twee manieren om een toepassing met ingeschakelde gebruikers toewijzing te maken. Voor één is de rol **globale beheerder** vereist, de tweede niet.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Bedrijfs toepassingen (vereist de rol globale beheerder)

1. Ga naar de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder**.
1. On the top bar, select the signed-in account. 
1. Under **Directory**, select the Azure AD tenant where the app will be registered.
1. In the navigation on the left, select **Azure Active Directory**. Als Azure Active Directory niet beschikbaar is in het navigatie venster, voert u de volgende stappen uit:

    1. Selecteer **alle services** boven aan het hoofd navigatie menu aan de linkerkant.
    1. Type in **Azure Active Directory** in the filter search box, and then select the **Azure Active Directory** item from the result.

1. In the **Azure Active Directory** pane, select **Enterprise Applications** from the **Azure Active Directory** left-hand navigation menu.
1. Select **All Applications** to view a list of all your applications.

     If you do not see the application you want show up here, use the various filters at the top of the **All applications** list to restrict the list or scroll down the list to locate your application.

1. Select the application you want to assign a user or security group to from the list.
1. On the application's **Overview** page, select **Properties** from the application’s left-hand navigation menu.
1. Locate the setting **User assignment required?** and set it to **Yes**. When this option is set to **Yes**, users must first be assigned to this application before they can access it.
1. Select **Save** to save this configuration change.

### <a name="app-registration"></a>App-registratie

1. Go to the [**Azure portal**](https://portal.azure.com/).
1. On the top bar, select the signed-in account. 
1. Under **Directory**, select the Azure AD tenant where the app will be registered.
1. In the navigation on the left, select **Azure Active Directory**.
1. In the **Azure Active Directory** pane, select **App Registrations** from the **Azure Active Directory** left-hand navigation menu.
1. Create or select the app you want to manage. You need to be **Owner** of this app registration.
1. On the application's **Overview** page, follow the **Managed application in local directory** link under the essentials in the top of the page. This will take you to the _managed Enterprise Application_ of your app registration.
1. From the navigation blade on the left, select **Properties**.
1. Locate the setting **User assignment required?** and set it to **Yes**. When this option is set to **Yes**, users must first be assigned to this application before they can access it.
1. Select **Save** to save this configuration change.

## <a name="assign-users-and-groups-to-the-app"></a>Assign users and groups to the app

Once you've configured your app to enable user assignment, you can go ahead and assign users and groups to the app.

1. Select the **Users and groups** pane in the application’s left-hand navigation menu.
1. At the top of the **Users and groups** list, select the **Add user** button to open the **Add Assignment** pane.
1. Select the **Users** selector from the **Add Assignment** pane. 

     A list of users and security groups will be shown along with a textbox to search and locate a certain user or group. This screen allows you to select multiple users and groups in one go.

1. Once you are done selecting the users and groups, press the **Select** button on bottom to move to the next part.
1. Press the **Assign** button on the bottom to finish the assignments of users and groups to the app. 
1. Confirm that the users and groups you added are showing up in the updated **Users and groups** list.

