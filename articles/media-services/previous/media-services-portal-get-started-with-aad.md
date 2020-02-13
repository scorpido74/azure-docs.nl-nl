---
title: Aan de slag met Azure AD-verificatie met behulp van de Azure Portal | Microsoft Docs
description: Meer informatie over het gebruik van de Azure Portal om toegang te krijgen tot Azure Active Directory-verificatie (Azure AD) om de Azure Media Services API te gebruiken.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 4428187c985c1004c88f2ac20b0e5811803cce2a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162766"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Aan de slag met Azure AD-verificatie met behulp van de Azure Portal

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Maak kennis met de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Meer informatie over het gebruik van de Azure Portal om toegang te krijgen tot Azure Active Directory-verificatie (Azure AD) voor toegang tot de Azure Media Services API.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Als u geen account hebt, kunt u beginnen met een [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Een Media Services-account. Zie [een Azure Media Services-account maken met behulp van de Azure Portal](media-services-portal-create-account.md)voor meer informatie.
- Controleer de Access Azure Media Services- [API met Azure AD-verificatie voor meer informatie](media-services-use-aad-auth-to-access-ams-api.md). 

Wanneer u Azure AD-verificatie met Azure Media Services gebruikt, hebt u twee verificatie opties:

- **Gebruikers verificatie**. Verifieer een persoon die de app gebruikt om te communiceren met Media Services-resources. De interactieve toepassing moet eerst de gebruiker vragen om referenties. Een voor beeld is een beheer console-app die door geautoriseerde gebruikers wordt gebruikt voor het bewaken van coderings taken of live streamen. 
- **Service-Principal-verificatie**. Verifieer een service. Toepassingen die meestal gebruikmaken van deze verificatie methode zijn apps die daemon services, middelste laag Services of geplande taken uitvoeren: Web apps, functie-apps, Logic apps, Api's of een micro service.

> [!IMPORTANT]
> Media Services ondersteunt momenteel het Azure Access Control service-verificatie model. Access Control autorisatie wordt echter afgeschaft op 1 juni 2018. We raden u aan om zo snel mogelijk naar het Azure Active Directory-verificatiemodel te migreren.

## <a name="select-the-authentication-method"></a>De verificatie methode selecteren

1. Selecteer uw Media Services account in de [Azure Portal](https://portal.azure.com/).
2. Selecteer hoe u verbinding wilt maken met de Media Services-API.

    ![De pagina verbindings methode selecteren](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Gebruikersverificatie

Als u verbinding wilt maken met de Media Services-API met behulp van de optie voor gebruikers verificatie, moet de client-app een Azure AD-token aanvragen met de volgende para meters:  

* Azure AD-Tenant eindpunt
* Media Services resource-URI
* Client-ID van Media Services (systeem eigen) 
* Media Services (systeem eigen) URI voor omleiding van toepassing 
* Resource-URI voor REST Media Services

U kunt de waarden voor deze para meters ophalen op de pagina **Media Services-API met gebruikers verificatie** . 

![De pagina verbinding maken met gebruikers verificatie](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Als u verbinding maakt met de Media Services-API met behulp van de Media Services Microsoft .NET SDK, zijn de vereiste waarden voor u beschikbaar als onderdeel van de SDK. Zie [Azure AD-verificatie gebruiken om toegang te krijgen tot de Azure Media Services-API met .net](media-services-dotnet-get-started-with-aad.md)voor meer informatie.

Als u geen gebruik maakt van de Media Services .NET-client-SDK, moet u hand matig een Azure AD-token aanvraag maken met behulp van de para meters die eerder zijn besproken. Zie [de Azure AD-verificatie bibliotheek gebruiken om het Azure AD-token op te halen](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)voor meer informatie.

## <a name="service-principal-authentication"></a>Verificatie van service-principal

Als u verbinding wilt maken met de Media Services-API met behulp van de optie Service-Principal, moet uw middelste app (Web API of Web Application) een Azure AD-token aanvragen met de volgende para meters:  

* Azure AD-Tenant eindpunt
* Media Services resource-URI 
* Resource-URI voor REST Media Services
* Azure AD-toepassings waarden: de **client-id** en het **client geheim**

U kunt de waarden voor deze para meters ophalen op de pagina **verbinding maken met Media Services-API met Service-Principal** . Gebruik deze pagina om een nieuwe Azure AD-toepassing te maken of om een bestaande app te selecteren. Nadat u de Azure AD-app hebt geselecteerd, kunt u de client-ID ophalen (toepassings-ID) en de client Secret-waarden (sleutel) genereren. 

![Verbinding maken met de Service Principal-pagina](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Wanneer de Blade **Service-Principal** wordt geopend, wordt de eerste Azure AD-toepassing geselecteerd die voldoet aan de volgende criteria:

- Het is een geregistreerde Azure AD-toepassing.
- Het heeft Inzender of eigenaars Access Control machtigingen voor het account.

Nadat u een Azure AD-app hebt gemaakt of geselecteerd, kunt u een client geheim (sleutel) en de client-ID (toepassings-ID) maken en kopiëren. Het client geheim en de client-ID zijn vereist om in dit scenario het toegangs token op te halen.

Als u geen machtigingen hebt voor het maken van Azure AD-apps in uw domein, worden de besturings elementen van de Azure AD-App van de Blade niet weer gegeven en wordt er een waarschuwings bericht weer gegeven.

Als u verbinding maakt met de Media Services-API met behulp van de Media Services .NET SDK, raadpleegt u [Azure AD-verificatie gebruiken om toegang te krijgen tot de Azure Media Services-API met .net](media-services-dotnet-get-started-with-aad.md).

Als u de Media Services .NET-client-SDK niet gebruikt, moet u hand matig een Azure AD-token aanvraag maken met behulp van de para meters die eerder zijn besproken. Zie [de Azure AD-verificatie bibliotheek gebruiken om het Azure AD-token op te halen](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)voor meer informatie.

### <a name="get-the-client-id-and-client-secret"></a>De client-ID en het client geheim ophalen

Nadat u een bestaande Azure AD-app hebt geselecteerd of de optie voor het maken van een nieuwe hebt geselecteerd, worden de volgende knoppen weer gegeven:

![Knop machtigingen beheren en de toepassing beheren](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Als u de Blade van de Azure AD-toepassing wilt openen, klikt u op **toepassing beheren**. Op de Blade **toepassing beheren** kunt u de client-id van de app ophalen (toepassings-id). Selecteer **sleutels**om een client geheim (sleutel) te genereren.

![Optie Blade sleutels van toepassing beheren](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Machtigingen en de toepassing beheren

Nadat u de Azure AD-toepassing hebt geselecteerd, kunt u de toepassing en machtigingen beheren. Als u uw Azure AD-toepassing wilt instellen voor toegang tot andere toepassingen, klikt u op **machtigingen beheren**. Voor beheer taken, zoals het wijzigen van sleutels en antwoord-Url's of het bewerken van het manifest van de toepassing, klikt u op **toepassing beheren**.

### <a name="edit-the-apps-settings-or-manifest"></a>De instellingen of het manifest van de app bewerken

Als u de instellingen of het manifest van de app wilt bewerken, klikt u op **toepassing beheren**.

![Toepassings pagina beheren](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met [het uploaden van bestanden naar uw account](media-services-portal-upload-files.md).
