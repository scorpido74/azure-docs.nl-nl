---
title: Een Azure AD-Tenant maken voor Azure Red Hat open Shift
description: U kunt als volgt een Azure Active Directory-Tenant (Azure AD) maken om uw Microsoft Azure Red Hat open Shift-cluster te hosten.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 775aa44043e9c8900bd0c040ab603736314ea34e
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469980"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Een Azure AD-Tenant maken voor Azure Red Hat open Shift

Voor Microsoft Azure Red Hat open Shift is een [Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) -Tenant vereist waarin u uw cluster kunt maken. Een *Tenant* is een toegewezen exemplaar van Azure AD dat een organisatie of app-ontwikkelaar ontvangt wanneer ze een relatie met micro soft maken door zich aan te melden voor Azure, Microsoft Intune of Microsoft 365. Elke Azure AD-Tenant is uniek en los van andere Azure AD-tenants en heeft zijn eigen werk-en school identiteiten en app-registraties.

Als u nog geen Azure AD-Tenant hebt, volgt u deze instructies om er een te maken.

## <a name="create-a-new-azure-ad-tenant"></a>Een nieuwe Azure AD-tenant maken

Een Tenant maken:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account dat u wilt koppelen aan uw Azure Red Hat open Shift-cluster.
2. Open de [blade Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) om een nieuwe Tenant te maken (ook wel een nieuwe *Azure Active Directory*genoemd).
3. Geef een **naam**op voor de organisatie.
4. Geef een **initiële domein naam**op. Er wordt een *onmicrosoft.com* aan toegevoegd. U kunt de waarde voor *organisatie naam* hier opnieuw gebruiken.
5. Kies een land of regio waar de Tenant wordt gemaakt.
6. Klik op **Create**.
7. Nadat uw Azure AD-Tenant is gemaakt, selecteert **u de koppeling Klik hier om uw nieuwe directory te beheren** . De naam van uw nieuwe Tenant wordt weer gegeven in de rechter bovenhoek van de Azure Portal:  

    ![Scherm afbeelding van de portal met de naam van de Tenant in de rechter bovenhoek][tenantcallout]  

8. Noteer de *Tenant-id* zodat u later kunt opgeven waar u uw Azure Red Hat open Shift-cluster wilt maken. In de portal ziet u nu de Blade Azure Active Directory overzicht voor uw nieuwe Tenant. Selecteer **Eigenschappen** en kopieer de waarde voor de **map-id**. We verwijzen naar deze waarde, zoals `TENANT` in de zelf studie [een Azure Red Hat open Shift-cluster maken](tutorial-create-cluster.md) .

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Resources

Raadpleeg de [documentatie van Azure Active Directory](../active-directory/index.yml) voor meer informatie over [Azure AD-tenants](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het maken van een Service-Principal, het genereren van een client geheim en verificatie-call back-URL en het maken van een nieuwe Active Directory gebruiker voor het testen van apps op uw Azure Red Hat open Shift-cluster.

[Een object en gebruiker maken voor Azure AD-apps](howto-aad-app-configuration.md)