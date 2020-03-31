---
title: Geavanceerde gegevensbeveiliging voor IaaS in Azure Security Center | Microsoft Documenten
description: " Meer informatie over het inschakelen van geavanceerde gegevensbeveiliging voor IaaS in Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282729"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Geavanceerde gegevensbeveiliging voor SQL-servers op Azure Virtual Machines (Preview)
Geavanceerde gegevensbeveiliging voor SQL Servers op Azure Virtual Machines is een uniform pakket voor geavanceerde SQL-beveiligingsmogelijkheden. Deze preview-functie bevat functionaliteit voor het identificeren en beperken van potentiële databasekwetsbaarheden en het detecteren van afwijkende activiteiten die kunnen wijzen op bedreigingen voor uw database. 

Dit beveiligingsaanbod voor Azure VM SQL-servers is gebaseerd op dezelfde fundamentele technologie die wordt gebruikt in het [Azure SQL Database Advanced Data Security-pakket.](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)


## <a name="overview"></a>Overzicht

Geavanceerde gegevensbeveiliging biedt een reeks geavanceerde SQL-beveiligingsmogelijkheden, bestaande uit kwetsbaarheidsbeoordeling en geavanceerde bedreigingsbeveiliging.

* [Kwetsbaarheidsbeoordeling](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) is een eenvoudig te configureren service die potentiële databasekwetsbaarheden kan opsporen, bijhouden en helpen oplossen. Het biedt inzicht in uw beveiligingsstatus en bevat de stappen om beveiligingsproblemen op te lossen en uw databaseversterkingen te verbeteren.
* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen aangeven om toegang te krijgen tot uw SQL-server of deze te exploiteren. Het controleert voortdurend uw database op verdachte activiteiten en biedt actiegerichte beveiligingswaarschuwingen op afwijkende databasetoegangspatronen. Deze waarschuwingen bieden de verdachte activiteit details en aanbevolen acties om te onderzoeken en de dreiging te beperken.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Aan de slag met geavanceerde gegevensbeveiliging voor SQL op Azure VM's

Met de volgende stappen u aan de slag met Advanced Data Security voor SQL in Azure VMs Public Preview.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Geavanceerde gegevensbeveiliging voor SQL instellen op Azure VM's

Geavanceerde gegevensbeveiliging voor SQL-servers op virtuele machines inschakelen op abonnements-/werkruimteniveau:
 
1. Open de pagina Prijzen & **instellingen** in de zijbalk van het Beveiligingscentrum.

1. Selecteer het abonnement of de werkruimte waarvoor u Geavanceerde gegevensbeveiliging voor SQL op Azure VM's wilt inschakelen.

