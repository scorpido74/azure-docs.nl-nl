---
title: Aanvragen voor een toegangs pakket weer geven en beheren in azure AD rechten beheer (preview)-Azure Active Directory
description: Meer informatie over het weer geven, opnieuw verwerken en annuleren van aanvragen voor een toegangs pakket in Azure Active Directory rechten beheer (preview).
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
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7211fea4781904b9d97428ecf00b57970e4b57d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430277"
---
# <a name="view-and-manage-requests-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Aanvragen voor een toegangs pakket in azure AD-rechts beheer weer geven en beheren (preview-versie)

> [!IMPORTANT]
> Azure Active Directory (Azure AD)-rechts beheer is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In het beheer van rechten van Azure AD kunt u zien wie toegangs pakketten, hun beleid en status heeft aangevraagd. In dit artikel wordt beschreven hoe u aanvragen voor een toegangs pakket weergeeft, opnieuw verwerkt en annuleert.

## <a name="view-requests"></a>Aanvragen weer geven

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Klik op een specifieke aanvraag om aanvullende informatie weer te geven.

    ![Lijst met aanvragen voor een toegangs pakket](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="view-a-requests-delivery-errors"></a>De leverings fouten van een aanvraag weer geven

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Selecteer de aanvraag die u wilt weer geven.

    Als de aanvraag een leverings fout bevat, wordt de status van de aanvraag niet **bezorgd** of **gedeeltelijk bezorgd**.

    Als er bezorgings fouten zijn, wordt in het detail venster van de aanvraag een telling van de bezorgings fouten weer gegeven.

1. Klik op het aantal om alle bezorgings fouten van de aanvraag weer te geven.

## <a name="reprocess-a-request"></a>Een aanvraag opnieuw verwerken

Als een aanvraag een fout tegen komt, kunt u de aanvraag opnieuw verwerken om deze opnieuw te proberen. U kunt alleen een aanvraag met de status **bezorgd** of **gedeeltelijk** opnieuw verwerken, en een voltooide datum van minder dan een week.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Klik op de aanvraag die u opnieuw wilt verwerken.

1. Klik in het deel venster aanvraag Details op **aanvraag opnieuw verwerken**.

    ![Een mislukte aanvraag opnieuw verwerken](./media/entitlement-management-access-package-requests/reprocess-request.png)

## <a name="cancel-a-pending-request"></a>Een aanvraag in behandeling annuleren

U kunt een aanvraag die in behandeling is, alleen annuleren als deze nog niet is bezorgd of waarvoor de bezorging is mislukt.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Klik op de aanvraag die u wilt annuleren.

1. Klik in het deel venster aanvraag Details op **Aanvraag annuleren**.

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor aanvraag-en goed keuring wijzigen voor een toegangs pakket](entitlement-management-access-package-request-policy.md)
- [Toewijzingen voor een toegangs pakket weer geven, toevoegen en verwijderen](entitlement-management-access-package-assignments.md)