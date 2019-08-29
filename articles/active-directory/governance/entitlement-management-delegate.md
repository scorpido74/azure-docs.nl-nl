---
title: Taken delegeren in azure AD rechten beheer (preview)-Azure Active Directory
description: Meer informatie over de rollen die u kunt toewijzen aan het overdragen van taken in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 064724b3c6a5faa485850ecdfa3d3759d3631be0
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124913"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Taken in het beheer van rechten van Azure AD delegeren (preview-versie)

> [!IMPORTANT]
> Azure Active Directory (Azure AD)-rechts beheer is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Standaard kunnen globale beheerders en gebruikers beheerders alle aspecten van het beheer van rechten van Azure AD maken en beheren. Het is echter mogelijk dat de gebruikers in deze rollen niet alle scenario's kennen waarin toegangs pakketten zijn vereist. Normaal gesp roken is het gebruikers binnen afdelingen die weten wie ze moeten kunnen samen werken. 

In plaats van onbeperkte machtigingen te verlenen aan niet-beheerders, kunt u gebruikers de minste machtigingen verlenen die ze nodig hebben om hun taak uit te voeren en te voor komen dat er conflicterende of onjuiste toegangs rechten zijn. In dit artikel worden de rollen beschreven die u kunt toewijzen om verschillende taken in het rechts beheer te delegeren. 

## <a name="delegate-example-for-departmental-adoption"></a>Voor beeld van delegeren voor het goed keuren van afdelingen

Als u wilt weten hoe u taken kunt delegeren in het recht beheer, kunt u een voor beeld overwegen. 

Stel dat uw organisatie de volgende vijf gebruikers heeft:

| Gebruiker | Afdeling | Opmerkingen |
| --- | --- | --- |
| Els | it | Globale beheerder |
| Bob | Research | Bob is ook eigenaar van een onderzoek groep |
| Carole | Research |  |
| Dave | Marketing |  |
| Elisa | Marketing | Elisa is ook eigenaar van een marketing toepassing |

Zowel de onderzoeks-als marketing afdeling willen het rechten beheer voor hun gebruikers gebruiken. Anja is nog niet gereed voor andere afdelingen om het beheer van rechten te gebruiken. Dit is een manier waarop Anja taken kan delegeren aan de afdelingen voor onderzoek en marketing.

1. Anja maakt een nieuwe Azure AD-beveiligings groep voor catalogus makers en voegt Bob, Carola, Dave en Elisa toe als leden van die groep.

1. Anja maakt gebruik van de rechten voor het beheer van de groep aan de rol catalogus makers.

1. Carola maakt een **onderzoek** catalogus en voegt Bob toe als mede-eigenaar van die catalogus. Bob voegt de groep onderzoek die eigenaar is van de catalogus toe als resource, zodat deze kan worden gebruikt in een toegangs pakket voor samen werking op het gebied van onderzoek.

1. Dave maakt een **marketing** catalogus en voegt Elisa toe als mede-eigenaar van die catalogus. Elisa voegt de marketing toepassing toe die eigenaar is van de catalogus als resource, zodat deze kan worden gebruikt in een toegangs pakket voor de samen werking in de marketing.

De afdelingen voor onderzoek en marketing kunnen nu gebruikmaken van het rechten beheer. Bob, Carola, Dave en Elisa kunnen toegangs pakketten maken en beheren in hun respectieve catalogi.

![Voor beeld van een recht op gedelegeerd beheer](./media/entitlement-management-delegate/elm-delegate.png)


## <a name="entitlement-management-roles"></a>Rechten beheer rollen

Het beheer van rechten heeft de volgende rollen die specifiek zijn voor het beheer van rechten.

| Role | Description |
| --- | --- |
| Maker van catalogus | Maak en beheer catalogi. Doorgaans een IT-beheerder die geen globale beheerder of een resource-eigenaar is voor een verzameling resources. De persoon die een catalogus maakt, wordt automatisch de eerste catalogus eigenaar van de catalogus en kan extra catalogus eigenaren toevoegen. |
| Catalogus eigenaar | Bestaande catalogi bewerken en beheren. Doorgaans een IT-beheerder of eigenaar van een resource, of een gebruiker die de eigenaar van de catalogus heeft opgegeven. |
| Toegangs pakket beheer | Bewerk en beheer alle bestaande toegangs pakketten in een catalogus. |

Daarnaast hebben een aangewezen goed keurder en een aanvrager van een toegangs pakket ook rechten, hoewel ze geen rollen zijn.
 
* Fiatteur Geautoriseerd door een beleid voor het goed keuren of weigeren van aanvragen voor toegang tot pakketten, hoewel ze de toegangs pakket definities niet kunnen wijzigen.
* Aanvrager Geautoriseerd door een beleid van een toegangs pakket om het toegangs pakket aan te vragen.

De volgende tabel bevat de taken die door deze rollen kunnen worden uitgevoerd.

