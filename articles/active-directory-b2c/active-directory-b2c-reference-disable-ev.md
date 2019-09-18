---
title: E-mail verificatie uitschakelen tijdens de aanmelding van de consument in Azure Active Directory B2C | Microsoft Docs
description: Een onderwerp waarin wordt uitgelegd hoe u e-mail verificatie kunt uitschakelen tijdens de aanmelding van de consument in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d827843bdbaff1ad95dce3318f39867abe8b2d0f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065623"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>E-mail verificatie uitschakelen tijdens de aanmelding van de consument in Azure Active Directory B2C
Als deze optie is ingeschakeld, geeft Azure Active Directory B2C (Azure AD B2C) een gebruiker de mogelijkheid om zich aan te melden voor toepassingen door een e-mail adres op te geven en een lokaal account te maken. Azure AD B2C zorgt voor geldige e-mail adressen door consumenten te verplichten te verifiëren tijdens het registratie proces. Ook wordt voor komen dat een schadelijk geautomatiseerd proces valse accounts voor de toepassingen genereert.

Sommige ontwikkel aars van toepassingen geven de voor keur aan een e-mail verificatie overs Laan tijdens het registratie proces. ook moeten gebruikers het e-mail adres later verifiëren. Ter ondersteuning hiervan kan Azure AD B2C worden geconfigureerd om e-mail verificatie uit te scha kelen. Dit maakt een probleemloos aanmeldings proces en biedt ontwikkel aars de flexibiliteit om de consumenten te onderscheiden die hun e-mail adres van die gebruikers hebben geverifieerd.

Standaard hebben aanmeldings gebruikers stromen een e-mail verificatie ingeschakeld. Gebruik de volgende stappen om deze functie uit te scha kelen:

1. Klik op **gebruikers stromen**.
2. Klik op uw gebruikers stroom (bijvoorbeeld ' B2C_1_SiUp ') om deze te openen.
3. Klik op **pagina-indelingen**.
4. Klik op de **aanmeldings pagina voor het lokale account**.
5. Klik op **e-mail adres** in de kolom **naam** onder de sectie **gebruikers kenmerken** .
6. Selecteer onder **verificatie vereist**de optie **Nee**.
7. Klik op **Opslaan** boven aan de blade. U bent klaar.

> [!NOTE]
> Als u e-mail verificatie in het registratie proces uitschakelt, kan dit leiden tot spam. Als u de standaard instelling uitschakelt, wordt u aangeraden uw eigen verificatie systeem toe te voegen.
>
>
