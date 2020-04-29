---
title: Uw AppSource-pakket opslaan in azure Storage en een URL genereren met SAS-sleutel
description: Meer informatie over de stappen die nodig zijn om een AppSource-pakket te uploaden en te beveiligen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: dsindona
ms.openlocfilehash: 57bc370fd160b8b3d6d7941ea28cd460c99f3d96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285364"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Uw AppSource-pakket opslaan in azure Storage en een URL genereren met SAS-sleutel
=============================================================================

Om de beveiliging van uw bestanden te behouden, moeten alle partners hun AppSource-pakket bestand opslaan in een Azure Blob-opslag account en een SAS-sleutel gebruiken om het te delen. Het pakket bestand wordt opgehaald van uw Azure-opslag locatie voor certificering en kan worden gebruikt voor AppSource-experimenten.

Gebruik de volgende stappen om uw pakket te uploaden naar Blob Storage:

1. Ga naar <https://azure.microsoft.com> en maak een gratis proef-of gefactureerd account.

2. Meld u aan bij [Azure Portal](https://portal.azure.com/).

3. Maak een nieuw opslag account door op **+ Nieuw** te klikken en naar het gegevens-en **opslag** account te gaan.

   ![Blade gegevens en opslag op Microsoft Azure Portal](media/CRMScreenShot7.png)

4. Voer een **naam** en naam voor de **resource groep** in en klik op de knop **maken** .

   ![Opslag account maken op Microsoft Azure Portal](media/CRMScreenShot8.png)

5. Navigeer naar de zojuist gemaakte resource groep en maak een nieuwe BLOB-container.

   ![Pakket uploaden als blob met Microsoft Azure Portal](media/CRMScreenShot9.png)

6. Als u dit nog niet hebt gedaan, downloadt en installeert u de micro soft- [Azure Storage Explorer](https://storageexplorer.com/).

7. Open Storage Explorer en gebruik het pictogram om verbinding te maken met uw Azure Storage-account.

8. Ga naar de BLOB-container die u hebt gemaakt en klik op **uploaden** om uw pakket zip-bestand toe te voegen.

   ![Pakket uploaden met behulp van micro soft Storage Explorer](media/CRMScreenShot10.png)

9. Klik met de rechter muisknop op het bestand en selecteer **Shared Access Signature ophalen**.

   ![Gedeelde toegangs handtekening van een Azure-bestand ophalen](media/CRMScreenShot11.png)

10. Wijzig de **verloop tijd** zodat de SAS gedurende een maand actief is en klik vervolgens op **maken**.

    ![De verval datum van SAS van een Azure-bestand wijzigen](media/CRMScreenShot12.png)

11. Kopieer het URL-veld en sla het op voor later. Als u het gekoppelde aanbod maakt, moet u deze URL invoeren. 

    ![De SAS-URL van een Azure-bestand kopiëren](media/CRMScreenShot13.png)

