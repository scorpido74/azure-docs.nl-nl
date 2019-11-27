---
title: Ondersteuning voor de beoordeling/migratie van fysieke servers met Azure Migrate
description: Hiermee wordt een overzicht gegeven van de ondersteuning voor fysieke server evaluatie/migratie met Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: b5b5da6282b1df6c70fd58dcf8c417250de81b73
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196338"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>Ondersteuningsmatrix voor evaluatie en migratie van fysieke servers

U kunt de [Azure migrate-service](migrate-overview.md) gebruiken voor het beoordelen en migreren van machines naar de Microsoft Azure Cloud. In dit artikel vindt u een overzicht van de ondersteunings instellingen en beperkingen voor het beoordelen en migreren van on-premises fysieke servers.



## <a name="physical-server-scenarios"></a>Scenario's voor fysieke servers

De tabel bevat een overzicht van de ondersteunde scenario's voor fysieke servers.

**Implementatie** | **Details***
--- | ---
**On-premises fysieke servers beoordelen** | [Stel](tutorial-prepare-physical.md) uw eerste beoordeling in.<br/><br/> [Voer](tutorial-assess-physical.md) een evaluatie uit.
**Fysieke servers migreren naar Azure** | [Probeer](tutorial-migrate-physical-virtual-machines.md) de migratie naar Azure uit te proberen.


## <a name="azure-migrate-projects"></a>Azure Migrate projecten

**Ondersteuning** | **Details**
--- | ---
**Azure-machtigingen** | U hebt machtigingen voor Inzender of eigenaar nodig in het abonnement om een Azure Migrate project te maken.
**Fysieke servers** | Evalueer Maxi maal 35.000 fysieke servers in één project. U kunt meerdere projecten in een Azure-abonnement hebben. Een project kan bestaan uit fysieke servers, virtuele VMware-machines en virtuele Hyper-V-machines, tot aan de evaluatie limieten.
**Geografie** | U kunt Azure Migrate projecten maken in een aantal geographs. Hoewel u projecten in specifieke geografische grafieken kunt maken, kunt u machines voor andere doel locaties evalueren of migreren. De Geografie van het project wordt alleen gebruikt om de gedetecteerde meta gegevens op te slaan.

  **Geografie** | **Opslag locatie van meta gegevens**
  --- | ---
  Azure Government | VS (overheid) - Virginia
  Azië en Stille Oceaan | Azië Azië-oost of Zuidoost
  Australië | Australië-oost of Australië-zuidoost
  Brazilië | Brazilië - zuid
  Canada | Canada-centraal of Canada-oost
  Europa | Europa - noord of Europa - west
  Frankrijk | Frankrijk - centraal
  India | Centraal-India of India-zuid
  Japan |  Japan-Oost of Japan-West
  Korea | Korea-centraal of Korea-zuid
  Verenigd Koninkrijk | UK-zuid of UK-west
  Verenigde Staten | VS-midden, VS-West 2


 > [!NOTE]
 > Ondersteuning voor Azure Government is momenteel alleen beschikbaar voor de [oudere versie](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) van Azure Migrate.


