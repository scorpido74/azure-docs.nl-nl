---
title: Verbeteringen in gebruikers beheer (preview)-Azure Active Directory | Microsoft Docs
description: Hierin wordt beschreven hoe Azure Active Directory zoeken, filteren en meer informatie over uw gebruikers door de gebruiker kan worden gezocht.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 09/03/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e0c8e6fb3bab179483d03320e6d90ab712ec528
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89493347"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Verbeteringen in gebruikers beheer (preview) in Azure Active Directory

In dit artikel wordt beschreven hoe u de preview-versie van verbeterd gebruikers beheer gebruikt in de portal van Azure Active Directory (Azure AD). De pagina's **alle gebruikers** en **Verwijderde gebruikers** zijn bijgewerkt met meer informatie en kunnen gebruikers gemakkelijker vinden. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Wijzigingen in de preview zijn onder andere:

- Meer zicht bare gebruikers eigenschappen, waaronder de object-ID, de synchronisatie status van de Directory, het aanmaak type en de identiteits verlener
- Met zoeken kunt u nu gecombineerde Zoek opdrachten van namen, e-mails en object-Id's
- Verbeterd filteren op gebruikers type (lid en gast), adreslijst synchronisatie status en aanmaak type

> [!NOTE]
> Deze preview is momenteel niet beschikbaar voor Azure AD B2C-tenants.

## <a name="find-the-preview"></a>De preview-versie zoeken

De preview-versie is standaard ingeschakeld, zodat u deze meteen kunt gebruiken. U kunt de nieuwste functies en verbeteringen bekijken door preview- **functies** te selecteren op de pagina **alle gebruikers** . Op alle pagina's die zijn bijgewerkt als onderdeel van deze preview, wordt een voorbeeld code weer gegeven. Als u problemen ondervindt, kunt u teruggaan naar de oude ervaring:

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) en selecteer **gebruikers**.
1. Selecteer op de pagina **gebruikers – alle gebruikers** de banner boven aan de pagina.
1. In het deel venster **Preview-functies** schakelt u **uitgebreid gebruikers beheer** uit.

   ![Hoe en wanneer u uitgebreid gebruikers beheer in-en uitschakelt](./media/users-search-enhanced/enable-preview.png)

We stellen uw feedback op prijs zodat we onze ervaring kunnen verbeteren.

## <a name="more-user-properties"></a>Meer gebruikers eigenschappen

Er zijn enkele wijzigingen aangebracht in de beschik bare kolommen op de pagina's **alle gebruikers** en **Verwijderde gebruikers** . Naast de bestaande kolommen die we bieden voor het beheren van uw lijst met gebruikers, hebben we nog enkele kolommen toegevoegd.

### <a name="all-users-page"></a>Pagina alle gebruikers

Hieronder ziet u de eigenschappen van de gebruiker op de pagina **alle gebruikers** :

