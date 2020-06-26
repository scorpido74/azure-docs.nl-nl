---
title: Multi-Factor Authentication in Azure Active Directory B2C | Microsoft Docs
description: Multi-Factor Authentication in te scha kelen in consumenten toepassingen die door Azure Active Directory B2C zijn beveiligd.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a683e34b1021754f5680092346d896ab7059aa1b
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85389034"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Multi-factor Authentication inschakelen in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) kan rechtstreeks worden geïntegreerd met [Azure multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md) zodat u een tweede beveiligingslaag kunt toevoegen om u aan te melden en zich aan te melden bij uw toepassingen. U schakelt multi-factor Authentication in zonder één regel code te schrijven. Als u al registraties hebt gemaakt en gebruikers stromen hebt aangemeld, kunt u nog steeds multi-factor Authentication inschakelen.

Deze functie helpt toepassingen bij het afhandelen van scenario's zoals de volgende:

- U hebt geen multi-factor Authentication nodig om toegang te krijgen tot één toepassing, maar u hebt deze nodig om toegang te krijgen tot een ander programma. Bijvoorbeeld, de klant kan zich aanmelden bij een automatische verzekerings toepassing met een sociaal of lokaal account, maar moet het telefoon nummer controleren voordat ze toegang krijgen tot de toepassing voor de start van de verzekering die is geregistreerd in dezelfde map.
- U hebt geen multi-factor Authentication nodig om toegang te krijgen tot een toepassing in het algemeen, maar u hebt deze nodig om toegang te krijgen tot de gevoelige gedeelten erin. De klant kan zich bijvoorbeeld aanmelden bij een bank toepassing met een sociaal of lokaal account en het account saldo controleren, maar moet het telefoon nummer verifiëren voordat een overschrijving wordt gedaan.

## <a name="set-multi-factor-authentication"></a>Multi-factor Authentication instellen

Wanneer u een gebruikers stroom maakt, hebt u de mogelijkheid om multi-factor Authentication in te scha kelen.

![Multi-factor Authentication instellen](./media/custom-policy-multi-factor-authentication/add-policy.png)

**Multi-factor Authentication** instellen op **ingeschakeld**.

U kunt de **gebruikers stroom uitvoeren** gebruiken om de ervaring te controleren. Bevestig het volgende scenario:

Er wordt een klant account in uw Tenant gemaakt voordat de multi-factor Authentication-stap wordt uitgevoerd. Tijdens de stap wordt de klant gevraagd om een telefoon nummer op te geven en te verifiëren. Als de verificatie is geslaagd, wordt het telefoon nummer aan het account gekoppeld voor later gebruik. Zelfs als de klant annuleert of uitvalt, kan de klant worden gevraagd om een telefoon nummer opnieuw te verifiëren tijdens de volgende aanmelding waarbij multi-factor Authentication is ingeschakeld.

## <a name="add-multi-factor-authentication"></a>Multi-factor Authentication toevoegen

Het is mogelijk om multi-factor Authentication in te scha kelen voor een gebruikers stroom die u eerder hebt gemaakt.

Multi-factor Authentication inschakelen:

1. Open de gebruikers stroom en selecteer vervolgens **Eigenschappen**.
2. Selecteer bij multi- **Factor Authentication**de optie **ingeschakeld**.
3. Klik bovenaan de pagina op **Opslaan**.


