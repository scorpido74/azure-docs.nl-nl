---
title: 'Azure Portal: Open Source FHIR-server implementeren voor Azure - Azure API for FHIR'
description: In deze snelstartgids wordt uitgelegd hoe u de Microsoft Open Source FHIR-server implementeert met Azure Portal.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 7fa119db0c974c93aff667060d153b21b8de16bb
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843484"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Quickstart: Een Open Source FHIR-server implementeren met behulp van Azure Portal

In deze quickstart wordt uitgelegd hoe u een Open Source FHIR-server in Azure implementeert met behulp van Azure Portal. We gebruiken de koppelingen voor eenvoudige implementatie in de [opensource-opslagplaats](https://github.com/Microsoft/fhir-server)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="github-open-source-repository"></a>Opensource-GitHub-opslagplaats

Ga naar de pagina [GitHub-implementatie](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) en zoek de knoppen Implementeren naar Azure op:

![Pagina van opensource-implementatie](media/quickstart-oss-portal/deployment-page-oss.png)

Klik op de knop Implementatie. Azure Portal wordt geopend.

## <a name="fill-in-deployment-parameters"></a>Implementatieparameters invullen

Kies ervoor om een nieuwe resourcegroep te maken en geef deze een unieke naam. De enige andere vereiste parameter is een naam voor de service.

![Aangepaste implementatieparameters](media/quickstart-oss-portal/deployment-custom-parameters.png)

U ziet dat bij de implementatie de broncode rechtstreeks wordt opgehaald uit de opensource-opslagplaats op GitHub. Als u de opslagplaats hebt gevorkt, kunt u naar uw eigen vork en naar een specifieke vertakking verwijzen.

Nadat u de details hebt ingevuld, kunt u de implementatie starten.

## <a name="validate-fhir-server-is-running"></a>Controleren of de FHIR-server draait

Zodra de implementatie is voltooid, kunt u uw browser naar `https://SERVICENAME.azurewebsites.net/metadata` laten verwijzen om een mogelijkheidsinstructie te verkrijgen. Het duurt ongeveer een minuut voordat de server de eerste keer reageert.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep en alle gerelateerde resources verwijderen wanneer u deze niet meer nodig hebt. Hiertoe selecteert u de resourcegroep met de ingerichte resources, selecteert u **Resourcegroep verwijderen** en vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de Microsoft Open Source FHIR-server voor Azure geïmplementeerd in uw abonnement. Als u wilt weten hoe u de FHIR-API kunt openen met behulp van Postman, gaat u verder met de zelfstudie Postman.
 
>[!div class="nextstepaction"]
>[Toegang tot de FHIR-API met Postman](access-fhir-postman-tutorial.md)