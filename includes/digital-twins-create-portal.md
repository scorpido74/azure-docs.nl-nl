---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/24/2019
ms.custom: include file
ms.openlocfilehash: 92b9a4754769566feb3658e07081e9fdae78fcfc
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903875"
---
1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Selecteer op de start pagina **+ een resource maken**. Zoek naar **Digital apparaatdubbels**en selecteer **Digital apparaatdubbels**. Selecteer **Maken** om het implementatieproces te starten.

   [![selecties voor het maken van een nieuw digitaal Apparaatdubbels-exemplaar](./media/create-digital-twins-portal/create-digital-twins.png)](./media/create-digital-twins-portal/create-digital-twins.png#lightbox)

1. In het deelvenster **Digital Twins** voert u de volgende informatie in:
   * **Resourcenaam**: geef uw instantie van Digital Twins een unieke naam.
   * **Abonnement**: kies het abonnement dat u wilt gebruiken om deze instantie van Digital Twins te maken. 
   * **Resourcegroep**: selecteer of maak een [resourcegroep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) voor de instantie van Digital Twins.
   * **Locatie**: selecteer de locatie die het dichtst bij uw apparaten in de buurt is.

     [![deel venster Digital Apparaatdubbels met ingevoerde gegevens](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Controleer uw Digital Twins-gegevens en selecteer **Maken**. Het kan een paar minuten duren voordat uw instantie van Digital Twins is gemaakt. U kunt de voortgang bewaken via het deelvenster **Meldingen**.

1. Open het deelvenster **Overzicht** van de instantie van Digital Twins. Zoals u ziet, wordt er een koppeling weergegeven onder **Beheer API**.

   De indeling van de URL van de **Beheer API** is `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. Deze URL leidt u naar de documentatie van de REST API van Azure Digital Twins, die van toepassing is op uw instantie. Zie [Het gebruik van Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md) voor informatie over hoe u deze API-documentatie dient te lezen en gebruiken.

    Kopieer en wijzig de **API** -URL voor beheer in deze indeling: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Uw toepassing gebruikt de aangepaste URL als de basis-URL voor toegang tot uw instantie. Kopieer deze gewijzigde URL naar een tijdelijk bestand. U hebt deze URL nodig in de volgende sectie.

    [![-beheer-API](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)