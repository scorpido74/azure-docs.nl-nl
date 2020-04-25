---
title: Sku's voor een installatie kopie van Azure containers | Azure Marketplace
description: Sku's configureren voor een Azure-container.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: 5f37d157e11b8cd7cf093fa558e81d9a1ce345a9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146192"
---
# <a name="container-skus-tab"></a>Het tabblad container Sku's

> [!IMPORTANT]
> Vanaf 13 april 2020 gaan we het beheer van uw Azure-container aanbiedingen naar het partner centrum verplaatsen. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in [een Azure-container aanbieding maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) om uw gemigreerde aanbiedingen te beheren.

Op het tabblad **sku's** van de pagina **nieuwe aanbieding** kunt u een of meer sku's maken en deze koppelen aan uw nieuwe aanbieding.  U kunt verschillende Sku's gebruiken om een oplossing te onderscheiden van functie sets, facturerings modellen of andere kenmerken.

## <a name="sku-settings"></a>SKU-instellingen

Wanneer u begint met het maken van een nieuwe aanbieding, zijn er geen Sku's gekoppeld aan de aanbieding. Voer de volgende stappen uit om een nieuwe SKU te maken:

1. Op het tabblad Sku's selecteert u **nieuwe SKU**

   ![Nieuwe SKU-prompt](./media/containers-sku-settings.png)

2. Geef de vereiste SKU-en container informatie op. Elke SKU komt overeen met een container installatie kopie. Er zijn twee onderdelen voor een SKU:

    -   SKU-meta gegevens
    -   Metagegevens van de container


### <a name="sku-metadata"></a>SKU-meta gegevens

De SKU-meta gegevens bevatten informatie over het weer geven van de container.

![SKU-meta gegevens](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metagegevens van de container

De meta gegevens van de container bevatten referentie gegevens van de opslag plaats Details van de installatie kopie in Azure Container Registry (ACR). Azure Marketplace kopieert deze installatie kopie naar een openbaar REGI ster dat specifiek is voor de Marketplace en maakt de installatie kopie na certificering beschikbaar voor klanten. Alle aanvragen van de Azure-gebruiker om een Azure Marketplace-container installatie kopie te gebruiken, worden aangeboden vanuit het open bare REGI ster van de Marketplace, niet ACR.

![Metagegevens van de container](./media/containers-image-repository.png)
    
De **Details van de installatie kopie opslagplaats** in de vorige scherm opname bevatten de volgende velden.  Vereiste velden worden gedicteerd door een asterisk (*).

-   **Abonnements-\* id** : de id van het Azure-abonnement waarin de ACR aanwezig is.
-   **\* Naam van resource groep** : de naam van de resource groep van de ACR.
-   **Register naam\* ** : de naam van de ACR.
-   **\* Naam van opslag plaats** : de naam van de opslag plaats. Wanneer deze naam is ingesteld, kan deze waarde niet worden gewijzigd. Gebruik een unieke naam om een conflict met andere aanbiedingen in uw account te voor komen.
-   **Gebruikers\* naam** : de gebruikers naam (beheerder-gebruikers naam) die is gekoppeld aan de ACR-installatie kopie.
-   **Wacht\* woord** : het wacht woord dat is gekoppeld aan de ACR-installatie kopie.

    >[!NOTE]
    >De gebruikers naam en het wacht woord zijn vereist om ervoor te zorgen dat partners toegang hebben tot de ACR die worden vermeld in het publicatie proces.


### <a name="image-version"></a>Installatiekopieversie

Wanneer u een container installatie kopie publiceert, kunt u een of meer afbeeldings Tags en SHA-samen vattingen opgeven.

**Afbeeldings\* code of Digest**
 
- Deze tag of samen vatting moet een `latest` tag en een versie label bevatten (bijvoorbeeld vanaf `xx.xx.xx-` waarbij XX een getal is). Ze moeten [manifest Tags](https://github.com/estesp/manifest-tool) zijn voor het richten op meerdere platforms. Alle labels waarnaar wordt verwezen door een manifest-tag, moeten ook worden toegevoegd, zodat ze kunnen worden geüpload. 
- U kunt verschillende versies van de container met behulp van Tags toevoegen. Alle manifest Tags (behalve `latest`) moeten beginnen met of `X.Y-` `X.Y.Z-` met X, Y en Z een geheel getal zijn. <br/> Als een `latest` tag bijvoorbeeld verwijst naar `1.0.1-linux-x64`, `1.0.1-linux-arm32`en `1.0.1-windows-arm32`, moeten deze tags hier worden toegevoegd.

>[!NOTE]
>Vergeet niet om een **test code** toe te voegen aan uw installatie kopie, zodat u de afbeelding tijdens het testen kunt identificeren.


## <a name="next-steps"></a>Volgende stappen

Gebruik het [tabblad Marketplace](./cpp-marketplace-tab.md) om een beschrijving voor de Marketplace voor uw aanbieding te maken. 
