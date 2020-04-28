---
title: Toegangs aanvragen goed keuren of weigeren-Azure AD-rechts beheer
description: Meer informatie over het gebruik van de portal mijn toegang voor het goed keuren of weigeren van aanvragen voor een toegangs pakket in Azure Active Directory rechten beheer.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79261721"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Toegangs aanvragen goed keuren of weigeren in azure AD-rechts beheer

Met het beheer van rechten van Azure AD kunt u beleid configureren om goed keuring van toegangs pakketten te vereisen en een of meer goed keurders kiezen. In dit artikel wordt beschreven hoe aangewezen goed keurders aanvragen voor toegangs pakketten kunnen goed keuren of weigeren.

## <a name="open-request"></a>Aanvraag openen

De eerste stap voor het goed keuren of weigeren van toegangs aanvragen is het zoeken en openen van de goedkeurings aanvraag in behandeling. Er zijn twee manieren om de toegangs aanvraag te openen.

**Vereiste rol:** Fiatteur

1. Zoek naar een e-mail bericht van Microsoft Azure waarin u wordt gevraagd of u een aanvraag wilt goed keuren of weigeren. Hier volgt een voor beeld van een e-mail bericht:

    ![Aanvraag voor toegang tot pakket e-mail goed keuren](./media/entitlement-management-shared/approver-request-email.png)

1. Klik op de koppeling **aanvraag goed keuren of weigeren** om de toegangs aanvraag te openen.

1. Meld u aan bij de portal van mijn toegang.

Als u het e-mail bericht niet hebt, kunt u de toegangs aanvragen vinden die in behandeling zijn door de volgende stappen uit te voeren.

1. Meld u aan bij de portal van mijn [https://myaccess.microsoft.com](https://myaccess.microsoft.com)toegang op.  (Voor de Amerikaanse overheid is het domein in de koppeling naar de portal van `myaccess.microsoft.us`mijn toegang.)

1. Klik in het linkermenu op **goed keuringen** om een lijst weer te geven met toegangs aanvragen die in behandeling zijn.

1. Zoek de aanvraag op het tabblad **in behandeling** .

## <a name="approve-or-deny-request"></a>Aanvraag goed keuren of weigeren

Nadat u een goedkeurings aanvraag in behandeling hebt geopend, ziet u details die u helpen bij het maken van een goed keuring of het weigeren van een beslissing.

**Vereiste rol:** Fiatteur

1. Klik op de koppeling **weer geven** om het deel venster toegangs aanvraag te openen.

1. Klik op **Details** om de details van de toegangs aanvraag te bekijken.

    De details bestaan uit de naam van de gebruiker, de organisatie, de start-en eind datum van de toegang, indien van toepassing, zakelijke rechtvaardiging, wanneer de aanvraag is ingediend en wanneer de aanvraag verloopt.

1. Klik op **goed keuren** of **weigeren**.

1. Voer, indien nodig, een reden in.

    ![Mijn toegangs Portal-toegangs aanvraag](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Klik op **verzenden** om uw beslissing in te dienen.

    Als een beleid is geconfigureerd met meerdere goed keurders, hoeft slechts één fiatteur een beslissing te nemen over de goed keuring in behandeling. Nadat een fiatteur zijn of haar beslissing heeft ingediend bij de toegangs aanvraag, wordt de aanvraag voltooid en is deze niet langer beschikbaar voor de andere goed keurders om de aanvraag goed te keuren of te weigeren. De andere goed keurders kunnen de beslissing van de aanvraag en de besluit Maker in hun mijn Access-Portal bekijken. Op dit moment wordt slechts één fase goed keuring ondersteund.

    Als geen van de geconfigureerde goed keurders de toegangs aanvraag kan goed keuren of weigeren, verloopt de aanvraag na de geconfigureerde aanvraag duur. De gebruiker ontvangt een melding dat de toegangs aanvraag is verlopen en dat deze de toegangs aanvraag opnieuw moet indienen.

## <a name="next-steps"></a>Volgende stappen

- [Toegang aanvragen tot een toegangs pakket](entitlement-management-request-access.md)
- [Aanvraag proces en e-mail meldingen](entitlement-management-process.md)
