---
title: Data Services van Azure Arc enabled-opmerkingen bij de release
description: Nieuwste release opmerkingen
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: 94074c2c5e11187252084832e5a20a197f6723fd
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359813"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Release opmerkingen-Azure Arc ingeschakelde Data Services (preview-versie)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>Oktober 2020 

Azure data CLI ( `azdata` )-versie nummer: 20.2.3. Downloaden op [https://aka.ms/azdata](https://aka.ms/azdata) .

### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

In deze release worden de volgende belang rijke wijzigingen geïntroduceerd: 

* In de PostgreSQL-aangepaste resource definitie (CRD) wordt de naam van de term `shards` gewijzigd in `workers` . Deze term ( `workers` ) komt overeen met de naam van de opdracht regel parameter.

* `azdata arc postgres server delete` Hiermee wordt om bevestiging gevraagd voordat een post gres-exemplaar wordt verwijderd.  Gebruik `--force` om de prompt te overs Laan.

### <a name="additional-changes"></a>Aanvullende wijzigingen

* Er is een nieuwe optionele para meter toegevoegd met de `azdata arc postgres server create` naam `--volume-claim mounts` . De waarde is een door komma's gescheiden lijst met volume claim koppelingen. Een volume claim koppeling is een paar volume type en PVC-naam. Het enige volume type dat op dit moment wordt ondersteund, is `backup` .  In PostgreSQL, wanneer het volume type is `backup` , wordt het PVC gekoppeld aan `/mnt/db-backups` .  Hierdoor kunnen back-ups tussen PostgresSQL-instanties worden gedeeld, zodat de back-up van een PostgresSQL-exemplaar in een ander exemplaar kan worden hersteld.

* Een nieuwe korte naam voor aangepaste resource definities voor PostgresSQL: 

  * `pg11` 

  * `pg12`

* Telemetrie uploaden door de gebruiker te voorzien van:

   * Aantal punten dat naar Azure is geüpload

     of 

   * Als er geen gegevens zijn geladen in azure, wordt u gevraagd om het opnieuw te proberen.

* `azdata arc dc debug copy-logs` Lees nu ook uit `/var/opt/controller/log` map en verzamelt postgresql engine-Logboeken in Linux.

*   Een werk indicator weer geven tijdens het maken en herstellen van de back-up met PostgreSQL grootschalige.

* `azdata arc postrgres backup list` bevat nu informatie over de grootte van de back-up.

* De eigenschap admin name van SQL Managed instance is toegevoegd aan de rechter kolom van de Blade overzicht in het Azure Portal.

* Azure Data Studio ondersteunt het configureren van het aantal worker-knoop punten, de vCore en de geheugen instellingen voor PostgreSQL grootschalige. 

* Preview ondersteunt Backup/Restore voor post gres-versie 11 en 12.

## <a name="september-2020"></a>September 2020

Data Services van Azure-Arc is beschikbaar voor open bare preview. Met Arc enabled Data Services kunt u overal gegevens Services beheren.

- SQL Managed Instance
- PostgreSQL Hyperscale

Zie [Wat zijn Azure Arc-gegevens Services?](overview.md) voor instructies.

## <a name="known-limitations-and-issues"></a>Bekende beperkingen en problemen

- Exemplaar namen mogen niet langer zijn dan 13 tekens
- Geen in-place upgrade voor de Azure Arc-gegevens controller of-data base-exemplaren.
- Arc-containerinstallatiekopieën met Data Services worden niet ondertekend.  Mogelijk moet u uw Kubernetes-knooppunten configureren zodat niet-ondertekende containerinstallatiekopieën kunnen worden opgehaald.  Als u bijvoorbeeld docker als container runtime gebruikt, kunt u de omgevings variabele DOCKER_CONTENT_TRUST = 0 instellen en opnieuw starten.  Andere container-runtimes hebben vergelijkbare opties zoals in [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Kan geen door Azure Arc ingeschakelde SQL Managed instances of PostgreSQL grootschalige Server-groepen maken van de Azure Portal.
- Als u nu gebruikmaakt van NFS, moet u `allowRunAsRoot` `true` in uw implementatie profiel bestand instellen voordat u de Azure Arc-gegevens controller maakt.
- Alleen SQL-en PostgreSQL-aanmeldings verificatie.  Er wordt geen ondersteuning geboden voor Azure Active Directory of Active Directory.
- Voor het maken van een gegevens controller voor open Shift zijn beperkte beveiligings beperkingen vereist.  Zie documentatie voor details.
- Als u Azure Kubernetes service Engine (AKS-Engine) op Azure Stack hub met Azure Arc data controller-en data base-instanties gebruikt, wordt het upgraden naar een nieuwere versie van Kubernetes niet ondersteund. Verwijder de Azure Arc-gegevens controller en alle data base-exemplaren voordat u het Kubernetes-cluster bijwerkt.
- Azure Kubernetes service (AKS), clusters die [meerdere beschikbaarheids zones](../../aks/availability-zones.md) omvatten, worden momenteel niet ondersteund voor Azure Arc-gegevens Services. Als u dit probleem wilt voor komen, moet u, wanneer u het AKS-cluster maakt in Azure Portal, als u een regio selecteert waar zones beschikbaar zijn, alle zones verwijderen uit het selectie besturings element. Zie de volgende afbeelding:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Schakel de selectie vakjes voor elke zone uit om geen op te geven.":::


### <a name="known-issues-for-azure-arc-enabled-postgresql-hyperscale"></a>Bekende problemen voor Azure Arc enabled PostgreSQL grootschalige   

- Preview biedt geen ondersteuning voor Backup/Restore voor PostgreSQL versie 11-engine. Het ondersteunt alleen Backup/Restore voor PostgreSQL versie 12.
- `azdata arc dc debug copy-logs` ndoes PostgreSQL-engine-logboeken niet verzamelen in Windows.
- Het opnieuw maken van een server groep met de naam van een server groep die zojuist is verwijderd, kan mislukken of reageert niet meer. 
   - **Tijdelijke oplossing** Gebruik dezelfde naam niet wanneer u een server groep opnieuw maakt of wacht op de load balancer/externe service van eerder verwijderde Server groep. Ervan uitgaande dat de naam van de Server groep die u hebt verwijderd `postgres01` , werd gehost in een naam ruimte `arc` , voordat u een server groep met dezelfde naam maakt, moet u wachten totdat deze `postgres01-external-svc` niet wordt weer gegeven in de uitvoer van de kubectl-opdracht `kubectl get svc -n arc` .
 - Het laden van de pagina overzicht en configuratie van Compute + Storage in Azure Data Studio is traag. 



## <a name="next-steps"></a>Volgende stappen
  
> **Wilt u gewoon iets uitproberen?**  
> Ga snel aan de slag met [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) op Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) of in een Azure-VM.

[Installeer de client-hulpprogramma's](install-client-tools.md)

[Maak de Azure Arc gegevenscontroller](create-data-controller.md) (hiervoor moeten eerst de client-hulpprogramma's worden geïnstalleerd)

[Maak een Azure SQL Managed Instance op Azure Arc](create-sql-managed-instance.md) (hiervoor moet u eerst een Azure Arc-gegevenscontroller maken)

[Maak een Azure Database for PostgreSQL Hyperscale-servergroep op Azure Arc](create-postgresql-hyperscale-server-group.md) (eerst moet een Azure Arc-gegevenscontroller gemaakt worden)
