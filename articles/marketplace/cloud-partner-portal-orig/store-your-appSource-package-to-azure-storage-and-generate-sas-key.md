---
title: Uw AppSource-pakket opslaan in Azure-opslag en een URL genereren met SAS-sleutel
description: Geeft de stappen die nodig zijn om een AppSource-pakket te uploaden en te beveiligen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: dsindona
ms.openlocfilehash: 57bc370fd160b8b3d6d7941ea28cd460c99f3d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285364"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Uw AppSource-pakket opslaan in Azure-opslag en een URL genereren met SAS-sleutel
=============================================================================

Om de beveiliging van uw bestanden te behouden, moeten alle partners hun AppSource-pakketbestand opslaan in een Azure blob-opslagaccount en een SAS-sleutel gebruiken om het te delen. We halen het pakketbestand op van uw Azure-opslaglocatie voor certificering en gebruiken het voor AppSource-proefversies.

Gebruik de volgende stappen om uw pakket te uploaden naar blob-opslag:

1. Ga <https://azure.microsoft.com> naar en maak een gratis proefversie of gefactureerd account aan.

2. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

3. Maak een nieuw opslagaccount aan door op **+ Nieuw** te klikken en naar het data **+ opslagaccount** te gaan.

   ![Gegevens + opslagblad op Microsoft Azure Portal](media/CRMScreenShot7.png)

4. Voer **een** naam en **naam van de brongroep** in en klik op **Knop Maken.**

   ![Opslagaccount maken op Microsoft Azure Portal](media/CRMScreenShot8.png)

5. Navigeer naar de nieuw gemaakte resourcegroep en maak een nieuwe blobcontainer.

   ![Pakket uploaden als blob met Microsoft Azure Portal](media/CRMScreenShot9.png)

6. Als u dit nog niet hebt gedaan, downloadt en installeert u de Microsoft [Azure Storage Explorer.](https://storageexplorer.com/)

7. Open Storage Explorer en gebruik het pictogram om verbinding te maken met uw Azure-opslagaccount.

8. Navigeer naar de blobcontainer die u hebt gemaakt en klik op **Uploaden** om het zip-bestand van uw pakket toe te voegen.

   ![Pakket uploaden met Microsoft Storage Explorer](media/CRMScreenShot10.png)

9. Klik met de rechtermuisknop op het bestand en selecteer **Gedeelde toegangshandtekening downloaden**.

   ![Gedeelde toegangshandtekening van een Azure-bestand downloaden](media/CRMScreenShot11.png)

10. Wijzig de **vervaldatum** om de SAS een maand actief te maken en klik op **Maken**.

    ![De SAS-vervaldatum van een Azure-bestand wijzigen](media/CRMScreenShot12.png)

11. Kopieer het URL-veld en sla het op voor later. U moet deze URL invoeren wanneer u de bijbehorende aanbieding maakt. 

    ![De SAS-URL van een Azure-bestand kopiëren](media/CRMScreenShot13.png)

