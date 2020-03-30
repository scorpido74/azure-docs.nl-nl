---
title: Leden en eigenaren zoeken en filteren (voorbeeld) - Azure Active Directory | Microsoft Documenten
description: Zoek- en filtergroepen van leden en eigenaren in de Azure-portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a815446b79b3e5ec0a75e5d179953956643b16c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206109"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Zoekgroepen en leden (voorbeeld) in Azure Active Directory

In dit artikel vindt u hoe u zoeken naar leden en eigenaren van een groep en hoe u zoekfilters gebruiken als onderdeel van de preview-functie voor groepsverbetering in de Azure AD-portal (Azure AD). Er zijn veel verbeteringen in de groepservaringen om u te helpen uw groepen, inclusief leden en eigenaren, snel en eenvoudig te beheren. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over voorvertoningen.

Wijzigingen in deze preview zijn onder andere:

- Nieuwe zoekmogelijkheden voor groepen, zoals subtekenreekszoeken in groepsnamen
- Nieuwe filter- en sorteeropties op leden- en eigenaarlijsten
- Nieuwe zoekmogelijkheden voor leden- en eigenaarlijsten
- Nauwkeurigere groepstellingen voor grote groepen

## <a name="enabling-and-managing-the-preview"></a>De preview in- en beheren

We hebben het gemakkelijk gemaakt om deel te nemen aan de preview:

  1. Meld u aan bij de [Azure AD-portal](https://portal.azure.com)en selecteer **Groepen**.
  2. Selecteer in de pagina Groepen – Alle groepen de banner boven aan de pagina om deel te nemen aan het voorbeeld.

U ook de nieuwste functies en verbeteringen bekijken door **Voorbeeldinformatie** te selecteren op de pagina **Alle groepen.** Nadat u bent lid geworden van de preview, u de voorbeeldtag zien op alle groepenpagina's die verbeteringen hebben en deel uitmaken van de preview. Niet elke pagina met groepen is bijgewerkt als onderdeel van deze preview.

Als u problemen ondervindt, u de verouderde ervaring terugschakelen door de banner boven aan de pagina **Alle groepen** te selecteren. We stellen uw feedback op prijs, zodat we onze ervaring kunnen verbeteren.

## <a name="group-search-and-sorting"></a>Groepszoeken en sorteren

De zoekfunctie voor groepenlijst is verbeterd, zodat wanneer u een `startswith` zoektekenreeks invoeren, de zoekopdracht automatisch een en substring-zoekopdracht uitvoert in de lijst met groepsnamen. De substring zoekopdracht wordt alleen uitgevoerd op hele woorden, en bevat geen speciale tekens. Substring zoeken is hoofdlettergevoelig.

![nieuwe subtekenreekszoekopdrachten op de pagina Alle groepen](./media/groups-members-owners-search/groups-search-preview.png)

Een zoekopdracht naar 'beleid' retourneert nu bijvoorbeeld zowel 'MDM-beleid – West' als 'Beleidsgroep'. Een groep met de naam 'New_policy' wordt niet geretourneerd.

- U dezelfde zoekopdracht ook uitvoeren op lijsten met groepslidmaatschapslijsten.
- U de lijst met groepen nu sorteren op naam met de pijlen rechts van de naamkolomkop om de lijst in oplopende of aflopende volgorde te sorteren.

## <a name="group-member-search-and-filtering"></a>Zoeken en filteren van groepsleden

### <a name="search-group-member-and-owner-lists"></a>Lijst met groepsleden en eigenaarzoeken

U nu de leden van een specifieke groep op naam doorzoeken en dezelfde zoekopdracht uitvoeren op de lijst met de eigenaren van een groep. Als u in de nieuwe ervaring een tekenreeks invoert in het zoekvak, wordt automatisch een beginzoekopdracht uitgevoerd. Bijvoorbeeld, een zoektocht naar "Scott" zal Scott Wilkinson terug.

![nieuwe substring zoekopdrachten op de lijst met groepsleden en -eigenaren](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Lijst met leden en eigenaren filteren

Naast zoeken u nu ook de leden- en eigenaarlijsten filteren op gebruikerstype. Dit is de informatie in de kolom Gebruikerstype van de lijst. U de lijst dus filteren op leden en gasten om te bepalen of er gasten in de groep zijn.

### <a name="view-and-manage-membership"></a>Lidmaatschap weergeven en beheren

Naast het bekijken van de directe leden van een specifieke groep, u nu de lijst van alle leden van de groep bekijken op de pagina Leden. De ledenlijst bevat alle unieke leden van de groep, inclusief alle tijdelijke leden.

U ook de lijst met directe leden en de lijst met alle leden afzonderlijk zoeken en filteren. Het filteren van de lijst met alle leden heeft geen invloed op de filters die worden toegepast op de lijst met directe leden.

## <a name="improved-group-member-counts"></a>Verbeterde groepleden tellen

We hebben de pagina **groepsoverzicht** verbeterd om groepsledentellingen voor groepen van elke grootte te bieden. U zien dat het lid telt, zelfs voor groepen met meer dan 1.000 leden. U nu het totale aantal directe leden voor een groep en het totale aantal leden van het lidmaatschap (alle unieke leden van groepsleden inclusief tijdelijke leden) zien op de **pagina Overzicht.**

![Hogere nauwkeurigheid in groepslidmaatschaptelt](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over het werken met groepen in Azure AD.

- [Groepen en leden weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Groepslidmaatschap beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Dynamische regels voor gebruikers in een groep beheren](groups-create-rule.md)
- [Instellingen van uw groep bewerken](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Toegang tot resources met behulp van groepen beheren](../fundamentals/active-directory-manage-groups.md)
- [Toegang tot SaaS-apps met behulp van groepen beheren](groups-saasapps.md)
- [Groepen met behulp van PowerShell-opdrachten beheren](groups-settings-v2-cmdlets.md)
- [Een Azure-abonnement toevoegen aan Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
