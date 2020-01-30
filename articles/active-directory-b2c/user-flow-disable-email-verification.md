---
title: E-mail verificatie uitschakelen tijdens het aanmelden van de klant
titleSuffix: Azure AD B2C
description: Meer informatie over het uitschakelen van e-mail verificatie tijdens het aanmelden van een klant in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c5b3598e33dac131c8881248a5f4b740a6302e20
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848016"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>E-mail verificatie uitschakelen tijdens het aanmelden van de klant in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) controleert standaard het e-mail adres van uw klant voor lokale accounts (accounts voor gebruikers die zich aanmelden met een e-mail adres of gebruikers naam). Azure AD B2C zorgt voor geldige e-mail adressen door klanten te verplichten te verifiëren tijdens het registratie proces. Ook wordt voor komen dat een kwaadwillende actor geautomatiseerde processen gebruikt voor het genereren van frauduleuze accounts in uw toepassingen.

Sommige toepassings ontwikkelaars geven de voor keur aan een e-mail verificatie overs Laan tijdens het registratie proces. ook kunnen klanten hun e-mail adres later verifiëren. Ter ondersteuning hiervan kan Azure AD B2C worden geconfigureerd om e-mail verificatie uit te scha kelen. Dit maakt een probleemloos registratie proces en biedt ontwikkel aars de flexibiliteit om klanten te onderscheiden die hun e-mail adres van klanten hebben gecontroleerd.

Volg deze stappen om de e-mail verificatie uit te scha kelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Gebruik het filter voor **adres lijst en abonnementen** in het bovenste menu om de map te selecteren die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer **gebruikers stromen**.
1. Selecteer de gebruikers stroom waarvoor u de e-mail verificatie wilt uitschakelen. Bijvoorbeeld *B2C_1_signinsignup*.
1. Selecteer **pagina-indelingen**.
1. Selecteer de **pagina voor het registreren van een lokaal account**.
1. Onder **gebruikers kenmerken**selecteert u **e-mail adres**.
1. Selecteer in de vervolg keuzelijst **vereist verificaties** de optie **Nee**.
1. Selecteer **Opslaan**. E-mail verificatie is nu uitgeschakeld voor deze gebruikers stroom.

> [!WARNING]
> Als u e-mail verificatie in het registratie proces uitschakelt, kan dit leiden tot spam. Als u de standaard verificatie van e-mail berichten voor Azure AD B2C uitschakelt, wordt u aangeraden een vervangend verificatie systeem te implementeren.
