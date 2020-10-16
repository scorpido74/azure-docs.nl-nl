---
title: Beperkte Kerberos-overdracht met Azure Active Directory
description: Richt lijnen voor architectuur over het bereiken van dit verificatie patroon
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77f90cd7aa8d972226a8f134eaa7b3abfe7bea66
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114259"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Windows-verificatie-beperkte Kerberos-overdracht met Azure Active Directory

Kerberos-beperkte delegering (KCD) biedt beperkte delegering tussen bronnen en is gebaseerd op namen van service Principles. Het vereist dat domein beheerders de delegaties maken en beperkt zijn tot één domein. Op resources gebaseerde KCD wordt vaak gebruikt als een manier om Kerberos-verificatie te bieden voor een webtoepassing die gebruikers in meerdere domeinen in een Active Directory-forest heeft.

Azure Active Directory-toepassingsproxy kunt eenmalige aanmelding (SSO) en externe toegang bieden aan KCD-toepassingen waarvoor een Kerberos-ticket is vereist voor toegang en Kerberos-beperkte delegering (KCD).

U schakelt SSO in voor uw on-premises KCD-toepassingen die gebruikmaken van geïntegreerde Windows-authenticatie (IWA) door toepassings proxy connectors toestemming te geven om gebruikers in Active Directory te imiteren. De Application proxy connector gebruikt deze machtiging voor het verzenden en ontvangen van tokens voor de gebruikers.

## <a name="use-when"></a>Wanneer gebruiken

U moet externe toegang bieden, beveiligen met pre-authenticatie en eenmalige aanmelding bieden voor on-premises IWA-toepassingen.

![Diagram van architectuur](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>Onderdelen van systeem

* **Gebruiker**: toegang tot verouderde toepassing die wordt aangeboden door de toepassings proxy.

* **Webbrowser**: het onderdeel waarmee de gebruiker communiceert om toegang te krijgen tot de externe URL van de toepassing.

* **Azure AD**: verifieert de gebruiker. 

* **Application proxy-service**: fungeert als omgekeerde proxy voor het verzenden van een aanvraag van de gebruiker naar de on-premises toepassing. Het wordt gezit in azure AD. Toepassings proxy kan ook beleid voor voorwaardelijke toegang afdwingen.

* **Application proxy-connector**: on-premises op Windows-servers geïnstalleerd om verbinding te kunnen maken met de toepassing. Retourneert het antwoord naar Azure AD. Voert KCD-onderhandelingen uit met Active Directory en imiteert de gebruiker om een Kerberos-token aan de toepassing te krijgen.

* **Active Directory**: verzendt het Kerberos-token voor de toepassing naar de Application proxy-connector.

* **Verouderde toepassingen**: toepassingen die gebruikers aanvragen ontvangen van de toepassings proxy. De verouderde toepassingen retour neren de reactie van de Application proxy-connector.

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Windows-verificatie (KCD) implementeren met Azure AD

* [Beperkte Kerberos-delegatie voor eenmalige aanmelding voor uw apps met de toepassingsproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd) 

* [Een on-premises toepassing voor externe toegang toevoegen via Application Proxy in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
