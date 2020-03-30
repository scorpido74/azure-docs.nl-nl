---
title: Eigenschappen van een B2B-gastgebruiker - Azure Active Directory | Microsoft Documenten
description: Eigenschappen en statussen van Azure Active Directory B2B-gebruikers voor en na het inwisselen van uitnodigingen
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f5002e361653614c966dc43301afa83eb7b200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050800"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Eigenschappen van een Azure Active Directory B2B-samenwerkingsgebruiker

In dit artikel worden de eigenschappen en statussen van het B2B-gastgebruikersobject in Azure Active Directory (Azure AD) voor en na het inwisselen van uitnodigingen beschreven. Een B2B-samenwerkingsgebruiker (Azure AD business-to-business) is een gebruiker met UserType = Gast. Deze gastgebruiker komt meestal uit een partnerorganisatie en heeft standaard beperkte bevoegdheden in de uitnodigende map.

Afhankelijk van de behoeften van de uitnodigende organisatie kan een Azure AD B2B-samenwerkingsgebruiker zich in een van de volgende accountvermeldingen bevinden:

- Status 1: Homed in een extern exemplaar van Azure AD en vertegenwoordigd als gastgebruiker in de uitnodigende organisatie. In dit geval meldt de B2B-gebruiker zich aan met een Azure AD-account dat behoort tot de uitgenodigde tenant. Als de partnerorganisatie Azure AD niet gebruikt, wordt de gastgebruiker in Azure AD nog steeds gemaakt. De vereisten zijn dat ze hun uitnodiging inwisselen en Azure AD hun e-mailadres verifieert. Deze regeling wordt ook wel een just-in-time (JIT) huur of een "virale" huur.

   > [!IMPORTANT]
   > **Vanaf 31 maart 2021**ondersteunt Microsoft niet langer het inwisselen van uitnodigingen door onbeheerde Azure AD-accounts en tenants te maken voor B2B-samenwerkingsscenario's. Ter voorbereiding moedigen we klanten aan om te kiezen voor [eenmalige wachtwoordverificatie voor e-mail.](one-time-passcode.md) We zijn blij met uw feedback over deze openbare preview-functie en zijn verheugd om nog meer manieren te creëren om samen te werken.

- Status 2: Homed in een Microsoft of een ander account en vertegenwoordigd als gastgebruiker in de hostorganisatie. In dit geval meldt de gastgebruiker zich aan met een Microsoft-account of een sociaal account (google.com of iets dergelijks). De identiteit van de uitgenodigde gebruiker wordt gemaakt als een Microsoft-account in de map van de uitnodigende organisatie tijdens het inwisselen van aanbiedingen.

- Status 3: Homed in de on-premises Active Directory van de hostorganisatie en gesynchroniseerd met het Azure AD van de hostorganisatie. U Azure AD Connect gebruiken om de partneraccounts naar de cloud te synchroniseren als Azure AD B2B-gebruikers met UserType = Gast. Zie [Toegang tot lokaal beheerde partneraccounts verlenen voor lokaal beheerde partners](hybrid-on-premises-to-cloud.md).

- Status 4: Homed in het Azure AD van de hostorganisatie met UserType = Gast en referenties die de hostorganisatie beheert.

  ![Diagram met de vier gebruikerstoestanden](media/user-properties/redemption-diagram.png)


Laten we nu eens kijken hoe een Azure AD B2B-samenwerkingsgebruiker eruit ziet in Azure AD.

### <a name="before-invitation-redemption"></a>Voor het inwisselen van uitnodigingen

Status 1- en status2-accounts zijn het resultaat van het uitnodigen van gastgebruikers om samen te werken met behulp van de eigen referenties van de gastgebruikers. Wanneer de uitnodiging in eerste instantie naar de gastgebruiker wordt verzonden, wordt er een account gemaakt in uw directory. Aan dit account zijn geen referenties gekoppeld omdat verificatie wordt uitgevoerd door de identiteitsprovider van de gastgebruiker. De eigenschap **Bron** voor het gastgebruikersaccount in uw directory is ingesteld op **Invited user**. 

