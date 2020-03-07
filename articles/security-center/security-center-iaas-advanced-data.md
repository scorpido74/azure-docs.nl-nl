---
title: Geavanceerde gegevens beveiliging voor IaaS in Azure Security Center | Microsoft Docs
description: " Meer informatie over het inschakelen van geavanceerde gegevens beveiliging voor IaaS in Azure Security Center. "
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390032"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Geavanceerde gegevens beveiliging voor SQL-servers in azure Virtual Machines (preview-versie)
Geavanceerde gegevens beveiliging voor SQL-servers in azure Virtual Machines is een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Deze preview-functie bevat functionaliteit voor het identificeren en beperken van potentiële database problemen en het detecteren van afwijkende activiteiten die kunnen wijzen op bedreigingen voor uw data base. 

Deze beveiligings aanbieding voor Azure Vm's SQL-servers is gebaseerd op dezelfde fundamentele technologie die wordt gebruikt in het [Azure SQL database geavanceerde gegevens beveiligings pakket](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Overzicht

Geavanceerde gegevens beveiliging biedt een aantal geavanceerde SQL-beveiligings mogelijkheden, bestaande uit evaluatie van beveiligings problemen en geavanceerde beveiliging tegen bedreigingen.

* [Evaluatie van beveiligings problemen](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) is een eenvoudig te configureren service waarmee u mogelijke beveiligings problemen met een Data Base kunt detecteren, bijhouden en helpen oplossen. Het biedt inzicht in de beveiligings status en bevat de stappen voor het oplossen van beveiligings problemen en het uitbreiden van uw data base-Fortifications.
* [Geavanceerde bedreigingen beveiliging](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot uw SQL-Server of deze misbruiken. Het controleert uw data base voortdurend op verdachte activiteiten en biedt actie gerichte beveiligings waarschuwingen voor afwijkende database toegangs patronen. Deze waarschuwingen bieden informatie over verdachte activiteiten en aanbevolen acties voor het onderzoeken en oplossen van de dreiging.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Aan de slag met geavanceerde gegevens beveiliging voor SQL op virtuele machines van Azure

Met de volgende stappen kunt u aan de slag met geavanceerde gegevens beveiliging voor SQL op de open bare preview van Azure Vm's.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Geavanceerde gegevens beveiliging instellen voor SQL op virtuele Azure-machines

Geavanceerde gegevens beveiliging inschakelen voor SQL-servers op Virtual Machines op het niveau van het abonnement/de werk ruimte:
 
1. Open op de zijbalk van Security Center de pagina **prijzen &-instellingen** .

1. Selecteer het abonnement of de werk ruimte waarvoor u geavanceerde gegevens beveiliging wilt inschakelen voor SQL op Azure-Vm's.

1. Schakel de optie voor **SQL-servers in VM (preview)** in op ingeschakeld. 

    (Klik op de scherm opname om uit te vouwen)

    [![Security Center aanbevelingen en waarschuwingen zoals weer gegeven in het Windows-beheer centrum](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Geavanceerde gegevens beveiliging voor SQL-servers wordt ingeschakeld op alle SQL-servers die zijn verbonden met de geselecteerde werk ruimte of de standaard werkruimte van het geselecteerde abonnement.

    >[!NOTE]
    > De oplossing is volledig actief na de eerste keer opnieuw opstarten van de SQL Server. 

Als u een nieuwe werk ruimte wilt maken, volgt u de instructies in [een log Analytics-werk ruimte maken](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Als u de host van de SQL Server wilt verbinden met een werk ruimte, volgt u de instructies in [Windows-computers verbinden met Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


## <a name="set-up-email-notification-for-security-alerts"></a>E-mail meldingen instellen voor beveiligings waarschuwingen 

U kunt een lijst met ontvangers instellen om een e-mail melding te ontvangen wanneer Security Center waarschuwingen worden gegenereerd. Het e-mail bericht bevat een directe koppeling naar de waarschuwing in Azure Security Center met alle relevante informatie. 

1. Ga naar **Security Center** > **prijzen & instellingen** en klik op het betreffende abonnement

    ![Abonnements instellingen](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Klik in het menu instellingen op **e-mail meldingen**. 
1. Voer in het tekstvak **e-mail adres** de e-mail adressen in om de meldingen te ontvangen. U kunt meer dan één e-mail adres invoeren door de e-mail adressen te scheiden door een komma (,).  Bijvoorbeeld admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

    ![E-mailinstellingen](./media/security-center-advanced-iaas-data/email-settings.png)

1. Stel in de instellingen voor **e-mail meldingen** de volgende opties in:
  
    * **E-mail melding verzenden voor waarschuwingen met hoge urgentie**: in plaats van e-mail verzenden voor alle waarschuwingen, verzendt u alleen voor waarschuwingen met hoge urgentie.
    * **Ook e-mail meldingen verzenden naar abonnements eigenaren**: verzend meldingen naar de eigen aars van de abonnementen.

1. Klik boven in het scherm met **e-mail meldingen** op **Opslaan**.

  > [!NOTE]
  > Zorg ervoor dat u op **Opslaan** klikt voordat u het venster sluit of de nieuwe instellingen voor **e-mail meldingen** worden niet opgeslagen.

## <a name="explore-vulnerability-assessment-reports"></a>Rapporten van evaluatie van beveiligings problemen verkennen

Het dash board evaluatie van beveiligings problemen biedt een overzicht van de resultaten van de evaluatie in al uw data bases. U kunt de distributie van data bases weer geven op basis van SQL Server versie, samen met een samen vatting van fouten versus het door geven van data bases en een algehele samen vatting van mislukte controles volgens risico distributie.

U kunt de resultaten van de evaluatie van de beveiligings problemen rechtstreeks vanuit Security Center weer geven.

1. Selecteer op de zijbalk van Security Center onder RESOURCE SECURITY-hygiëne **gegevens & opslag**.

1. Selecteer de aanbevolen **beveiligings problemen voor uw SQL-data bases in vm's moeten worden hersteld (preview-versie)** . Zie [Security Center-aanbevelingen](security-center-recommendations.md)voor meer informatie. 

    [![* * beveiligings problemen voor uw SQL-data bases in Vm's moeten worden hersteld (preview) * * Recommendation](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    De gedetailleerde weer gave voor deze aanbeveling wordt weer gegeven.

    [![gedetailleerde weer gave voor de * * beveiligings problemen voor uw SQL-data bases in Vm's moet worden hersteld (preview-versie) * * aanbeveling](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Meer informatie over inzoomen:

    * Voor een overzicht van gescande resources (data bases) en de lijst met beveiligings controles die zijn getest, klikt u op de server van belang.
    [![beveiligings problemen die zijn gegroepeerd op SQL Server](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Voor een overzicht van de beveiligings problemen gegroepeerd op een specifieke SQL database, klikt u op de gewenste data base.
    [![beveiligings problemen die zijn gegroepeerd op SQL Server](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    In elke weer gave worden de beveiligings controles gesorteerd op **Ernst**. Klik op een specifieke beveiligings controle om het deel venster Details te bekijken met een **Beschrijving**, het probleem te **verhelpen** en andere gerelateerde informatie, zoals **impact** of **Bench Mark**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Geavanceerde bedreigings beveiliging voor SQL-servers op Azure Vm's-waarschuwingen
Waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om SQL-servers te openen of misbruik te maken. Deze gebeurtenissen kunnen de volgende waarschuwingen activeren:

### <a name="anomalous-access-pattern-alerts-preview"></a>Waarschuwingen voor afwijkende toegangs patronen (preview-versie)

* **Toegang vanaf ongebruikelijke locatie:** Deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor SQL Server, waarbij iemand zich vanaf een ongebruikelijke geografische locatie heeft aangemeld bij de SQL-Server. Mogelijke oorzaken:
    * Een aanvaller of voormalig kwaad aardig gebruik heeft toegang verkregen tot uw SQL Server.
    * Een rechtmatige gebruiker heeft uw SQL Server vanaf een nieuwe locatie geopend.
* **Toegang tot een toepassing die mogelijk schadelijk is**: deze waarschuwing wordt geactiveerd wanneer een mogelijk schadelijke toepassing wordt gebruikt voor toegang tot de database. Mogelijke oorzaken:
    * Een aanvaller probeert uw SQL te schenden met behulp van gang bare hulpprogram ma's voor aanvallen.
    * Een rechtmatige indringings test in actie.
* **Toegang vanaf ongebruikelijke klant**: deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangspatroon tot de SQL-server, waarbij iemand zich vanuit een ongebruikelijke klant (SQL-gebruiker) heeft aangemeld bij de SQL-server. Mogelijke oorzaken:
    * Een aanvaller of voormalig kwaad aardig gebruik heeft toegang verkregen tot uw SQL Server. 
    * Een rechtmatige gebruiker heeft uw SQL Server geopend vanuit een nieuwe principal.
* **Brute Force SQL-referenties**: deze waarschuwing wordt geactiveerd wanneer er een abnormaal groot aantal mislukte aanmeldingen met andere referenties is. Mogelijke oorzaken:
    * Een aanvaller probeert uw SQL te schenden met behulp van een brute kracht.
    * Een rechtmatige indringings test in actie.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Potentiële SQL-injectie aanvallen (ondersteund in SQL Server 2019)

* **Beveiligings probleem met SQL-injectie**: deze waarschuwing wordt geactiveerd wanneer een toepassing een mislukte SQL-instructie in de data base genereert. Deze waarschuwing kan duiden op een mogelijk beveiligingsprobleem met SQL-injectieaanvallen. Mogelijke oorzaken:
    * Een fout in de toepassingscode die de foutieve SQL-instructie maakt
    * Toepassingscode of opgeslagen procedures schonen gebruikersinvoer niet op tijdens het construeren van de foutieve SQL-instructie, dit kan worden misbruikt voor SQL-injectie
* **Mogelijke SQL-injectie**: deze waarschuwing wordt geactiveerd wanneer een actieve aanval wordt uitgevoerd tegen een geïdentificeerd beveiligingslek voor SQL-injectie in een toepassing. Dit betekent dat de aanvaller schadelijke SQL-instructies probeert te injecteren met de kwetsbare toepassingscode of opgeslagen procedures.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Onveilige opdracht (ondersteund in SQL Server 2019)

* **Mogelijk onveilige actie**: deze waarschuwing wordt geactiveerd wanneer een zeer bevoegde en mogelijk onveilige opdracht wordt uitgevoerd. Mogelijke oorzaken:
    * De opdracht die moet worden uitgeschakeld voor betere beveiliging postuur is ingeschakeld.
    * Een aanvaller probeert SQL Access te gebruiken of bevoegdheden te escaleren.   


## <a name="explore-and-investigate-security-alerts"></a>Beveiligings waarschuwingen verkennen en onderzoeken

Uw gegevens beveiligings waarschuwingen zijn beschikbaar op de pagina waarschuwingen van Security Center, het tabblad Beveiliging van de resource of via de directe koppeling in de e-mail berichten met waarschuwingen.

1. Waarschuwingen weer geven:

    * In Security Center klikt u op **beveiligings waarschuwingen** in de zijbalk en selecteert u een waarschuwing.
    * Open in het resource bereik de relevante resource pagina en klik op de zijbalk op **beveiliging**. 

1. Waarschuwingen zijn zodanig ontworpen dat ze zich op zichzelf bevinden, met gedetailleerde stappen voor herbemiddeling en informatie over onderzoek. U kunt verder onderzoeken door andere Azure Security Center en Azure-Sentinel-mogelijkheden te gebruiken voor een bredere weer gave:

    * Schakel de controle functie van SQL Server in voor verdere onderzoeken. Als u een Azure Sentinel-gebruiker bent, kunt u de SQL-controle logboeken uploaden van de gebeurtenissen van het Windows-beveiligings logboek naar Sentinel en een rijke onderzoek ervaring hebben.
    * Gebruik de aanbevelingen van Security Center voor de hostmachine die in elke waarschuwing worden aangegeven om uw beveiligings postuur te verbeteren. Hierdoor worden de Risico's van toekomstige aanvallen verminderd. 


## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor verwante materialen:

- [Aanbevelingen herstellen](security-center-remediate-recommendations.md)