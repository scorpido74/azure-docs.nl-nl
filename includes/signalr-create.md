---
title: bestand opnemen
description: bestand opnemen
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 39bff26baea622e6c0ed524ca68c3c8bae4e770d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76020967"
---
1. Als u een Azure signalerings service resource wilt maken, meldt u zich eerst aan bij de [Azure Portal](https://portal.azure.com). Selecteer in de linkerbovenhoek van de pagina **+ een resource maken**. In het tekstvak **Marketplace doorzoeken** voert u de **seingevings service**in.

2. Selecteer de **seingevings service** in de resultaten en selecteer **maken**.

3. Voeg op de pagina nieuwe **seingevings** instellingen de volgende instellingen toe voor de nieuwe signalerings Bron:

    | Naam | Aanbevolen waarde | Beschrijving |
    | ---- | ----------------- | ----------- |
    | Resourcenaam | *testsignalr* | Voer een unieke resourcenaam in voor de SignalR-resource. De naam moet een teken reeks van 1 tot 63 tekens zijn en mag alleen cijfers, letters en het koppel teken`-`() bevatten. De naam mag niet beginnen of eindigen met het koppel teken en de opeenvolgende afbreek streepjes zijn ongeldig.|
    | Abonnement | Kies uw abonnement |  Selecteer het Azure-abonnement dat u wilt gebruiken om SignalR te testen. Als uw account slechts één abonnement heeft, wordt dit automatisch geselecteerd en wordt de vervolg keuzelijst **abonnement** niet weer gegeven.|
    | Resourcegroep | Een resource groep maken met de naam *SignalRTestResources*| Selecteer of maak een resourcegroep voor uw SignalR-resource. Deze groep is handig als u meerdere resources wilt ordenen die u op hetzelfde moment wilt verwijderen door de resource groep te verwijderen. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../articles/azure-resource-manager/management/overview.md) voor meer informatie. |
    | Locatie | *VS - oost* | Gebruik **Locatie** om de geografische locatie op te geven waar SignalR-resource wordt gehost. Voor de beste prestaties wordt u aangeraden om de resource te maken in dezelfde regio als de andere onderdelen van uw toepassing. |
    | Prijscategorie | *Gratis* | Momenteel zijn de opties **gratis** en **standaard** beschikbaar. |
    | Vastmaken aan dashboard | ✔ | Schakel dit selectie vakje in als u wilt dat de resource wordt vastgemaakt aan uw dash board zodat u deze gemakkelijker kunt vinden. |

4. Selecteer **Maken**. Het volt ooien van de implementatie kan enkele minuten duren.

5. Nadat de implementatie is voltooid, selecteert u **sleutels** onder **instellingen**. Kopieer uw connection string voor de primaire sleutel. U gebruikt deze teken reeks later om uw app te configureren voor het gebruik van de service resource van Azure signalering.

    Deze verbindingsreeks heeft de volgende vorm:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
