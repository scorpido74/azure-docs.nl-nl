---
title: 'Snelstartgids: interactieve kaarten zoeken met Azure Maps | Microsoft Azure kaarten'
description: Meer informatie over het maken van een demo-webtoepassing voor het zoeken in interactieve mappen met behulp van Microsoft Azure Maps Web SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 1/14/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 4081dd383fc0e7378bc8c8438781c13e6a34e075
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156434"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>Snelstartgids: een interactieve Zoek toewijzing maken met behulp van Azure Maps

In dit artikel wordt gedemonstreerd hoe u een kaart maakt met Azure Maps om gebruikers een interactieve zoekervaring te bieden. U wordt door de volgende basisstappen geleid:
* Uw eigen Azure Maps-account maken.
* Uw accountsleutel ophalen om te gebruiken in de demo-webtoepassing.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Een account van Azure Maps maken

Voer de volgende stappen uit om een nieuw Maps-account te maken:

1. Klik in de linkerbovenhoek van [Azure Portal](https://portal.azure.com) op **Een resource maken**.
2. Typ **Maps** in het vak *Marketplace doorzoeken*.
3. Selecteer **Toewijzingen** in de *Resultaten*. Klik op de knop **Maken** die onder de kaart wordt weergegeven.
4. Voer de volgende waarden in op de pagina **Azure Kaarten-account maken**:
    * Het *Abonnement* dat u wilt gebruiken voor dit account.
    * De naam van de *Resourcegroep* voor dit account. U kunt kiezen om een *Nieuwe* of *Bestaande* resourcegroep te gebruiken.
    * De *Naam* van uw nieuwe account.
    * De *prijs categorie* voor dit account.
    * Lees de *licentie* en de *privacyverklaring*, en schakel het selectievakje in om de voorwaarden te accepteren.
    * Klik op de knop **Maken**.

![Maps-account maken in de portal](./media/quick-demo-map-app/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>De primaire sleutel voor uw account ophalen

Als het Azure Kaarten-account is gemaakt, haalt u de sleutel op waarmee u query's kunt uitvoeren op de API's van kaarten. U kunt het beste de primaire sleutel van uw account gebruiken als abonnements sleutel bij het aanroepen van Azure Maps Services.

1. Open uw Maps-account in de portal.
2. Selecteer in de sectie instellingen de optie **verificatie**.
3. Kopieer de **Primaire Sleutel** naar het Klembord. Sla de sleutel lokaal op voor gebruik verderop in deze zelfstudie.

![Azure Maps sleutel voor primaire sleutel in Azure Portal ophalen](./media/quick-demo-map-app/get-key.png)

## <a name="download-the-application"></a>De toepassing downloaden

1. Ga naar [interactiveSearch. html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) en klik hierop om de inhoud van de GitHub-gebruikers interface weer te geven. Klik met de rechter muisknop op de **onbewerkte** knop en kopieer de inhoud van het bestand of ' opslaan als ' om het bestand te downloaden.
2. Sla de inhoud van dit bestand lokaal op als **AzureMapDemo.html**. Open het in een teksteditor.
3. Zoek naar de tekenreeks `<Your Azure Maps Key>`. Vervang deze door de waarde van de **primaire sleutel** uit de voorgaande sectie.

## <a name="open-the-application"></a>De toepassing openen

1. Open het bestand **AzureMapDemo.html** in een browser naar keuze.
2. Bekijk de kaart van Los Angeles. Zoom in en uit om te zien hoe de kaart automatisch met meer of minder informatie wordt weergegeven, afhankelijk van het zoomniveau. 
3. Wijzig de standaardinstelling voor het midden van de kaart. Zoek in het bestand **AzureMapDemo.html** naar de variabele **center**. Vervang de lengte- en breedtegraad voor deze variabele door deze nieuwe waarden: **[-74.0060, 40.7128]** . Sla het bestand op en vernieuw de browser.
4. Probeer de interactieve zoekervaring uit. Zoek in het zoekvak linksboven in de demo-webtoepassing naar **restaurants**.
5. Beweeg de muis over de lijst met adressen en locaties die worden weergegeven onder het zoekvak. U ziet hoe bij de bijbehorende speld op de kaart een pop-upvenster met informatie over die locatie wordt weergegeven. Ten behoeve van de privacy van particuliere bedrijven worden er fictieve namen en adressen weergegeven.

    ![Interactieve map zoeken webtoepassing](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Resources opschonen

In de zelfstudies vindt u gedetailleerde informatie over het gebruik en de configuratie van Azure Maps met uw account. Als u wilt doorgaan met de zelfstudies, verwijder de resources die u in deze quickstart hebt gemaakt dan niet. Als u niet van plan bent om door te gaan, voert u deze stappen uit voor het opschonen van de resources:

1. Sluit de browser waarin de **AzureMapDemo.html**-webtoepassing wordt uitgevoerd.
2. Selecteer in het linker menu van de Azure-portal de optie **Alle resources**. Selecteer vervolgens uw Azure Maps-account. Selecteer bovenaan de blade **Alle resources** de optie **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure Maps-account gemaakt en een demo-toepassing gemaakt. Bekijk de volgende zelf studies voor meer informatie over Azure Maps:

> [!div class="nextstepaction"]
> [Zoeken naar nuttige plaatsen in de buurt met behulp van Azure Maps](tutorial-search-location.md)

Bekijk de volgende handleidingen voor meer codevoorbeelden en een interactieve coderingservaring:

> [!div class="nextstepaction"]
> [Een adres vinden met behulp van de Azure Maps-zoekservice](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Azure Maps Map Control gebruiken](how-to-use-map-control.md)
