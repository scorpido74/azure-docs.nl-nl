---
title: Over Azure Migrate
description: Meer informatie over de Azure Migrate-service.
ms.topic: overview
ms.date: 03/22/2020
ms.custom: mvc
ms.openlocfilehash: a9723f15d496393d27bdd227ec1121a7878b37e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80127735"
---
# <a name="about-azure-migrate"></a>Over Azure Migrate

In dit artikel vindt u een snel overzicht van de Azure Migrate-service.

Azure Migrate biedt een gecentraliseerde hub voor het beoordelen en migreren van on-premises servers, infrastructuur, toepassingen en gegevens naar Azure. Azure Migrate biedt de volgende functies:

- **Uniform migratieplatform:** één portal om uw migratiereis naar Azure te starten, uit te voeren en bij te houden.
- **Reeks instrumenten**: Een reeks instrumenten voor beoordeling en migratie. Hulpprogramma's: Azure Migrate: Server Assessment en Azure Migrate: Server Migration. Azure Migrate integreert met andere Azure-services en met andere hulpprogramma's en onafhankelijke softwareleveranciers (ISV)-aanbiedingen.
- **Beoordeling en migratie:** In de Azure Migrate-hub u beoordelen en migreren:
    - **Servers:** On-premises servers beoordelen en migreren naar Azure VM's.
    - **Databases**: On-premises databases beoordelen en migreren naar Azure SQL DB of naar Azure SQL Managed Instance.
    - **Webtoepassingen:** on-premises webtoepassingen beoordelen en migreren naar Azure App Service met behulp van de Azure App Service Assistant.
    - **Virtuele bureaubladen:** beoordeel en migreer uw on-premises virtuele desktopinfrastructuur (VDI) naar Windows Virtual Desktop in Azure.
    - **Gegevens:** migreer snel en kosteneffectief grote hoeveelheden gegevens naar Azure met Azure Data Box-producten. 


## <a name="integrated-tools"></a>Geïntegreerde gereedschappen

De Azure Migrate-hub bevat deze hulpprogramma's.

