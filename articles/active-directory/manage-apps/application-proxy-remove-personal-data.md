---
title: Persoonlijke gegevens verwijderen - Azure Active Directory Application Proxy
description: Verwijder persoonlijke gegevens uit connectors die zijn geïnstalleerd op apparaten voor Azure Active Directory Application Proxy.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe97956d99bd5c677e499b532ef85a1bb4d324ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275414"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Persoonlijke gegevens verwijderen voor Azure Active Directory Application Proxy

Azure Active Directory Application Proxy vereist dat u connectors op uw apparaten installeert, wat betekent dat er mogelijk persoonlijke gegevens op uw apparaten staan. In dit artikel vindt u stappen voor het verwijderen van die persoonsgegevens om de privacy te verbeteren.

## <a name="where-is-the-personal-data"></a>Waar zijn de persoonsgegevens?

Het is mogelijk voor Application Proxy om persoonlijke gegevens te schrijven naar de volgende logtypen:

- Logboeken voor connectorgebeurtenissen
- Windows-gebeurtenislogboeken

## <a name="remove-personal-data-from-windows-event-logs"></a>Persoonlijke gegevens verwijderen uit Gebeurtenislogboeken van Windows

Zie [Instellingen voor gebeurtenislogboeken](https://technet.microsoft.com/library/cc952132.aspx)voor informatie over het configureren van gegevensbewaring voor de Windows-gebeurtenislogboeken. Zie [Windows-gebeurtenislogboeken gebruiken](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx)voor meer informatie over Windows-gebeurtenislogboeken.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Persoonlijke gegevens verwijderen uit logboeken van connectorgebeurtenissen

Om ervoor te zorgen dat de logboeken van application proxy geen persoonlijke gegevens bevatten, u het als:

- Gegevens verwijderen of weergeven wanneer dat nodig is, of
- Logboekregistratie uitschakelen

Gebruik de volgende secties om persoonlijke gegevens uit logboeken voor connectorgebeurtenissen te verwijderen. U moet het verwijderingsproces voltooien voor alle apparaten waarop de connector is geïnstalleerd.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Specifieke gegevens weergeven of exporteren

Als u specifieke gegevens wilt weergeven of exporteren, zoekt u naar gerelateerde vermeldingen in elk van de logboeken van de connectorgebeurtenis. De logboeken `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`bevinden zich op .

Aangezien de logboeken tekstbestanden zijn, u [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) gebruiken om te zoeken naar tekstvermeldingen die betrekking hebben op een gebruiker.  

Als u persoonlijke gegevens wilt vinden, zoekt u in logbestanden naar UserID.

Als u persoonlijke gegevens wilt vinden die zijn vastgelegd door een toepassing die kerberos beperkte delegatie gebruikt, zoekt u naar deze onderdelen van het gebruikersnaamtype:

- On-premises gebruikersnaam
- Gebruikersnaam deel van de hoofdnaam van de gebruiker
- Gebruikersnaam deel van on-premises gebruikersnaam
- Naam van de on-premises beveiligingsaccountsbeheerder (SAM)

### <a name="delete-specific-data"></a>Specifieke gegevens verwijderen

Ga als u specifieke gegevens verwijderen:

1. Start de Microsoft Azure AD Application Proxy Connector-service opnieuw om een nieuw logboekbestand te genereren. Met het nieuwe logboekbestand u de oude logboekbestanden verwijderen of wijzigen. 
1. Volg het eerder beschreven [proces van weergave of exporteren](#view-or-export-specific-data) om informatie te vinden die moet worden verwijderd. Doorzoek alle connectorlogboeken.
1. Verwijder de relevante logboekbestanden of verwijder selectief de velden die persoonsgegevens bevatten. U ook alle oude logboekbestanden verwijderen als u ze niet meer nodig hebt.

### <a name="turn-off-connector-logs"></a>Verbindingslogboeken uitschakelen

Een optie om ervoor te zorgen dat de connectorlogs geen persoonlijke gegevens bevatten, is het uitschakelen van de logboekgeneratie. Als u wilt stoppen met het genereren `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`van connectorlogboeken, verwijdert u de volgende gemarkeerde regel uit .

![Toont een codefragment met de gemarkeerde code om te verwijderen](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Volgende stappen

Zie Hoe u veilige [externe toegang tot on-premises toepassingen bieden](application-proxy.md)voor een overzicht van application proxy.