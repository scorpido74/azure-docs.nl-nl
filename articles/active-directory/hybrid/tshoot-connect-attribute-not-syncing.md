---
title: Problemen oplossen met een kenmerk dat niet wordt gesynchroniseerd in Azure AD Connect | Microsoft Docs'
description: In dit onderwerp worden stappen gegeven voor het oplossen van problemen met kenmerksynchronisatie met behulp van de probleemoplossingstaak.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60455907"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Problemen oplossen met een kenmerk dat niet wordt gesynchroniseerd in Azure AD Connect

## <a name="recommended-steps"></a>**Aanbevolen stappen**

Voordat u synchronisatieproblemen met kenmerken onderzoekt, u het synchronisatieproces van **Azure AD Connect** begrijpen:

  ![Azure AD Connect-synchronisatieproces](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **CS:** Connectorruimte, een tabel in de database.
* **MV:** Metaverse, een tabel in de database.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Synchronisatiestappen**

* Importeren uit AD: Active Directory-objecten worden naar AD CS gebracht.

* Importeren uit AAD: Azure Active Directory-objecten worden in AAD CS gebracht.

* Synchronisatie: **Binnenkomende synchronisatieregels** en **uitgaande synchronisatieregels** worden uitgevoerd in de volgorde van het voorrangsgetal van lager naar hoger. Als u de synchronisatieregels wilt weergeven, u vanuit de bureaubladtoepassingen naar **De editor voor synchronisatieregels** gaan. De **binnenkomende synchronisatieregels** brengen gegevens van CS naar MV. De **uitgaande synchronisatieregels** verplaatst gegevens van MV naar CS.

* Exporteren naar AD: na het uitvoeren van Synchronisatie worden objecten geëxporteerd van AD CS naar **Active Directory**.

* Exporteren naar AAD: Na het uitvoeren van Synchronisatie worden objecten geëxporteerd van AAD CS naar **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Stap voor stap onderzoek**

* We beginnen onze zoektocht vanuit de **Metaverse** en bekijken de attribuuttoewijzing van bron naar doel.

* Start **Synchronization Service Manager** vanuit de desktoptoepassingen, zoals hieronder wordt weergegeven:

  ![Synchronisatieservicebeheer starten](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* Selecteer in **de synchronisatieservicemanager**de **metaverse zoekopdracht**, selecteer **Bereik op objecttype,** selecteer het object met een kenmerk en klik op De knop **Zoeken.**

  ![Metaverse zoeken](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Dubbelklik op het object in de **metaverse** zoekopdracht om alle kenmerken te bekijken. U op het tabblad **Connectors** klikken om het bijbehorende object in alle **verbindingsruimten**te bekijken.

  ![Metaverse objectconnectoren](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Dubbelklik op de **Active Directory Connector** om de kenmerken **Connectorruimte** weer te geven. Klik op de knop **Voorbeeld,** klik in het volgende dialoogvenster op de knop **Voorbeeld genereren.**

  ![Kenmerken van connectorruimte](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Klik nu op de **invoerattribuutstroom**, hiermee wordt de stroom van kenmerken van **Active Directory Connector Space** naar de **metaverse**weergegeven. De kolom **Synchronisatieregel** geeft aan welke **synchronisatieregel** heeft bijgedragen aan dat kenmerk. De kolom **Gegevensbron** toont u de kenmerken uit de **connectorruimte**. De kolom **Metaverse attribuut** toont u de kenmerken in **de metaverse**. U hier zoeken naar het kenmerk dat niet synchroniseert. Als u het kenmerk hier niet vindt, wordt dit niet in kaart gebracht en moet u een nieuwe aangepaste **synchronisatieregel** maken om het kenmerk in kaart te brengen.

  ![Kenmerken van connectorruimte](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Klik op de **eigenschappenstroom exporteren** in het linkerdeelvenster om de kenmerkstroom van **Metaverse** terug te geven naar **Active Directory Connector Space** met uitgaande **synchronisatieregels**.

  ![Kenmerken van connectorruimte](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Op dezelfde manier u het object **Azure Active Directory Connector Space** bekijken en de **voorbeeldweergave** genereren om de kenmerkstroom van **Metaverse** naar de **connectorruimte** weer te geven en vice versa, op deze manier u onderzoeken waarom een kenmerk niet synchroniseert.

## <a name="recommended-documents"></a>**Aanbevolen documenten**
* [Azure AD Connect-synchronisatie: technische concepten](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Azure AD Connect-synchronisatie: de architectuur begrijpen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Azure AD Connect-synchronisatie: declaratieve inrichting begrijpen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD Connect-synchronisatie: declaratieve inrichtingsexpressies begrijpen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Azure AD Connect-synchronisatie: inzicht in de standaardconfiguratie](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Synchronisatie van Azure AD Connect: gebruikers, groepen en contactpersonen begrijpen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Azure AD Connect-synchronisatie: schaduwkenmerken](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Volgende stappen

- [Synchronisatie van Azure AD Connect](how-to-connect-sync-whatis.md).
- [Wat is hybride identiteit?](whatis-hybrid-identity.md)
