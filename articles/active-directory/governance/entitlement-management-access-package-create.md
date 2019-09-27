---
title: Een nieuw toegangs pakket maken in azure AD rechten beheer (preview)-Azure Active Directory
description: Meer informatie over het maken van een nieuw toegangs pakket met resources die u wilt delen in Azure Active Directory rechten beheer (preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/24/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1283549ce78535757b7ba42eaf7054538f0c9784
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326346"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Een nieuw toegangs pakket maken in azure AD-rechts beheer (preview-versie)

> [!IMPORTANT]
> Azure Active Directory (Azure AD)-rechts beheer is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met een toegangs pakket kunt u een eenmalige configuratie uitvoeren van resources en beleids regels die automatisch de toegang beheren voor de levens duur van het toegangs pakket. In dit artikel wordt beschreven hoe u een nieuw toegangs pakket maakt.

## <a name="overview"></a>Overzicht

Alle toegangs pakketten moeten in een container worden geplaatst die een catalogus wordt genoemd. Een catalogus definieert welke resources u aan uw toegangs pakket kunt toevoegen. Als u geen catalogus opgeeft, wordt uw toegangs pakket in de algemene catalogus geplaatst. Op dit moment kunt u een bestaand toegangs pakket niet naar een andere catalogus verplaatsen.

Alle toegangs pakketten moeten ten minste één beleid hebben. Beleids regels opgeven wie het toegangs pakket kan aanvragen en ook de instellingen voor goed keuring en verloop datum. Wanneer u een nieuw toegangs pakket maakt, kunt u een eerste beleids regel voor gebruikers in uw Directory maken, voor gebruikers die niet in uw Directory zijn, alleen voor beheerders direct toewijzingen of u kunt ervoor kiezen om het beleid later te maken.

In het volgende diagram ziet u het proces op hoog niveau voor het maken van een nieuw toegangs pakket.

![Een Access package-proces maken](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Nieuw toegangs pakket starten

**Vereiste rol:** Globale beheerder, gebruikers beheerder of catalogus eigenaar

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Klik op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het linkermenu op **toegangs pakketten**.

    ![Het beheer van rechten in het Azure Portal](./media/entitlement-management-shared/elm-access-packages.png)

1. Klik op **nieuw toegangs pakket**.

## <a name="basics"></a>Basics

Op het tabblad **basis beginselen** geeft u het toegangs pakket een naam en geeft u op in welke catalogus u het toegangs pakket wilt maken.

1. Voer een weergave naam en een beschrijving in voor het toegangs pakket. Gebruikers krijgen deze informatie te zien wanneer ze een aanvraag indienen voor het toegangs pakket.

1. Selecteer in de vervolg keuzelijst **catalogus** de catalogus waarin u het toegangs pakket wilt maken. U kunt bijvoorbeeld een catalogus eigenaar hebben die alle marketing resources beheert die kunnen worden aangevraagd. In dit geval kunt u de marketing catalogus selecteren.

    U ziet alleen catalogi waarvoor u gemachtigd bent om toegangs pakketten te maken in. Als u een toegangs pakket wilt maken in een bestaande catalogus, moet u Mini maal een globale beheerder, een gebruikers beheerder, een catalogus eigenaar in die catalogus zijn of toegang krijgen tot pakket beheer in die catalogus.

    ![Toegangs pakket-basis beginselen](./media/entitlement-management-access-package-create/basics.png)

    Als u een globale beheerder of een gebruikers beheerder bent en u uw toegangs pakket wilt maken in een nieuwe catalogus die niet wordt weer gegeven, klikt u op **nieuwe maken**. Voer de naam en beschrijving van de catalogus in en klik vervolgens op **maken**.

    Het toegangs pakket dat u maakt en alle resources die erin worden opgenomen, wordt toegevoegd aan de nieuwe catalogus. U kunt later ook extra catalogus eigenaren toevoegen.


1. Klik op **Volgende**.

## <a name="resource-roles"></a>Resourcerollen

Op het tabblad **resource rollen** selecteert u de resources die u wilt toevoegen in het toegangs pakket.  Gebruikers die het toegangs pakket aanvragen en ontvangen, ontvangen alle resource rollen in het toegangs pakket.

1. Klik op het resource type dat u wilt toevoegen (**groepen en teams**, **toepassingen**of **share point-sites**).

1. Selecteer in het deel venster selecteren dat wordt weer gegeven een of meer resources uit de lijst.

    ![Toegangs pakket-resource rollen](./media/entitlement-management-access-package-create/resource-roles.png)

    Als u het toegangs pakket maakt in de algemene catalogus of een nieuwe catalogus, kunt u een wille keurige bron kiezen uit de map waarvan u de eigenaar bent. U moet ten minste een globale beheerder, een gebruikers beheerder of een maker van de catalogus zijn.

    Als u het toegangs pakket maakt in een bestaande catalogus, kunt u alle resources selecteren die zich al in de catalogus bevinden zonder dat hiervoor eigenaar van is.

    Als u een globale beheerder, een gebruikers beheerder of een catalogus eigenaar bent, hebt u de extra optie om resources te selecteren waarvan u eigenaar bent die nog niet in de catalogus staan. Als u resources selecteert die zich momenteel niet in de geselecteerde catalogus bekomen, worden deze resources ook toegevoegd aan de catalogus zodat andere catalogus beheerders toegangs pakketten met kunnen maken. Als u alleen resources wilt selecteren die zich momenteel in de geselecteerde catalogus bevinden, schakelt u het selectie vakje **alleen bekijken** boven in het venster pan selecteren in.

1. Wanneer u de resources hebt geselecteerd, selecteert u in de lijst met **rollen** de rol die gebruikers moeten worden toegewezen aan de resource.

    ![Toegangs pakket-resource functie selecteren](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klik op **Volgende**.

## <a name="policy"></a>Beleid

Op het tabblad **beleid** maakt u het eerste beleid om op te geven wie het toegangs pakket en ook de instellingen voor goed keuring en verloop kan aanvragen. U kunt later meer beleids regels maken om extra groepen gebruikers toe te staan het toegangs pakket aan te vragen met hun eigen goed keuring en verloop instellingen. U kunt er ook voor kiezen om het beleid later te maken.

1. Stel de schakel optie **eerste beleid maken** in op **nu** of **later**.

    ![Toegangs pakket-beleid](./media/entitlement-management-access-package-create/policy.png)

1. Als u **later**selecteert, gaat u naar de sectie [beoordeling + maken](#review--create) om uw toegangs pakket te maken.

1. Als u **nu**selecteert, voert u de stappen uit in een van de volgende beleids secties.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Controleren en maken

Op het tabblad **controleren en maken** kunt u uw instellingen controleren en controleren of er validatie fouten zijn.

1. De instellingen van het toegangs pakket controleren

    ![Toegangs pakket-beleids instelling beleid inschakelen](./media/entitlement-management-access-package-create/review-create.png)

1. Klik op **maken** om het toegangs pakket te maken.

    Het nieuwe toegangs pakket wordt weer gegeven in de lijst met toegangs pakketten.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaand toegangspakket bewerken en beheren](entitlement-management-access-package-edit.md)
- [Een catalogus eigenaar of een Access Package Manager toevoegen](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Een catalogus maken en beheren](entitlement-management-catalog-create.md)
