---
title: Gebruikersgegevens beheren in Azure Security Center | Microsoft Documenten
description: Meer informatie over het beheren van de gebruikersgegevens in Azure Security Center. Het beheren van gebruikersgegevens omvat de mogelijkheid om gegevens te openen, te verwijderen of te exporteren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: bf715d872fab421de30ebcb146a1981a7d008738
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585977"
---
# <a name="manage-user-data-in-azure-security-center"></a>Gebruikersgegevens beheren in Azure Security Center
In dit artikel vindt u informatie over hoe u de gebruikersgegevens beheren in Azure Security Center. Het beheren van gebruikersgegevens omvat de mogelijkheid om gegevens te openen, te verwijderen of te exporteren.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Een gebruiker van het Beveiligingscentrum die de rol van reader-, eigenaar-, inzender- of accountbeheerder heeft toegewezen, heeft toegang tot klantgegevens in de tool. Zie [Ingebouwde rollen voor Azure-functiegebaseerdtoegangsbeheer voor](../role-based-access-control/built-in-roles.md) meer informatie over de rollen Reader, Owner en Inzender voor meer informatie over de rol van accountbeheerder en ingebouwde functies. Zie [Azure-abonnementsbeheerders](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Persoonsgegevens zoeken en identificeren
Een gebruiker van het Security Center kan zijn persoonlijke gegevens bekijken via de Azure-portal. Security Center slaat alleen beveiligingscontactgegevens op, zoals e-mailadressen en telefoonnummers. Zie [Beveiligingscontactgegevens opgeven in Azure Security Center](security-center-provide-security-contact-details.md)voor meer informatie.

In de Azure-portal kan een gebruiker toegestane IP-configuraties weergeven met de just-in-time VM-toegangsfunctie van het Security Center. Zie [Toegang tot virtuele machines beheren met just-in-time](security-center-just-in-time.md)voor meer informatie.

In de Azure-portal kan een gebruiker beveiligingswaarschuwingen van beveiligingscentrum bekijken, waaronder IP-adressen en gegevens van aanvallers. Zie [Beveiligingswaarschuwingen beheren en reageren in Azure Security Center](security-center-managing-and-responding-alerts.md)voor meer informatie.

## <a name="classifying-personal-data"></a>Persoonlijke gegevens classificeren
U hoeft geen persoonlijke gegevens te classificeren die zijn gevonden in de beveiligingscontactfunctie van Security Center. De opgeslagen gegevens zijn een e-mailadres (of meerdere e-mailadressen) en een telefoonnummer. [Contactgegevens](security-center-provide-security-contact-details.md) worden gevalideerd door Security Center.

U hoeft de IP-adressen en poortnummers die zijn opgeslagen door de [just-in-time-functie](security-center-just-in-time.md) van Security Center niet te classificeren.

Alleen een gebruiker die de rol van beheerder heeft toegewezen, kan persoonlijke gegevens classificeren door [waarschuwingen te bekijken](security-center-managing-and-responding-alerts.md) in Security Center.

## <a name="securing-and-controlling-access-to-personal-data"></a>Beveiligen en beheren van de toegang tot persoonsgegevens
Een gebruiker van het Beveiligingscentrum die de rol van reader, eigenaar, inzender of accountbeheerder heeft toegewezen, heeft toegang tot [beveiligingscontactgegevens.](security-center-provide-security-contact-details.md)

Een gebruiker van het Beveiligingscentrum die de rol van reader-, eigenaar-, inzender- of accountbeheerder heeft toegewezen, heeft toegang tot zijn [just-in-time-beleid.](security-center-just-in-time.md)

Een gebruiker van het Beveiligingscentrum die de rol van lezer, eigenaar, inzender of accountbeheerder heeft toegewezen, kan zijn [waarschuwingen](security-center-managing-and-responding-alerts.md)bekijken.

## <a name="updating-personal-data"></a>Persoonlijke gegevens bijwerken
Een gebruiker van het Beveiligingscentrum die de rol van eigenaar, inzender of accountbeheerder heeft toegewezen, kan [beveiligingscontactgegevens](security-center-provide-security-contact-details.md) bijwerken via de Azure-portal.

Een gebruiker van het Beveiligingscentrum die de rol van eigenaar, inzender of accountbeheerder heeft toegewezen, kan zijn [just-in-time-beleid bijwerken.](security-center-just-in-time.md)

Een accountbeheerder kan geen waarschuwingsincidenten bewerken. Een [waarschuwingsincident](security-center-managing-and-responding-alerts.md) wordt beschouwd als beveiligingsgegevens en wordt alleen gelezen.

## <a name="deleting-personal-data"></a>Persoonlijke gegevens verwijderen
Een gebruiker van het Beveiligingscentrum die de rol van eigenaar, inzender of accountbeheerder heeft toegewezen, kan [beveiligingscontactgegevens](security-center-provide-security-contact-details.md) verwijderen via de Azure-portal.

Een gebruiker van het Beveiligingscentrum die de rol van eigenaar, inzender of accountbeheerder heeft toegewezen, kan het [just-in-time-beleid](security-center-just-in-time.md) via de Azure-portal verwijderen.

Een gebruiker van het Beveiligingscentrum kan geen waarschuwingsincidenten verwijderen. Om veiligheidsredenen wordt een [waarschuwingsincident](security-center-managing-and-responding-alerts.md) beschouwd als alleen-lezen gegevens.

## <a name="exporting-personal-data"></a>Persoonlijke gegevens exporteren
Een gebruiker van het Beveiligingscentrum die de rol van lezer, eigenaar, inzender of accountbeheerder heeft toegewezen, kan [beveiligingscontactgegevens](security-center-provide-security-contact-details.md) exporteren door:

- Kopiëren vanuit de Azure-portal
- Download HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Een gebruiker van het Beveiligingscentrum die de rol van accountbeheerder heeft toegewezen, kan het [just-in-time-beleid](security-center-just-in-time.md) met de IP-adressen exporteren door:

- Kopiëren vanuit de Azure-portal
- Download HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Een accountbeheerder kan de waarschuwingsgegevens exporteren door:

- Kopiëren vanuit de Azure-portal
- Download HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Zie [Beveiligingswaarschuwingen (GET-verzameling ophalen) voor](https://msdn.microsoft.com/library/mt704050.aspx)meer informatie.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Beperking van het gebruik van persoonsgegevens voor profilering of marketing zonder toestemming
Een gebruiker van het Security Center kan ervoor kiezen om zich af te melden door zijn [beveiligingscontactgegevens te](security-center-provide-security-contact-details.md)verwijderen.

[Just-in-time gegevens](security-center-just-in-time.md) worden beschouwd als niet-identificeerbare gegevens en worden bewaard voor een periode van 30 dagen.

[Waarschuwingsgegevens](security-center-managing-and-responding-alerts.md) worden beschouwd als beveiligingsgegevens en worden bewaard voor een periode van twee jaar.

## <a name="auditing-and-reporting"></a>Controle en rapportage
Controleer logboeken van beveiligingscontact, just-in-time en waarschuwingsupdates worden bijgehouden in [Azure Activity Logs.](../azure-monitor/platform/platform-logs-overview.md)