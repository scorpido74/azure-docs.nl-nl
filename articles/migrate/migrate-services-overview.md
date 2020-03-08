---
title: Over Azure Migrate
description: Meer informatie over de Azure Migrate-service.
ms.topic: overview
ms.date: 12/29/2019
ms.custom: mvc
ms.openlocfilehash: c336b2f95a17896edcc7eaad2b2586c6859804f9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361925"
---
# <a name="about-azure-migrate"></a>Over Azure Migrate

Dit artikel bevat een kort overzicht van de Azure Migrate-service.

Gebruik Azure Migrate om naar Azure te migreren. Azure Migrate biedt een gecentraliseerde hub voor het beoordelen en migreren van on-premises infra structuur, toepassingen en gegevens naar Azure, met de volgende functies:

- **Uniform migratie platform**: een enkele portal om uw migratie traject naar Azure te starten, uit te voeren en bij te houden.
- **Bereik van hulpprogram ma's**: een aantal hulpprogram ma's voor evaluatie en migratie. De hub bevat Azure Migrate: Server evaluatie en Azure Migrate: Server migratie. Het wordt geïntegreerd met andere Azure-Services en met andere hulpprogram ma's en ISV-aanbiedingen (Independent Software Vendor).
- **Beoordeling en migratie**: In de Azure migrate hub kunt u evalueren en migreren:
    - **Servers**: on-premises servers beoordelen en migreren naar Azure-vm's.
    - **Data bases**: on-premises data bases beoordelen en migreren naar Azure SQL DB of naar Azure SQL Managed instance.
    - **Webtoepassingen**: evaluatie en migratie van on-premises webtoepassingen naar Azure app service, met behulp van de Azure app service-assistent.
    - **Virtuele Bureau bladen**: uw on-premises Virtual Desktop Infrastructure (VDI) controleren en migreren naar virtueel bureau blad van Windows in Azure.
    - **Gegevens**: Migreer grote hoeveel heden gegevens snel en rendabel naar Azure met behulp van Azure data Box producten. 


## <a name="integrated-tools"></a>Geïntegreerde hulpprogram ma's

De Azure Migrate hub biedt de volgende hulpprogram ma's.

