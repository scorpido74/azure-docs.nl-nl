---
title: Geavanceerde bedreigingsbeveiliging - Azure-portal - Azure-database voor MySQL
description: Meer informatie over het configureren van Advanced Threat Protection om afwijkende databaseactiviteiten te detecteren die potentiële beveiligingsbedreigingen voor de database aangeven.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8eec40006a280b69387e14a5841360da65616ca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062343"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Geavanceerde bedreigingsbeveiliging voor Azure-database voor MySQL

Advanced Threat Protection for Azure Database for MySQL detecteert vreemde activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot databases of om deze aan te vallen.

Advanced Threat Protection maakt deel uit van het Advanced Data Security-aanbod, een uniform pakket voor geavanceerde beveiligingsmogelijkheden. Advanced Threat Protection kan worden benaderd en beheerd via de [Azure-portal](https://portal.azure.com) en is momenteel in preview.

> [!NOTE]
> De functie Advanced Threat Protection is **niet** beschikbaar in de volgende Azure-overheids- en soevereine cloudregio's: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Ga naar [producten die per regio beschikbaar zijn](https://azure.microsoft.com/global-infrastructure/services/) voor algemene beschikbaarheid van producten.
>

> [!NOTE]
> Deze functie is beschikbaar in alle regio's van Azure waar Azure Database voor MySQL is geïmplementeerd voor servers voor algemeen gebruik en geheugengeoptimaliseerd.

## <a name="set-up-threat-detection"></a>Bedreigingsdetectie instellen
1. Start de Azure-portal op [https://portal.azure.com](https://portal.azure.com).
2. Navigeer naar de configuratiepagina van de Azure Database for MySQL-server die u wilt beveiligen. Selecteer **geavanceerde bedreigingsbeveiliging (voorbeeld) in**de beveiligingsinstellingen .
3. Ga als een voorbeeld van de configuratie van **Advanced Threat Protection (Preview):**

   - Geavanceerde bedreigingsbeveiliging inschakelen op de server.
   - Geef in **Geavanceerde instellingen voor bedreigingsbeveiliging**in het vak **Waarschuwingen verzenden naar** tekst de lijst met e-mails om beveiligingswaarschuwingen te ontvangen bij detectie van afwijkende databaseactiviteiten.
  
   ![Bedreigingsdetectie instellen](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Ontdek afwijkende databaseactiviteiten

U ontvangt een e-mailmelding bij detectie van afwijkende databaseactiviteiten. De e-mail bevat informatie over de verdachte beveiligingsgebeurtenis, inclusief de aard van de afwijkende activiteiten, de naam van de database, de naam van de server, de naam van de toepassing en de gebeurtenistijd. Bovendien biedt de e-mail informatie over mogelijke oorzaken en aanbevolen acties om de potentiële bedreiging voor de database te onderzoeken en te beperken.
 
1. Klik op de koppeling **Recente waarschuwingen weergeven** in de e-mail om de Azure-portal te starten en de pagina Waarschuwingen van azure Security Center weer te geven, die een overzicht biedt van actieve bedreigingen die zijn gedetecteerd in de SQL-database.
    
    ![Afwijkend activiteitenrapport](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Actieve bedreigingen weergeven:

    ![Actieve bedreigingen](./media/howto-database-threat-protection-portal/active-threats.png)

2. Klik op een specifieke waarschuwing om aanvullende details en acties te krijgen voor het onderzoeken van deze bedreiging en het herstellen van toekomstige bedreigingen.
    
    ![Specifieke waarschuwing](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Waarschuwingen voor bedreigingsdetectie verkennen

SQL Database Threat Detection integreert de waarschuwingen met [Azure Security Center.](https://azure.microsoft.com/services/security-center/) Een live SQL-detectietegel volgt de status van actieve bedreigingen op de database en SQL ATP-pagina's in de Azure-portal.

Klik **op Waarschuwing voor bedreigingsdetectie** om de pagina Waarschuwingen van azure security center te starten en een overzicht te krijgen van actieve SQL-bedreigingen die in de database worden gedetecteerd.

   ![Waarschuwing voor bedreigingsdetectie](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Zie de pagina Azure [Database for MySQL-prijzen voor](https://azure.microsoft.com/pricing/details/mysql/) meer informatie over prijzen  
