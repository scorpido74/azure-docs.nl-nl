---
title: Toegangsaanvragen goedkeuren of weigeren - Azure AD-rechtenbeheer
description: Meer informatie over het gebruik van de My Access-portal om aanvragen voor een toegangspakket in Azure Active Directory-rechtenbeheer goed te keuren of te weigeren.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/27/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec63efe16f1120ca6e7d07c8917d8ad2b3a0e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261721"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Toegangsaanvragen goedkeuren of weigeren in Azure AD-rechtenbeheer

Met Azure AD-rechtenbeheer u beleidsregels configureren om goedkeuring voor toegangspakketten te vereisen en een of meer fiatteurs kiezen. In dit artikel wordt beschreven hoe aangewezen fiatteurs aanvragen voor toegangspakketten kunnen goedkeuren of weigeren.

## <a name="open-request"></a>Open aanvraag

De eerste stap om toegangsaanvragen goed te keuren of te weigeren, is het zoeken en openen van de toegangsaanvraag in afwachting van goedkeuring. Er zijn twee manieren om het toegangsverzoek te openen.

**Vereiste rol:** Goedkeurder

1. Zoek naar een e-mail van Microsoft Azure waarin u wordt gevraagd een aanvraag goed te keuren of te weigeren. Hier is een voorbeeld e-mail:

    ![Verzoek om toegang tot pakkete-mail goed te keuren](./media/entitlement-management-shared/approver-request-email.png)

1. Klik **op** de koppeling Verzoek goedkeuren of weigeren om het toegangsverzoek te openen.

1. Meld u aan bij de My Access-portal.

Als u de e-mail niet hebt, u de toegangsaanvragen in afwachting van uw goedkeuring vinden door deze stappen te volgen.

1. Meld u aan bij [https://myaccess.microsoft.com](https://myaccess.microsoft.com)de My Access-portal op .  (Voor de Amerikaanse regering is `myaccess.microsoft.us`het domein in de link My Access-portal .)

1. Klik in het linkermenu op **Goedkeuringen** om een lijst met toegangsaanvragen te bekijken die zijn goedgekeurd.

1. Zoek het verzoek op het tabblad **Inbehandeling.**

## <a name="approve-or-deny-request"></a>Verzoek goedkeuren of weigeren

Nadat u een toegangsaanvraag hebt geopend in afwachting van goedkeuring, u details zien waarmee u een beslissing nemen om een beslissing te nemen of te weigeren.

**Vereiste rol:** Goedkeurder

1. Klik **op** de koppeling Weergave om het deelvenster Toegangsaanvraag te openen.

1. Klik op **Details** om details over het toegangsverzoek te bekijken.

    De details omvatten de naam, organisatie, start- en einddatum van de toegang, indien aanwezig, zakelijke rechtvaardiging, wanneer de aanvraag is ingediend en wanneer de aanvraag verloopt.

1. Klik **op Goedkeuren** of **Weigeren**.

1. Voer indien nodig een reden in.

    ![Mijn Toegangsportal - Toegangsaanvraag](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Klik **op Verzenden** om uw beslissing in te dienen.

    Als een beleid is geconfigureerd met meerdere fiatteurs, hoeft slechts één goedkeurder een beslissing te nemen over de goedkeuring die nog moet worden goedgekeurd. Nadat een goedkeurder zijn beslissing heeft ingediend bij het toegangsverzoek, is de aanvraag voltooid en is deze niet meer beschikbaar voor de andere fiatteurs om het verzoek goed te keuren of te weigeren. De andere fiatteurs kunnen de aanvraagbeslissing en de beslisser zien in hun My Access-portal. Op dit moment wordt alleen een enkele fase goedkeuring ondersteund.

    Als geen van de geconfigureerde fiatteurs het toegangsverzoek kan goedkeuren of weigeren, verloopt het verzoek na de geconfigureerde aanvraagduur. De gebruiker krijgt een melding dat zijn toegangsverzoek is verlopen en dat hij het toegangsverzoek opnieuw moet indienen.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot een toegangspakket aanvragen](entitlement-management-request-access.md)
- [Proces aanvragen en e-mailmeldingen](entitlement-management-process.md)
