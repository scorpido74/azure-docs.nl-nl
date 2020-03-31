---
title: Bronkaartondersteuning voor JavaScript-toepassingen - Azure Monitor Application Insights
description: Meer informatie over het uploaden van bronkaarten naar uw eigen opslagaccount Blob-container met Behulp van Application Insights.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474880"
---
# <a name="source-map-support-for-javascript-applications"></a>Bronkaartondersteuning voor JavaScript-toepassingen

Application Insights ondersteunt het uploaden van bronkaarten naar je eigen Storage Account Blob Container.
Bronkaarten kunnen worden gebruikt om callstacks te ontstoppen die op de pagina van de transactiegegevens van het einde worden gevonden. Elke uitzondering die wordt verzonden door de [JavaScript SDK][ApplicationInsights-JS] of de [Node.js SDK][ApplicationInsights-Node.js] kan worden geminificeerd met bronkaarten.

![Een oproepstack loskoppelen door te koppelen aan een opslagaccount](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Een nieuw opslagaccount en Blob-container maken

Als u al een bestaand opslagaccount of blobcontainer hebt, u deze stap overslaan.

1. [Een nieuw opslagaccount maken][create storage account]
2. [Maak een blobcontainer][create blob container] in uw opslagaccount. Zorg ervoor dat u het 'niveau voor openbare toegang' instelt op `Private`, om ervoor te zorgen dat uw bronkaarten niet openbaar toegankelijk zijn.

> [!div class="mx-imgBorder"]
>![Het toegangsniveau voor containers moet zijn ingesteld op Privé](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Uw bronkaarten naar uw Blob-container duwen

U moet uw pijplijn voor continue implementatie integreren met uw opslagaccount door deze te configureren om uw bronkaarten automatisch te uploaden naar de geconfigureerde Blob-container. U moet uw bronkaarten niet uploaden naar een submap in de Blob-container. momenteel wordt de bronkaart alleen opgehaald uit de hoofdmap.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Bronkaarten uploaden via Azure Pipelines (aanbevolen)

Als u Azure Pipelines gebruikt om uw toepassing continu te bouwen en te implementeren, voegt u een [Azure File Copy-taak][azure file copy] toe aan uw pijplijn om uw bronkaarten automatisch te uploaden.

> [!div class="mx-imgBorder"]
> ![Een Azure File Copy-taak toevoegen aan uw pijplijn om uw brontoewijzingen te uploaden naar Azure Blob Storage](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Uw Application Insights-bron configureren met een bronmapopslagaccount

### <a name="from-the-end-to-end-transaction-details-page"></a>Vanaf de pagina met de end-to-end transactiedetails

Op het tabblad end-to-end transactiedetails u op *Unminify* klikken en wordt een prompt weergegeven om te configureren als uw bron niet is geconfigureerd.

1. Bekijk in de portal de details van een uitzondering die is geminificeerd.
2. Klik op *Unminify*
3. Als uw bron niet is geconfigureerd, verschijnt er een bericht waarin u wordt gevraagd te configureren.

### <a name="from-the-properties-page"></a>Vanaf de eigenschappenpagina

Als u het opslagaccount of de Blob-container wilt configureren of wijzigen die is gekoppeld aan uw Application Insights Resource, u dit doen door het tabblad *Eigenschappen van* de toepassingsstatistieken te bekijken.

1. Navigeer naar het tabblad *Eigenschappen* van de bron Application Insights.
2. Klik op *Blobcontainer bronkaart wijzigen*.
3. Selecteer een andere Blob-container als de container met bronkaarten.
4. Klik op `Apply`.

> [!div class="mx-imgBorder"]
> ![Uw geselecteerde Azure Blob Container opnieuw configureren door naar het eigenschappenblad te navigeren](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>RBAC-instellingen (Required role-based access control) op uw Blob-container

Elke gebruiker op de portal die deze functie gebruikt, moet ten minste als [opslagblobgegevenslezer][storage blob data reader] aan uw Blob-container zijn toegewezen. U moet deze rol toewijzen aan iemand anders die de bronkaarten via deze functie gebruikt.

> [!NOTE]
> Afhankelijk van hoe de container is gemaakt, is dit mogelijk niet automatisch toegewezen aan u of uw team.

### <a name="source-map-not-found"></a>Bronkaart niet gevonden

1. Controleren of de bijbehorende bronmap is geüpload naar de juiste blobcontainer
2. Controleer of het bronmapbestand is vernoemd naar het JavaScript-bestand waarnaar het wordt toegewezen, met achtervoegsels met `.map`.
    - Zoekt bijvoorbeeld `/static/js/main.4e2ca5fa.chunk.js` naar de blob met de naam`main.4e2ca5fa.chunk.js.map`
3. Controleer de console van uw browser om te zien of er fouten worden geregistreerd. Neem dit op in elk ondersteuningsticket.

## <a name="next-steps"></a>Volgende stappen

* [Taak Azure-bestandskopie](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme