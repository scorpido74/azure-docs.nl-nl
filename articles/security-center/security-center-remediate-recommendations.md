---
title: Aanbevelingen herstellen in Azure Security Center | Microsoft Docs
description: In dit document wordt uitgelegd hoe u aanbevelingen in Azure Security Center herstelt om uw Azure-resources te beschermen en te blijven voldoen aan het beveiligings beleid.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2019
ms.author: v-mohabe
ms.openlocfilehash: a32e344ffe33f411bae85763ae3b919040c1109b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575612"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Aanbevelingen herstellen in Azure Security Center

Aanbevelingen bieden suggesties voor een betere beveiliging van uw resources.  U implementeert een aanbeveling door de herstels tappen te volgen die worden beschreven in de aanbeveling. 

## Herstels tappen<a name="remediation-steps"></a>

Nadat u alle aanbevelingen hebt bekeken, moet u bepalen welke u het eerst wilt herstellen. U wordt aangeraden de effect op een [beveiligde Score](security-center-recommendations.md#monitor-recommendations) te gebruiken om te helpen bepalen wat u eerst moet doen.

1. Klik in de lijst op de aanbeveling.
1. Volg de instructies in de sectie **herstel stappen** . Elke aanbeveling heeft een eigen set instructies. Hieronder vindt u de herstels tappen voor het configureren van toepassingen zodat alleen verkeer via HTTPS wordt toegestaan.

    ![Details van de aanbeveling](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Zodra dit is voltooid, wordt een melding weer gegeven dat u wordt geïnformeerd als het herstel is geslaagd.

## Herstel oplossen met één klik (preview-versie)<a name="one-click"></a>

Met één klik kunt u een aanbeveling voor een groot aantal resources herstellen met één klik. Het is een optie die alleen beschikbaar is voor specifieke aanbevelingen. Met één klik op correctie vereenvoudigt u herstel en kunt u snel uw beveiligde Score verbeteren en de beveiliging in uw omgeving verhogen.

Voor het implementeren van één klik op herbemiddeling:

1. Klik in de lijst met aanbevelingen die het label **1-klik-Fix** hebben op de aanbeveling.  

   ![Selecteer een oplossing met één klik](./media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)

2. Op het tabblad **beschadigde bronnen** selecteert u de resources waarvoor u de aanbeveling wilt implementeren en klikt u op **herstellen**. 

    > [!NOTE]
    > Sommige van de vermelde bronnen zijn mogelijk uitgeschakeld, omdat u niet over de juiste machtigingen beschikt om deze te wijzigen.

3. In het bevestigings venster leest u de details van het herstel en de implicaties. 

   ![Oplossen met één klik](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > De implicaties worden weer gegeven in het grijze vak in het venster **resources herstellen** dat wordt geopend nadat u op **herstellen**hebt geklikt. Ze geven een lijst weer met de wijzigingen die worden aangebracht wanneer u doorgaat met de 1-klik op herstel.

4. Voer indien nodig de relevante para meters in en goed keuring van het herstel.

    > [!NOTE]
    > Het kan enkele minuten duren voordat het herstel is voltooid om de resources te bekijken op het tabblad onterecht **resources** . Controleer het [activiteiten logboek](#activity-log)om de herstel acties weer te geven.

5. Zodra dit is voltooid, wordt een melding weer gegeven dat u wordt geïnformeerd als het herstel is geslaagd.

## Logboek registratie met één klik in het activiteiten logboek<a name="activity-log"></a>

Voor de herstel bewerking wordt gebruikgemaakt van een sjabloon implementatie of REST PATCH-API-aanroep om de configuratie van de bron toe te passen. Deze bewerkingen worden geregistreerd in het [Azure-activiteiten logboek](../azure-resource-manager/resource-group-audit.md).


## <a name="recommendations-with-one-click-remediation"></a>Aanbevelingen met één klik op herstel

|Aanbeveling|Implicatie|
|---|---|
|Controle op SQL-servers moet zijn ingeschakeld|Met deze actie wordt SQL-controle op deze servers en de bijbehorende data bases ingeschakeld. <br>**Opmerking**: <ul><li>Voor elke regio van de geselecteerde SQL-servers wordt een opslag account voor het opslaan van audit logboeken gemaakt en gedeeld door alle servers in die regio.</li><li>Verwijder of wijzig de naam van de resource groep of de opslag accounts niet om de juiste controle te garanderen.</li></ul>|
|Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw door SQL beheerde instanties|Met deze actie wordt SQL Advanced Data Security (ADS) ingeschakeld voor de geselecteerde SQL Managed instances. <br>**Opmerking**: <ul><li>Voor elke regio en resource groep van de geselecteerde SQL Managed instances wordt een opslag account voor het opslaan van scan resultaten gemaakt en gedeeld door alle instanties in die regio.</li><li> ADS wordt in rekening gebracht op $15 per SQL Managed instance.</li></ul>|
|De evaluatie van beveiligings problemen moet worden ingeschakeld voor uw door SQL beheerde instanties|Met deze actie wordt SQL-evaluatie van beveiligings problemen voor de geselecteerde SQL Managed instances ingeschakeld. <br>**Opmerking**:<ul><li>Evaluatie van SQL-beveiligings problemen maakt deel uit van het SQL Advanced Data Security-pakket (ADS). Als advertenties niet is ingeschakeld, wordt deze automatisch ingeschakeld op het beheerde exemplaar.</li><li>Voor elke regio en resource groep van de geselecteerde SQL Managed instances wordt een opslag account voor het opslaan van scan resultaten gemaakt en gedeeld door alle instanties in die regio.</li><li>ADS wordt in rekening gebracht op $15 per SQL Server.</li></ul>||
|Geavanceerde gegevens beveiliging moet zijn ingeschakeld op uw SQL-servers|Met deze actie wordt Geavanceerd gegevens beveiliging (ADS) ingeschakeld op deze geselecteerde servers en de bijbehorende data bases. <br>**Opmerking**:<ul><li>Voor elke regio en resource groep van de geselecteerde SQL-servers wordt een opslag account voor het opslaan van scan resultaten gemaakt en gedeeld door alle servers in die regio. <</li><li>ADS wordt in rekening gebracht op $15 per SQL Server.</li></ul>||
|De evaluatie van beveiligings problemen moet worden ingeschakeld op uw SQL-servers|Met deze actie wordt SQL-evaluatie van beveiligings problemen op deze geselecteerde servers en de bijbehorende data bases ingeschakeld. <br>**Opmerking**:<ul><li>Evaluatie van SQL-beveiligings problemen maakt deel uit van het SQL Advanced Data Security-pakket (ADS). Als ADS niet al is ingeschakeld, wordt deze automatisch ingeschakeld op de SQL-Server.</li><li>Voor elke regio en resource groep van de geselecteerde SQL-servers wordt een opslag account voor het opslaan van scan resultaten gemaakt en gedeeld door alle instanties in die regio.</li><li>ADS wordt in rekening gebracht op $15 per SQL Server.</li></ul>||
|Transparante gegevens versleuteling voor SQL-data bases moet zijn ingeschakeld|Met deze actie wordt SQL Database Transparent Data Encryption (TDE) ingeschakeld voor de geselecteerde data bases. <br>**Opmerking**: Standaard worden door service beheerde TDE-sleutels gebruikt. 
|Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld|Met deze actie wordt de beveiliging van uw opslag account bijgewerkt zodat alleen aanvragen voor beveiligde verbindingen worden toegestaan. (HTTPS). <br>**Opmerking**:<ul><li>Aanvragen die gebruikmaken van HTTP, worden geweigerd.</li><li>Wanneer u de service Azure files gebruikt, mislukt de verbinding zonder versleuteling, inclusief scenario's met SMB 2,1, SMB 3,0 zonder versleuteling en enkele van de Linux SMB-client.  Meer informatie.</li></ul>|
|Web-App moet alleen toegankelijk zijn via HTTPS|Met deze actie wordt alle verkeer van HTTP naar HTTPS omgeleid voor de geselecteerde resources. <br>**Opmerking**:<ul><li>Een HTTPS-eind punt zonder SSL-certificaat wordt weer gegeven in de browser met een ' privacy-fout '. Gebruikers met een aangepast domein moeten daarom controleren of ze een SSL-certificaat hebben ingesteld.</li><li>Zorg ervoor dat de firewalls van de app service worden beveiligd door pakketten en webtoepassingen, zodat HTTPS-sessies worden doorgestuurd.</li></ul>|
|Functie-App moet alleen toegankelijk zijn via HTTPS|Met deze actie wordt alle verkeer van HTTP naar HTTPS omgeleid voor de geselecteerde resources. <br>**Opmerking**:<ul><li>Een HTTPS-eind punt zonder SSL-certificaat wordt weer gegeven in de browser met een ' privacy-fout '. Gebruikers met een aangepast domein moeten daarom controleren of ze een SSL-certificaat hebben ingesteld.</li><li>Zorg ervoor dat de firewalls van de app service worden beveiligd door pakketten en webtoepassingen, zodat HTTPS-sessies worden doorgestuurd.</li></ul>|
|De API-app mag alleen toegankelijk zijn via HTTPS|Met deze actie wordt alle verkeer van HTTP naar HTTPS omgeleid voor de geselecteerde resources. <br>**Opmerking**:<ul><li>Een HTTPS-eind punt zonder SSL-certificaat wordt weer gegeven in de browser met een ' privacy-fout '. Gebruikers met een aangepast domein moeten daarom controleren of ze een SSL-certificaat hebben ingesteld.</li><li>Zorg ervoor dat de firewalls van de app service worden beveiligd door pakketten en webtoepassingen, zodat HTTPS-sessies worden doorgestuurd.</li></ul>|
|Foutopsporing op afstand moet worden uitgeschakeld voor Web-App|Met deze actie wordt externe fout opsporing uitgeschakeld.|
|Fout opsporing op afstand moet worden uitgeschakeld voor functie-app|Met deze actie wordt externe fout opsporing uitgeschakeld.|
|Fout opsporing op afstand moet worden uitgeschakeld voor de API-app|Met deze actie wordt externe fout opsporing uitgeschakeld.|
|CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw webtoepassing|Deze actie blokkeert andere domeinen om toegang te krijgen tot uw webtoepassing. Als u specifieke domeinen wilt toestaan, voert u deze in het veld toegestane oorsprong in (gescheiden door komma's). <br>**Opmerking**: Als u het veld leeg laat, worden alle cross-Origin-aanroepen geblokkeerd. de titel van het parameter veld: Toegestane oorsprongen|
|CORS kunt u voorkomen dat elke resource toegang tot uw functie-App|Deze actie blokkeert andere domeinen om toegang te krijgen tot uw functie toepassing. Als u specifieke domeinen wilt toestaan, voert u deze in het veld toegestane oorsprong in (gescheiden door komma's). <br>**Opmerking**: Als u het veld leeg laat, worden alle cross-Origin-aanroepen geblokkeerd. de titel van het parameter veld: Toegestane oorsprongen|
|CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw API-app|Deze actie blokkeert andere domeinen om toegang te krijgen tot uw API-toepassing. Als u specifieke domeinen wilt toestaan, voert u deze in het veld toegestane oorsprong in (gescheiden door komma's). <br>**Opmerking**: Als u het veld leeg laat, worden alle cross-Origin-aanroepen geblokkeerd. de titel van het parameter veld: Toegestane oorsprongen|
|Monitoring agent moet zijn ingeschakeld op uw virtuele machines|Met deze actie wordt een bewakings agent op de geselecteerde virtuele machines geïnstalleerd. Selecteer een werk ruimte voor de agent die u wilt rapporteren.<ul><li>Als uw update beleid is ingesteld op automatisch, wordt het geïmplementeerd op nieuwe bestaande exemplaren.</li><li>Als uw update beleid is ingesteld op hand matig en u de agent wilt installeren op bestaande exemplaren, schakelt u de optie selectie vakje in.  [Meer informatie](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Diagnostische logboeken in Key Vault moeten worden ingeschakeld|Met deze actie worden Diagnostische logboeken ingeschakeld op sleutel kluizen. Diagnostische logboeken en metrische gegevens worden opgeslagen in de geselecteerde werk ruimte.|
|Diagnostische logboeken in service bus moeten worden ingeschakeld|Met deze actie worden Diagnostische logboeken ingeschakeld op de service bus. Diagnostische logboeken en metrische gegevens worden opgeslagen in de geselecteerde werk ruimte.|

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u aanbevelingen in Security Center kunt herstellen. Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md): Meer informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen en-resource groepen.
* [Beveiligings status controleren in azure Security Center](security-center-monitoring.md): Meer informatie over het controleren van de status van uw Azure-resources.
