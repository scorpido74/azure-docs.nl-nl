---
title: Geavanceerde gegevens beveiliging van Azure Security Center voor SQL-machines (preview)
description: Meer informatie over het inschakelen van geavanceerde gegevens beveiliging voor SQL-machines in Azure Security Center
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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: f159d2cdc48b144d0c75c62cd8a7ba6667424243
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86043866"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>Geavanceerde gegevens beveiliging voor SQL-machines (preview-versie)

De geavanceerde gegevens beveiliging van Azure Security Center voor SQL-machines beveiligt SQL-servers die worden gehost in azure, op andere Cloud omgevingen, en zelfs on-premises machines. Hiermee worden de beveiligingen voor uw Azure-systeem eigen SQL-servers uitgebreid om hybride omgevingen volledig te ondersteunen.

Deze preview-functie bevat functionaliteit voor het identificeren en beperken van potentiële database problemen en het detecteren van afwijkende activiteiten die kunnen wijzen op bedreigingen voor uw Data Base: 

* **Beoordeling van beveiligings** problemen: de scan service om mogelijke beveiligings problemen met een Data Base op te sporen, op te sporen en te helpen oplossen. Evaluatie scans bieden een overzicht van de beveiligings status van uw SQL-machines en Details van eventuele beveiligings resultaten.

* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) : de detectie service die CONTINU uw SQL-servers bewaakt voor bedreigingen, zoals SQL-injectie, brute-force aanvallen en misbruik van bevoegdheden. Deze service biedt actie gerichte beveiligings waarschuwingen in Azure Security Center met details van de verdachte activiteit, richt lijnen voor het oplossen van problemen met de bedreigingen en opties voor het voortzetten van uw onderzoeken met Azure Sentinel.

>[!TIP]
> Geavanceerde gegevens beveiliging voor SQL-machines is een uitbrei ding van het [geavanceerde gegevens beveiligings pakket](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)van Azure Security Center, dat beschikbaar is voor Azure SQL database, Azure Synapse en SQL Managed instance.


## <a name="set-up-advanced-data-security-for-sql-machines"></a>Geavanceerde gegevens beveiliging instellen voor SQL-machines 

Het instellen van de geavanceerde gegevens beveiliging van Azure Security Center voor SQL-machines omvat twee stappen:

* Richt de Log Analytics-agent in op de host van uw SQL-Server. Dit geeft de verbinding met Azure.

* Schakel de optionele bundel in op de pagina prijzen en instellingen van Security Center.

Beide worden hieronder beschreven.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Stap 1. Richt de Log Analytics-agent in op de host van uw SQL-Server:

