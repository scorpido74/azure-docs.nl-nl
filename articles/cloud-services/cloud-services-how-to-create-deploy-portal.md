---
title: Een cloudservice maken en implementeren | Microsoft Documenten
description: Meer informatie over het maken en implementeren van een cloudservice met behulp van de Azure-portal.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 53f53976b20359afc45abe1b25ca60325b5d6a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386167"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Een cloudservice maken en implementeren
De Azure-portal biedt u op twee manieren om een cloudservice te maken en te implementeren: *Snel maken* en *aanpassen maken.*

In dit artikel wordt uitgelegd hoe u de methode Snel maken gebruiken om een nieuwe cloudservice te maken en vervolgens **Uploaden** te gebruiken om een cloudservicepakket in Azure te uploaden en te implementeren. Wanneer u deze methode gebruikt, stelt de Azure-portal handige koppelingen beschikbaar voor het voltooien van alle vereisten. Als u klaar bent om uw cloudservice te implementeren wanneer u deze maakt, u beide tegelijk doen met Aangepaste maken.

> [!NOTE]
> Als u van plan bent uw cloudservice te publiceren vanuit Azure DevOps, gebruikt u Snel maken en stelt u Azure DevOps-publicatie in vanuit azure Quickstart of het dashboard. Zie [Continue levering aan Azure met Azure DevOps][TFSTutorialForCloudService]of zie help voor de pagina Snel aan de **slag** voor meer informatie.
>
>

## <a name="concepts"></a>Concepten
Er zijn drie componenten nodig om een toepassing als cloudservice in Azure te implementeren:

* **Servicedefinitie**  
  Het definitiebestand voor cloudservices (.csdef) definieert het servicemodel, inclusief het aantal rollen.
* **Serviceconfiguratie**  
  Het configuratiebestand voor cloudservices (.cscfg) biedt configuratie-instellingen voor de cloudservice en afzonderlijke rollen, inclusief het aantal rolexemplaren.
* **Servicepakket**  
  Het servicepakket (.cspkg) bevat de toepassingscode en -configuraties en het servicedefinitiebestand.

U hier meer over weten en hoe u een pakket [kunt](cloud-services-model-and-package.md)maken.

## <a name="prepare-your-app"></a>Uw app voorbereiden
Voordat u een cloudservice implementeren, moet u het cloudservicepakket (.cspkg) maken op basis van uw toepassingscode en een configuratiebestand voor cloudservices (cscfg). De Azure SDK biedt hulpprogramma's voor het voorbereiden van deze vereiste implementatiebestanden. U de SDK installeren vanaf de pagina [Azure Downloads,](https://azure.microsoft.com/downloads/) in de taal waarin u uw toepassingscode het liefst ontwikkelt.

Drie functies voor cloudservices vereisen speciale configuraties voordat u een servicepakket exporteert:

* Als u een cloudservice wilt implementeren die Ssl (Secure Sockets Layer) gebruikt voor gegevensversleuteling, [configureert u uw toepassing](cloud-services-configure-ssl-certificate-portal.md#modify) voor SSL.
* Als u Extern bureaublad-verbindingen wilt configureren voor rolinstanties, [configureert u de rollen](cloud-services-role-enable-remote-desktop-new-portal.md) voor Extern bureaublad.
* Als u verbose-monitoring voor uw cloudservice wilt configureren, schakelt u Azure Diagnostics in voor de cloudservice. *Minimale bewaking* (het standaardbewakingsniveau) maakt gebruik van prestatiemeteritems die zijn verzameld van de hostbesturingssystemen voor rolexemplaren (virtuele machines). *Verbose monitoring* verzamelt aanvullende statistieken op basis van prestatiegegevens binnen de rolinstanties om een betere analyse van problemen die optreden tijdens de verwerking van toepassingen mogelijk te maken. Zie Diagnostische gegevens inschakelen in [Azure](cloud-services-dotnet-diagnostics.md)voor meer informatie over het inschakelen van Azure Diagnostics.

Als u een cloudservice wilt maken met implementaties van webrollen of werknemersrollen, moet u [het servicepakket maken.](cloud-services-model-and-package.md#servicepackagecspkg)

## <a name="before-you-begin"></a>Voordat u begint
* Als u de Azure SDK niet hebt geïnstalleerd, klikt u op **Azure SDK installeren** om de pagina Azure [Downloads](https://azure.microsoft.com/downloads/)te openen en downloadt u de SDK voor de taal waarin u uw code het liefst ontwikkelt. (Je hebt de kans om dit later te doen.)
* Als een rolinstantie een certificaat vereist, maakt u de certificaten. Cloudservices vereisen een .pfx-bestand met een privésleutel. U de certificaten uploaden naar Azure terwijl u de cloudservice maakt en implementeert.

## <a name="create-and-deploy"></a>Maken en implementeren
1. Log in bij de [Azure-portal](https://portal.azure.com/).
2. Klik **op Een bron maken > Compute**en blader omlaag en klik op **Cloudservice**.

    ![Uw cloudservice publiceren](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. Voer in het nieuwe deelvenster **Cloudservice** een waarde in voor de **DNS-naam.**
4. Maak een nieuwe **resourcegroep** of selecteer een bestaande groep.
5. Selecteer een **locatie**.
6. Klik **op Pakket**. Hiermee wordt het **deelvenster Een pakket uploaden** geopend. Vul de vereiste velden in. Als een van uw rollen één instantie bevat, moet u ervoor zorgen **dat Implementeren zelfs als een of meer rollen één instantie bevatten,** is geselecteerd.
7. Controleer of **De implementatie van start** is geselecteerd.
8. Klik **op OK** en sluit het deelvenster Een pakket **uploaden.**
9. Als u geen certificaten hebt om toe te voegen, klikt u op **Maken**.

    ![Uw cloudservice publiceren](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Een certificaat uploaden
Als uw implementatiepakket is [geconfigureerd om certificaten te gebruiken,](cloud-services-configure-ssl-certificate-portal.md#modify)u het certificaat nu uploaden.

1. Selecteer **Certificaten**en selecteer in het deelvenster **Certificaten toevoegen** het SSL-certificaat .pfx-bestand en geef vervolgens het **wachtwoord** voor het certificaat op.
2. Klik **op Certificaat bijvoegen**en klik vervolgens op **OK** in het deelvenster **Certificaten toevoegen.**
3. Klik **op Maken** in het deelvenster **Cloudservice.** Wanneer de implementatie de status **Gereed** heeft bereikt, u doorgaan naar de volgende stappen.

    ![Uw cloudservice publiceren](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Controleer of uw implementatie is voltooid
1. Klik op het exemplaar van de cloudservice.

    De status moet aantonen dat de service **actief**is.
2. Klik **onder Essentials**op de **URL van** de site om uw cloudservice in een webbrowser te openen.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Volgende stappen
* [Algemene configuratie van uw cloudservice.](cloud-services-how-to-configure-portal.md)
* Een [aangepaste domeinnaam configureren](cloud-services-custom-domain-name-portal.md).
* [Beheer uw cloudservice.](cloud-services-how-to-manage-portal.md)
* [Ssl-certificaten](cloud-services-configure-ssl-certificate-portal.md)configureren .



