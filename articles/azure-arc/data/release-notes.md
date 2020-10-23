---
title: Data Services van Azure Arc enabled-opmerkingen bij de release
description: Nieuwste release opmerkingen
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3c20bbd3ab02cd1eccd00e2d36c14eebf2f63205
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360298"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Release opmerkingen-Azure Arc ingeschakelde Data Services (preview-versie)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>September, 2020

Data Services van Azure-Arc is beschikbaar voor open bare preview. Met Arc enabled Data Services kunt u overal gegevens Services beheren.

- SQL Managed Instance
- PostgreSQL Hyperscale

Zie [Wat zijn Azure Arc-gegevens Services?](overview.md) voor instructies.

### <a name="known-issues"></a>Bekende problemen

De volgende problemen zijn van toepassing op deze release:

* De **postgresql grootschalige-Server groep wordt verwijderd**: als u de configuratie van de Server groep of het exemplaar hebt gewijzigd, wacht u totdat de bewerking is voltooid voordat u een postgresql grootschalige-Server groep verwijdert.

* ** `azdata notebook run` kan mislukken**: als u dit probleem wilt afronden, voert u `azdata notebook run` uit in een virtuele python-omgeving. Dit probleem kan ook worden veroorzaakt door een mislukte poging om een SQL Managed instance of een PostgreSQL grootschalige-Server groep te maken met behulp van de wizard Azure Data Studio implementatie. In dit geval kunt u het notitie blok openen en op de knop **alles uitvoeren** boven aan het notitie blok klikken.

## <a name="next-steps"></a>Volgende stappen

> **Wilt u gewoon iets uitproberen?**  
> Ga snel aan de slag met [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) op Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) of in een Azure-VM.

[Installeer de client-hulpprogramma's](install-client-tools.md)

[Maak de Azure Arc gegevenscontroller](create-data-controller.md) (hiervoor moeten eerst de client-hulpprogramma's worden geïnstalleerd)

[Maak een Azure SQL Managed Instance op Azure Arc](create-sql-managed-instance.md) (hiervoor moet u eerst een Azure Arc-gegevenscontroller maken)

[Maak een Azure Database for PostgreSQL Hyperscale-servergroep op Azure Arc](create-postgresql-hyperscale-server-group.md) (eerst moet een Azure Arc-gegevenscontroller gemaakt worden)

## <a name="known-limitations-and-issues"></a>Bekende beperkingen en problemen

- Namen van door SQL beheerde exemplaren mogen niet langer zijn dan 13 tekens
- Geen in-place upgrade voor de Azure Arc-gegevens controller of-data base-exemplaren.
- Arc-containerinstallatiekopieën met Data Services worden niet ondertekend.  Mogelijk moet u uw Kubernetes-knooppunten configureren zodat niet-ondertekende containerinstallatiekopieën kunnen worden opgehaald.  Als u bijvoorbeeld docker als container runtime gebruikt, kunt u de omgevings variabele DOCKER_CONTENT_TRUST = 0 instellen en opnieuw starten.  Andere container-runtimes hebben vergelijkbare opties zoals in [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Kan geen door Azure Arc ingeschakelde SQL Managed instances of PostgreSQL grootschalige Server-groepen maken van de Azure Portal.
- Als u nu gebruikmaakt van NFS, moet u allowRunAsRoot instellen op True in het implementatie profiel bestand voordat u de Azure Arc-gegevens controller maakt.
- Alleen SQL-en PostgreSQL-aanmeldings verificatie.  Er wordt geen ondersteuning geboden voor Azure Active Directory of Active Directory.
- Voor het maken van een gegevens controller voor open Shift zijn beperkte beveiligings beperkingen vereist.  Zie documentatie voor details.
- Het schalen van het aantal post gres grootschalige-worker _-knoop punten_ wordt niet ondersteund.
- Als u Azure Kubernetes service Engine (AKS-Engine) op Azure Stack hub met Azure Arc data controller-en data base-instanties gebruikt, wordt het upgraden naar een nieuwere versie van Kubernetes niet ondersteund. Verwijder de Azure Arc-gegevens controller en alle data base-exemplaren voordat u het Kubernetes-cluster bijwerkt.
- Preview biedt geen ondersteuning voor Backup/Restore voor post gres versie 11-engine. Het ondersteunt alleen Backup/Restore voor post gres versie 12.
- Azure Kubernetes service (AKS), clusters die [meerdere beschikbaarheids zones](../../aks/availability-zones.md) omvatten, worden momenteel niet ondersteund voor Azure Arc-gegevens Services. Als u dit probleem wilt voor komen, moet u, wanneer u het AKS-cluster maakt in Azure Portal, als u een regio selecteert waar zones beschikbaar zijn, alle zones verwijderen uit het selectie besturings element. Zie de volgende afbeelding:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Schakel de selectie vakjes voor elke zone uit om geen op te geven.":::

  