## <a name="assessment-physical-server-requirements"></a>Beoordeling: vereisten voor fysieke servers

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Fysieke server implementatie**       | De fysieke server kan zelfstandig of in een cluster worden geïmplementeerd. |
| **Machtigingen**           | **Windows:** Stel een lokale gebruikers account in op alle Windows-servers die u wilt toevoegen in de detectie. Het gebruikers account moet worden toegevoegd aan deze groepen-Extern bureaublad gebruikers, prestatie meter gebruikers en prestatie logboek gebruikers. <br/> **Linux:** U hebt een hoofd account nodig op de Linux-servers die u wilt detecteren. |
| **Besturingssysteem** | Alle [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -en [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -besturings systemen worden ondersteund, met uitzonde ring van het volgende:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="assessment-appliance-requirements"></a>Beoordeling-vereisten voor apparaten

Voor evaluatie voert Azure Migrate een licht gewicht in voor het detecteren van fysieke servers en het verzenden van meta gegevens en prestatie gegevens van de server naar Azure Migrate. Het apparaat kan worden uitgevoerd op een fysieke server of een virtuele machine en u kunt dit instellen met behulp van een Power shell-script dat u van de Azure Portal downloadt. De volgende tabel bevat een overzicht van de vereisten voor het apparaat.

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Implementatie van het apparaat**   |  U implementeert het apparaat op een fysieke server of een virtuele machine.<br/>  Op de hostcomputer moet Windows Server 2012 R2 of later worden uitgevoerd.<br/> De host moet voldoende ruimte hebben om 16 GB RAM-geheugen, 8 Vcpu's, ongeveer 80 GB aan opslag ruimte toe te wijzen en een externe switch voor de apparaat-VM.<br/> Het apparaat heeft een statisch of dynamisch IP-adres nodig en Internet toegang.
| **Azure Migrate project**  |  Een apparaat kan worden gekoppeld aan een enkel project.<br/> Een wille keurig aantal apparaten kan aan één project worden gekoppeld.<br/> U kunt Maxi maal 35.000 computers in een project evalueren.
| **Detectie**              | Eén apparaat kan Maxi maal 250 servers detecteren.
| **Beoordelings groep**       | U kunt Maxi maal 35.000 computers in één groep toevoegen.
| **Evaluatie**             | U kunt Maxi maal 35.000 computers in één evaluatie evalueren.


## <a name="assessment-appliance-url-access"></a>Beoordeling: toestel-URL-toegang

Voor het beoordelen van Vm's heeft het Azure Migrate-apparaat Internet verbinding nodig.

- Wanneer u het apparaat implementeert, controleert Azure Migrate een connectiviteits controle op de Url's die in de onderstaande tabel worden samenvatten.
- Als u een proxy op basis van een URL gebruikt, verleent u toegang tot de Url's in de tabel en zorgt u ervoor dat de proxy alle CNAME-records verhelpt die zijn ontvangen tijdens het opzoeken van de Url's.
- Als u een onderschepende proxy hebt, moet u mogelijk het server certificaat van de proxy server importeren naar het apparaat.


**URL** | **Details**  
--- | ---
*.portal.azure.com | Navigeren naar de Azure Portal
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Meld u aan bij uw Azure-abonnement
*.microsoftonline.com <br/> *.microsoftonline-p.com | Het maken van Azure Active Directory-toepassingen voor het apparaat aan service communicatie.
management.azure.com | Het maken van Azure Active Directory-toepassingen voor het apparaat aan service communicatie.
dc.services.visualstudio.com | Logboekregistratie en bewaking
*.vault.azure.net | Geheimen in Azure Key Vault beheren bij de communicatie tussen het apparaat en de service.
aka.ms/* | Toegang tot ook wel-koppelingen toestaan.
https://download.microsoft.com/download/* | Down loads van de micro soft-download site toestaan.



## <a name="assessment-port-requirements"></a>Beoordeling-poort vereisten

De volgende tabel bevat een overzicht van de poort vereisten voor evaluatie.

**Apparaatconfiguratie** | **Verbinding**
--- | ---
**Apparaat** | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Uitgaande verbindingen op de poorten 443, 5671 en 5672 voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
**Fysieke servers** | **Windows:** Binnenkomende verbindingen op poort 443, 5989 voor het ophalen van meta gegevens van de configuratie en prestaties van Windows-servers. <br/> **Linux:**  Binnenkomende verbindingen op poort 22 (UDP) voor het ophalen van meta gegevens van de configuratie en prestaties van Linux-servers. |


## <a name="next-steps"></a>Volgende stappen

De [evaluatie van de fysieke server voorbereiden](tutorial-prepare-physical.md) voor de evaluatie en migratie van fysieke servers.
