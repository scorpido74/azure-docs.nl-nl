---
title: Toegang aanvragen tot een toegangs pakket in azure AD-rechts beheer (preview)-Azure Active Directory
description: Informatie over het gebruik van de portal mijn toegang om toegang aan te vragen voor een toegangs pakket in Azure Active Directory rechten beheer (preview).
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e57a216efee39df0ad22712b281073a68600e0df
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990760"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Toegang aanvragen tot een toegangs pakket in azure AD-rechts beheer (preview-versie)

> [!IMPORTANT]
> Azure Active Directory (Azure AD)-rechts beheer is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met het beheer van rechten van Azure AD maakt een toegangs pakket een eenmalige configuratie van resources en beleids regels waarmee de toegang voor de levens duur van het toegangs pakket automatisch wordt beheerd. 

Een Access package manager kan beleid zodanig configureren dat gebruikers goed keuring nodig hebben om toegang te krijgen tot pakketten. Een gebruiker die toegang tot een toegangs pakket nodig heeft, kan een aanvraag indienen om toegang te krijgen. In dit artikel wordt beschreven hoe u een toegangs aanvraag verzendt.

## <a name="sign-in-to-the-my-access-portal"></a>Meld u aan bij de portal van mijn toegang

De eerste stap is om u aan te melden bij de portal mijn toegang, waar u toegang tot een toegangs pakket kunt aanvragen.

**Vereiste rol:** Aanvrager

1. Zoek naar een e-mail of een bericht van het project of Business Manager waarmee u werkt. Het e-mail bericht moet een koppeling bevatten naar het toegangs pakket waartoe u toegang nodig hebt. De koppeling begint met `myaccess`, bevat een directory-hint en eindigt met een toegangs pakket-ID.
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Open de koppeling.

1. Meld u aan bij de portal van mijn toegang.

    Zorg ervoor dat u uw werk-of school account voor uw organisatie gebruikt. Als u het niet zeker weet, kunt u contact met uw project of Business Manager.

## <a name="request-an-access-package"></a>Een toegangs pakket aanvragen

Zodra u het toegangs pakket hebt gevonden in de portal van mijn toegang, kunt u een aanvraag indienen.

**Vereiste rol:** Aanvrager

1. Zoek het toegangs pakket in de lijst.  Als dat nodig is, kunt u zoeken door een zoek reeks te typen en vervolgens het filter **naam**, **catalogus**of **resources** te selecteren.

    ![Mijn Access-Portal-zoeken naar resources](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Klik op het vinkje om het toegangs pakket te selecteren.

1. Klik op **toegang aanvragen** om het deel venster toegang aanvragen te openen.

    ![Mijn Access-Portal-toegangs pakketten](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Als het vak **zakelijke rechtvaardiging** wordt weer gegeven, typt u een motivering voor toegang nodig.

1. Als de **aanvraag voor een specifieke periode** is ingeschakeld, selecteert u **Ja** of **Nee**.

1. Geef indien nodig de begin datum en eind datum op.

    ![Mijn Access-portal-toegang aanvragen](./media/entitlement-management-shared/my-access-request-access.png)

1. Wanneer u klaar bent, klikt u op **verzenden** om uw aanvraag in te dienen.

1. Klik op **aanvraag geschiedenis** om een lijst met uw aanvragen en de status weer te geven.

    Als het toegangs pakket goed keuring vereist, heeft de aanvraag nu een goedkeurings status in behandeling.

### <a name="select-a-policy"></a>Beleid selecteren

Als u toegang tot een toegangs pakket aanvraagt dat meerdere beleids regels van toepassing heeft, wordt u mogelijk gevraagd om een beleid te selecteren. Een Access package manager kan bijvoorbeeld een toegangs pakket configureren met twee beleids regels voor twee groepen interne werk nemers. Het eerste beleid kan toegang tot 60 dagen toestaan en moet goed keuring vereisen. Met het tweede beleid is het mogelijk dat u twee dagen toegang hebt en geen goed keuring vereist. Als u dit scenario ondervindt, moet u het beleid selecteren dat u wilt gebruiken.

![Mijn Access-portal-toegang aanvragen-meerdere beleids regels](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="cancel-a-request"></a>Een aanvraag annuleren

Als u een toegangs aanvraag verzendt en de aanvraag nog steeds de **goedkeurings** status in behandeling heeft, kunt u de aanvraag annuleren.

**Vereiste rol:** Aanvrager

1. Klik in de portal mijn toegang aan de linkerkant op **aanvraag geschiedenis** om een lijst met uw aanvragen en de status weer te geven.

1. Klik op de koppeling **weer geven** voor de aanvraag die u wilt annuleren.

1. Als de aanvraag de **goedkeurings** status in behandeling heeft, kunt u op **Aanvraag annuleren** klikken om de aanvraag te annuleren.

    ![Mijn toegangs Portal-aanvraag annuleren](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Klik op **aanvraag geschiedenis** om te bevestigen dat de aanvraag is geannuleerd.

## <a name="next-steps"></a>Volgende stappen

- [Toegangs aanvragen goed keuren of weigeren](entitlement-management-request-approve.md)
- [Aanvraag proces en e-mail meldingen](entitlement-management-process.md)