| Taak | Maker van catalogus | Catalogus eigenaar | Toegangs pakket beheer | Goedkeurder |
| --- | :---: | :---: | :---: | :---: |
| [Een nieuwe catalogus maken](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Een resource toevoegen aan een catalogus](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Een catalogus bewerken](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Een catalogus verwijderen](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Een catalogus eigenaar of een Access Package Manager toevoegen aan een catalogus](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Een nieuw toegangs pakket maken in een catalogus](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Resource rollen beheren in een toegangs pakket](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Beleid maken en bewerken](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Een gebruiker rechtstreeks toewijzen aan een toegangs pakket](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Weer geven wie een toewijzing voor een toegangs pakket heeft](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Aanvragen van een toegangs pakket weer geven](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [De leverings fouten van een aanvraag weer geven](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Een aanvraag in behandeling annuleren](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Een toegangs pakket verbergen](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Een toegangs pakket verwijderen](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Een toegangs aanvraag goed keuren](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Vereiste rollen om resources toe te voegen aan een catalogus

Een globale beheerder kan elke groep (in de Cloud gemaakte beveiligings groepen of in de Cloud gemaakte Office 365-groepen), toepassing of share point online-site toevoegen aan of verwijderen uit een catalogus. Een gebruikers beheerder kan een wille keurige groep of toepassing toevoegen aan of verwijderen uit een catalogus.

Voor een gebruiker die geen globale beheerder of gebruikers beheerder is, om groepen, toepassingen of share point online-sites toe te voegen aan een catalogus, moet die gebruiker *zowel* de vereiste beheer functie voor Azure AD-adreslijst functies als de bevoegdheid van de catalogus eigenaar hebben. De volgende tabel geeft een lijst van de functie combinaties die nodig zijn om resources toe te voegen aan een catalogus. Als u resources uit een catalogus wilt verwijderen, moet u dezelfde rollen hebben.

| Azure AD-adreslijst functie | Rol van rechten beheer | Kan beveiligings groep toevoegen | Kan Office 365-groep toevoegen | Kan app toevoegen | Kan de share point online-site toevoegen |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Globale beheerder](../users-groups-roles/directory-assign-admin-roles.md) | N.v.t. |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gebruikers beheerder](../users-groups-roles/directory-assign-admin-roles.md) | N.v.t. |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [InTune-beheerder](../users-groups-roles/directory-assign-admin-roles.md) | Catalogus eigenaar | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-beheerder](../users-groups-roles/directory-assign-admin-roles.md) | Catalogus eigenaar |  | :heavy_check_mark: |  |  |
| [Teams service-beheerder](../users-groups-roles/directory-assign-admin-roles.md) | Catalogus eigenaar |  | :heavy_check_mark: |  |  |
| [Share point-beheerder](../users-groups-roles/directory-assign-admin-roles.md) | Catalogus eigenaar |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Toepassings beheerder](../users-groups-roles/directory-assign-admin-roles.md) | Catalogus eigenaar |  |  | :heavy_check_mark: |  |
| [Beheerder van de Cloud toepassing](../users-groups-roles/directory-assign-admin-roles.md) | Catalogus eigenaar |  |  | :heavy_check_mark: |  |
| Gebruiker | Catalogus eigenaar | Alleen als de groeps eigenaar | Alleen als de groeps eigenaar | Alleen als de eigenaar van de app |  |


## <a name="add-a-catalog-creator"></a>Een maker van de catalogus toevoegen

Als u het maken van catalogi wilt delegeren, voegt u gebruikers toe aan de maker van de catalogus.  U kunt afzonderlijke gebruikers toevoegen, of voor het gemak kunnen een groep toevoegen, waarvan de leden vervolgens catalogi kunnen maken. Volg deze stappen om een gebruiker toe te wijzen aan de maker van de catalogus.

**Vereiste rol:** Globale beheerder of gebruikers beheerder

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links in het gedeelte **beheer rechten** op **instellingen**.

1. Klik op **Bewerken**.

1. Klik in de sectie **rechten beheer** voor gemachtigden op **catalogus makers toevoegen** om de gebruikers of groepen te selecteren die de leden van deze rol moeten zijn.

1. Klik op **Selecteren**.

1. Klik op **Opslaan**.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Een catalogus eigenaar of een Access Package Manager toevoegen

Als u het beheer van een catalogus of toegangs pakketten in de catalogus wilt delegeren, voegt u gebruikers toe aan de rol van de catalogus of Access Package Manager. Iedereen die een catalogus maakt, wordt de eerste eigenaar van de catalogus. Volg deze stappen om een gebruiker toe te wijzen aan de rol van catalogus eigenaar of toegangs pakket beheer.

**Vereiste rol:** Globale beheerder, gebruikers beheerder of catalogus eigenaar

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus waaraan u beheerders wilt toevoegen.

1. Klik in het menu links op **rollen en beheerders**.

1. Klik op **eigen aren toevoegen** of **Voeg toegangs pakket beheerders toe** om de leden voor deze rollen te selecteren.

1. Klik op **selecteren** om deze leden toe te voegen.

## <a name="next-steps"></a>Volgende stappen

- [Goed keurders toevoegen](entitlement-management-access-package-edit.md#policy-request)
- [Resources toevoegen aan een catalogus](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