**Hulpprogramma** | **Beoordelen/migreren** | **Details**
--- | --- | ---
**Azure Migrate: Server evaluatie** | Servers beoordelen | Ontdek en evalueer on-premises VMware-Vm's, Hyper-V-Vm's en fysieke servers om erachter te komen of ze gereed zijn voor migratie naar Azure.
**Azure Migrate: Server migratie** | Servers migreren | Migreer VMware-Vm's, Hyper-V-Vm's, fysieke servers, evenals andere gevirtualiseerde machines, en open bare Cloud-Vm's naar Azure. 
**Data base-Migration Assistant (DMA)** | On-premises SQL Server data bases evalueren voor migratie naar Azure SQL DB, Azure SQL Managed instance of Azure Vm's met SQL Server. | DMA biedt informatie over mogelijke blokkerings problemen voor migratie. Deze bevat niet-ondersteunde functies, evenals nieuwe functies waarvan u na de migratie kunt profiteren en helpt u bij het identificeren van het juiste pad voor database migratie. [Meer informatie](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Database Migration Service (DMS)** | Migreer on-premises data bases naar Azure-Vm's waarop SQL, Azure SQL DB en Azure SQL Managed instances worden uitgevoerd. | Meer [informatie](https://docs.microsoft.com/azure/dms/dms-overview) over DMS.
**Movere** | Servers beoordelen | [Meer informatie](#movere) over indrijfen.
**Migration Assistant van web-app** | Evalueer en migreer on-premises web-apps naar Azure. |  Gebruik de Azure App Service Migration Assistant om on-premises websites te evalueren voor migratie naar Azure App Service.<br/><br/> U kunt .NET-en PHP-web-apps migreren naar Azure met behulp van Azure App Service Migration Assistant. Meer [informatie](https://appmigration.microsoft.com/) over de assistent.
**Azure Data Box** | Offline gegevens migratie. | Gebruik Azure Data Box producten om grote hoeveel heden gegevens offline naar Azure te verplaatsen. [Meer informatie](https://docs.microsoft.com/azure/databox/).

## <a name="isv-integration"></a>ISV-integratie

Azure Migrate integreert met een aantal ISV-aanbiedingen. 

**ISV** | **Functie**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Servers migreren
[Cloudamize](https://www.cloudamize.com/platform) | Servers beoordelen
[Cohuur technologie](https://www.corenttech.com/AzureMigrate/) | Servers beoordelen en migreren
[Apparaat 42](https://docs.device42.com/) | Servers beoordelen
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI beoordelen
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Servers migreren
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Servers beoordelen
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Servers en data bases beoordelen


## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: hulp programma voor Server evaluatie

Met de Azure Migrate: het hulp programma Server evaluatie detecteert en evalueert on-premises VMware-Vm's, Hyper-V-Vm's en fysieke servers voor migratie naar Azure. Het helpt u bij het identificeren van het volgende:

- **Azure-gereedheid:** Bepaal of on-premises machines gereed zijn voor migratie naar Azure.
- **Azure-grootte:** De geschatte grootte van virtuele Azure-machines na de migratie.
- **Schatting van de kosten van Azure:** Geschatte kosten voor het uitvoeren van on-premises servers in Azure.
- **Afhankelijkheids visualisatie:** Als u Server evaluatie met afhankelijkheids visualisatie gebruikt, kunt u effectief afhankelijkheden tussen servers en optimale manieren voor het verplaatsen van afhankelijke servers naar Azure identificeren.

Server assessment maakt gebruik van een licht gewicht dat u on-premises implementeert en registreert bij Server evaluatie.

- Het apparaat wordt uitgevoerd op een fysieke server of virtuele machine en kan eenvoudig worden geïnstalleerd met behulp van een gedownloade sjabloon.
- Het apparaat detecteert on-premises machines en stuurt voortdurend de meta gegevens en prestatie gegevens van machines naar Azure Migrate.
- De detectie van het apparaat is zonder agent. Er hoeft niets te worden geïnstalleerd op gedetecteerde computers.
- Na de detectie kunt u gedetecteerde computers in groepen verzamelen en groepen voor migratie beoordelen.


## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: hulp programma voor server migratie

Het Azure Migrate: hulp programma voor Server Migratie helpt u bij het migreren van on-premises virtuele VMware-machines, Hyper-V-Vm's, fysieke servers, andere gevirtualiseerde machines en open bare Cloud-Vm's naar Azure. U kunt machines migreren nadat u ze hebt geëvalueerd, of ze migreren zonder een evaluatie.


## <a name="select-a-tool"></a>Selecteer een hulp programma

In de Azure Migrate hub selecteert u het hulp programma dat u wilt gebruiken voor de evaluatie en voegt u het toe aan een Azure Migrate-project. Als u een ISV-hulp programma of een verwerkings functie toevoegt:

- Ga aan de slag door een licentie te verkrijgen of u aan te melden voor een gratis proef versie, in overeenstemming met de instructies van het hulp programma. Hulpprogram ma's voor licentie verlening wordt bepaald door de ISV/het hulp programma. 
- In elk hulp programma is er een optie om verbinding te maken met Azure Migrate. Volg de instructies om verbinding te maken.
- Volg uw migratie traject vanuit het Azure Migrate project, in alle hulpprogram ma's.


## <a name="movere"></a>Movere

De verhuis wagen is een SaaS-platform dat business intelligence verbetert door nauw keurig volledige IT-omgevingen binnen één dag te presen teren. Naarmate organisaties groeien, wijzigen en digitaal optimaliseren, biedt de oplossing ondernemingen met het vertrouwen dat ze nodig hebben om inzicht te hebben in hun omgeving, ongeacht het platform, de toepassing of de geografie. De aandrijf periode is door micro soft [verworven](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) en wordt niet meer verkocht als een zelfstandige aanbieding.  De verhuis wagen is beschikbaar via de micro soft-oplossing voor de evaluatie van oplossingen en Cloud economie. [Meer informatie](https://www.movere.io) over indrijfen. Als u vragen hebt, verzendt u deze naar: movereq@microsoft.com of neemt u contact op met uw micro soft-vertegenwoordiger.

We raden u aan om ook Azure Migrate, onze ingebouwde migratie service, te bekijken. Azure Migrate biedt een centrale hub om uw migratie naar de cloud te vereenvoudigen. De hub biedt uitgebreide ondersteuning voor verschillende werk belastingen, zoals fysieke en virtuele servers, data bases en toepassingen. End-to-end-zicht baarheid maakt het eenvoudig om de voortgang te volgen tijdens de detectie, evaluatie en migratie. Met zowel Azure-als partner-ISV-hulpprogram ma's die zijn ingebouwd, is Azure Migrate ook voorzien van een uitgebreide reeks functies, waaronder de detectie van virtuele en fysieke servers, het op prestaties gebaseerde formaat, kosten planning, op import gebaseerde beoordelingen en toepassingen zonder agent afhankelijkheids analyse. Als u op zoek bent naar deskundige hulp om aan de slag te gaan, heeft micro soft een ervaren [Azure expert managed service provider](https://azure.microsoft.com/partners) om u over de reis te leiden. Bekijk de [Azure migrate-website](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Azure Migrate versies

Er zijn twee versies van de Azure Migrate-service:

- **Huidige versie**: gebruik deze versie om Azure migrate projecten te maken, on-premises machines te detecteren en beoordelingen en migraties te organiseren. Meer [informatie](whats-new.md) over wat er nieuw is in deze versie.
- **Vorige versie**: als u de vorige versie van Azure migrate hebt gebruikt (alleen de evaluatie van on-premises virtuele VMware-machines werd ondersteund), moet u nu de huidige versie gebruiken. U kunt niet meer Azure Migrate projecten maken met de vorige versie en u wordt aangeraden geen nieuwe detecties uit te voeren. Als u toegang wilt krijgen tot bestaande projecten, zoekt en selecteert u in de Azure Portal **Azure migrate**. Op het dash board van **Azure migrate** ziet u een melding en een koppeling voor toegang tot oude Azure migrate projecten.



## <a name="next-steps"></a>Volgende stappen

- Probeer onze zelf studies om virtuele [VMware-machines](tutorial-prepare-vmware.md), [virtuele Hyper-V-machines](tutorial-prepare-hyper-v.md)en [fysieke servers](tutorial-prepare-physical.md)te beoordelen.
- [Bekijk de veelgestelde vragen](resources-faq.md) over Azure Migrate.
