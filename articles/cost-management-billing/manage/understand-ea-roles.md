---
title: Inzicht in beheerdersrollen voor bedrijven in Azure | Microsoft Docs
description: Meer informatie over zakelijke beheerdersrollen in Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: banders
ms.openlocfilehash: 7b3b3e96f9e065614e8acc5305658c2e1d0abd4a
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270223"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Inzicht in Azure Enterprise Agreement-beheerdersrollen in Azure

Voor het beheren van het gebruik en de uitgaven van uw organisatie kunnen Azure-klanten met een Enterprise Agreement (EA) vijf beheerdersrollen toewijzen:

- Zakelijke beheerder
- Zakelijke beheerder (alleen lezen)<sup>1</sup>
- Afdelingsbeheerder
- Afdelingsbeheerder (alleen lezen)
- Eigenaar van account<sup>2</sup>

<sup>1</sup> Deze rol omvat de Factuurcontact van het EA-contract.

<sup>2</sup> Factuurcontact kan niet worden toegevoegd of gewijzigd in de Azure EA-portal, en wordt toegevoegd aan de EA-inschrijving op basis van de gebruiker die op overeenkomstniveau is ingesteld als de Factuurcontact. Als u de Factuurcontact wilt wijzigen, moet u via een partner-/softwareadviseur een aanvraag indienen bij het ROC (Regional Operations Center).

Deze rollen zijn specifiek voor het beheren van Azure Enterprise Agreements en zijn een aanvulling op de ingebouwde rollen waarover Azure beschikt om toegang tot resources te beheren. Zie [Ingebouwde rollen voor Azure-resources](../../role-based-access-control/built-in-roles.md) voor meer informatie.

In de volgende secties worden de beperkingen en mogelijkheden van elke rol beschreven.

## <a name="user-limit-for-admin-roles"></a>Gebruikerslimiet voor beheerdersrollen

|Rol| Gebruikerslimiet|
|---|---|
|Zakelijke beheerder|Onbeperkt|
|Zakelijke beheerder (alleen lezen)|Onbeperkt|
|Afdelingsbeheerder|Onbeperkt|
|Afdelingsbeheerder (alleen lezen)|Onbeperkt|
|Accounteigenaar|1 per account<sup>3</sup>|

<sup>3</sup> Voor elk account is een uniek Microsoft-account of werk- of schoolaccount vereist.

## <a name="organization-structure-and-permissions-by-role"></a>De organisatiestructuur en machtigingen per rol

|Taken| Zakelijke beheerder|Zakelijke beheerder (alleen lezen)|Afdelingsbeheerder|Afdelingsbeheerder (alleen lezen)|Accounteigenaar|
|---|---|---|---|---|---|
|Zakelijke beheerders weergeven|✔|✔|✘|✘|✘|
|Zakelijke beheerders toevoegen of verwijderen|✔|✘|✘|✘|✘|
|Contactpersonen voor meldingen weergeven<sup>4</sup> |✔|✔|✘|✘|✘|
|Contactpersonen voor meldingen toevoegen of verwijderen<sup>4</sup> |✔|✘|✘|✘|✘|
|Afdelingen maken en beheren |✔|✘|✘|✘|✘|
|Afdelingsbeheerders weergeven|✔|✔|✔|✔|✘|
|Afdelingsbeheerders toevoegen of verwijderen|✔|✘|✔|✘|✘|
|Accounts in de inschrijving weergeven |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|
|Accounts aan de inschrijving toevoegen en de accounteigenaar wijzigen|✔|✘|✔<sup>5</sup>|✘|✘|
|Abonnementen en abonnementsmachtigingen maken en beheren|✘|✘|✘|✘|✔|

- <sup>4</sup> Contactpersonen voor meldingen ontvangen e-mailberichten over de Azure Enterprise Agreement.
- <sup>5</sup> Taak is beperkt tot accounts in uw afdeling.


