---
title: Nieuwe nieuwe in Azure Migreren
description: Meer informatie over de nieuwe en recente updates in de Azure Migrate-service.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: ba85b0a4ed30320099388ccb48dd91ad6445efd5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677250"
---
# <a name="whats-new-in-azure-migrate"></a>Nieuwe nieuwe in Azure Migreren

[Azure Migrate](migrate-services-overview.md) helpt u bij het detecteren, beoordelen en migreren van on-premises servers, apps en gegevens naar de Microsoft Azure-cloud. In dit artikel worden nieuwe releases en functies in Azure Migrate samengevat.


## <a name="update-april-2020"></a>Update (april 2020)

Azure Migrate ondersteunt implementaties in Azure Government. 

- U Vm's, Hyper-V VM's en fysieke servers ontdekken en beoordelen.
- U VMware VM's, Hyper-V VM's en fysieke servers migreren naar Azure.
- Voor VMware-migratie u agentloze of op agentgebaseerde migratie gebruiken. [Meer informatie](server-migrate-overview.md).
- [Bekijk](migrate-support-matrix.md#supported-geographies-azure-government) ondersteunde regio's en regio's voor Azure Government.
- [Afhankelijkheidsanalyse op basis van agenten](concepts-dependency-visualization.md#agent-based-analysis) wordt niet ondersteund in Azure Government.
- Functies in preview worden ondersteund in Azure Government, met name [agentless afhankelijkheidsanalyse](concepts-dependency-visualization.md#agentless-analysis)en [toepassingsdetectie](how-to-discover-applications.md).

## <a name="update-march-2020"></a>Update (maart 2020)

Er is nu een op scripts gebaseerde installatie beschikbaar om het [Azure Migrate-toestel in](migrate-appliance.md)te stellen:

- De op scripts gebaseerde installatie is een alternatief voor de . OVA (VMware)/VHD (Hyper-V) installatie van het toestel.
- Het biedt een PowerShell-installatiescript dat kan worden gebruikt om het toestel voor VMware/Hyper-V in te stellen op een bestaande machine met Windows Server 2016.

## <a name="update-november-2019"></a>Update (november 2019)

Er zijn een aantal nieuwe functies toegevoegd aan Azure Migrate:

- **Fysieke serverbeoordeling**. Beoordeling van on-premises fysieke servers wordt nu ondersteund, naast fysieke servermigratie die al wordt ondersteund.
- **Beoordeling op basis van invoer**. Beoordeling van machines met behulp van metagegevens en prestatiegegevens in een CSV-bestand wordt nu ondersteund.
- **Toepassingsdetectie**: Azure Migrate ondersteunt nu detectie op toepassingsniveau van apps, rollen en functies met behulp van het Azure Migrate-toestel. Dit wordt momenteel alleen ondersteund voor VMware VM's en is beperkt tot detectie (beoordeling wordt momenteel niet ondersteund). [Meer informatie](how-to-discover-applications.md)
- **Agentless dependency visualisatie**: U hoeft niet langer expliciet agents te installeren voor afhankelijkheidsvisualisatie. Zowel agentless als agent-based worden nu ondersteund.
- **Virtueel bureaublad:** gebruik ISV-hulpprogramma's om on-premises virtuele desktopinfrastructuur (VDI) te beoordelen en te migreren naar Windows Virtual Desktop in Azure.
- **Web-app**: De Azure App Service Migration Assistant, die wordt gebruikt voor het beoordelen en migreren van webapps, is nu geïntegreerd in Azure Migrate.

Er zijn nieuwe beoordelings- en migratiehulpprogramma's toegevoegd aan Azure Migrate:

- **Rackware**: Cloudmigratie aanbieden.
- **Movere**: Beoordeling aanbieden.

[Meer informatie](migrate-services-overview.md) over het gebruik van hulpprogramma's en ISV-aanbiedingen voor beoordeling en migratie in Azure Migrate.

## <a name="azure-migrate-current-version"></a>Huidige versie van Azure Migreren

De huidige versie van Azure Migrate (uitgebracht in juli 2019) biedt een aantal nieuwe functies:

- **Uniform migratieplatform**: Azure Migrate biedt nu één portal om uw migratiereis naar Azure te centraliseren, te beheren en bij te houden, met een verbeterde implementatiestroom en portalervaring.
- **Beoordelings- en migratietools:** Azure Migrate biedt native tools en integreert met andere Azure-services, evenals met onafhankelijke ISV-hulpprogramma's (Independent Software Vendor). [Meer informatie](migrate-services-overview.md#isv-integration) over ISV-integratie.
- **Azure Migrate assessment**: Met het Azure Migrate Server Assessment tool u VMware VM's en Hyper-V VM's beoordelen op migratie naar Azure. U ook beoordelen op migratie met andere Azure-services en ISV-hulpprogramma's.
- **Migratie van Azure Migreren:** met het hulpprogramma Azure Migrate Server Migration u on-premises Vm's vmware en Hyper-VVm's migreren naar Azure, evenals fysieke servers, andere gevirtualiseerde servers en private/public cloud VM's. Bovendien u migreren naar Azure met BEHULP VAN ISV-hulpprogramma's.
- **Azure Migrate appliance**: Azure Migrate implementeert een lichtgewicht toestel voor detectie en beoordeling van on-premises Vm's vmware en Hyper-V VM's.
    - Dit toestel wordt gebruikt door Azure Migrate Server Assessment en Azure Migrate Server Migration voor agentless migratie.
    - Het toestel detecteert continu metagegevens en prestatiegegevens van de server, met het oog op beoordeling en migratie.  
- **VMware VM-migratie**: Azure Migrate Server Migration biedt een aantal methoden voor het migreren van on-premises VmVm's naar Azure.  Een agentloze migratie met behulp van het Azure Migrate-toestel en een op agent gebaseerde migratie die een replicatietoestel gebruikt, en implementeert een agent op elke vm die u wilt migreren. [Meer informatie](server-migrate-overview.md)
 - **Databasebeoordeling en -migratie:** Vanuit Azure Migrate u on-premises databases beoordelen op migratie naar Azure met behulp van de Azure Database Migration Assistant. U databases migreren met de Azure Database Migration Service.
- **Web-app-migratie**: U web-apps beoordelen met behulp van een openbare eindpunt-URL met de Azure App-service. Voor migratie van interne .NET-apps u de App Service Migration Assistant downloaden en uitvoeren.
- **Gegevensvak**: Importeer grote hoeveelheden offline gegevens in Azure met Azure Data Box in Azure Migrate.

## <a name="azure-migrate-previous-version"></a>Azure Vorige versie migreren

Als u de vorige versie van Azure Migrate gebruikt (alleen beoordeling van on-premises VMware VM's werd ondersteund), moet u nu de huidige versie gebruiken. In de vorige versie u geen nieuwe Azure Migrate-projecten meer maken of nieuwe ontdekkingen uitvoeren. U hebt nog steeds toegang tot bestaande projecten. Zoek hiervoor in de Azure-portal > **Alle services**naar **Azure Migreren**. In de Azure Migrate-meldingen is er een koppeling om toegang te krijgen tot oude Azure Migrate-projecten.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over prijzen voor Azure Migrate vindt u [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).
- [Bekijk de veelgestelde vragen](resources-faq.md) over Azure Migrate.
- Probeer onze tutorials om [VMware VM's](tutorial-assess-vmware.md) en [Hyper-V VM's](tutorial-assess-hyper-v.md)te beoordelen.
