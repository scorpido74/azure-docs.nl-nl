---
title: 'Quick Start: een Analysis Services-server maken in Azure Portal | Microsoft Docs'
description: Meer informatie over het snel maken van een Azure Analysis Services Server-exemplaar met behulp van de Azure Portal.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4c1b22d4871d3e490f59be71b63569fdf563ce45
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75442839"
---
# <a name="quickstart-create-a-server---portal"></a>Snelstart: Een server maken - Portal

In deze snelstart wordt beschreven hoe u een Azure Analysis Services-serverresource in uw Azure-abonnement maakt met behulp van de portal.

## <a name="prerequisites"></a>Vereisten 

* **Azure-abonnement**: ga naar [gratis proefversie van Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) om een account te maken.
* **Azure Active Directory**: uw abonnement moet zijn gekoppeld aan een Azure Active Directory-tenant. En u moet zijn aangemeld bij Azure met een account in deze Azure Active Directory. Raadpleeg voor meer informatie [Verificatie en gebruikersmachtigingen](analysis-services-manage-users.md).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal 

[Aanmelden bij de portal](https://portal.azure.com)


## <a name="create-a-server"></a>Een server maken

1. Klik op **+ een resource** > **Analytics** > -**Analysis Services**maken.

    ![Portal](./media/analysis-services-create-server/aas-create-server-portal.png)

2. Vul in **Analysis Services** de vereiste velden in. Druk vervolgens op **Maken**.
   
   * **Servernaam**: typ een unieke naam om te verwijzen naar de server. De server naam moet beginnen met een kleine letter en tussen 3 en 128 kleine letters en cijfers bevatten. Spaties en speciale tekens zijn niet toegestaan.
   * **Abonnement**: selecteer het abonnement waaraan deze server wordt gekoppeld.
   * **Resourcegroep**: maak een nieuwe resourcegroep of selecteer een bestaande resourcegroep. Resourcegroepen zijn ontworpen om u te helpen bij het beheren van een verzameling Azure-resources. Zie [Resourcegroepen](../azure-resource-manager/management/overview.md) voor meer informatie.
   * **Locatie**: op deze Azure-datacenterlocatie wordt de server gehost. Kies een locatie die zich zo dicht mogelijk bij uw grootste gebruikersgroep bevindt.
   * **Prijscategorie**: selecteer een prijscategorie. Als u de voorbeeldmodeldatabase wilt testen en waarschijnlijk wilt installeren, selecteert u de gratis **D1**-laag. Zie [Prijzen van Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/) voor meer informatie. 
   * **Beheerder**: dit is standaard het account waarmee u bent aangemeld. U kunt een ander account kiezen in uw Azure Active Directory.
   * **Instelling voor back-upopslag**: optioneel. Als u al een [opslagaccount](../storage/common/storage-introduction.md) hebt, kunt u dit als de standaardopslag opgeven voor back-ups van de modeldatabase. U kunt de instellingen voor [back-up en herstellen](analysis-services-backup.md) ook later opgeven.
   * **Vervaldatum van de opslagsleutel**: optioneel. Geef een verloopperiode op voor de opslagsleutel.

Het maken van de server duurt gewoonlijk minder dan een minuut. Als u **Toevoegen aan de portal** hebt geselecteerd, navigeert u naar de portal om de nieuwe server te zien. U kunt ook naar **alle services** > **Analysis Services** gaan om te zien of uw server klaar is. Servers ondersteunen tabellaire modellen op de compatibiliteits niveaus 1200 en hoger. Het compatibiliteits niveau van het model is opgegeven in Visual Studio of SSMS.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de server als u deze niet meer nodig hebt. Klik in het **Overzicht** van de server op **Verwijderen**. 

 ![Opschonen](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Volgende stappen
In deze snelstart hebt u geleerd hoe u een server maakt in uw Azure-abonnement. Nu u een server hebt gemaakt, kunt u deze beveiligen door een serverfirewall te configureren. (Optioneel) U kunt ook rechtstreeks vanuit de portal een eenvoudig voorbeeldgegevensmodel toevoegen aan de server. Een voorbeeldmodel is handig als u meer wilt weten over het configureren van modeldatabaserollen en het testen van clientverbindingen. Als u meer wilt weten, gaat u verder met de zelfstudie waarin u leert een voorbeeldmodel toe te voegen.

> [!div class="nextstepaction"]
> [Snelstartgids: Server firewall configureren-Portal](analysis-services-qs-firewall.md)   
> [!div class="nextstepaction"]
> [Zelfstudie: Een voorbeeldmodel toevoegen aan uw server](analysis-services-create-sample-model.md)