1. Schakel de optie in voor **SQL-servers in VM (Preview)** ingeschakeld. 

    (Klik op de screenshot om uit te vouwen)

    [![Aanbevelingen en waarschuwingen van het Beveiligingscentrum zoals te zien in Windows Admin Center](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Geavanceerde gegevensbeveiliging voor SQL-servers wordt ingeschakeld op alle SQL-servers die zijn aangesloten op de geselecteerde werkruimte of de standaardwerkruimte van het geselecteerde abonnement.

    >[!NOTE]
    > De oplossing zal volledig actief zijn na de eerste herstart van de SQL Server. 

Als u een nieuwe werkruimte wilt maken, volgt u de instructies in [Een werkruimte Logboekanalyse maken](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Als u de host van de SQL Server wilt verbinden met een werkruimte, volgt u de instructies in [Windows-computers verbinden met Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)


## <a name="set-up-email-notification-for-security-alerts"></a>E-mailmelding instellen voor beveiligingswaarschuwingen 

U een lijst met geadresseerden instellen om een e-mailmelding te ontvangen wanneer waarschuwingen van het Beveiligingscentrum worden gegenereerd. De e-mail bevat een directe link naar de waarschuwing in Azure Security Center met alle relevante details. 

1. Ga naar Security > **Center-instellingen &** en klik op het desbetreffende abonnement **Security Center**

    ![Abonnementsinstellingen](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Klik in het menu Instellingen op **E-mailmeldingen**. 
1. Voer in het tekstvak **E-mailadres** de e-mailadressen in om de meldingen te ontvangen. U meer dan één e-mailadres invoeren door de e-mailadressen te scheiden met een komma (,).  Bijvoorbeeld admin1@mycompany.com,admin2@mycompany.comadmin3@mycompany.com

    ![E-mailinstellingen](./media/security-center-advanced-iaas-data/email-settings.png)

1. Stel in de instellingen **voor e-mailmelding** de volgende opties in:
  
    * **E-mailmelding verzenden voor waarschuwingen met hoge ernst:** In plaats van e-mails te verzenden voor alle waarschuwingen, verzendt u alleen voor waarschuwingen met hoge ernst.
    * **Stuur ook e-mailmeldingen naar abonnementseigenaren**: Stuur ook meldingen naar de eigenaren van abonnementen.

1. Klik boven aan het scherm **E-mailmeldingen** op **Opslaan**.

  > [!NOTE]
  > Klik op **Opslaan** voordat u het venster sluit of de nieuwe **instellingen voor e-mailmeldingen** worden niet opgeslagen.

## <a name="explore-vulnerability-assessment-reports"></a>Rapporten over kwetsbaarheidsbeoordeling verkennen

Het dashboard voor kwetsbaarheidsbeoordeling biedt een overzicht van uw beoordelingsresultaten in al uw databases. U de distributie van databases bekijken volgens de SQL Server-versie, samen met een samenvatting van falende versus passerende databases en een algemene samenvatting van falende controles op basis van risicoverdeling.

U de resultaten van de kwetsbaarheidsbeoordeling rechtstreeks vanuit Security Center bekijken.

1. Selecteer **gegevens & Opslag**onder de zijbalk van Het Beveiligingscentrum van Security Center.

1. Selecteer de aanbeveling **Kwetsbaarheden in uw SQL-databases in VM's moeten worden gesaneerd (Preview).** Zie [Aanbevelingen voor beveiligingscentrum voor](security-center-recommendations.md)meer informatie. 

    [![**Kwetsbaarheden in uw SQL-databases in VM's moeten worden verholpen (Preview)** aanbeveling](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    De gedetailleerde weergave voor deze aanbeveling wordt weergegeven.

    [![Gedetailleerde weergave voor de aanbeveling **Kwetsbaarheden in uw SQL-databases in VM's moet worden gesaneerd (Preview)**](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Ga als u op zoek naar meer informatie:

    * Klik op de server van belang voor een overzicht van gescande bronnen (databases) en de lijst met beveiligingscontroles die zijn getest.
    [![Kwetsbaarheden gegroepeerd door SQL-server](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Klik voor een overzicht van de kwetsbaarheden die zijn gegroepeerd in een specifieke SQL-database op de database van belang.
    [![Kwetsbaarheden gegroepeerd door SQL-server](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    In elke weergave worden de beveiligingscontroles gesorteerd op **ernst.** Klik op een specifieke beveiligingscontrole om een detailvenster met een **beschrijving**te zien, hoe u deze **herstellen** en andere gerelateerde informatie zoals **Impact** of **Benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Geavanceerde bedreigingsbeveiliging voor SQL-servers op Azure VM's-waarschuwingen
Waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot SQL-servers of deze te exploiteren. Deze gebeurtenissen kunnen de volgende waarschuwingen activeren:

### <a name="anomalous-access-pattern-alerts-preview"></a>Afwijkende toegangspatroonwaarschuwingen (Voorbeeld)

* **Toegang vanaf ongebruikelijke locatie:** Deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangspatroon voor SQL-server, waarbij iemand zich heeft aangemeld bij de SQL-server vanaf een ongebruikelijke geografische locatie. Mogelijke oorzaken:
    * Een aanvaller of voormalig kwaadwillend bedrijf heeft toegang tot uw SQL Server.
    * Een legitieme gebruiker heeft vanaf een nieuwe locatie toegang tot uw SQL Server.
* **Toegang tot een toepassing die mogelijk schadelijk is**: deze waarschuwing wordt geactiveerd wanneer een mogelijk schadelijke toepassing wordt gebruikt voor toegang tot de database. Mogelijke oorzaken:
    * Een aanvaller die uw SQL probeert te schenden met algemene aanvalstools.
    * Een legitieme penetratie testen in actie.
* **Toegang vanaf ongebruikelijke klant**: deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangspatroon tot de SQL-server, waarbij iemand zich vanuit een ongebruikelijke klant (SQL-gebruiker) heeft aangemeld bij de SQL-server. Mogelijke oorzaken:
    * Een aanvaller of voormalig kwaadwillend bedrijf heeft toegang tot uw SQL Server. 
    * Een legitieme gebruiker heeft toegang tot uw SQL Server met een nieuwe principal.
* **Brute Force SQL-referenties**: deze waarschuwing wordt geactiveerd wanneer er een abnormaal groot aantal mislukte aanmeldingen met andere referenties is. Mogelijke oorzaken:
    * Een aanvaller die je SQL probeert te schenden met brute kracht.
    * Een legitieme penetratie testen in actie.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Mogelijke SQL-injectieaanvallen (ondersteund in SQL Server 2019)

* **Kwetsbaarheid voor SQL-injectie:** deze waarschuwing wordt geactiveerd wanneer een toepassing een defecte SQL-instructie in de database genereert. Deze waarschuwing kan duiden op een mogelijk beveiligingsprobleem met SQL-injectieaanvallen. Mogelijke oorzaken:
    * Een fout in de toepassingscode die de foutieve SQL-instructie maakt
    * Toepassingscode of opgeslagen procedures schonen gebruikersinvoer niet op tijdens het construeren van de foutieve SQL-instructie, dit kan worden misbruikt voor SQL-injectie
* **Mogelijke SQL-injectie**: deze waarschuwing wordt geactiveerd wanneer een actieve aanval wordt uitgevoerd tegen een geïdentificeerd beveiligingslek voor SQL-injectie in een toepassing. Dit betekent dat de aanvaller schadelijke SQL-instructies probeert te injecteren met de kwetsbare toepassingscode of opgeslagen procedures.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Onveilige opdracht (ondersteund in SQL Server 2019)

* **Mogelijk onveilige actie:** deze waarschuwing wordt geactiveerd wanneer een zeer bevoorrechte en potentieel onveilige opdracht wordt uitgevoerd. Mogelijke oorzaken:
    * Commando die aanbevolen wordt om te worden uitgeschakeld voor een betere beveiligingshouding is ingeschakeld.
    * Een aanvaller die SQL-toegang probeert te misbruiken of bevoegdheden probeert te escaleren.   


## <a name="explore-and-investigate-security-alerts"></a>Beveiligingswaarschuwingen verkennen en onderzoeken

Uw waarschuwingen voor gegevensbeveiliging zijn beschikbaar op de waarschuwingspagina van het Beveiligingscentrum, het beveiligingstabblad van de bron of via de directe koppeling in de waarschuwingse-mails.

1. Ga als volgt te werk als u waarschuwingen wilt weergeven:

    * In Beveiligingscentrum - Klik op **Beveiligingswaarschuwingen** vanaf de zijbalk en selecteer een waarschuwing.
    * In het resourcebereik - Open de relevante resourcepagina en klik vanaf de zijbalk op **Beveiliging**. 

1. Waarschuwingen zijn ontworpen om op zichzelf te staan, met gedetailleerde herstelstappen en onderzoeksinformatie in elk systeem. U het verder onderzoeken door andere Azure Security Center- en Azure Sentinel-mogelijkheden te gebruiken voor een bredere weergave:

    * Schakel de controlefunctie van SQL Server in voor verder onderzoek. Als u een Azure Sentinel-gebruiker bent, u de SQL-controlelogboeken van de gebeurtenissen van het Windows Security Log uploaden naar Sentinel en genieten van een uitgebreide onderzoekservaring.
    * Gebruik de aanbevelingen van Security Center voor de hostmachine die in elke waarschuwing zijn aangegeven, om uw beveiligingshouding te verbeteren. Dit vermindert de risico's van toekomstige aanvallen. 


## <a name="next-steps"></a>Volgende stappen

Zie voor gerelateerd materiaal het volgende artikel:

- [Aanbevelingen herstellen](security-center-remediate-recommendations.md)