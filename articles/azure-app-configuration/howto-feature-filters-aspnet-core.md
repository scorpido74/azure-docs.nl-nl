---
title: Functiefilters gebruiken om een functie in te schakelen voor een subset van gebruikers
titleSuffix: Azure App Configuration
description: Meer informatie over het gebruik van functiefilters om een functie in te schakelen voor een subset van gebruikers
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 181c97615985283011834dcf9145810b1563fb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056999"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Functiefilters gebruiken om een functie in te schakelen voor een subset van gebruikers

Met functievlaggen u functionaliteit in uw toepassing activeren of deactiveren. Een eenvoudige functievlag is aan of uit. De applicatie gedraagt zich altijd op dezelfde manier. U bijvoorbeeld een nieuwe functie achter een functievlag uitrollen. Wanneer de functievlag is ingeschakeld, zien alle gebruikers de nieuwe functie. Als u de functievlag uitschakelt, wordt de nieuwe functie verborgen.

Met een _voorwaardelijke functievlag_ kan de functievlag daarentegen dynamisch worden ingeschakeld of uitgeschakeld. De toepassing kan zich anders gedragen, afhankelijk van de criteria voor de functievlag. Stel dat u uw nieuwe functie eerst aan een kleine subset van gebruikers wilt weergeven. Met een voorwaardelijke functievlag u de functievlag voor sommige gebruikers inschakelen terwijl u deze voor anderen uitschakelt. _Functiefilters_ bepalen de status van de functievlag telkens wanneer deze wordt geëvalueerd.

De `Microsoft.FeatureManagement` bibliotheek bevat twee functiefilters:

- `PercentageFilter`hiermee wordt de functievlag ingeschakeld op basis van een percentage.
- `TimeWindowFilter`hiermee wordt de functievlag gedurende een bepaald tijdsvenster inschakelt.

U ook uw eigen functiefilter maken dat de [Microsoft.FeatureManagement.IFeatureFilter-interface](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)implementeert.

## <a name="registering-a-feature-filter"></a>Een functiefilter registreren

U registreert een functiefilter `AddFeatureFilter` door de methode aan te roepen en de naam van het functiefilter op te geven. De volgende code registreert `PercentageFilter`bijvoorbeeld:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Een functiefilter configureren in Azure App-configuratie

Sommige functiefilters hebben extra instellingen. `PercentageFilter` Hiermee activeert u bijvoorbeeld een functie op basis van een percentage. Het heeft een instelling die het te gebruiken percentage definieert.

U deze instellingen configureren voor functievlaggen die zijn gedefinieerd in Azure App-configuratie. Voer bijvoorbeeld de volgende `PercentageFilter` stappen uit die u wilt gebruiken om de functievlag in te schakelen voor 50% van de aanvragen voor een web-app:

1. Volg de instructies in [Quickstart: Functievlaggen toevoegen aan een ASP.NET Core-app](./quickstart-feature-flag-aspnet-core.md) om een web-app met een functievlag te maken.

1. Ga in de Azure-portal naar uw configuratiearchief en klik op **Functiebeheer**.

1. Klik op het contextmenu voor de *functievlag van de bètafunctie* die u in de snelstart hebt gemaakt. Klik op **Bewerken**.

    > [!div class="mx-imgBorder"]
    > ![Functievlag bètabewerken](./media/edit-beta-feature-flag.png)

1. Selecteer in het scherm **Bewerken** de knop **Aan-radio** als deze nog niet is geselecteerd. Klik vervolgens op de knop **Filter toevoegen.** (Het label **van** de keuzerondje wordt gewijzigd in **Voorwaardelijke**tekst .)

1. Voer **in** het veld Sleutel *Microsoft.Percentage*in .

    > [!div class="mx-imgBorder"]
    > ![Functiefilter toevoegen](./media/feature-flag-add-filter.png)

1. Klik op het contextmenu naast de filtertoets van de functie. Klik **op Parameters bewerken**.

    > [!div class="mx-imgBorder"]
    > ![Functiefilterparameters bewerken](./media/feature-flag-edit-filter-parameters.png)

1. Plaats de plaats onder de **koptekst Naam,** zodat tekstvakken in het raster worden weergegeven. Voer een *waardenaam* en een **waarde** van 50 in. **Name** Het veld **Waarde** geeft het percentage aanvragen aan waarvoor het functiefilter moet worden ingeschakeld.

    > [!div class="mx-imgBorder"]
    > ![Filterparameters voor functies instellen](./media/feature-flag-set-filter-parameters.png)

1. Klik **op Toepassen** om terug te keren naar het vlagscherm van de functie **bewerken.** Klik vervolgens nogmaals op **Toepassen** om de instellingen voor de functievlag op te slaan.

1. De **status** van de functievlag wordt nu weergegeven als *voorwaardelijk*. Deze status geeft aan dat de functievlag per aanvraag wordt ingeschakeld of uitgeschakeld op basis van de criteria die worden afgedwongen door het functiefilter.

    > [!div class="mx-imgBorder"]
    > ![Voorwaardelijke functievlag](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Functiefilters in actie

Als u de effecten van deze functievlag wilt zien, start u de toepassing en drukt u meerdere keren op de knop **Vernieuwen** in uw browser. U ziet dat het *bèta-item* ongeveer 50% van de tijd op de werkbalk wordt weergegeven. Het is de rest van de `PercentageFilter` tijd verborgen, *Beta* omdat de bètafunctie wordt gedeactiveerd voor een subset van aanvragen. De volgende video toont dit gedrag in actie.

> [!div class="mx-imgBorder"]
> ![PercentageFilter in actie](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Overzicht van functiebeheer](./concept-feature-management.md)