![Schermafbeelding van gebruikerseigenschappen voor het inwisselen van de aanbieding](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Na het inwisselen van uitnodigingen

Nadat de gastgebruiker de uitnodiging heeft geaccepteerd, wordt de eigenschap **Bron** bijgewerkt op basis van de identiteitsprovider van de gastgebruiker.

Voor gastgebruikers in status 1 is de **bron** **Externe Azure Active Directory**.

![Status 1 gastgebruiker na inwisseling van het aanbod](media/user-properties/after-redemption-state1.png)

Voor gastgebruikers in staat 2 is de **bron** **Microsoft-account**.

![Status 2 gastgebruiker na inwisseling van aanbieding](media/user-properties/after-redemption-state2.png)

Voor gastgebruikers in staat 3 en staat 4 is de eigenschap **Source** ingesteld op **Azure Active Directory** of Windows Server Active **Directory**, zoals beschreven in de volgende sectie.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Belangrijkste eigenschappen van de Azure AD B2B-samenwerkingsgebruiker
### <a name="usertype"></a>UserType
Deze eigenschap geeft de relatie van de gebruiker met de host huurovereenkomst aan. Deze eigenschap kan twee waarden hebben:
- Lid: Deze waarde geeft een werknemer van de hostorganisatie en een gebruiker in de salarisadministratie van de organisatie aan. Deze gebruiker verwacht bijvoorbeeld toegang te hebben tot sites met alleen intern gebruik. Deze gebruiker wordt niet beschouwd als een externe medewerker.

- Gast: deze waarde geeft een gebruiker aan die niet als intern in het bedrijf wordt beschouwd, zoals een externe medewerker, partner of klant. Van zo'n gebruiker wordt niet verwacht dat hij bijvoorbeeld een interne memo van een CEO ontvangt of bedrijfsvoordelen ontvangt.

  > [!NOTE]
  > Het UserType heeft geen relatie met hoe de gebruiker zich aanmeldt, de maprol van de gebruiker, enzovoort. Deze eigenschap geeft gewoon de relatie van de gebruiker met de hostorganisatie aan en stelt de organisatie in staat om beleid af te dwingen dat afhankelijk is van deze eigenschap.

### <a name="source"></a>Bron
Deze eigenschap geeft aan hoe de gebruiker zich aanmeldt.

- Uitgenodigde gebruiker: deze gebruiker is uitgenodigd, maar heeft nog geen uitnodiging ingewisseld.

- Externe Azure Active Directory: deze gebruiker is homed in een externe organisatie en verifieert met behulp van een Azure AD-account dat behoort tot de andere organisatie. Dit type aanmelding komt overeen met staat 1.

- Microsoft-account: deze gebruiker wordt thuisgemaakt in een Microsoft-account en verifieert met een Microsoft-account. Dit type aanmelding komt overeen met staat 2.

- Windows Server Active Directory: deze gebruiker is aangemeld vanuit de on-premises Active Directory die tot deze organisatie behoort. Dit type aanmelding komt overeen met staat 3.

- Azure Active Directory: deze gebruiker verifieert met behulp van een Azure AD-account dat tot deze organisatie behoort. Dit type aanmelding komt overeen met staat 4.
  > [!NOTE]
  > Bron en UserType zijn onafhankelijke eigenschappen. Een waarde van bron impliceert geen specifieke waarde voor UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Kunnen Azure AD B2B-gebruikers worden toegevoegd als leden in plaats van als gasten?
Een Azure AD B2B-gebruiker en gastgebruiker zijn doorgaans synoniem. Daarom wordt een Azure AD B2B-samenwerkingsgebruiker standaard toegevoegd als gebruiker met UserType = Gast. In sommige gevallen is de partnerorganisatie echter lid van een grotere organisatie waartoe ook de gastorganisatie behoort. Als dit het zo is, wil de gastorganisatie gebruikers in de partnerorganisatie mogelijk behandelen als leden in plaats van als gasten. Gebruik de API's azure AD B2B Invitation Manager om een gebruiker van de partnerorganisatie toe te voegen of uit te nodigen aan de hostorganisatie als lid.

## <a name="filter-for-guest-users-in-the-directory"></a>Filteren voor gastgebruikers in de map

![Schermafbeelding van het filter voor gastgebruikers](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>UserType converteren
Het is mogelijk om UserType om te zetten van lid naar gast en vice versa met PowerShell. De eigenschap UserType vertegenwoordigt echter de relatie van de gebruiker met de organisatie. Daarom moet u deze eigenschap alleen wijzigen als de relatie van de gebruiker met de organisatie verandert. Als de relatie van de gebruiker verandert, moet de gebruikersnaam (UPN) dan veranderen? Moet de gebruiker toegang blijven houden tot dezelfde bronnen? Moet een postvak worden toegewezen? We raden u af om het UserType te wijzigen door PowerShell als atoomactiviteit te gebruiken. In het geval dat deze eigenschap onveranderlijk wordt door PowerShell te gebruiken, raden we u ook af om afhankelijk te zijn van deze waarde.

## <a name="remove-guest-user-limitations"></a>Beperkingen van gastgebruikers verwijderen
Er kunnen gevallen zijn waarin u uw gastgebruikers hogere bevoegdheden wilt geven. U een gastgebruiker aan een rol toevoegen en zelfs de standaardgebruikersbeperkingen voor gasten in de map verwijderen om een gebruiker dezelfde bevoegdheden te geven als leden.

Het is mogelijk om de standaardbeperkingen uit te schakelen, zodat een gastgebruiker in de bedrijfsmap dezelfde machtigingen heeft als een lidgebruiker.

![Schermafbeelding van de optie Externe gebruikers in de gebruikersinstellingen](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kan ik gastgebruikers zichtbaar maken in de Exchange Global Address List?
Ja. Standaard zijn gastobjecten niet zichtbaar in de algemene adreslijst van uw organisatie, maar u Azure Active Directory PowerShell gebruiken om ze zichtbaar te maken. Zie Zie **Kan ik gastobjecten zichtbaar maken in de algemene adreslijst?** in [Gasttoegang beheren in Office 365-groepen](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#add-guests-to-the-global-address-list). 

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
* [Gebruikerstokens voor B2B-samenwerking](user-token.md)
* [B2B-samenwerking gebruiker claims mapping](claims-mapping.md)