## <a name="usage-and-costs-access-by-role"></a>Toegang tot gebruik en kosten per rol

|Taken| Zakelijke beheerder|Zakelijke beheerder (alleen lezen)|Afdelingsbeheerder|Afdelingsbeheerder (alleen lezen) |Accounteigenaar|
|---|---|---|---|---|---|
|Tegoedsaldo weergeven, inclusief financiële toezegging|✔|✔|✘|✘|✘|
|Bestedingsquotum van afdeling weergeven|✔|✔|✘|✘|✘|
|Bestedingsquotum van afdeling instellen|✔|✘|✘|✘|✘|
|Het EA-prijzenoverzicht van de organisatie weergeven|✔|✔|✘|✘|✘|
|Details over gebruik en kosten weergeven|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|
|Resources beheren in Azure Portal|✘|✘|✘|✘|✔|

- <sup>6</sup> Hiervoor is vereist dat de ondernemingsbeheerder het beleid **DA-weergavekosten** inschakelt in de Enterprise Portal. De afdelingsbeheerder kan vervolgens gedetailleerde kosten voor de afdeling bekijken.
- <sup>7</sup> Hiervoor is vereist dat de ondernemingsbeheerder het beleid **AO-weergavekosten** inschakelt in de Enterprise Portal. De accounteigenaar kan vervolgens gedetailleerde kosten voor het account zien.


## <a name="pricing-in-azure-portal"></a>Prijzen in Azure Portal

Mogelijk ziet u verschillende prijzen in Azure Portal, afhankelijk van uw beheerdersrol en de manier waarop de beleidsregels voor weergavekosten zijn ingesteld door de zakelijke beheerder. De twee beleidsregels in de zakelijke portal die invloed hebben op de prijzen die u in Azure Portal ziet, zijn:

- DA-weergavekosten
- AO-weergavekosten

Zie [Toegang tot factureringsgegevens beheren voor Azure](manage-billing-access.md) voor informatie over de manier waarop u deze beleidsregels instelt.

In de volgende tabel ziet u de relatie tussen de Enterprise Agreement-beheerdersrollen, het weergavekostenbeleid, de RBCA-rol (op rollen gebaseerde toegangscontrole) in Azure Portal en de prijzen die u ziet in Azure Portal. De zakelijke beheerder ziet altijd gebruiksgegevens op basis van de EA-prijzen van de organisatie. De afdelingsbeheerder en de accounteigenaar zien echter verschillende prijsweergaven op basis van het weergavekostenbeleid en hun RBAC-rol. De afdelingsbeheerdersrol die in de volgende tabel wordt vermeld, verwijst naar zowel de rol van afdelingsbeheerder als de rol van afdelingsbeheerder (alleen lezen).

|Enterprise Agreement-beheerdersrol|Weergavekostenbeleid voor rol|RBAC-rol|Prijsweergave|
|---|---|---|---|
|Accounteigenaar of afdelingsbeheerder|✔ Ingeschakeld|Eigenaar|EA-prijzen van de organisatie|
|Accounteigenaar of afdelingsbeheerder|✘ Uitgeschakeld|Eigenaar|Retailprijzen|
|Accounteigenaar of afdelingsbeheerder|✔ Ingeschakeld |geen|Geen prijzen|
|Accounteigenaar of afdelingsbeheerder|✘ Uitgeschakeld |geen|Geen prijzen|
|Geen|Niet van toepassing |Eigenaar|Retailprijzen|

U stelt de zakelijke beheerdersrol en beleidsregels voor weergavekosten in via de zakelijke portal. De RBAC-rol kan worden bijgewerkt in Azure Portal. Zie [Toegang beheren met op rollen gebaseerd toegangsbeheer en Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot factureringsgegevens beheren voor Azure](manage-billing-access.md)
- [Toegang beheren met op rollen gebaseerd toegangsbeheer en Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Ingebouwde rollen voor Azure-resources](../../role-based-access-control/built-in-roles.md)
