---
title: Een Azure AD-tenant maken voor Azure Red Hat OpenShift
description: Zo maak je een Azure Active Directory -tenant (Azure AD) om uw Microsoft Azure Red Hat OpenShift-cluster te hosten.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243690"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Een Azure AD-tenant maken voor Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift vereist een [Azure Active Directory (Azure AD)-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) waarin u uw cluster maken. Een *tenant* is een speciaal exemplaar van Azure AD dat een organisatie of app-ontwikkelaar ontvangt wanneer deze een relatie met Microsoft maakt door zich aan te melden voor Azure, Microsoft Intune of Microsoft 365. Elke Azure AD-tenant is verschillend en staat los van andere Azure AD-tenants en heeft zijn eigen werk- en schoolidentiteiten en app-registraties.

Als u nog geen Azure AD-tenant hebt, volgt u deze instructies om er een te maken.

## <a name="create-a-new-azure-ad-tenant"></a>Een nieuwe Azure AD-tenant maken

Ga als lid van het nieuwe bedrijf naar een tenant:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met het account dat u wilt koppelen aan uw Azure Red Hat OpenShift-cluster.
2. Open het [Azure Active Directory-blad](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) om een nieuwe tenant te maken (ook wel een nieuwe *Azure Active Directory genoemd).*
3. Geef een **organisatienaam op**.
4. Geef een **oorspronkelijke domeinnaam op.** Dit zal *onmicrosoft.com* toegevoegd aan het. U de waarde voor *organisatienaam* hier opnieuw gebruiken.
5. Kies een land of regio waar de huurder wordt gemaakt.
6. Klik **op Maken**.
7. Nadat uw Azure AD-tenant is gemaakt, selecteert u de **klik hier om uw nieuwe mapkoppeling te beheren.** Uw nieuwe tenantnaam moet rechtsboven in de Azure-portal worden weergegeven:  

    ![Schermafbeelding van de portal met de tenantnaam rechtsboven][tenantcallout]  

8. Noteer de *tenant-id,* zodat u later opgeven waar u uw Azure Red Hat OpenShift-cluster maken. In de portal ziet u nu het overzichtsblad azure Active Directory voor uw nieuwe tenant. Selecteer **Eigenschappen** en kopieer de waarde voor uw **directory-id**. We verwijzen naar deze `TENANT` waarde als in de [zelfstudie van Een Azure Red Hat OpenShift](tutorial-create-cluster.md) maken.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Resources

Bekijk [Azure Active Directory-documentatie](https://docs.microsoft.com/azure/active-directory/) voor meer informatie over [Azure AD-tenants](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het maken van een serviceprincipal, het genereren van een clientgeheim en de terugroepen-URL voor verificatie en het maken van een nieuwe Active Directory-gebruiker voor het testen van apps op uw Azure Red Hat OpenShift-cluster.

[Een object en gebruiker maken voor Azure AD-apps](howto-aad-app-configuration.md)
