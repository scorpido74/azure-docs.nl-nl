---
title: Accepteert mijn aanmeldingspagina voor Azure AD Microsoft-accounts | Microsoft Documenten
description: Hoe berichten op het scherm het opzoeken van gebruikersnaam weergeven tijdens aanmelding
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024286"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Aanmeldingsopties voor Microsoft-accounts in Azure Active Directory

De Microsoft 365-aanmeldingspagina voor Azure Active Directory (Azure AD) ondersteunt werk- of schoolaccounts en Microsoft-accounts, maar afhankelijk van de situatie van de gebruiker kan dit het een of de ander zijn. De aanmeldingspagina van Azure AD ondersteunt bijvoorbeeld:

* Apps die aanmeldingen van beide typen account accepteren
* Organisaties die gasten accepteren

## <a name="identification"></a>Identificatie
U zien of de aanmeldingspagina die uw organisatie gebruikt Microsoft-accounts ondersteunt door te kijken naar de hinttekst in het gebruikersnaamveld. Als in de hinttekst 'E-mail, telefoon of Skype' staat, ondersteunt de aanmeldingspagina Microsoft-accounts.

![Verschil tussen aanmeldingspagina's voor account](./media/signin-account-support/ui-prompt.png)

[Aanvullende aanmeldingsopties werken alleen voor persoonlijke Microsoft-accounts,](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) maar kunnen niet worden gebruikt voor het aanmelden bij bronnen voor werk- of schoolaccounts.

## <a name="next-steps"></a>Volgende stappen

[Uw aanmeldingsmerking aanpassen](../fundamentals/add-custom-domain.md)