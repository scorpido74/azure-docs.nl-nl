---
title: bestand opnemen
description: bestand opnemen
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 0a228f6d8b4f72acd3783f27bf192fe6bd13f988
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75392158"
---
## <a name="create-an-azure-signalr-service-instance"></a>Een Azure SignalR Service-exemplaar maken

Uw toepassing maakt verbinding met een SignalR-Service-exemplaar in Azure.

1. Selecteer de knop Nieuw in de linkerbovenhoek van Azure Portal. Typ in het scherm Nieuw *SignalR Service* in het zoekvak in en druk op Enter.

    ![Zoek naar SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Selecteer **SignalR Service** in de zoekresultaten en selecteer **Maken**.

1. Voer de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Resourcenaam** | Wereldwijd unieke naam | Naam ter identificatie van uw nieuwe SignalR Service-exemplaar. Geldige tekens zijn `a-z`, `0-9` en `-`.  | 
    | **Abonnement** | Uw abonnement | Het abonnement waarin dit nieuwe SignalR Service-exemplaar is gemaakt. | 
    | **[Resource groep](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Naam voor de nieuwe resourcegroep waarin het SignalR Service-exemplaar moet worden gemaakt. | 
    | **Locatie** | VS - west | Kies een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. |
    | **Prijs categorie** | Gratis | Probeer Azure SignalR Service gratis uit. |
    | **Aantal eenheden** |  Niet van toepassing | Het aantal eenheden geeft aan hoeveel verbindingen uw SignalR Service-exemplaar kan accepteren. Dit kan alleen worden geconfigureerd in de Standard-laag. |
    | **Service modus** |  Serverloos | Voor gebruik met Azure Functions of REST API. |

    ![SignalR Service maken](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Selecteer **Maken** om te beginnen met het implementeren van het SignalR-Service-exemplaar.

1. Nadat het exemplaar is geïmplementeerd, opent u het in de portal en zoekt u de pagina instellingen. Wijzig de service modus instelling op alleen *Server* als u Azure signalerings service gebruikt via Azure functions binding of rest API. Zorg ervoor dat het *klassiek* of *standaard* is.