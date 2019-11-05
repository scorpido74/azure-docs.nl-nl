---
title: Overzicht van .NET-protocol voor Azure AD | Microsoft Docs
description: HTTP-berichten gebruiken om met behulp van Azure AD toegang toe te staan tot webtoepassingen en web-API's in uw tenant.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522653"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Uw toepassing registreren bij uw AD-tenant
Registreer eerst uw toepassing met uw Azure Active Directory-Tenant (Azure AD). Na registratie beschikt u over een toepassings-id voor uw toepassing en kan uw toepassing tokens ontvangen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
   
1. Kies uw Azure AD-Tenant door uw account te selecteren in de rechter bovenhoek van de pagina, gevolgd door het selecteren van de **map navigatie wisselen** en vervolgens de juiste Tenant te selecteren. 
   - Sla deze stap over als u slechts één Azure AD-Tenant onder uw account hebt of als u de juiste Azure AD-Tenant al hebt geselecteerd.
   
1. Zoek in het Azure Portal naar en selecteer **Azure Active Directory**.
   
1. Selecteer **app-registraties**In het **Azure Active Directory** menu links en selecteer vervolgens **nieuwe registratie**.
   
1. Volg de aanwijzingen op het scherm en maak een nieuwe toepassing. Het maakt niet uit of het een webtoepassing of een toepassing voor een open bare client (Mobile & bureau blad) voor deze zelf studie is, maar als u specifieke voor beelden wilt voor webtoepassingen of open bare client toepassingen, raadpleegt u onze [Snelstartgids](../articles/active-directory/develop/v1-overview.md).
   
   - **Naam** is de naam van de toepassing en beschrijft de toepassing voor eindgebruikers.
   - Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
   - Geef de **omleidings-URI**op. Voor webtoepassingen is dit de basis-URL van uw app waarmee gebruikers zich kunnen aanmelden.  Bijvoorbeeld `http://localhost:12345`. Voor de open bare client (mobiel & bureau blad) gebruikt Azure AD deze om token antwoorden te retour neren. Voer een waarde in die specifiek is voor uw toepassing.  Bijvoorbeeld `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Zodra u de registratie hebt voltooid, wordt uw toepassing door Azure AD toegewezen aan een unieke client-id (de **toepassings-id**). U hebt deze waarde nodig in de volgende secties, dus kopieer deze van de toepassings pagina.
   
1. Als u uw toepassing wilt vinden in de Azure Portal, selecteert u **app-registraties**en selecteert u **alle toepassingen weer geven**.
