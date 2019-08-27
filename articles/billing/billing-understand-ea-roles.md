---
title: Meer informatie over beheerders rollen voor Enter prise in azure | Microsoft Docs
description: Meer informatie over ondernemings beheerders rollen in Azure.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2018
ms.author: banders
ms.openlocfilehash: 39526defb8f41ddacb0a26d7ad852f820ca6ea77
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034531"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Meer informatie over Azure Enterprise Agreement-beheer rollen in azure

Om het gebruik en de uitgaven van uw organisatie te helpen beheren, kunnen Azure-klanten met een Enterprise Agreement (EA) vijf afzonderlijke beheerders rollen toewijzen:

- Ondernemingsbeheerder
- Enter prise-beheerder (alleen-lezen)
- Afdelingsbeheerder
- Afdelings beheerder (alleen-lezen)
- Accounteigenaar
 
Deze rollen zijn specifiek voor het beheer van Azure Enter prise-overeenkomsten en zijn naast de ingebouwde rollen Azure om de toegang tot resources te beheren. Zie [ingebouwde rollen voor Azure-resources](../role-based-access-control/built-in-roles.md)voor meer informatie.

In de volgende secties worden de beperkingen en mogelijkheden van elke rol beschreven.

## <a name="user-limit-for-admin-roles"></a>Gebruikers limiet voor beheerders rollen

|Role| Gebruikers limiet|
|---|---|
|Ondernemingsbeheerder|Onbeperkt|
|Enter prise-beheerder (alleen-lezen)|Onbeperkt|
|Afdelingsbeheerder|Onbeperkt|
|Afdelings beheerder (alleen-lezen)|Onbeperkt|
|Accounteigenaar|1 per account<sup>1</sup>|

<sup>1</sup> voor elk account is een uniek Microsoft-account-of werk-of school account vereist.

## <a name="organization-structure-and-permissions-by-role"></a>Organisatie structuur en-machtigingen op rol

|Taken| Ondernemingsbeheerder|Enter prise-beheerder (alleen-lezen)|Afdelingsbeheerder|Afdelings beheerder (alleen-lezen)|Accounteigenaar|
|---|---|---|---|---|---|
|Ondernemings Administrators weer geven|✔|✔|✘|✘|✘|
|Ondernemings Administrators toevoegen of verwijderen|✔|✘|✘|✘|✘|
|Meldings contactpersonen weer geven<sup>2</sup> |✔|✔|✘|✘|✘|
|Meldings contactpersonen toevoegen of verwijderen<sup>2</sup> |✔|✘|✘|✘|✘|
|Afdelingen maken en beheren |✔|✘|✘|✘|✘|
|Afdelings beheerders weer geven|✔|✔|✔|✔|✘|
|Afdelings beheerders toevoegen of verwijderen|✔|✘|✔|✘|✘|
|Accounts in de inschrijving weer geven |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Accounts toevoegen aan de inschrijving en de eigenaar van het account wijzigen|✔|✘|✔<sup>3</sup>|✘|✘|
|Abonnementen en abonnements machtigingen maken en beheren|✘|✘|✘|✘|✔|

- <sup>2</sup> er worden e-mail meldingen verzonden over de Enterprise Agreement van de Azure-contact persoon.
- <sup>3</sup> taak is beperkt tot accounts in uw afdeling.


## <a name="usage-and-costs-access-by-role"></a>Gebruik en kosten toegang per rol

|Taken| Ondernemingsbeheerder|Enter prise-beheerder (alleen-lezen)|Afdelingsbeheerder|Afdelings beheerder (alleen-lezen) |Accounteigenaar|
|---|---|---|---|---|---|
|Credit saldo weer geven inclusief monetaire toezeg ging|✔|✔|✘|✘|✘|
|Uitgave quota's voor afdelingen weer geven|✔|✔|✘|✘|✘|
|Uitgave quota van afdelingen instellen|✔|✘|✘|✘|✘|
|EA-prijs lijst van organisatie weer geven|✔|✔|✘|✘|✘|
|Details over gebruik en kosten weer geven|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Resources in Azure Portal beheren|✘|✘|✘|✘|✔|

- <sup>4</sup> vereist dat de Enter prise-beheerder het beleid **voor da** -toeslagen in de Enter prise Portal inschakelt. De afdelings beheerder kan vervolgens de kosten Details voor de afdeling bekijken.
- <sup>5</sup> vereist dat de ondernemings beheerder **ao** in de Enter prise Portal inschakelen. De eigenaar van het account kan vervolgens de kosten Details voor het account weer geven.


## <a name="pricing-in-azure-portal"></a>Prijzen in Azure Portal

U ziet mogelijk verschillende prijzen in de Azure Portal, afhankelijk van uw administratieve rol en hoe de beleids regels voor de weer gave van kosten worden ingesteld door de ondernemings beheerder. De twee beleids regels in de Enter prise portal die van invloed zijn op de prijzen die u ziet in de Azure Portal zijn:

- DA-kosten weer geven
- Kosten AO weer geven

Zie [toegang beheren tot facturerings gegevens voor Azure](billing-manage-access.md)voor meer informatie over het instellen van dit beleid.

In de volgende tabel ziet u de relatie tussen de Enterprise Agreement beheerders rollen, het beleid voor het weer geven van rechten, de functie op basis van op rollen gebaseerde toegangs beheer (RBAC) in de Azure Portal en de prijzen die u ziet in de Azure Portal. De Enter prise-beheerder ziet altijd gebruiks gegevens op basis van de EA-prijzen van de organisatie. De afdeling beheerder en account eigenaar zien echter verschillende prijs weergaven op basis van het beleid voor de weer gave van kosten en hun RBAC-rol. De rol afdelings beheerder in de volgende tabel verwijst naar de rollen afdeling beheerder en afdelings beheerder (alleen-lezen).

|Beheerdersrol Enterprise Agreement|Kosten beleid voor rol weer geven|RBAC-rol|Prijs weergave|
|---|---|---|---|
|Account eigenaar of afdelings beheerder|✔ Ingeschakeld|Eigenaar|EA-prijzen van organisatie|
|Account eigenaar of afdelings beheerder|✘ Is uitgeschakeld|Eigenaar|Prijzen voor winkels|
|Account eigenaar of afdelings beheerder|✔ Ingeschakeld |geen|Geen prijzen|
|Account eigenaar of afdelings beheerder|✘ Is uitgeschakeld |geen|Geen prijzen|
|Geen|Niet van toepassing |Eigenaar|Prijzen voor winkels|

U stelt de rol ondernemings beheerder in en bekijkt het beleid voor kosten in de Enter prise Portal. De RBAC-rol kan worden bijgewerkt in de Azure Portal. Zie [toegang beheren met RBAC en de Azure Portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Beheer de toegang tot factureringsgegevens voor Azure](billing-manage-access.md)
- [Toegang beheren met op rollen gebaseerd toegangsbeheer en Azure Portal](../role-based-access-control/role-assignments-portal.md)
- [Ingebouwde rollen voor Azure-resources](../role-based-access-control/built-in-roles.md)