- **SQL Server op de Azure-VM** : als uw SQL-machine wordt gehost op een Azure-VM, kunt u [de log Analytics agent automatisch inrichten](security-center-enable-data-collection.md#workspace-configuration). U kunt ook de hand matige procedure voor het [toevoegen van een virtuele machine van Azure](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines)volgen.

- **SQL Server op Azure Arc** : als uw SQL Server wordt gehost op een [Azure Arc](https://docs.microsoft.com/azure/azure-arc/) -computer, kunt u de log Analytics agent implementeren met behulp van de Security Center aanbeveling ' Log Analytics agent moet worden geïnstalleerd op uw op Windows gebaseerde Azure Arc-computers (preview) '. U kunt ook de hand matige procedure volgen in de [Azure Arc-documentatie](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).

- **SQL Server on-premises** : als uw SQL Server wordt gehost op een on-premises Windows-machine zonder Azure Arc, hebt u twee opties om deze te verbinden met Azure:
    
    - **Azure-Arc implementeren** : u kunt een Windows-machine verbinden met Security Center. Azure Arc biedt echter een diep gaande integratie in *al* uw Azure-omgeving. Als u Azure Arc hebt ingesteld, ziet u de pagina **SQL Server – Azure Arc** in de portal en worden uw beveiligings waarschuwingen weer gegeven op een specifiek tabblad **beveiliging** op die pagina. De eerste en aanbevolen optie is om [Azure Arc op de host in te stellen](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) en de instructies voor **SQL Server op Azure Arc**te volgen.
        
    - **De Windows-machine verbinden zonder Azure Arc** : als u ervoor kiest om verbinding te maken met een SQL Server die wordt uitgevoerd op een Windows-computer zonder Azure Arc te gebruiken, volgt u de instructies in [Windows-computers verbinden met Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>Stap 2. Schakel de optionele bundel in op de pagina prijzen en instellingen van Security Center:

1. Open op de zijbalk van Security Center de pagina **prijzen &-instellingen** .

    - Als u de **standaard werkruimte van Azure Security Center** gebruikt (met de naam ' defaultworkspace-[uw abonnement-id]-[regio] '), selecteert u het relevante **abonnement**.

    - Als u **een niet-standaard werk ruimte**gebruikt, selecteert u de relevante **werk ruimte** (Voer indien nodig de naam van de werk ruimte in het filter in):

        ![titel](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. Schakel de optie voor **SQL-servers op computers (preview)** in op ingeschakeld. 

    [![Security Center prijs pagina met optionele bundels](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Geavanceerde gegevens beveiliging voor SQL-servers op machines wordt ingeschakeld op alle SQL-servers die zijn verbonden met de geselecteerde werk ruimte. De beveiliging is volledig actief na de eerste keer opnieuw opstarten van de SQL Server. 

    >[!TIP] 
    > Als u een nieuwe werk ruimte wilt maken, volgt u de instructies in [een log Analytics-werk ruimte maken](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).


1. U kunt ook e-mail meldingen configureren voor beveiligings waarschuwingen. 
    U kunt een lijst met ontvangers instellen om een e-mail melding te ontvangen wanneer Security Center waarschuwingen worden gegenereerd. Het e-mail bericht bevat een directe koppeling naar de waarschuwing in Azure Security Center met alle relevante informatie. Zie [e-mail meldingen instellen voor beveiligings waarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)voor meer informatie.



## <a name="explore-vulnerability-assessment-reports"></a>Rapporten van evaluatie van beveiligings problemen verkennen

De service voor evaluatie van beveiligings problemen scant uw data bases eenmaal per week. De scans worden uitgevoerd op dezelfde dag van de week waarop u de service hebt ingeschakeld.

Het dash board evaluatie van beveiligings problemen biedt een overzicht van de evaluatie resultaten voor al uw data bases, samen met een samen vatting van gezonde en beschadigde data bases en een algehele samen vatting van mislukte controles volgens risico distributie.

U kunt de resultaten van de evaluatie van de beveiligings problemen rechtstreeks vanuit Security Center weer geven.

1. Open de pagina **aanbevelingen** vanuit de zijbalk van Security Center en selecteer de aanbevolen **beveiligings problemen op uw SQL-servers op computers moeten worden hersteld (preview)**. Zie [Security Center-aanbevelingen](security-center-recommendations.md)voor meer informatie. 


    [![* * Beveiligings problemen op uw SQL-servers op computers moeten worden hersteld (preview-versie) * * aanbeveling](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    De gedetailleerde weer gave voor deze aanbeveling wordt weer gegeven.

    [![Gedetailleerde weer gave voor de * * beveiligings problemen op uw SQL-servers op computers moet worden hersteld (preview) * * Recommendation](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Voor meer informatie, inzoomen:

    * Selecteer de gewenste server voor een overzicht van gescande resources (data bases) en de lijst met beveiligings controles die zijn getest.

    * Selecteer de gewenste Data Base voor een overzicht van de beveiligings problemen die zijn gegroepeerd op een specifieke SQL database.

    In elke weer gave worden de beveiligings controles gesorteerd op **Ernst**. Klik op een specifieke beveiligings controle om het deel venster Details te bekijken met een **Beschrijving**, het probleem te **verhelpen** en andere gerelateerde informatie, zoals **impact** of **Bench Mark**.

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>Waarschuwingen voor Advanced Threat Protection voor SQL-servers op computers
Waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om SQL-machines te openen of misbruik te maken. Deze gebeurtenissen kunnen waarschuwingen activeren die worden weer gegeven in de [sectie waarschuwingen voor SQL database en SQL Data Warehouse van de pagina waarschuwings referentie](alerts-reference.md#alerts-sql-db-and-warehouse).



## <a name="explore-and-investigate-security-alerts"></a>Beveiligings waarschuwingen verkennen en onderzoeken

Beveiligings waarschuwingen zijn beschikbaar op de pagina waarschuwingen van Security Center, het tabblad Beveiliging van de resource of via de directe koppeling in de e-mail waarschuwingen.

1. Als u waarschuwingen wilt weer geven, selecteert u **beveiligings waarschuwingen** op de zijbalk van Security Center en selecteert u een waarschuwing.

1. Waarschuwingen zijn zodanig ontworpen dat ze zich op zichzelf bevinden, met gedetailleerde stappen voor herbemiddeling en informatie over onderzoek. U kunt verder onderzoeken door andere Azure Security Center en Azure-Sentinel-mogelijkheden te gebruiken voor een bredere weer gave:

    * Schakel de controle functie van SQL Server in voor verdere onderzoeken. Als u een Azure Sentinel-gebruiker bent, kunt u de SQL-controle logboeken uploaden van de gebeurtenissen van het Windows-beveiligings logboek naar Sentinel en een rijke onderzoek ervaring hebben. [Meer informatie over het controleren van SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15).
    * Gebruik de aanbevelingen van Security Center voor de hostmachine die in elke waarschuwing worden aangegeven om uw beveiligings postuur te verbeteren. Hierdoor worden de Risico's van toekomstige aanvallen verminderd. 

    Meer [informatie over het beheren van en reageren op waarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).


## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor verwante materialen:

- [Beveiligings waarschuwingen voor SQL Database en SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)
- [E-mail meldingen instellen voor beveiligings waarschuwingen](security-center-provide-security-contact-details.md)
- [Meer informatie over Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Geavanceerde gegevens beveiligings pakket van Azure Security Center](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)