---
title: SKU's voor een Azure Containers-afbeelding | Azure Marketplace
description: SKU's configureren voor een Azure-container.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: adbe3d4c498c8f4e4968ca903f78c34aedca9a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279994"
---
# <a name="container-skus-tab"></a>Tabblad SKU's voor containers

Op het tabblad **SKU's** van de pagina **Nieuwe aanbieding** u een of meer SKU's maken en deze koppelen aan uw nieuwe aanbieding.  U verschillende SKU's gebruiken om een oplossing te onderscheiden op functiesets, factureringsmodellen of andere kenmerken.

## <a name="sku-settings"></a>SKU-instellingen

Wanneer u een nieuwe aanbieding maakt, zijn er geen SKU's gekoppeld aan de aanbieding. Voer de volgende stappen uit om een nieuwe SKU te maken:

1. Selecteer op het tabblad SKU's de optie **Nieuwe SKU**

   ![Nieuwe SKU-prompt](./media/containers-sku-settings.png)

2. Geef de vereiste SKU- en containerinformatie. Elke SKU komt overeen met een containerafbeelding. Er zijn twee delen aan een SKU:

    -   SKU-metagegevens
    -   Metagegevens van de container


### <a name="sku-metadata"></a>SKU-metagegevens

De SKU-metagegevens bevatten weergavegegevens voor de containervermelding.

![SKU-metagegevens](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metagegevens van de container

De containermetagegevens bevatten referentiegegevens van uw afbeeldingsarchiefgegevens in Azure Container Registry (ACR). Azure Marketplace kopieert deze afbeelding naar een Marketplace-specifiek openbaar register en maakt de afbeelding na certificering beschikbaar voor klanten. Alle aanvragen van de Azure-gebruiker om een Azure Marketplace-containerafbeelding te gebruiken, worden weergegeven vanuit het openbare register van de Marketplace, niet acr.

![Metagegevens van de container](./media/containers-image-repository.png)
    
De **afbeeldingsarchiefdetails** in de vorige schermopname bevatten de volgende velden.  Vereiste velden worden aangeklaagd door een sterretje (*).

-   **Abonnements-ID\* ** - De Azure-abonnements-ID waar de ACR aanwezig is.
-   **Naam resourcegroep\* ** - De naam van de resourcegroep van de ACR.
-   **Registernaam\* ** - De ACR-naam.
-   **Naam van\* de opslagplaats** - De naam van de opslagplaats. Nadat deze naam is ingesteld, kan deze waarde niet worden gewijzigd. Gebruik een unieke naam om een conflict met andere aanbiedingen in uw account te voorkomen.
-   **Gebruikersnaam\* ** - De gebruikersnaam (gebruikersnaam van de beheerder) die is gekoppeld aan de ACR-afbeelding.
-   **Wachtwoord\* ** - Het wachtwoord dat is gekoppeld aan de ACR-afbeelding.

    >[!NOTE]
    >De gebruikersnaam en het wachtwoord zijn vereist om ervoor te zorgen dat partners toegang hebben tot de ACR die in het publicatieproces wordt genoemd.


### <a name="image-version"></a>Installatiekopieversie

Wanneer u een containerafbeelding publiceert, u een of meer afbeeldingstags en SHA-samenvattingen verstrekken.

**Afbeeldingstag\* of digest**
 
- Deze tag of digest `latest` moet een tag en een `xx.xx.xx-` versietag bevatten (bijvoorbeeld te beginnen met waar xx een getal is). Ze moeten [manifesttags](https://github.com/estesp/manifest-tool) zijn om meerdere platforms te targeten. Alle tags waarnaar wordt verwezen door een manifesttag moeten ook worden toegevoegd, zodat we ze kunnen uploaden. 
- U verschillende versies van de container toevoegen met behulp van tags. Alle manifesttags `latest`(behalve) moeten `X.Y-` `X.Y.Z-` beginnen met een of wanneer X, Y, Z gehele getallen zijn. <br/> `latest` Als een tag bijvoorbeeld `1.0.1-linux-x64`verwijst `1.0.1-linux-arm32`naar `1.0.1-windows-arm32`, en deze tags hier moet worden toegevoegd.

>[!NOTE]
>Vergeet niet om een **testtag** aan uw afbeelding toe te voegen, zodat u de afbeelding tijdens het testen identificeren.


## <a name="next-steps"></a>Volgende stappen

Gebruik het [tabblad Marketplace](./cpp-marketplace-tab.md) om een marktplaatsbeschrijving voor uw aanbieding te maken. 
