---
title: Clienttools installeren
description: Installeer azdata, kubectl, Azure CLI, psql, Azure Data Studio (insiders) en de Arc-extensie voor Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3eb41b868aba7e7350f54594fa43f01eeeabebc4
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936743"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Installeer de client hulpprogramma's voor het implementeren en beheren van Azure Arc ingeschakelde gegevens Services

> [!IMPORTANT]
> Als u een nieuwe maandelijkse versie bijwerkt, moet u er ook voor zorgen dat u de nieuwste versie van Azure Data Studio, het Azure data CLI-hulp programma (azdata) en de Azure data CLI-en Azure-Arc-extensies voor Azure Data Studio bijwerkt.

Dit document begeleidt u stapsgewijs door de stappen voor het installeren van de Azure data CLI (azdata), Azure Data Studio, Azure CLI (AZ) en het Kubernetes CLI-hulp programma (kubectl) op uw client computer.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Hulpprogram ma's voor het maken en beheren van gegevens services die geschikt zijn voor Azure Arc 

De volgende tabel bevat algemene hulpprogram ma's die nodig zijn voor het maken en beheren van Azure Arc-gegevens Services en het installeren van deze hulpprogram ma's:

| Hulpprogramma | Vereist | Beschrijving | Installatie |
|---|---|---|---|
| Azure-gegevens CLI (azdata) | Yes | Opdracht regel programma voor het installeren en beheren van een big data cluster. Azure data CLI bevat ook een opdracht regel programma om verbinding te maken met en query's uit te voeren op Azure SQL-en SQL Server-instanties en post gres-servers met behulp van de opdrachten `azdata sql query` (een enkele query uitvoeren vanaf de opdracht regel), `azdata sql shell` (een interactieve shell), `azdata postgres query` en `azdata postgres shell` . | [Installeren](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | Yes | Hulp programma voor uitgebreide ervaring voor het maken van verbinding met en het uitvoeren van query's op diverse data bases, waaronder Azure SQL, SQL Server, PostrgreSQL en MySQL. Uitbrei dingen van Azure Data Studio bieden een beheer ervaring voor gegevens services die zijn ingeschakeld voor Azure Arc. | [Installeren](https://aka.ms/getazuredatastudio) |
| Azure data CLI-extensie voor Azure Data Studio | Yes | Extensie voor Azure Data Studio waarmee Azure data CLI wordt geïnstalleerd als u dit nog niet hebt gedaan.| Installeren uit de galerie met extensies in Azure Data Studio.|
| Azure Arc-extensie voor Azure Data Studio | Yes | Uitbrei ding voor Azure Data Studio die een beheer ervaring biedt voor Azure Arc ingeschakelde gegevens Services. Er is een afhankelijkheid van de Azure data CLI-extensie voor Azure Data Studio. | Installeren uit de galerie met extensies in Azure Data Studio.|
| PostgreSQL-extensie in Azure Data Studio | No | PostgreSQL-extensie voor Azure Data Studio die beheer mogelijkheden biedt voor PostgreSQL. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Installeren uit de galerie met extensies in Azure Data Studio.|
| Azure CLI (AZ)<sup>1</sup> | Yes | Moderne opdracht regel interface voor het beheren van Azure-Services. Wordt gebruikt in combi natie met AKS-implementaties en voor het uploaden van gegevens Services-inventarisatie en facturerings gegevens van Azure-Arc ingeschakeld naar Azure. ([Meer informatie](/cli/azure/?view=azure-cli-latest&preserve-view=true)). | [Installeren](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) |
| Kubernetes CLI (kubectl)<sup>2</sup> | Yes | Opdracht regel programma voor het beheren van het Kubernetes-cluster ([meer informatie](https://kubernetes.io/docs/tasks/tools/install-kubectl/)). | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| krul <sup>3</sup> | Vereist voor een aantal voorbeeld scripts. | Opdracht regel programma voor het overdragen van gegevens met Url's. | [Windows](https://curl.haxx.se/windows/) \| Linux: een krul pakket installeren |
| OC | Vereist voor Red Hat open Shift en Azure RedHat-openstaande implementaties. |`oc` is de open Shift-opdracht regel interface (CLI). | [De CLI installeren](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> u moet Azure CLI versie 2.0.4 of hoger gebruiken. Voer uit `az --version` om de versie te vinden, indien nodig.

<sup>2</sup> u moet `kubectl` versie 1,13 of hoger gebruiken. Daarnaast moet de versie van de `kubectl` één secundaire versie van uw Kubernetes-cluster plus of min. Als u een specifieke versie op de client wilt installeren `kubectl` , raadpleegt u [ `kubectl` binary installeren via krul](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (in windows 10 gebruikt u cmd.exe en niet Windows Power shell om krul uit te voeren).

<sup>3</sup> als u Power shell gebruikt, is krul een alias voor de cmdlet invoke-WebRequest.

## <a name="next-steps"></a>Volgende stappen

[De Azure Arc-gegevens controller maken](create-data-controller.md)
