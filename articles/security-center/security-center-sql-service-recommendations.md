---
title: Azure-gegevens-en-opslag Services beveiligen in Azure Security Center | Microsoft Docs
description: Dit document bevat aanbevelingen in Azure Security Center waarmee u uw gegevens en Azure SQL-service kunt beveiligen en in overeenstemming bent met het beveiligings beleid.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: v-mohabe
ms.openlocfilehash: 5f34f5e0c622ff107da880d9a4a77c63568b63b0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100938"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Azure-gegevens-en-opslag Services beveiligen in Azure Security Center
In dit onderwerp wordt beschreven hoe u beveiligings aanbevelingen voor gegevens en opslag resources weergeeft en implementeert. Deze aanbevelingen Azure Security Center gevonden bij het analyseren van de beveiligings status van uw Azure-resources.

## <a name="view-your-data-security-information"></a>Uw gegevens beveiligings gegevens weer geven

1. Klik in de sectie **beveiligings hygiëne voor bronnen** op **gegevens-en opslag resources**.

   ![Gegevens & storage-resources](./media/security-center-monitoring/click-data.png)

    De pagina **gegevens beveiliging** wordt geopend met aanbevelingen voor gegevens bronnen.

     ![Gegevensresources](./media/security-center-monitoring/sql-overview.png)

Op deze pagina kunt u het volgende doen:

* Klik op het tabblad **overzicht** om alle aanbevelingen voor de gegevens bronnen weer te geven die u wilt herstellen. 
* Klik op elk tabblad en Bekijk de aanbevelingen per resource type.

    > [!NOTE]
    > Zie [Azure Storage versleuteling voor Data-at-rest](../storage/common/storage-service-encryption.md)voor meer informatie over opslag versleuteling.


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Een aanbeveling voor een gegevens bron herstellen

1. Klik op een resource in een van de resource tabbladen. De pagina informatie wordt geopend met een lijst met de aanbevelingen die moeten worden hersteld.

    ![Resource gegevens](./media/security-center-monitoring/sql-recommendations.png)

2. Klik op een aanbeveling. De pagina aanbeveling wordt geopend en toont de herstels tappen voor het implementeren van de aanbeveling.

   ![Herstelstappen](./media/security-center-monitoring/remediate1.png)

3. Klik op **actie ondernemen**. De pagina Resource-instellingen wordt weer gegeven.

    ![Aanbeveling inschakelen](./media/security-center-monitoring/remediate2.png)

4. Volg de herstels tappen en klik op **Opslaan**.

## <a name="data-and-storage-recommendations"></a>Aanbevelingen voor gegevens en opslag

|Resourcetype|Beveiligingsscore|Aanbeveling|Description|
|----|----|----|----|
|Storage-account|20|Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld|Beveiligde overdracht is een optie die ervoor zorgt dat uw opslag account alleen aanvragen van beveiligde verbindingen (HTTPS) accepteert. HTTPS zorgt voor verificatie tussen de-server en de service en beveiligt de door Voer van gegevens via netwerklaag aanvallen, zoals man-in-the-Middle, inkomend en inkomend en sessie overname.|
|Redis|20|Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld|Schakel alleen verbindingen via SSL naar Azure cache in voor redis. Gebruik van beveiligde verbindingen zorgt voor verificatie tussen de server en de service en beveiligt de door Voer van gegevens via netwerklaag aanvallen, zoals man-in-the-Middle, inkomend en inkomend en sessie overname.|
|SQL|15|Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld|Schakel transparante gegevens versleuteling in om gegevens op rest te beveiligen en te voldoen aan nalevings vereisten.|
|SQL|15|SQL Server-controle moet zijn ingeschakeld|Schakel controle in voor Azure SQL-servers. (Alleen Azure SQL-service. Bevat geen SQL die wordt uitgevoerd op uw virtuele machines.)|
|Data Lake Analytics|5|Diagnostische logboeken in Data Lake Analytics moeten worden ingeschakeld|Logboeken inschakelen en ze maximaal een jaar bewaren. Hiermee kunt u activiteit sporen onderzoek opnieuw maken wanneer een beveiligingsincident voordoet of uw netwerk is gecompromitteerd. |
|Data Lake Store|5|Diagnostische logboeken in Azure Data Lake Store moeten worden ingeschakeld|Logboeken inschakelen en ze maximaal een jaar bewaren. Hiermee kunt u activiteit sporen onderzoek opnieuw maken wanneer een beveiligingsincident voordoet of uw netwerk is gecompromitteerd. |
|SQL|30|Beveiligings problemen voor uw SQL-data bases moeten worden hersteld|Met evaluatie van SQL-beveiligings problemen wordt uw data base gescand op beveiligings lekken en worden eventuele afwijkingen van aanbevolen procedures, zoals onjuiste configuratie, buitensporige machtigingen en onbeveiligde gevoelige gegevens, weer gegeven. Door de gevonden beveiligings problemen op te lossen, kunt u uw data base Security stature aanzienlijk verbeteren.|
|SQL|20|Een Azure AD-beheerder voor SQL server inrichten|Richt een Azure AD-beheerder in voor uw SQL-Server om Azure AD-verificatie in te scha kelen. Met Azure AD-verificatie kunt u eenvoudig beheer van machtigingen en gecentraliseerd identiteits beheer van database gebruikers en andere micro soft-Services.|
|Storage-account|15|Toegang tot opslag accounts met firewall-en virtuele-netwerk configuraties moet worden beperkt|Controleer onbeperkte netwerk toegang in de firewall-instellingen van uw opslag account. In plaats daarvan configureert u netwerk regels zodat alleen toepassingen van toegestane netwerken toegang hebben tot het opslag account. Als u verbindingen van specifieke internet-of on-premises clients wilt toestaan, kunt u toegang verlenen tot verkeer van specifieke Azure Virtual Networks of voor open bare IP-adresbereiken voor Internet.|
|Storage-account|1|Opslag accounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources|Gebruik nieuwe Azure Resource Manager V2 voor uw opslag accounts om beveiligings uitbreidingen te bieden zoals: sterker toegangs beheer (RBAC), betere controle, implementatie en beheer op basis van Resource Manager, toegang tot beheerde identiteiten, toegang tot de sleutel kluis voor geheimen en Azure AD-gebaseerde verificatie en ondersteuning voor Tags en resource groepen voor eenvoudiger beveiligings beheer.|

## <a name="see-also"></a>Zie ook
Zie de volgende onderwerpen voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:

* [Uw machines en toepassingen beveiligen in Azure Security Center](security-center-virtual-machine-protection.md)
* [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)

Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Setting security policies in Azure Security Center](tutorial-security-policy.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
* [Azure Security Center FAQ](security-center-faq.md) (Veelgestelde vragen over Azure Security Center): raadpleeg veelgestelde vragen over het gebruik van de service.
