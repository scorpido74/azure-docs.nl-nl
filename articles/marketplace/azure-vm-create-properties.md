---
title: Eigenschappen van de aanbieding van virtuele machines op Azure Marketplace configureren
description: Meer informatie over het configureren van de eigenschappen voor de aanbieding van virtuele machines op Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 30e5ea4df3de0b8a10c9c1f3b4b27a9e0a19359e
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491202"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>Eigenschappen van de aanbieding van virtuele machines configureren

Op de pagina **Eigenschappen** definieert u de categorieën die worden gebruikt voor het groeperen van de aanbieding van de virtuele machine (VM) op Azure Marketplace, de versie van uw toepassing en de juridische contracten die uw aanbieding ondersteunen.

## <a name="select-a-category"></a>Een categorie selecteren

Selecteer categorieën en subcategorieën om uw aanbieding te plaatsen in de juiste Azure Marketplace-Zoek gebieden. Zorg ervoor dat u beschrijft hoe uw aanbod deze categorieën ondersteunt in de beschrijving van de aanbieding. Selecteer:

- Ten minste één en Maxi maal twee categorieën, met inbegrip van een primaire en secundaire categorie (optioneel).
- Maxi maal twee subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert u **niet van toepassing**.

Bekijk de volledige lijst met categorieën en subcategorieën in [Aanbevolen procedures voor aanbiedingen](gtm-offer-listing-best-practices.md). Virtuele machine aanbiedingen worden altijd weer gegeven onder de **reken** categorie op Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Voor waarden opgeven

Geef onder **juridisch** recht voor waarden op voor uw aanbieding. U hebt hiervoor twee opties:

- [Het standaard contract gebruiken met optionele wijzigingen](#use-the-standard-contract)
- [Gebruik uw eigen voor waarden](#use-your-own-terms-and-conditions)

Zie voor meer informatie over het standaard contract en de optionele wijzigingen het [standaard contract voor de micro soft Commercial Marketplace](standard-contract.md). U kunt het [standaard](https://go.microsoft.com/fwlink/?linkid=2041178) -PDF-bestand downloaden (zorg ervoor dat de pop-upblokkering is uitgeschakeld).

### <a name="use-the-standard-contract"></a>Het standaard contract gebruiken

Micro soft biedt een standaard contract dat u voor uw aanbiedingen kunt gebruiken in de commerciële Marketplace om het aankoop proces voor klanten te vereenvoudigen en de juridische complexiteit voor software leveranciers te reduceren. Wanneer u uw software onder het Standard-contract aanbiedt, hoeven klanten slechts één keer te lezen en te accepteren en hoeft u geen aangepaste voor waarden te maken.

1. Selecteer het selectie vakje **het standaard contract voor commerciële Marketplace van micro soft gebruiken** .

   ![Illustreert het selectie vakje het standaard contract voor commerciële Marketplace van micro soft gebruiken.](partner-center-portal/media/use-standard-contract.png)
1. In het **bevestigings** dialoogvenster selecteert u **accepteren**. Mogelijk moet u omhoog schuiven om het weer te geven.
1. Selecteer **concept opslaan** voordat u doorgaat.

   > [!NOTE]
   > Nadat u een aanbieding hebt gepubliceerd met het standaard contract voor de commerciële Marketplace, kunt u uw eigen aangepaste voor waarden niet gebruiken. U kunt uw oplossing aanbieden onder het Standard-contract met optionele wijzigingen of onder uw eigen voor waarden.

### <a name="add-amendments-to-the-standard-contract-optional"></a>Wijzigingen aan het standaard contract toevoegen (optioneel)

Er zijn twee soorten wijzigingen beschikbaar: *universeel* en *aangepast*.

#### <a name="add-universal-amendment-terms"></a>Universele wijzigings voorwaarden toevoegen

Voer in de voor **waarden voor universele wijzigingen in het standaard contract voor de commerciële Marketplace van micro soft** de voor waarden in voor Universal-wijzigingen. U kunt een onbeperkt aantal tekens in dit vak invoeren. Deze voor waarden worden weer gegeven aan klanten in AppSource, Azure Marketplace en/of Azure Portal tijdens de detectie-en aankoop stroom.

#### <a name="add-one-or-more-custom-amendments"></a>Een of meer aangepaste wijzigingen toevoegen

1. Onder aanpassingen **aan het standaard contract voor de commerciële Marketplace van micro soft** , selecteert u de koppeling **aangepaste wijzigings termijn toevoegen (max. 10)** .
1. Voer in het vak **aangepaste wijzigings voorwaarden** uw wijzigings voorwaarden in.
1. Voer in het vak **Tenant-id** een Tenant-id in. Alleen klanten die zijn gekoppeld aan de Tenant-Id's die u voor deze aangepaste voor waarden opgeeft, worden weer gegeven in de inkoop stroom van de aanbieding in de Azure Portal.
   > [!TIP]
   > Een Tenant-ID identificeert uw klant in Azure. U kunt uw klant vragen voor deze id en ze kunnen deze vinden door naar [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Eigenschappen** te gaan. De waarde van de Directory-ID is de Tenant-ID (bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e` ). U kunt ook de Tenant-ID van de organisatie zoeken met behulp van de domein naam-URL op [Wat is mijn Microsoft Azure en Office 365-Tenant-id?](https://www.whatismytenantid.com/).
1. Voer desgewenst in het vak **Beschrijving** een beschrijvende beschrijving in voor de Tenant-id. Deze beschrijving helpt u bij het identificeren van de klant die u wilt richten op de wijziging.
1. Als u nog een Tenant-ID wilt toevoegen, selecteert u de koppeling **toevoegen van de Tenant-id van een klant** en herhaalt u stap 3 en 4. U kunt Maxi maal 20 Tenant-Id's toevoegen.
1. Herhaal de stappen 1 tot en met 5 om een andere wijzigings termijn toe te voegen. U kunt Maxi maal tien aangepaste voor waarden per aanbieding opgeven. 
2. Selecteer **concept opslaan** voordat u doorgaat.

### <a name="use-your-own-terms-and-conditions"></a>Gebruik uw eigen voor waarden

U kunt ervoor kiezen om uw eigen voor waarden op te geven in plaats van het standaard contract. Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

1. Zorg ervoor dat het selectie vakje **het standaard contract voor commerciële Marketplace van micro soft gebruiken** is uitgeschakeld onder **juridisch**.
1. Voer in het vak voor **waarden** maxi maal 10.000 tekens tekst in.
1. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad **aanbieding**.

## <a name="next-steps"></a>Volgende stappen

- [Vermelding voor een VM-aanbieding configureren](azure-vm-create-listing.md)