**Hulpprogramma** | **Beoordelen/migreren** | **Details**
--- | --- | ---
**Azure Migreren:Serverbeoordeling** | Beoordeel servers. | Ontdek en beoordeel on-premises VM's vMware, Hyper-V VM's en fysieke servers ter voorbereiding op migratie naar Azure.
**Azure Migreren:Servermigratie** | Servers migreren. | Migreer VMware VM's, Hyper-V VM's, fysieke servers, andere gevirtualiseerde machines en openbare cloud VM's naar Azure. 
**Databasemigratieassistent (DMA)** | On-premises SQL Server-databases beoordelen op migratie naar Azure SQL DB, Azure SQL Managed Instance of naar Azure VM's met SQL Server. | DMA helpt bij het lokaliseren van potentiële blokkeringsproblemen voor migratie. Het identificeert niet-ondersteunde functies, nieuwe functies waarvan u profiteren na de migratie en helpt u het juiste pad voor databasemigratie te identificeren. [Meer informatie](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Databasemigratieservice (DMS)** | Migreren on-premises databases naar Azure VM's met SQL, Azure SQL DB en Azure SQL Managed Instances. | [Meer informatie](https://docs.microsoft.com/azure/dms/dms-overview) over DMS.
**Movere** | Beoordeel servers. | [Meer informatie](#movere) over Movere.
**Web App-migratieassistent** | On-premises web-apps beoordelen en migreren naar Azure. |  Gebruik de Azure App Service Migration Assistant om on-premises websites te beoordelen op migratie naar de Azure App Service.<br/><br/> Gebruik de Assistent om .NET- en PHP-webapps te migreren naar Azure. [Meer informatie](https://appmigration.microsoft.com/) over de Azure App Service Migration Assistant.
**Azure Data Box** | Offline gegevensmigratie. | Gebruik Azure Data Box-producten om grote hoeveelheden gegevens offline naar Azure te verplaatsen. [Meer informatie](https://docs.microsoft.com/azure/databox/).

## <a name="isv-integration"></a>ISV-integratie

Azure Migrate integreert met een aantal ISV-aanbiedingen. 

**Isv**    | **Functie**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Servers migreren
[Cloudamize](https://www.cloudamize.com/platform) | Servers beoordelen
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Servers beoordelen en migreren
[Apparaat 42](https://docs.device42.com/) | Servers beoordelen
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Vdi beoordelen
[RackWare (RackWare)](https://go.microsoft.com/fwlink/?linkid=2102735) | Servers migreren
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Servers beoordelen
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Servers en databases beoordelen


## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate Server Assessment Tool

Het hulpprogramma Azure Migrate:Server Assessment detecteert en beoordeelt on-premises Vm's vMware, Hyper-V VM's en fysieke servers voor migratie naar Azure. Dit is wat de tool doet:

- **Azure-gereedheid:** Beoordeelt of on-premises machines klaar zijn voor migratie naar Azure.
- **Azure-formaat:** Schat de grootte van Azure VM's na migratie.
- **Schatting van Azure-kosten:** Geschatte kosten voor het uitvoeren van on-premises servers in Azure.
- **Afhankelijkheidsanalyse:** Als u Serverbeoordeling gebruikt met [afhankelijkheidsanalyse,](concepts-dependency-visualization.md)u effectief afhankelijkheden van meerdere servers identificeren en strategieën optimaliseren voor het verplaatsen van onderling afhankelijke servers naar Azure.


Server assessment maakt gebruik van een lichtgewicht [Azure Migrate-toestel](migrate-appliance.md) dat u on-premises implementeert.

- Het toestel draait op een VM of fysieke server. U het eenvoudig installeren met behulp van een gedownloade sjabloon.
- Het toestel detecteert on-premises machines en stuurt voortdurend machinemetadata en prestatiegegevens naar Azure Migrate.
- Toesteldetectie is agentloos. Er is niets geïnstalleerd op ontdekte machines.
- Na de detectie van het apparaat verzamelt u gedetecteerde machines in groepen en voert u beoordelingen uit voor een groep.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate-servermigratiehulpprogramma

Met het hulpprogramma Azure Migreren:Servermigratie u on-premises Vm's vMware, Hyper-V VM's, fysieke servers, andere gevirtualiseerde machines en openbare cloudVM's migreren naar Azure. U machines migreren nadat u ze hebt beoordeeld of zonder beoordeling migreren. 

Voor agentloze migratie van Vm's zonder agent en migratie van Hyper V VM's maakt servermigratie gebruik van een Azure Migrate-toestel dat u on-premises implementeert. Het toestel wordt ook gebruikt als u serverbeoordeling instelt en het wordt beschreven in de vorige sectie.


## <a name="selecting-assessmentmigration-tools"></a>Beoordelings-/migratietools selecteren

In de azure migrate hub selecteert u het hulpprogramma dat u wilt gebruiken voor beoordeling of migratie en voegt u het toe aan een Azure Migrate-project. Als u een ISV-gereedschap of Movere toevoegt:

- Om aan de slag te gaan, krijgt u een licentie, of meld u aan voor een gratis proefperiode, in overeenstemming met de tool instructies. Tools licenties wordt bepaald door de ISV of tool. 
- In elk hulpprogramma is er een optie om verbinding te maken met Azure Migrate. Volg de gereedschapsinstructies om verbinding te maken.
- U volgt uw migratiereis vanuit het Azure Migrate-project, voor alle hulpprogramma's.


## <a name="movere"></a>Movere

Movere is een SaaS-platform dat de business intelligence verhoogt door volledige IT-omgevingen binnen één dag nauwkeurig te presenteren. Naarmate organisaties groeien, veranderen en digitaal optimaliseren, biedt de oplossing ondernemingen het vertrouwen dat ze nodig hebben om zichtbaarheid en controle over hun omgevingen te hebben, ongeacht platform, toepassing of geografie. Movere werd [overgenomen](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) door Microsoft en wordt niet langer verkocht als een standalone aanbieding.  Movere is beschikbaar via de Microsoft Solution Assessment- en Cloud Economics-programma's. [Meer informatie](https://www.movere.io) over Movere. 

We raden u aan om ook te kijken naar Azure Migrate, onze ingebouwde migratieservice. Azure Migrate biedt een centrale hub om uw migratie naar de cloud te vereenvoudigen. De hub biedt uitgebreide ondersteuning voor verschillende workloads, waaronder fysieke en virtuele servers, databases en toepassingen. End-to-end zichtbaarheid maakt het gemakkelijk om de voortgang bij te houden tijdens ontdekking, beoordeling en migratie. Met zowel Azure- als partner-ISV-tools ingebouwd, heeft Azure Migrate ook een uitgebreid scala aan functies, waaronder virtuele en fysieke serverdetectie, prestatiegebaseerde juiste grootte, kostenplanning, beoordelingen op basis van import en agentless toepassing afhankelijkheidsanalyse. Als u op zoek bent naar deskundige hulp om aan de slag te gaan, heeft Microsoft een ervaren [Azure Expert Managed Service Provider](https://azure.microsoft.com/partners) om u op weg te helpen. Bekijk de [Azure Migrate-website](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Azure-migratieversies

Er zijn twee versies van de Azure Migrate-service:

- **Huidige versie**: Gebruik deze versie om Azure Migrate-projecten te maken, on-premises machines te ontdekken en beoordelingen en migraties te orkestreren. [Meer informatie](whats-new.md) over wat er nieuw is in deze versie.
- **Vorige versie**: Als u de vorige versie van Azure Migrate hebt gebruikt (alleen beoordeling van on-premises VMware VM's werd ondersteund), moet u nu de huidige versie gebruiken. U Azure Migrate-projecten niet langer maken met behulp van de vorige versie en we raden u aan geen nieuwe ontdekkingen uit te voeren. Als u toegang wilt krijgen tot bestaande projecten, zoekt en selecteert u **Azure Migrate**in de Azure-portal . Op het **Azure Migrate-dashboard** is er een melding en een koppeling om toegang te krijgen tot oude Azure Migrate-projecten.



## <a name="next-steps"></a>Volgende stappen

- Probeer onze tutorials om [VMware VM's,](tutorial-prepare-vmware.md) [Hyper-V VM's](tutorial-prepare-hyper-v.md)of [fysieke servers](tutorial-prepare-physical.md)te beoordelen.
- [Bekijk de veelgestelde vragen](resources-faq.md) over Azure Migrate.
