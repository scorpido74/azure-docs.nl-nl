---
title: Geen werkgroep gevonden voor een app Application Proxy
description: Problemen oplossen die u ondervinden wanneer er geen werkende connector in een connectorgroep voor uw toepassing is met de Azure AD-toepassingsproxy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ab0d1b3bbab9c97c04da4f918f3aaa2f1d07e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275623"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Geen werkgroep gevonden voor een toepassingsproxytoepassing

In dit artikel u de veelvoorkomende problemen oplossen wanneer er geen connector is gedetecteerd voor een toepassingsproxytoepassing die is geïntegreerd met Azure Active Directory.

## <a name="overview-of-steps"></a>Overzicht van stappen
Als er geen werkende connector in een connectorgroep voor uw toepassing is, zijn er een aantal manieren om het probleem op te lossen:

-   Als u geen connectors in de groep hebt, u het:

    -   Een nieuwe connector downloaden aan de rechterkant-on-premises server en deze toewijzen aan deze groep

    -   Een actieve connector naar de groep verplaatsen

-   Als u geen actieve connectors in de groep hebt, u het nieuwe bedrijf:

    -   De reden waarom uw connector inactief is en oplost identificeren

    -   Een actieve connector naar de groep verplaatsen

Als u het probleem wilt oplossen, opent u het menu 'Toepassingsproxy' in uw toepassing en bekijkt u het waarschuwingsbericht Connectorgroep. Als er geen connectors in de groep zijn, geeft het waarschuwingsbericht aan dat de groep ten minste één connector nodig heeft. Als u geen actieve connectors hebt, wordt dat in het waarschuwingsbericht uitgelegd. Het is gebruikelijk om inactieve connectors te hebben. 

   ![Selectie van connectorgroep in Azure-portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Zie het desbetreffende gedeelte hieronder voor meer informatie over elk van deze opties. De instructies gaan ervan uit dat u vanaf de pagina Connectorbeheer begint. Als u het foutbericht hierboven bekijkt, u naar deze pagina gaan door op het waarschuwingsbericht te klikken. U ook naar de pagina gaan door naar **Azure Active Directory**te gaan, te klikken op **Enterprise-toepassingen**en vervolgens **Application Proxy.**

   ![Groepsbeheer voor connectoren in Azure-portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Een nieuwe connector downloaden

Als u een nieuwe connector wilt downloaden, gebruikt u de knop 'Connector downloaden' boven aan de pagina.

Installeer de connector op een machine met directe zichtlijn naar de backend-toepassing. Normaal gesproken is de connector geïnstalleerd op dezelfde server als de toepassing. Na het downloaden moet de connector in dit menu worden weergegeven. klik op de connector en gebruik de vervolgkeuzelijst 'Connectorgroep' om te zien of deze tot de juiste groep behoort. Sla de wijziging op.

   ![De connector downloaden van de Azure-portal](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Een actieve connector verplaatsen

Als u een actieve connector hebt die tot de groep moet behoren en zichtlijn heeft naar de doelbackendtoepassing, u de connector naar de toegewezen groep verplaatsen. Klik hiervoor op de connector. Gebruik in het veld 'Verbindingsgroep' de vervolgkeuzelijst om de juiste groep te selecteren en klik op Opslaan.

## <a name="resolve-an-inactive-connector"></a>Een inactieve connector oplossen

Als de enige connectors in de groep inactief zijn, bevinden ze zich waarschijnlijk op een machine die niet alle benodigde poorten heeft gedeblokkeerd.

zie de poorten Probleemoplossing document voor details over het onderzoeken van dit probleem.

## <a name="next-steps"></a>Volgende stappen
[Informatie over Azure AD-toepassingsproxyconnectors](application-proxy-connectors.md)


