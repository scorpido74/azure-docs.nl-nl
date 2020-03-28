---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/07/2020
ms.custom: include file
ms.openlocfilehash: 9ccdc7a438d1dade534d39dc97a39a3bdae37dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895434"
---
1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer de thuiszijbalk en **+ Maak een resource**. 

   [![De thuiszijbalk uitvouwen en vervolgens een resource maken](./media/create-digital-twins-portal/azure-portal-create-a-resource.png)](./media/create-digital-twins-portal/azure-portal-create-a-resource.png#lightbox)

1. Zoek naar **Digital Twins**en selecteer **Digital Twins**. 

   [![Selecties voor het maken van een nieuw Digital Twins-exemplaar](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png)](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png#lightbox)

   U ook **Internet of Things**selecteren en Digital Twins **(voorbeeld)** selecteren.

1. Selecteer **Maken** om het implementatieproces te starten.

   [![De implementatie van de resource maken en bevestigen](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png)](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png#lightbox)

1. In het deelvenster **Digital Twins** voert u de volgende informatie in:
   * **Resourcenaam**: geef uw instantie van Digital Twins een unieke naam.
   * **Abonnement**: kies het abonnement dat u wilt gebruiken om deze instantie van Digital Twins te maken. 
   * **Resourcegroep**: selecteer of maak een [resourcegroep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) voor de instantie van Digital Twins.
   * **Locatie**: selecteer de locatie die het dichtst bij uw apparaten in de buurt is.

     [![Digital Twins-deelvenster met ingevoerde gegevens](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Controleer uw Digital Twins-gegevens en selecteer **Maken**. Het kan een paar minuten duren voordat uw instantie van Digital Twins is gemaakt. U kunt de voortgang bewaken via het deelvenster **Meldingen**.

1. Open het deelvenster **Overzicht** van de instantie van Digital Twins. Zoals u ziet, wordt er een koppeling weergegeven onder **Beheer API**. De URL **van de Beheer-API** wordt opgemaakt als: 
   
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger
   ```
   
   Deze URL leidt u naar de documentatie van de REST API van Azure Digital Twins, die van toepassing is op uw instantie. Zie [Het gebruik van Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md) voor informatie over hoe u deze API-documentatie dient te lezen en gebruiken. Kopieer en wijzig de URL van de **Beheer-API** naar deze indeling: 
    
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/
   ```
    
   Uw toepassing gebruikt de aangepaste URL als de basis-URL voor toegang tot uw instantie. Kopieer deze gewijzigde URL naar een tijdelijk bestand. U hebt deze URL nodig in de volgende sectie.

   [![Overzicht van beheer-API](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)