---
title: Over Azure Migrate
description: Meer informatie over de Azure Migrate-service.
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fe6386346282cf182397f6420c541d629ba0aab3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81768397"
---
# <a name="about-azure-migrate"></a>Over Azure Migrate

Dit artikel bevat een kort overzicht van de Azure Migrate-service.

Azure Migrate biedt een gecentraliseerde hub voor het beoordelen en migreren van on-premises servers, infra structuur, toepassingen en gegevens in Azure.

Azure Migrate biedt de volgende functies:

- **Uniform migratie platform**: een enkele portal om uw migratie naar Azure te starten, uit te voeren en bij te houden.
- **Bereik van hulpprogram ma's**: een aantal hulpprogram ma's voor evaluatie en migratie. Hulpprogram ma's omvatten Azure Migrate: Server evaluatie en Azure Migrate: Server migratie. Azure Migrate integreert met andere Azure-Services en met andere hulpprogram ma's en ISV-aanbiedingen (Independent Software Vendor).
- **Evaluatie en migratie**: In de Azure migrate hub kunt u evalueren en migreren:
    - **Servers**: on-premises servers beoordelen en deze migreren naar Azure virtual machines.
    - **Data bases**: on-premises data bases beoordelen en deze migreren naar Azure SQL database of naar een Azure SQL database beheerd exemplaar.
    - **Webtoepassingen**: on-premises webtoepassingen beoordelen en deze migreren naar Azure app service met behulp van de Azure app Service Migration Assistant.
    - **Virtuele Bureau bladen**: Evalueer uw on-premises Virtual Desktop Infrastructure (VDI) en migreer deze naar virtueel bureau blad van Windows in Azure.
    - **Gegevens**: Migreer grote hoeveel heden gegevens snel en kosten effectief met behulp van Azure data Box producten.

## <a name="integrated-tools"></a>Geïntegreerde hulpprogram ma's

De Azure Migrate hub bevat deze hulpprogram ma's:

**Hulpprogramma** | **Beoordelen en migreren** | **Details**
--- | --- | ---
**Azure Migrate: Server evaluatie** | Servers beoordelen. | Ontdek en evalueer on-premises virtuele VMware-machines, Hyper-V-Vm's en fysieke servers in voor bereiding op de migratie naar Azure.
**Azure Migrate: Server migratie** | Servers migreren. | Migreer VMware-Vm's, Hyper-V-Vm's, fysieke servers, andere gevirtualiseerde machines en open bare Cloud-Vm's naar Azure.
**Data Migration Assistant** | On-premises SQL Server data bases evalueren voor migratie naar Azure SQL Database, een Azure SQL Database beheerde instantie of Azure-Vm's waarop SQL Server wordt uitgevoerd. | Data Migration Assistant helpt mogelijke problemen met het blok keren van de migratie te lokaliseren. Deze bevat niet-ondersteunde functies, nieuwe functies die u na de migratie kunnen voor delen en het juiste pad voor database migratie. [Meer informatie](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Azure Database Migration Service** | Migreer on-premises data bases naar Azure-Vm's waarop SQL Server, Azure SQL Database of Azure SQL Database beheerde exemplaren worden uitgevoerd. | Meer [informatie](https://docs.microsoft.com/azure/dms/dms-overview) over database Migration service.
**Movere** | Servers beoordelen. | [Meer informatie](#movere) over indrijfen.
**Web-app-Migratie-assistent** | On-premises web-apps beoordelen en deze migreren naar Azure. |  Gebruik Azure App Service Migration Assistant om on-premises websites te evalueren voor migratie naar Azure App Service.<br/><br/> Gebruik Migration Assistant om .NET-en PHP-web-apps te migreren naar Azure. Meer [informatie](https://appmigration.microsoft.com/) over Azure app Service Migration Assistant.
**Azure Data Box** | Offline gegevens migreren. | Gebruik Azure Data Box producten om grote hoeveel heden offline gegevens naar Azure te verplaatsen. [Meer informatie](https://docs.microsoft.com/azure/databox/).

> [!NOTE]
> Als u zich in Azure Government bevindt, kunnen externe, geïntegreerde hulpprogram ma's en ISV-aanbiedingen geen gegevens verzenden naar Azure Migrate projecten. U kunt hulpprogram ma's onafhankelijk gebruiken.

## <a name="isv-integration"></a>ISV-integratie

Azure Migrate integreert met verschillende ISV-aanbiedingen. 

**ISV**    | **Functie**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Servers migreren.
[Cloudamize](https://www.cloudamize.com/platform) | Servers beoordelen.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Servers beoordelen en migreren.
[Device42](https://docs.device42.com/) | Servers beoordelen.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Bepaal VDI.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Servers migreren.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Servers beoordelen.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Servers en data bases beoordelen.

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: hulp programma voor Server evaluatie

Met de Azure Migrate: Server Assessment kunt u on-premises VMware-Vm's, Hyper-V-Vm's en fysieke servers voor migratie naar Azure detecteren en beoordelen.

Het hulp programma doet het volgende:

- **Azure Readiness**: Hiermee wordt bepaald of on-premises machines gereed zijn voor migratie naar Azure.
- **Azure-grootte**: Hiermee wordt de grootte van virtuele Azure-machines geschat na de migratie.
- **Schatting**van de kosten van Azure: schatting van de kosten voor het uitvoeren van on-premises servers in Azure.
- **Afhankelijkheids analyse**: identificeert afhankelijkheden tussen servers en optimalisatie strategieën voor het verplaatsen van onafhankelijke servers naar Azure. Meer informatie over server evaluatie met [afhankelijkheids analyse](concepts-dependency-visualization.md).

Server evaluatie maakt gebruik van een licht gewicht [Azure migrate apparaat](migrate-appliance.md) dat u on-premises implementeert.

- Het apparaat wordt uitgevoerd op een virtuele machine of op een fysieke server. U kunt het eenvoudig installeren met behulp van een gedownloade sjabloon.
- Het apparaat detecteert on-premises machines. Ook worden er voortdurend meta gegevens en prestatie gegevens van de computer naar Azure Migrate verzonden.
- De detectie van het apparaat is zonder agent. Er wordt niets geïnstalleerd op gedetecteerde computers.
- Na de detectie van het apparaat kunt u gedetecteerde computers in groepen verzamelen en evaluaties uitvoeren voor elke groep.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: hulp programma voor server migratie

Met de Azure Migrate: hulp programma voor server migratie kunt u migreren naar Azure:

- On-premises VMware VM's
- Virtuele Hyper-V-machines
- Fysieke servers
- Andere gevirtualiseerde machines
- Vm's voor open bare Cloud

U kunt machines migreren nadat u ze hebt geëvalueerd of ze hebt gemigreerd zonder een evaluatie.

Voor de migratie van virtuele VMware-machines en migratie van Hyper-V-Vm's maakt de server migratie gebruik van een Azure Migrate apparaat dat u on-premises implementeert. Het apparaat wordt ook gebruikt als u Server evaluatie hebt ingesteld. Dit wordt beschreven in de vorige sectie.

## <a name="selecting-assessment-and-migration-tools"></a>Hulpprogram ma's voor evaluatie en migratie selecteren

In de Azure Migrate hub selecteert u het hulp programma dat u wilt gebruiken voor evaluatie of migratie en voegt u het toe aan een Azure Migrate-project. Als u een ISV-hulp programma of een verwerkings functie toevoegt:

- Als u aan de slag wilt gaan, moet u een licentie aanvragen of zich aanmelden voor een gratis proef versie door de instructies van het hulp programma te volgen. Elke ISV of elk hulp programma specificeert licentie voor het hulp programma.
- Elk hulp programma heeft een optie om verbinding te maken met Azure Migrate. Volg de instructies van het hulp programma om verbinding te maken.
- Volg uw migratie over alle hulpprogram ma's vanuit het Azure Migrate project.

## <a name="movere"></a>Movere

De verhuis wagen is een SaaS-platform (Software as a Service). Het verhoogt business intelligence door nauw keurig volledige IT-omgevingen binnen één dag te presen teren. Organisaties en ondernemingen groeien, wijzigen en digitaal optimaliseren. Als dit het geval is, biedt de indrijfer hen het benodigde vertrouwen om hun omgevingen, ongeacht het platform, de toepassing of de geografie, te bekijken en te beheren.

Micro soft heeft de verhuis wagen [overgenomen](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) en wordt niet meer verkocht als een zelfstandige aanbieding. De verhuis wagen is beschikbaar via het micro soft-programma voor de evaluatie van oplossingen en de Microsoft Cloud economie. [Meer informatie](https://www.movere.io) over indrijfen.

We raden u aan om ook Azure Migrate, onze ingebouwde migratie service, te bekijken. Azure Migrate biedt een centrale hub om uw Cloud migratie te vereenvoudigen. De hub biedt uitgebreide ondersteuning voor werk belastingen zoals fysieke en virtuele servers, data bases en toepassingen. Met end-to-end zicht baarheid kunt u de voortgang van de detectie, evaluatie en migratie eenvoudig bijhouden.

Met zowel Azure-als partner-ISV-hulpprogram ma's die zijn ingebouwd, heeft Azure Migrate een uitgebreid scala aan functies, waaronder:

- Detectie van virtuele en fysieke servers.
- Supportte op basis van prestaties.
- Kosten planning.
- Evaluaties op basis van een import.
- Afhankelijkheids analyse van toepassingen zonder agent.

Als u op zoek bent naar deskundige hulp om aan de slag te gaan, heeft micro soft bekwaamere [Azure experts managed service providers](https://azure.microsoft.com/partners) om u te helpen. Bekijk de [Azure migrate-website](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Azure Migrate versies

Er zijn twee versies van de Azure Migrate-service.

- **Huidige versie**: gebruik deze versie om Azure migrate projecten te maken, on-premises machines te detecteren en beoordelingen en migraties te organiseren. Meer [informatie](whats-new.md) over wat er nieuw is in deze versie.
- **Vorige versie**: de vorige versie van Azure migrate ondersteunt alleen de evaluatie van on-premises VMware-vm's. Als u de vorige versie hebt gebruikt, moet u nu de huidige versie gebruiken. U kunt niet meer Azure Migrate projecten maken met de vorige versie. En we raden u aan om geen nieuwe detecties te maken.

    Als u toegang wilt krijgen tot bestaande projecten in de Azure Portal, zoekt en selecteert u **Azure migrate**. Het **Azure migrate** dash board heeft een melding en een koppeling voor toegang tot oude Azure migrate projecten.

## <a name="next-steps"></a>Volgende stappen

- Probeer onze zelf studies om virtuele [VMware-machines](tutorial-prepare-vmware.md), [virtuele Hyper-V-machines](tutorial-prepare-hyper-v.md)of [fysieke servers](tutorial-prepare-physical.md)te beoordelen.
- [Bekijk de veelgestelde vragen](resources-faq.md) over Azure Migrate.
