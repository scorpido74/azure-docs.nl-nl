---
title: Aanbevelingen herstellen in Azure Security Center | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u aanbevelingen herstellen in Azure Security Center om uw resources te beschermen en te voldoen aan het beveiligingsbeleid.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603494"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Aanbevelingen oplossen in Azure Security Center

Aanbevelingen geven u suggesties over hoe u uw resources beter beveiligen. U implementeert een aanbeveling door de herstelstappen in de aanbeveling te volgen.

## <a name="remediation-steps"></a>Herstelstappen<a name="remediation-steps"></a>

Na het bekijken van alle aanbevelingen, beslissen welke eerst te saneren. We raden u aan de impact van de [beveiligde score](security-center-recommendations.md#monitor-recommendations) te gebruiken om prioriteit te geven aan wat u eerst moet doen.

1. Klik in de lijst op de aanbeveling.

1. Volg de instructies in de sectie **Herstelstappen.** Elke aanbeveling heeft zijn eigen set van instructies. In de volgende schermafbeelding worden herstelstappen weergegeven voor het configureren van toepassingen om alleen verkeer via HTTPS toe te staan.

    ![Aanbevelingsdetails](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Nadat u bent voltooid, wordt u op de hoogte gesteld of de herstelprocedure is geslaagd.

## <a name="quick-fix-remediation"></a>Herstel van snelle oplossing<a name="one-click"></a>

Met Quick Fix u snel een aanbeveling over meerdere resources herstellen. Het is alleen beschikbaar voor specifieke aanbevelingen. Quick Fix vereenvoudigt de hersteling en stelt u in staat om uw Secure Score snel te verhogen, waardoor de beveiliging van uw omgeving wordt verbeterd.

Ga als een snelle oplossingsherstel:

1. Uit de lijst met aanbevelingen die de **Quick Fix hebben!** label, klik op de aanbeveling.

    [![Selecteer Quick Fix!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Selecteer op het tabblad **Ongezonde resources** de resources waarop u de aanbeveling wilt implementeren en klik op **Herstellen**.

    > [!NOTE]
    > Sommige van de vermelde bronnen kunnen zijn uitgeschakeld, omdat u niet over de juiste machtigingen beschikt om ze te wijzigen.

1. Lees in het bevestigingsvak de details en implicaties van de herstelprocedure.

    ![Snelle oplossing](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > De implicaties worden vermeld in het grijze vak in het venster **Resources herstellen** dat wordt geopend nadat u op **Remediate**hebt geklikt. Ze geven aan welke wijzigingen er gebeuren wanneer u verdergaat met de quick fix-herstel.

1. Voeg indien nodig de relevante parameters in en keur de herstelprocedure goed.

    > [!NOTE]
    > Het kan enkele minuten duren nadat de herstelprocedure is voltooid om de resources op het tabblad **Gezonde resources** te bekijken. Als u de herstelacties wilt weergeven, controleert u het [activiteitenlogboek](#activity-log).

1. Nadat u bent voltooid, wordt u op de hoogte gesteld of de herstelprocedure is geslaagd.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Logboekregistratie voor herstel van snelle oplossing in het activiteitenlogboek<a name="activity-log"></a>

De herstelbewerking maakt gebruik van een sjabloonimplementatie of REST PATCH API-aanroep om de configuratie op de bron toe te passen. Deze bewerkingen zijn aangemeld bij [azure-activiteitenlogboek](../azure-resource-manager/management/view-activity-logs.md).


## <a name="recommendations-with-quick-fix-remediation"></a>Aanbevelingen met quick fix-herstel

|Aanbeveling|Implicatie|
|---|---|
|Controle op SQL-servers moet worden ingeschakeld|Met deze actie sql-controle op deze servers en hun databases worden ingeschakeld. <br>**Opmerking**: <ul><li>Voor elke regio van de geselecteerde SQL-servers wordt een opslagaccount voor het opslaan van controlelogboeken gemaakt en gedeeld door alle servers in die regio.</li><li>Als u een goede controle wilt controleren, verwijdert of wijzigt u de naam van de brongroep of de opslagaccounts niet.</li></ul>|
|Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-beheerde exemplaren|Met deze actie wordt SQL Advanced Data Security (ADS) ingeschakeld op de geselecteerde SQL-beheerde instanties. <br>**Opmerking**: <ul><li>Voor elke regio en resourcegroep van de geselecteerde SQL-beheerde exemplaren wordt een opslagaccount voor het opslaan van scanresultaten gemaakt en gedeeld door alle instanties in die regio.</li><li> ADS wordt in rekening gebracht voor $ 15 per SQL-beheerde instantie.</li></ul>|
|Kwetsbaarheidsbeoordeling moet worden ingeschakeld op uw SQL-beheerde exemplaren|Met deze actie wordt SQL Vulnerability Assessment ingeschakeld voor de geselecteerde SQL managed instances. <br>**Opmerking**:<ul><li>SQL Vulnerability Assessment maakt deel uit van het SQL Advanced Data Security (ADS) pakket. Als ADS nog niet is ingeschakeld, wordt dit automatisch ingeschakeld op de beheerde instantie.</li><li>Voor elke regio en resourcegroep van de geselecteerde SQL-beheerde exemplaren wordt een opslagaccount voor het opslaan van scanresultaten gemaakt en gedeeld door alle instanties in die regio.</li><li>Ads wordt in rekening gebracht voor $ 15 per SQL-server.</li></ul>||
|Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-servers|Met deze actie kunnen Advanced Data Security (ADS) op deze geselecteerde servers en hun databases worden ingeschakeld. <br>**Opmerking**:<ul><li>Voor elke regio en resourcegroep van de geselecteerde SQL-servers wordt een opslagaccount voor het opslaan van scanresultaten gemaakt en gedeeld door alle servers in die regio.<</li><li>Ads wordt in rekening gebracht voor $ 15 per SQL-server.</li></ul>||
|Kwetsbaarheidsbeoordeling moet worden ingeschakeld op uw SQL-servers|Met deze actie wordt SQL Vulnerability Assessment ingeschakeld op deze geselecteerde servers en hun databases. <br>**Opmerking**:<ul><li>SQL Vulnerability Assessment maakt deel uit van het SQL Advanced Data Security (ADS) pakket. Als ADS nog niet is ingeschakeld, wordt dit automatisch ingeschakeld op de SQL-server.</li><li>Voor elke regio en resourcegroep van de geselecteerde SQL-servers wordt een opslagaccount voor het opslaan van scanresultaten gemaakt en gedeeld door alle instanties in die regio.</li><li>Ads wordt in rekening gebracht voor $ 15 per SQL-server.</li></ul>||
|Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld|Met deze actie kan SQL Database Transparent Data Encryption (TDE) in de geselecteerde databases worden ingeschakeld. <br>**Opmerking**: Standaard worden servicebeheerde TDE-sleutels gebruikt.
|Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld|Met deze actie wordt de beveiliging van uw opslagaccount bijgewerkt om alleen aanvragen via beveiligde verbindingen toe te staan. (HTTPS). <br>**Opmerking**:<ul><li>Alle aanvragen met HTTP worden afgewezen.</li><li>Wanneer u de Azure-bestandenservice gebruikt, mislukt de verbinding zonder versleuteling, inclusief scenario's met SMB 2.1, SMB 3.0 zonder versleuteling en enkele smaken van de Linux SMB-client. Meer informatie.</li></ul>|
|Webtoepassing mag alleen toegankelijk zijn via HTTPS|Met deze actie wordt al het verkeer van HTTP naar HTTPS omgeleid naar de geselecteerde bronnen. <br>**Opmerking**:<ul><li>Een HTTPS-eindpunt zonder SSL-certificaat wordt weergegeven in de browser met een 'Privacy error'. Gebruikers die een aangepast domein hebben, moeten dus controleren of ze een SSL-certificaat hebben ingesteld.</li><li>Zorg ervoor dat firewalls voor pakketten en webtoepassingen de app-service beschermen en dat https-sessies worden doorgestuurd.</li></ul>|
|Functie-app mag alleen toegankelijk zijn via HTTPS|Met deze actie wordt al het verkeer van HTTP naar HTTPS omgeleid naar de geselecteerde bronnen. <br>**Opmerking**:<ul><li>Een HTTPS-eindpunt zonder SSL-certificaat wordt weergegeven in de browser met een 'Privacy error'. Gebruikers die een aangepast domein hebben, moeten dus controleren of ze een SSL-certificaat hebben ingesteld.</li><li>Zorg ervoor dat firewalls voor pakketten en webtoepassingen de app-service beschermen en dat https-sessies worden doorgestuurd.</li></ul>|
|API-app mag alleen toegankelijk zijn via HTTPS|Met deze actie wordt al het verkeer van HTTP naar HTTPS omgeleid naar de geselecteerde bronnen. <br>**Opmerking**:<ul><li>Een HTTPS-eindpunt zonder SSL-certificaat wordt weergegeven in de browser met een 'Privacy error'. Gebruikers die een aangepast domein hebben, moeten dus controleren of ze een SSL-certificaat hebben ingesteld.</li><li>Zorg ervoor dat firewalls voor pakketten en webtoepassingen de app-service beschermen en dat https-sessies worden doorgestuurd.</li></ul>|
|Externe foutopsporing moet worden uitgeschakeld voor webtoepassing|Met deze actie wordt het opsporen van externe fouten op afstand uitgeschakeld.|
|Externe foutopsporing moet worden uitgeschakeld voor functie-app|Met deze actie wordt het opsporen van externe fouten op afstand uitgeschakeld.|
|Externe foutopsporing moet worden uitgeschakeld voor API-app|Met deze actie wordt het opsporen van externe fouten op afstand uitgeschakeld.|
|CORS mag niet toestaan dat elke bron toegang krijgt tot uw webtoepassing|Met deze actie worden andere domeinen uitgesloten van toegang tot uw webtoepassing. Als u specifieke domeinen wilt toestaan, voert u deze in het veld Toegestane oorsprong (gescheiden door komma's). <br>**Opmerking**: Als u het veld leeg laat, worden alle oproepen van cross-origine geblokkeerd.'Param-veldtitel: 'Toegestane oorsprong'|
|CORS mag niet toestaan dat elke resource toegang krijgt tot uw functie-app|Met deze actie worden andere domeinen uitgesloten van toegang tot uw functietoepassing. Als u specifieke domeinen wilt toestaan, voert u deze in het veld Toegestane oorsprong (gescheiden door komma's). <br>**Opmerking**: Als u het veld leeg laat, worden alle oproepen van cross-origine geblokkeerd.'Param-veldtitel: 'Toegestane oorsprong'|
|CORS mag niet toestaan dat elke bron toegang krijgt tot uw API-app|Met deze actie worden andere domeinen uitgesloten van toegang tot uw API-toepassing. Als u specifieke domeinen wilt toestaan, voert u deze in het veld Toegestane oorsprong (gescheiden door komma's). <br>**Opmerking**: Als u het veld leeg laat, worden alle oproepen van cross-origine geblokkeerd.'Param-veldtitel: 'Toegestane oorsprong'|
|Monitoringagent moet ingeschakeld zijn op uw virtuele machines|Met deze actie wordt een bewakingsagent op de geselecteerde virtuele machines geïnstalleerd. Selecteer een werkruimte waaraan de agent moet rapporteren.<ul><li>Als uw updatebeleid is ingesteld op automatisch, wordt het geïmplementeerd op nieuwe bestaande exemplaren.</li><li>Als uw updatebeleid is ingesteld op handmatig en u de agent op bestaande instanties wilt installeren, schakelt u de optie selectievakje in. [Meer informatie](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Diagnostische logboeken in Key Vault moeten zijn ingeschakeld|Met deze actie kunnen diagnostische logboeken op belangrijke kluizen worden ingeschakeld. Diagnostische logboeken en statistieken worden opgeslagen in de geselecteerde werkruimte.|
|Diagnostische logboeken in servicebus moeten zijn ingeschakeld|Met deze actie kunnen diagnostische logboeken op de servicebus worden ingeschakeld. Diagnostische logboeken en statistieken worden opgeslagen in de geselecteerde werkruimte.|

## <a name="next-steps"></a>Volgende stappen

In dit document werd u getoond hoe u aanbevelingen herstellen in Security Center. Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Beveiligingsbeleid instellen in Azure Security Center](tutorial-security-policy.md) - Meer informatie over het configureren van beveiligingsbeleid voor uw Azure-abonnementen en brongroepen.
* [Beveiligingsstatusbewaking in Azure Security Center](security-center-monitoring.md) - Meer informatie over het bewaken van de status van uw Azure-resources.
