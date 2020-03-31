---
title: Meervoudige verificatie in Azure Active Directory B2C | Microsoft Documenten
description: Multi-Factor Authentication inschakelen in toepassingen die gericht zijn op consumenten en die zijn beveiligd door Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25724ba82e57c5e3800fa1a989dd4f504df1c163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189273"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Meervoudige verificatie inschakelen in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) integreert rechtstreeks met [Azure Multi-Factor Authentication,](../active-directory/authentication/multi-factor-authentication.md) zodat u een tweede beveiligingslaag toevoegen aan aanmeldings- en aanmeldingservaringen in uw toepassingen. U schakelt meervoudige verificatie in zonder één regel code te schrijven. Als u al aanmeldings- en aanmeldingsgebruikersstromen hebt gemaakt, u nog steeds meervoudige verificatie inschakelen.

Met deze functie kunnen toepassingen scenario's als de volgende verwerken:

- U hebt geen meervoudige verificatie nodig om toegang te krijgen tot één toepassing, maar u hebt wel een andere toepassing nodig. De klant kan zich bijvoorbeeld aanmelden bij een autoverzekeringsaanvraag met een sociaal of lokaal account, maar moet het telefoonnummer verifiëren voordat hij toegang krijgt tot de aanvraag voor een indezelfde map geregistreerde aanvraag voor een autoverzekering.
- U hebt geen meervoudige verificatie nodig om toegang te krijgen tot een toepassing in het algemeen, maar u hebt wel de toegang nodig om toegang te krijgen tot de gevoelige gedeelten die erin zitten. De klant kan zich bijvoorbeeld aanmelden bij een bankaanvraag met een sociale of lokale rekening en het rekeningsaldo controleren, maar moet het telefoonnummer verifiëren voordat u een overschrijving probeert.

## <a name="set-multi-factor-authentication"></a>Meervoudige verificatie instellen

Wanneer u een gebruikersstroom maakt, hebt u de optie om meervoudige verificatie in te schakelen.

![Meervoudige verificatie instellen](./media/custom-policy-multi-factor-authentication/add-policy.png)

**Multifactor-verificatie** instellen **op Ingeschakeld**.

U **Gebruikersstroom uitvoeren** gebruiken om de ervaring te verifiëren. Bevestig het volgende scenario:

Er wordt een klantaccount gemaakt in uw tenant voordat de multi-factor authenticatiestap plaatsvindt. Tijdens de stap wordt de klant gevraagd om een telefoonnummer op te geven en te verifiëren. Als de verificatie is geslaagd, wordt het telefoonnummer aan het account gekoppeld voor later gebruik. Zelfs als de klant annuleert of uitvalt, kan de klant worden gevraagd om een telefoonnummer opnieuw te verifiëren tijdens de volgende aanmelding met multi-factor authenticatie ingeschakeld.

## <a name="add-multi-factor-authentication"></a>Meervoudige verificatie toevoegen

Het is mogelijk om multi-factor authenticatie in te schakelen op een gebruikersstroom die u eerder hebt gemaakt.

Ga als u meerdealsteinse verificatie inschakelen:

1. Open de gebruikersstroom en selecteer **Eigenschappen**.
2. Selecteer naast **Multifactor-verificatie**de optie **Ingeschakeld**.
3. Klik bovenaan de pagina op **Opslaan**.


