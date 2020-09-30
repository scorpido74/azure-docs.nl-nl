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
ms.openlocfilehash: 57ab6bca820c4c25a9a56e4a801aa7d917d317ec
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978569"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Quickstart: Een Open Source FHIR-server implementeren met behulp van Azure Portal

In deze quickstart wordt uitgelegd hoe u een Open Source FHIR-server in Azure implementeert met behulp van Azure Portal. We gebruiken de koppelingen voor eenvoudige implementatie in de [opensource-opslagplaats](https://github.com/Microsoft/fhir-server)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="github-open-source-repository"></a>Opensource-GitHub-opslagplaats

Ga naar de pagina [GitHub-implementatie](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) en zoek de knoppen Implementeren naar Azure op:

>[!div class="mx-imgBorder"]
>![Pagina van opensource-implementatie](media/quickstart-oss-portal/deployment-page-oss.png)

Klik op de knop Implementatie. Azure Portal wordt geopend.

## <a name="fill-in-deployment-parameters"></a>Implementatieparameters invullen

Kies ervoor om een nieuwe resourcegroep te maken en geef deze een unieke naam. De enige andere vereiste parameters zijn een naam voor de service en het wachtwoord voor de SQL-beheerder.

>[!div class="mx-imgBorder"]
>![Aangepaste implementatieparameters](media/quickstart-oss-portal/deployment-custom-parameters.png)

Nadat u de details hebt ingevuld, kunt u de implementatie starten.

## <a name="validate-fhir-server-is-running"></a>Controleren of de FHIR-server draait

Zodra de implementatie is voltooid, kunt u uw browser naar `https://SERVICENAME.azurewebsites.net/metadata` laten verwijzen om een mogelijkheidsinstructie te verkrijgen. Het duurt ongeveer een minuut voordat de server de eerste keer reageert.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep en alle gerelateerde resources verwijderen wanneer u deze niet meer nodig hebt. Hiertoe selecteert u de resourcegroep met de ingerichte resources, selecteert u **Resourcegroep verwijderen** en vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de Microsoft Open Source FHIR-server voor Azure geïmplementeerd in uw abonnement. Als u wilt weten hoe u de FHIR-API kunt openen met behulp van Postman, gaat u verder met de zelfstudie Postman.
 
>[!div class="nextstepaction"]
>[Toegang tot FHIR-API met Postman](access-fhir-postman-tutorial.md)