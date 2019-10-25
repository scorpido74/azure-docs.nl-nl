---
title: Het inrichten van gebruikers configureren voor een toepassing in de Azure AD-galerie | Microsoft Docs
description: Zo kunt u snel uitgebreide gebruikers accounts configureren en de inrichting ongedaan maken voor toepassingen die al worden vermeld in de Azure AD-toepassings galerie
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bcc53b97b1187314404cfe075f6593f437e7bf4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789046"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Het inrichten van gebruikers configureren voor een toepassing in de Azure AD-galerie

Het inrichten van een *gebruikers account* is het maken, bijwerken en/of uitschakelen van gebruikers account records in het lokale gebruikers profiel archief van een toepassing. De meeste Cloud-en SaaS-toepassingen slaan de rollen en machtigingen van de gebruiker op in het eigen lokale gebruikers profiel archief en de aanwezigheid van een dergelijke gebruikers record in het lokale archief van de gebruiker is *vereist* voor eenmalige aanmelding en toegang tot het werk.

In het Azure Portal, het tabblad **inrichten** in het navigatie deel venster links voor een bedrijfs-app, wordt weer gegeven welke inrichtings modi voor die app worden ondersteund. Dit kan een van de volgende twee waarden zijn:

## <a name="configuring-an-application-for-manual-provisioning"></a>Een toepassing configureren voor hand matige inrichting

*Hand matig* inrichten houdt in dat gebruikers accounts hand matig moeten worden gemaakt met behulp van methoden van de app. Dit kan betekenen dat u zich kunt aanmelden bij een beheer portal voor die app en gebruikers kunt toevoegen via een gebruikers interface op het web. U kunt ook een werk blad met details van een gebruikers account uploaden met behulp van een mechanisme dat door de toepassing wordt gebruikt. Raadpleeg de documentatie van de app of neem contact op met de app-ontwikkelaar om te bepalen welke mechanismen beschikbaar zijn.

Als *hand matig* de enige modus is die wordt weer gegeven voor een bepaalde toepassing, betekent dit dat er nog geen automatische Azure AD-inrichtings connector is voor de app. Of de app biedt geen ondersteuning voor de vereisten van de gebruikers beheer-API van micro soft, die wordt gebruikt voor het bouwen van een geautomatiseerde inrichtings connector.

Als u ondersteuning wilt aanvragen voor automatische inrichting voor een bepaalde app, kunt u een aanvraag invullen met behulp van de [Azure Active Directory toepassings aanvragen](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Een toepassing configureren voor automatische inrichting

*Automatische* betekent dat een Azure AD-inrichtings connector is ontwikkeld voor deze toepassing. Voor meer informatie over de Azure AD-inrichtings service en hoe deze werkt, raadpleegt u gebruikers inrichten en het ongedaan maken van de [inrichting van SaaS-toepassingen met Azure Active Directory automatiseren](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Zie voor meer informatie over het inrichten van specifieke gebruikers en groepen voor een toepassing [beheren van het inrichten van gebruikers accounts voor zakelijke apps](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

De stappen die nodig zijn om automatische inrichting in te scha kelen en te configureren, zijn afhankelijk van de toepassing.

> [!NOTE]
> U moet beginnen met het vinden van de installatie-zelf studie die specifiek is voor het instellen van inrichting voor uw toepassing, en het volgen van de stappen voor het configureren van zowel de app als Azure AD om de inrichtings verbinding te maken. 

App-zelf studies vindt u in een [lijst met zelf studies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Het is belang rijk om rekening mee te houden bij het instellen van inrichting door de kenmerk toewijzingen en werk stromen te controleren en te configureren die bepalen welke gebruikers-(of groeps) eigenschappen stromen van Azure AD naar de toepassing. Dit omvat het instellen van de ' overeenkomende eigenschap ' die wordt gebruikt om gebruikers/groepen tussen de twee systemen uniek te identificeren en te vergelijken. Zie de koppeling in de *volgende stappen* voor meer informatie over kenmerk toewijzingen.

## <a name="next-steps"></a>Volgende stappen
[Toewijzings kenmerken van gebruikers aanpassen voor SaaS-toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

