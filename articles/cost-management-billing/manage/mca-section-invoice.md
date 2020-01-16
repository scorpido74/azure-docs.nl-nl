---
title: Uw factuur indelen op basis van uw behoeften-Azure
description: Meer informatie over hoe u de kosten op uw factuur kunt indelen.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c170a6a1a731a648c16e0081e760947256df8a0e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991059"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Kosten indelen door uw facturerings account aan te passen

Uw facturerings account voor micro soft Customer Agreement biedt u de flexibiliteit om uw kosten te organiseren op basis van uw behoeften op het gebied van de afdeling, het project of de ontwikkelings omgeving. 

In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om uw kosten te organiseren. Dit is van toepassing op een facturerings account voor een klant overeenkomst van micro soft. [Controleer of u toegang hebt tot een Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement).

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Uw account structureren met facturerings profielen en factuur secties

In het facturerings account voor een micro soft-klant overeenkomst gebruikt u facturerings profielen en factuur secties om uw kosten te organiseren.

![Scherm opname van de micro soft-facturerings hiërarchie van de klant overeenkomst](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Factureringsprofiel

Een facturerings profiel vertegenwoordigt een factuur en de gerelateerde facturerings gegevens, zoals betalings wijzen en facturerings adres. Aan het begin van de maand wordt voor elk factureringsprofiel in uw account een maandelijkse factuur gegenereerd. De factuur bevat kosten voor Azure-gebruik en andere aankopen van de vorige maand.

Er wordt automatisch een facturerings profiel gemaakt samen met uw facturerings account wanneer u zich aanmeldt voor Azure. U kunt extra facturerings profielen maken om uw kosten in meerdere maandelijkse facturen te organiseren. 

> [!IMPORTANT]
>
> Het maken van aanvullende facturerings profielen kan van invloed zijn op uw totale kosten. Zie overwegingen bij [het toevoegen van nieuwe facturerings profielen](#things-to-consider-when-adding-new-billing-profiles)voor meer informatie.

### <a name="invoice-section"></a>Factuursectie

Een gedeelte van de factuur bevat een groepering van de kosten in uw factuur. Er wordt automatisch een factuur sectie gemaakt voor elk facturerings profiel in uw account. U kunt extra secties maken om uw kosten te organiseren op basis van uw behoeften. Elk factuur gedeelte wordt op de factuur weer gegeven met de kosten die zijn gemaakt voor de maand. 

In de onderstaande afbeelding ziet u een factuur met twee factuur secties: engineering en marketing. De overzichts-en detail kosten voor elke sectie worden in de factuur weer gegeven. De prijzen die in de afbeelding worden weer gegeven, zijn alleen bedoeld als voor beeld en vertegenwoordigen niet de werkelijke prijzen van Azure-Services. 

![Afbeelding met een factuur met secties](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Structuur van het facturerings account voor algemene scenario's

In deze sectie worden algemene scenario's beschreven voor het organiseren van kosten en de bijbehorende facturerings account structuren:

|Scenario  |Structuur  |
|---------|---------|
|Meldt zich aan voor Azure en heeft één maandelijkse factuur nodig. | Een facturerings profiel en een factuur gedeelte. Deze structuur wordt automatisch ingesteld voor Jack wanneer hij zich aanmeldt voor Azure en geen extra stappen vereist. |

![Info afbeelding voor een eenvoudig facturerings scenario](./media/mca-section-invoice/organize-billing-scenario1.png)

|Scenario  |Structuur  |
|---------|---------|
|Contoso is een kleine organisatie die een enkele maandelijkse factuur nodig heeft, maar groeps kosten door hun afdelingen: marketing en Financiën.  | Een facturerings profiel voor contoso en een factuur gedeelte voor marketing-en financierings afdelingen. |

![Info afbeelding voor een eenvoudig facturerings scenario](./media/mca-section-invoice/organize-billing-scenario2.png)

|Scenario  |Structuur  |
|---------|---------|
|Fabrikam is een middel grote organisatie waarvoor facturen moeten worden gescheiden voor hun technische en marketing afdelingen. Voor technische afdeling willen ze kosten groeperen per omgeving-productie en ontwikkeling.  | Elk facturerings profiel voor marketing-en financiële afdelingen. Voor marketing afdeling, een factuur sectie voor productie-en ontwikkelings omgeving. |

![Info afbeelding voor een eenvoudig facturerings scenario](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Een nieuwe factuur sectie maken

Als u een factuursectie wilt maken, moet u een **factureringsprofiel-eigenaar** of een **factureringsprofiel-inzender** zijn. Zie voor meer informatie [factuur secties beheren voor het facturerings profiel](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van de zoekopdracht naar kostenbeheer en facturering in de Azure-portal](./media/mca-section-invoice/search-cmb.png)

3. Selecteer **facturerings profielen** in het linkerdeel venster. Selecteer een facturerings profiel in de lijst. De nieuwe sectie wordt weer gegeven op de factuur van het geselecteerde factuur profiel. 

   [Scherm opname van ![die de lijst met facturerings profielen bevat](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Selecteer **factuur secties** in het linkerdeel venster en selecteer vervolgens **toevoegen** boven aan de pagina.

   [Scherm afbeelding van ![waarop factuur secties worden toegevoegd](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Voer een naam in voor de factuur sectie. 

   [pagina voor het maken van de factuur sectie ![scherm afbeelding](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Selecteer **Maken**.

## <a name="create-a-new-billing-profile"></a>Een nieuw facturerings profiel maken

Als u een facturerings profiel wilt maken, moet u **eigenaar van het facturerings account** of een **facturerings account**zijn. Zie [facturerings profielen voor facturerings accounts beheren](understand-mca-roles.md#manage-billing-profiles-for-billing-account)voor meer informatie.

> [!IMPORTANT]
>
> Het maken van aanvullende facturerings profielen kan van invloed zijn op uw totale kosten. Zie overwegingen bij [het toevoegen van nieuwe facturerings profielen](#things-to-consider-when-adding-new-billing-profiles)voor meer informatie.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van de zoekopdracht naar kostenbeheer en facturering in de Azure-portal](./media/mca-section-invoice/search-cmb.png)

3. Selecteer **facturerings profielen** in het linkerdeel venster en selecteer vervolgens **toevoegen** boven aan de pagina.

   [Scherm opname van ![die de lijst met facturerings profielen bevat](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Als u de knop toevoegen niet ziet op de pagina voor het facturerings profiel, is de functie niet beschikbaar voor uw account. Het is momenteel alleen beschikbaar voor accounts die zijn ingesteld tijdens het werken met een micro soft-vertegenwoordiger.

4. Vul het formulier in en klik op **Maken**.

   [Scherm opname van ![die de pagina voor het maken van een facturerings profiel toont](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Veld  |Definitie  |
    |---------|---------|
    |Name     | Een weergave naam waarmee u het facturerings profiel gemakkelijk kunt herkennen in de Azure Portal.  |
    |IO-nummer    | Een optioneel inkooporder nummer. Het io-nummer wordt weer gegeven op de facturen die voor het facturerings profiel zijn gegenereerd. |
    |Factuuradres   | Het factuur adres wordt weer gegeven op de facturen die voor het facturerings profiel zijn gegenereerd. |
    |E-mail factuur   | Schakel het selectie vakje e-mail factureren in om de facturen voor dit facturerings profiel per e-mail te ontvangen. Als u zich niet aanmeldt, kunt u de facturen in het Azure Portal bekijken en downloaden.|

5. Selecteer **Maken**.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Kosten koppelen aan factuur secties en facturerings profielen

Zodra u uw facturerings account hebt aangepast op basis van uw behoeften, kunt u abonnementen en andere producten koppelen aan uw gewenste factuur sectie en facturerings profiel.

### <a name="link-a-new-subscription"></a>Een nieuw abonnement koppelen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Zoek naar **Abonnementen**.

   [Scherm afbeelding van ![waarin de zoek opdracht in de portal voor het abonnement wordt weer gegeven](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. Selecteer **Toevoegen** bovenaan de pagina.

   ![Schermopname van de knop Toevoegen in de weergave Abonnementen](./media/mca-section-invoice/subscription-add.png)

4. Als u toegang hebt tot meerdere facturerings accounts, selecteert u uw micro soft-account voor klant overeenkomst.

   ![Schermopname van de knop Toevoegen in de weergave Abonnementen](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Selecteer het facturerings profiel dat wordt gefactureerd voor het gebruik van het abonnement. De kosten voor Azure-gebruik en andere aankopen voor dit abonnement worden in rekening gebracht op de factuur van het geselecteerde facturerings profiel.

6. Selecteer de sectie factuur om de kosten van het abonnement te koppelen. De kosten worden weer gegeven in deze sectie op de factuur van het facturerings profiel.

7. Selecteer een Azure-abonnement en voer een beschrijvende naam in voor uw abonnement. 

9. Klik op **Maken**.  

### <a name="link-existing-subscriptions-and-products"></a>Bestaande abonnementen en producten koppelen

Als u beschikt over bestaande Azure-abonnementen of andere producten, zoals Azure Marketplace en bron bronnen voor apps, kunt u ze verplaatsen van hun bestaande factuur gedeelte naar een ander factuur gedeelte om uw kosten opnieuw in te delen. 

> [!IMPORTANT]
>
> Abonnementen en andere producten kunnen alleen worden verplaatst tussen de factuur gedeelten die deel uitmaken van hetzelfde facturerings profiel. Het verplaatsen van abonnementen en producten over factuur secties in verschillende facturerings profielen wordt niet ondersteund.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van zoekopdracht in portal naar abonnementen](./media/mca-section-invoice/search-cmb.png)

3. Als u een abonnement wilt koppelen aan een nieuwe factuur sectie, selecteert u **Azure-abonnementen** aan de linkerkant van het scherm. Selecteer **terugkerende kosten**voor andere producten, zoals Azure Marketplace en bron bronnen voor apps.

   [![scherm opname met de optie voor het wijzigen van de factuur sectie](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. Klik op de pagina op het weglatings teken (drie punten) voor het abonnement of product dat u wilt koppelen aan een nieuwe factuur sectie. Selecteer **factuur gedeelte wijzigen**.

5. Selecteer de sectie nieuwe factuur in de vervolg keuzelijst. In de vervolg keuzelijst worden alleen factuur secties weer gegeven die zijn gekoppeld aan hetzelfde facturerings profiel als de bestaande factuur sectie.

    [![scherm opname van het selecteren van een nieuwe factuur sectie](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Selecteer **Opslaan**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Aandachtspunten bij het toevoegen van nieuwe facturerings profielen

### <a name="azure-usage-charges-may-be-impacted"></a>Gebruiks kosten voor Azure kunnen worden beïnvloed

In uw facturerings account voor een klant overeenkomst van micro soft wordt het Azure-gebruik maandelijks geaggregeerd voor elk facturerings profiel. De prijzen voor Azure-resources met gelaagde prijzen worden bepaald op basis van het gebruik voor elk facturerings profiel afzonderlijk. Het gebruik is niet samengevoegd over de facturerings profielen bij het berekenen van de prijs. Dit kan invloed hebben op de totale kosten van Azure-gebruik voor accounts met meerdere facturerings profielen.

Laten we eens kijken naar een voor beeld van hoe de kosten voor twee scenario's verschillen. De prijzen die in de scenario's worden gebruikt, zijn alleen bedoeld als voor beeld en vertegenwoordigen niet de werkelijke prijzen van Azure-Services.

#### <a name="you-only-have-one-billing-profile"></a>U hebt slechts één facturerings profiel.

We gaan ervan uit dat u Azure Block Blob Storage gebruikt. de kosten zijn EUR. 00184 per GB voor de eerste 50 terabyte (TB) en vervolgens. 00177 per GB voor de volgende 450 terabytes (TB). U hebt 100 TB gebruikt in de abonnementen die aan uw facturerings profiel worden gefactureerd. hier ziet u hoeveel u in rekening brengt.

|  Prijs categorie (USD) |Hoeveelheid | Bedrag (USD)|
|---------|---------|---------|
|1,84 per TB voor de eerste 50 TB/maand    | 50 TB        | 92,0   |
|1,77 per TB voor de volgende 450 TB/maand    |  50 TB         | 88,5   |
|Totaal     |     100 TB  | 180,5

De totale kosten voor het gebruik van 100 TB aan gegevens in dit scenario zijn **180,5**

#### <a name="you-have-multiple-billing-profiles"></a>U hebt meerdere facturerings profielen.

We gaan ervan uit dat u een ander facturerings profiel hebt gemaakt en 50 GB gebruikt via abonnementen die worden gefactureerd aan het eerste facturerings profiel en 50 GB via abonnementen die worden gefactureerd voor het tweede facturerings profiel. Dit is de hoeveelheid die u in rekening brengt.

`Charges for the first billing profile`

|  Prijs categorie (USD) |Hoeveelheid | Bedrag (USD)|
|---------|---------|---------|
|1,84 per TB voor de eerste 50 TB/maand    | 50 TB        | 92,0  |
|1,77 per TB voor de volgende 450 TB/maand    |  0 TB         | 0,0  |
|Totaal     |     50 TB  | 92,0 

`Charges for the second billing profile`

|  Prijs categorie (USD) |Hoeveelheid | Bedrag (USD)|
|---------|---------|---------|
|1,84 per TB voor de eerste 50 TB/maand    | 50 TB        | 92,0  |
|1,77 per TB voor de volgende 450 TB/maand    |  0 TB         | 0,0  |
|Totaal     |     50 TB  | 92,0 

De totale kosten voor het gebruik van 100 TB aan gegevens in dit scenario is **184,0** (92,0 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Voor delen van Azure-reserve ring zijn mogelijk niet van toepassing op alle abonnementen

Azure-reserve ringen met een gedeeld bereik worden toegepast op abonnementen in één facturerings profiel en worden niet gedeeld in facturerings profielen. 

![Info afbeelding voor de reserverings toepassing voor de verschillende structuur van de facturerings account](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

In de bovenstaande afbeelding heeft Contoso twee abonnementen. De voor delen van Azure-reserve ring worden anders toegepast, afhankelijk van de manier waarop de facturerings account is gestructureerd. In het scenario aan de linkerkant wordt het voor deel van de reserve ring toegepast op beide abonnementen die worden gefactureerd aan het technisch facturerings profiel. In het scenario aan de rechter kant wordt het voor deel van de reserve ring alleen toegepast op abonnement 1 omdat dit het enige abonnement is dat wordt gefactureerd aan het technisch facturerings profiel. 

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u hulp nodig hebt, kunt u [contact opnemen met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

- [Een extra Azure-abonnement voor Microsoft-klantovereenkomst maken](create-subscription.md)
- [Factureringsrollen beheren in de Azure-portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Factureringseigenaar worden van Azure-abonnementen van andere gebruikers in andere factureringsaccounts](mca-request-billing-ownership.md)
