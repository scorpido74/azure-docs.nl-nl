---
title: Azure AD Connect-status en gebruikersprivacy | Microsoft Documenten
description: In dit document wordt de privacy van gebruikers beschreven met Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58cddcde71e6e86b9abe07eb4200f13ad55ea659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253661"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Gebruikersprivacy en Azure AD Connect-status 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>In dit artikel wordt ingehouden op Azure AD Connect Health en de privacy van gebruikers.  Zie het artikel [hier](reference-connect-user-privacy.md)voor informatie over Azure AD Connect en de privacy van gebruikers.

## <a name="user-privacy-classification"></a>Classificatie van gebruikersprivacy
Azure AD Connect Health valt in de categorie **gegevensverwerker** van GDPR-classificatie. Als pijplijn voor gegevensverwerkers biedt de service gegevensverwerkingsdiensten aan belangrijke partners en eindgebruikers. Azure AD Connect Health genereert geen gebruikersgegevens en heeft geen onafhankelijke controle over welke persoonlijke gegevens worden verzameld en hoe deze worden gebruikt. Het ophalen, samenvoegen, analyseren en rapporteren van gegevens in Azure AD Connect Health is gebaseerd op bestaande on-premises gegevens. 

## <a name="data-retention-policy"></a>Beleid voor het bewaren van gegevens
Azure AD Connect Health genereert geen rapporten, voert geen analyses uit of biedt geen inzichten na 30 dagen. Daarom slaat Azure AD Connect Health geen gegevens op die langer dan 30 dagen duren. Dit ontwerp voldoet aan de GDPR-voorschriften, de privacynalevingsvoorschriften van Microsoft en het azure AD-beleid voor het bewaren van gegevens. 

Servers met actieve statusservicegegevens zijn gedurende meer dan 30 opeenvolgende dagen **niet up-to-date** **foutwaarschuwingen,** wat erop wijst dat er gedurende die periode geen gegevens connectstatus hebben bereikt. Deze servers worden uitgeschakeld en worden niet weergegeven in de Connect Health-portal. Als u de servers opnieuw wilt inschakelen, moet u de zorgverlener verwijderen en [opnieuw installeren.](how-to-connect-health-agent-install.md) Houd er rekening mee dat dit niet geldt voor **waarschuwingen** met hetzelfde waarschuwingstype. Waarschuwingen geven aan dat er gedeeltelijke gegevens ontbreken op de server waarvoor u wordt gewaarschuwd. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Gegevensverzameling en -bewaking uitschakelen in Azure AD Connect-status
Met Azure AD Connect Health u het verzamelen van gegevens stoppen voor elke afzonderlijke bewaakte server of voor een exemplaar van een bewaakte service. U bijvoorbeeld het verzamelen van gegevens stoppen voor afzonderlijke ADFS-servers (Active Directory Federation Services) die worden bewaakt met Azure AD Connect-status. U ook het verzamelen van gegevens stoppen voor het gehele ADFS-exemplaar dat wordt gecontroleerd met Azure AD Connect-status. Wanneer u hiervoor kiest, worden de bijbehorende servers verwijderd uit de Azure AD Connect Health-portal, nadat u het verzamelen van gegevens hebt gestopt. 

>[!IMPORTANT]
> U hebt azure AD Global Administrator-bevoegdheden of de rol inzender in RBAC nodig om bewaakte servers uit Azure AD Connect Health te verwijderen.
>
> Het verwijderen van een server of service-instantie uit Azure AD Connect Health is geen omkeerbare actie. 

### <a name="what-to-expect"></a>Wat te verwachten?
Als u het verzamelen en controleren van gegevens voor een afzonderlijke bewaakte server of een instantie van een bewaakte service stopt, moet u het volgende noteren:

- Wanneer u een instantie van een bewaakte service verwijdert, wordt de instantie verwijderd uit de lijst met azure AD Connect-statusbewakingsservice in de portal. 
- Wanneer u een bewaakte server of een instantie van een bewaakte service verwijdert, wordt de statusagent NIET verwijderd of van uw servers verwijderd. De statusagent is geconfigureerd om geen gegevens naar Azure AD Connect Health te verzenden. U moet de statusagent handmatig verwijderen op eerder bewaakte servers.
- Als u de statusagent niet hebt verwijderd voordat u deze stap uitvoert, ziet u mogelijk foutgebeurtenissen op de server(s) die betrekking hebben op de statusagent.
- Alle gegevens die behoren tot de instantie van de bewaakte service worden verwijderd volgens het Microsoft Azure-beleid voor gegevensbewaring.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Gegevensverzameling en -bewaking uitschakelen voor een instantie van een bewaakte service
Zie [hoe u een service-exemplaar verwijdert uit Azure AD Connect-status](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Gegevensverzameling en -bewaking uitschakelen voor een bewaakte server
Zie [hoe u een server uit Azure AD Connect-status verwijdert.](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service)

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Gegevensverzameling en -bewaking uitschakelen voor alle bewaakte services in Azure AD Connect-status
Azure AD Connect Health biedt ook de mogelijkheid om het verzamelen van gegevens van **alle** geregistreerde services in de tenant te stoppen. We raden u aan zorgvuldig te overwegen en alle globale beheerders volledig te erkennen voordat u de actie onderneemt. Zodra het proces begint, stopt de Connect Health-service met het ontvangen, verwerken en rapporteren van alle gegevens van al uw services. Bestaande gegevens in de Connect Health-service worden niet langer dan 30 dagen bewaard.
Als u het verzamelen van gegevens van een specifieke server wilt stoppen, voert u stappen uit bij het verwijderen van specifieke servers. Als u het verzamelen van tenant-wise gegevens wilt stoppen, voert u de volgende stappen uit om het verzamelen van gegevens te stoppen en alle services van de tenant te verwijderen.

1. Klik op **Algemene instellingen** onder configuratie in het hoofdblad. 
2. Klik op de knop **Gegevensverzameling stoppen** boven aan het blad. De andere opties van tenantconfiguratie-instellingen worden uitgeschakeld zodra het proces wordt gestart.  
 
   ![Gegevensverzameling stoppen](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Zorg voor de lijst van services aan boord die worden beïnvloed door het stoppen van gegevensverzamelingen. 
4. Voer de exacte tenantnaam in om de **actieknop Verwijderen** in te schakelen
5. Klik op **Verwijderen** om het verwijderen van alle services te activeren. Connect Health stopt met het ontvangen, verwerken en rapporteren van alle gegevens die van uw onboarded services worden verzonden. Het hele proces van kan tot 24 uur duren. Merk op dat deze stap niet omkeerbaar is. 
6. Nadat het proces is voltooid, ziet u geen geregistreerde services meer in Connect Health. 

   ![Nadat het verzamelen van gegevens is gestopt](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Gegevensverzameling en -bewaking opnieuw inschakelen in Azure AD Connect-status
Als u de bewaking opnieuw wilt inschakelen in Azure AD Connect-status voor een eerder verwijderde bewaakte service, moet u de statusagent op alle servers verwijderen en [opnieuw installeren.](how-to-connect-health-agent-install.md)

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Gegevensverzameling en -bewaking opnieuw inschakelen voor alle bewaakte services

Het verzamelen van tenant-wise gegevens kan worden hervat in Azure AD Connect Health. We raden u aan zorgvuldig te overwegen en alle globale beheerders volledig te erkennen voordat u de actie onderneemt.

>[!IMPORTANT]
> De volgende stappen zijn beschikbaar na 24 uur uitschakelen actie.
> Na het inschakelen van gegevensverzameling worden de gepresenteerde informatie- en bewakingsgegevens in Connect Health geen eerder verzamelde verouderde gegevens weergegeven. 

1. Klik op **Algemene instellingen** onder configuratie in het hoofdblad. 
2. Klik op **knop Gegevensverzameling inschakelen** boven aan het blad. 
 
   ![Gegevensverzameling inschakelen](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Voer de exacte tenantnaam in om de knop **Inschakelen te** activeren.
4. Klik op **Knop Inschakelen** om toestemming te geven voor het verzamelen van gegevens in de service Status verbinden. De wijziging zal binnenkort worden toegepast. 
5. Volg het [installatieproces](how-to-connect-health-agent-install.md) om de agent opnieuw te installeren in de servers die moeten worden gecontroleerd en de services zullen aanwezig zijn in de portal.  


## <a name="next-steps"></a>Volgende stappen
* [Het Privacybeleid van Microsoft controleren op het vertrouwenscentrum](https://www.microsoft.com/trustcenter)
* [Azure AD Connect en gebruikersprivacy](reference-connect-user-privacy.md)