- Naam: de weergave naam van de gebruiker.
- Principal-naam van gebruiker: de user principal name (UPN) van de gebruiker.
- Gebruikers type: het gebruikers type van de gebruiker, ofwel lid of gast.
- Directory-synchronisatie: geeft aan of de gebruiker is gesynchroniseerd vanuit een on-premises Directory.
- Identiteits verlener: de verleners van de identiteit die wordt gebruikt om zich aan te melden bij een gebruikers account.
- Object-ID: de object-ID van de gebruiker.
- Type maken: Hiermee wordt aangegeven hoe het gebruikers account is gemaakt.
- Bedrijfs naam: de bedrijfs naam waaraan de gebruiker is gekoppeld.
- Uitnodigings status: de status van de uitnodiging voor een gast gebruiker.
- Mail: het e-mail adres van de gebruiker.

   ![nieuwe gebruikers eigenschappen die worden weer gegeven op de pagina's van alle gebruikers en verwijderde gebruikers](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>Pagina verwijderde gebruikers

De pagina **Verwijderde gebruikers** bevat alle kolommen die beschikbaar zijn op de pagina **alle gebruikers** en enkele extra kolommen, namelijk:

- Verwijderings datum: de datum waarop de gebruiker voor het eerst is verwijderd uit de organisatie (de gebruiker is herstorbaar).
- Permanente verwijderings datum: de datum waarop de gebruiker permanent is verwijderd uit de organisatie.

Sommige kolommen worden standaard weer gegeven. Als u andere kolommen wilt toevoegen, selecteert u **kolommen** op de pagina, selecteert u de kolom namen die u wilt toevoegen en selecteert u **OK** om uw voor keuren op te slaan.

### <a name="identity-issuers"></a>Identiteits verleners

Selecteer een vermelding in de kolom **identiteits verlener** voor een wille keurige gebruiker om aanvullende informatie over de certificaat verlener te bekijken, inclusief het aanmeldings type en de toegewezen id van de verlener. De vermeldingen in de kolom **identiteits verlener** kunnen meerdere waarden hebben. Als er meerdere verleners van de identiteit van de gebruiker zijn, wordt het woord meerdere weer geven in de kolom **identiteits verlener** op **alle gebruikers** en **Verwijderde gebruikers** pagina's, en in het detail venster worden alle verleners weer geven.

> [!NOTE]
> De **bron** kolom wordt vervangen door meerdere kolommen, inclusief het **aanmaak type**, de **Directory synchronisatie**en de **identiteits verlener** voor gedetailleerdere filters.

## <a name="user-list-search"></a>Zoek opdracht gebruikers lijst

Wanneer u een zoek reeks invoert, gebruikt de zoek opdracht ' begint met ' die nu namen, e-mail berichten of object-Id's in één zoek opdracht kan vinden. U kunt een van deze kenmerken in het zoekvak invoeren, waarna de zoek opdracht automatisch alle overeenkomende resultaten oplevert. U kunt dezelfde zoek opdracht uitvoeren op de pagina's **alle gebruikers** en **Verwijderde gebruikers** .

## <a name="user-list-filtering"></a>Filteren van gebruikers lijst

Filter mogelijkheden zijn uitgebreid om meer filter opties te bieden voor de pagina's **alle gebruikers** en **Verwijderde gebruikers** . U kunt nu tegelijkertijd filteren op meerdere eigenschappen, en u kunt filteren op meer eigenschappen.

### <a name="filtering-all-users-list"></a>De lijst alle gebruikers filteren

Hieronder ziet u de eigenschappen die kunnen worden gefilterd op de pagina **alle gebruikers** :

- Gebruikers type-lid of gast
- Status van Directory-synchronisatie-Ja
- Type uitnodiging maken, E-mail geverifieerd, lokaal account
- Uitnodigings status: acceptatie in behandeling, geaccepteerd
- Beheer eenheid: Selecteer deze optie om het bereik van de gebruikers die u bekijkt tot één beheer eenheid te beperken. Zie beheer van [administratieve eenheden](directory-administrative-units.md)voor meer informatie.

## <a name="filtering-deleted-users-list"></a>Lijst met verwijderde gebruikers filteren

De pagina **Verwijderde gebruikers** bevat extra filters, niet op de pagina **alle gebruikers** . Hieronder ziet u de eigenschappen die kunnen worden gefilterd op de pagina **Verwijderde gebruikers** :

- Gebruikers type-lid of gast
- Status van Directory-synchronisatie-Ja
- Type uitnodiging maken, E-mail geverifieerd, lokaal account
- Uitnodigings status: acceptatie in behandeling, geaccepteerd
- Verwijderings datum-laatste 7, 14 of 30 dagen
- Datum van definitieve verwijdering-afgelopen 7, 14 of 30 dagen

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

Vraag | Antwoord
-------- | ------
Wat gebeurt er met de bulk mogelijkheden voor gebruikers en gasten? | De bulk bewerkingen zijn allemaal nog steeds beschikbaar voor gebruikers en gasten, waaronder bulksgewijs maken, bulksgewijs uitnodigen, bulksgewijs verwijderen en gebruikers downloaden. We hebben deze zojuist samengevoegd in een menu met de naam **bulk bewerkingen**. U kunt de opties voor **bulk bewerkingen** vinden boven aan de pagina **alle gebruikers** .
Wat is er gebeurd met de bron kolom? | De **bron** kolom is vervangen door andere kolommen die vergelijk bare informatie geven, terwijl u kunt filteren op die waarden onafhankelijk van elkaar. Voor beelden zijn onder meer het **type maken**, de **Directory is gesynchroniseerd** en de identiteit van de **Uitgever**.
Wat is er gebeurd met de kolom gebruikers naam? | De kolom **gebruikers naam** is nog steeds, maar is gewijzigd in Principal-naam van **gebruiker**. Dit komt overeen met de informatie in die kolom. U ziet ook dat de volledige Principal-naam van de gebruiker nu wordt weer gegeven voor B2B-gasten. Dit komt overeen met wat u in MS Graph zou krijgen.  
Waarom kan ik alleen een zoek opdracht uitvoeren met ' starten met ' en niet naar ' bevat '? | Er zijn enkele beperkingen waardoor het niet mogelijk is om een ' contains '-Zoek opdracht uit te voeren. We hebben de feedback gehoord en blijven daarom afgestemd.
Waarom kan ik de kolommen niet sorteren? | Er zijn enkele beperkingen waardoor het niet mogelijk is om de kolommen te sorteren. We hebben de feedback gehoord en blijven daarom afgestemd.
Waarom kan ik de **gesynchroniseerde** kolom alleen filteren op Ja? | Er zijn enkele beperkingen waardoor het niet mogelijk is om deze eigenschap te filteren op geen waarde. We hebben de feedback gehoord en blijven daarom afgestemd.

## <a name="next-steps"></a>Volgende stappen

Gebruikers bewerkingen

- [Profiel gegevens toevoegen of wijzigen](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Gebruikers toevoegen of verwijderen](../fundamentals/add-users-azure-active-directory.md)

Bulk bewerkingen

- [Lijst met gebruikers downloaden](users-bulk-download.md)
- [Gebruikers bulksgewijs toevoegen](users-bulk-add.md)
- [Gebruikers bulksgewijs verwijderen](users-bulk-delete.md)
- [Gebruikers bulksgewijs herstellen](users-bulk-restore.md)
