---
title: Zelfstudie voor het voorbereiden van Azure-portal, datacenteromgeving om Azure Data Box Edge te implementeren | Microsoft Documenten
description: De eerste zelfstudie over het implementeren van Azure Data Box Edge omvat het voorbereiden van de Azure-portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 90ed4bf8f0389619f130e998ed76c720442092b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474472"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Zelfstudie: Voorbereiden op het implementeren van Azure Data Box Edge  

Dit is de eerste zelfstudie in de reeks zelfstudies voor implementatie die noodzakelijk zijn voor het voltooien van de implementatie van Azure Data Box Edge. In deze zelfstudie wordt beschreven hoe u Azure Portal voorbereidt voor de implementatie van een Data Box Edge-resource.

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. Het voorbereiden van de portal duurt minder dan 10 minuten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een nieuwe resource maken
> * De activeringssleutel ophalen

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

### <a name="get-started"></a>Aan de slag

Raadpleeg de volgende zelfstudies in de voorgeschreven volgorde voor het implementeren van Data Box Edge.

| **#** | **In deze stap** | **Gebruikt u deze documenten** |
| --- | --- | --- | 
| 1. |**[Azure Portal voorbereiden voor Data Box Edge](data-box-edge-deploy-prep.md)** |Maak en configureer uw Data Box Edge-resource voordat u een fysiek Data Box Edge-apparaat installeert. |
| 2. |**[Gegevensvakrand installeren](data-box-edge-deploy-install.md)**|Het fysieke Data Box Edge-apparaat uitpakken, plaatsen en aansluiten.  |
| 3. |**[Data Box Edge verbinden, instellen en activeren](data-box-edge-deploy-connect-setup-activate.md)** |Maak verbinding met de lokale webinterface, voltooi het instellen van het apparaat en activeer het. Het apparaat is klaar om er SMB- of NFS-shares op in te stellen.  |
| 4. |**[Gegevens overdragen met Data Box Edge](data-box-edge-deploy-add-shares.md)** |Voeg shares toe en maak verbinding met shares via SMB of NFS. |
| 5. |**[Gegevens transformeren met Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Configureer rekenmodules op het apparaat om de gegevens te transformeren terwijl deze naar Azure worden verplaatst. |

U kunt nu Azure Portal gaan instellen.

## <a name="prerequisites"></a>Vereisten

Hier volgen de configuratievereisten voor uw Data Box Edge-resource, uw Data Box Edge-apparaat en het datacenternetwerk.

### <a name="for-the-data-box-edge-resource"></a>Voor de Data Box Edge-resource

Zorg voordat u begint voor het volgende:

* Uw Microsoft Azure-abonnement is ingeschakeld voor een Azure Stack Edge-bron. Zorg ervoor dat u een ondersteund abonnement hebt gebruikt, zoals [Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/) [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)of [Microsoft Azure Sponsoring.](https://azure.microsoft.com/offers/ms-azr-0036p/)
* U hebt toegang tot de eigenaar of inzender op resourcegroepniveau voor de gegevensvakrand/gegevensboxgateway, IoT-hub en Azure Storage-bronnen.
    - Als u een Data Box Edge/Data Box Gateway-bron wilt maken, moet u machtigingen hebben als bijdrager (of hoger) op resourcegroepniveau. U moet er ook `Microsoft.DataBoxEdge` voor zorgen dat de provider is geregistreerd. Ga voor informatie over hoe u zich registreren naar [Resourceprovider registreren.](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers)
    - Als u een IoT Hub-bron wilt maken, controleert u of de microsoft.devices-provider is geregistreerd. Ga voor informatie over hoe u zich registreren naar [Resourceprovider registreren.](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers)
    - Als u een bron voor een opslagaccount wilt maken, hebt u opnieuw een bijdrager of een hoger toegangsbereik nodig op het niveau van de brongroep. Azure Storage is standaard een geregistreerde resourceprovider.
- U hebt beheerders- of gebruikerstoegang tot de Microsoft Graph API. Zie [Verwijzing naar microsoft graph-machtigingen voor](https://docs.microsoft.com/graph/permissions-reference)meer informatie .
- U hebt een Microsoft Azure Storage-account met toegangsreferenties.

### <a name="for-the-data-box-edge-device"></a>Voor het Data Box Edge-apparaat

Voordat u een fysiek apparaat implementeert, controleert u of:

- U hebt de veiligheidsinformatie bekeken die in het verzendpakket is opgenomen.
- U hebt een 1U-sleuf beschikbaar in een standaard 19"-rack in uw datacenter voor het monteren van het apparaat.
- U beschikt over een vlak, stabiel en horizontaal werkoppervlak waar u het apparaat veilig kunt neerleggen.
- De locatie waar u het apparaat wilt neerzetten, beschikt over reguliere wisselstroom van een onafhankelijke bron of over een vermogenseenheid (PDU) met noodvoeding (UPS).
- U hebt toegang tot een fysiek apparaat.


### <a name="for-the-datacenter-network"></a>Voor datacenternetwerk

Zorg voordat u begint voor het volgende:

- Het netwerk in uw datacenter is geconfigureerd volgens de netwerkvereisten voor uw Data Box Edge-apparaat. Zie [Systeemvereisten voor Data Box Edge](data-box-edge-system-requirements.md) voor meer informatie.

- Voor de normale bedrijfsomstandigheden van uw Data Box Edge hebt u:

    - Een downloadbandbreedte van minimaal 10 Mbps om ervoor te zorgen dat het apparaat up-to-date blijft.
    - Een minimum van 20 Mbps speciale upload- en downloadbandbreedte om bestanden over te zetten.

## <a name="create-a-new-resource"></a>Een nieuwe resource maken

Als u al een Data Box Edge-resource hebt voor het beheer van uw fysieke apparaat, kunt u deze stap overslaan en verdergaan met [Activeringscode ophalen](#get-the-activation-key).

Voer de volgende stappen uit in Azure Portal om een Data Box-resource te maken.

1. Uw Microsoft Azure-referenties gebruiken om u aan te melden bij 
    
    - De Azure-portal op [https://portal.azure.com](https://portal.azure.com)deze URL: .
    - Of de Azure Government-portal [https://portal.azure.us](https://portal.azure.us)op deze URL: . Ga voor meer informatie naar [Verbinding maken met Azure Government via de portal.](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal)

2. Selecteer in het linkerdeelvenster **+ Een resource maken**. Zoeken naar **Data Box Edge / Data Box Gateway**. Selecteer **Data Box Edge / Data Box Gateway**. Selecteer **Maken**.
3. Kies het abonnement dat u wilt gebruiken voor het Data Box Edge-apparaat. Selecteer de regio waar u de Data Box Edge-resource wilt implementeren. Zie [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)voor een lijst met alle regio's waar de Azure Stack Edge-bron beschikbaar is.

    Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren. De regio slaat alleen de metagegevens op voor apparaatbeheer. De werkelijke gegevens kunnen worden opgeslagen in elk opslagaccount.
    
    Selecteer bij **Maken** bij de optie **Data Box Edge**.

    ![Data Box Edge-service zoeken](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Voer op het tabblad **Basisbeginselen** de volgende **projectdetails**in of selecteer deze .
    
    |Instelling  |Waarde  |
    |---------|---------|
    |Abonnement    |Dit wordt automatisch ingevuld op basis van de eerdere selectie. Abonnement is gekoppeld aan uw factureringsrekening. |
    |Resourcegroep  |Maak een nieuwe groep of selecteer een bestaande groep.<br>Meer informatie over [Azure Resource Groups](../azure-resource-manager/management/overview.md).     |

4. Voer de volgende **instantiedetails**in of selecteer deze .

    |Instelling  |Waarde  |
    |---------|---------|
    |Name   | Een beschrijvende naam om de resource aan te duiden.<br>De naam is tussen 2 en 50 tekens lang en kan letters, cijfers en afbreekstreepjes bevatten.<br> De naam begint en eindigt met een letter of cijfer.        |
    |Regio     |Zie [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)voor een lijst met alle regio's waar de Azure Stack Edge-bron beschikbaar is. Als u Azure Government gebruikt, zijn alle overheidsregio's beschikbaar zoals weergegeven in de [Azure-regio's.](https://azure.microsoft.com/global-infrastructure/regions/)<br> Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren.|

    ![Details van projecten en instance](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Selecteer **Volgende: verzendadres**.

    - Als u al een apparaat hebt, selecteert u de keuzelijst met invoervak voor **Ik heb een Data Box Edge-apparaat**.
    - Als dit het nieuwe apparaat is dat u bestelt, voert u de naam van de contactpersoon, het bedrijf, het adres in om het apparaat te verzenden en contactgegevens.

    ![Verzendadres voor nieuw apparaat](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Selecteer **Volgende: Controleren + maken**.

7. Bekijk op het tabblad **Controleren + maken** de **prijsdetails**, **gebruiksvoorwaarden**en de details voor uw resource. Selecteer het keuzelijstje voor **ik heb de privacyvoorwaarden bekeken.**

    ![Gegevensvak edge-brongegevens en privacyvoorwaarden bekijken](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Selecteer **Maken**.

Het maken van de resource duurt enkele minuten. Nadat de resource is gemaakt en geïmplementeerd, wordt u hiervan op de hoogte gesteld. Selecteer **Ga naar resource**.

![Ga naar de resource Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Nadat de bestelling is geplaatst, controleert Microsoft de bestelling en neemt contact met u op (via e-mail) met verzendgegevens.

![Melding voor controle van de databoxrandorder](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>De activeringssleutel ophalen

Nadat de Azure Data Box Edge-resource is geactiveerd, hebt u de activeringssleutel nodig. Deze sleutel wordt gebruikt om uw Data Box Edge-apparaat te activeren en te verbinden met de resource. U kunt deze sleutel nu ophalen, terwijl u Azure Portal geopend hebt.

1. Selecteer de resource die u hebt gemaakt. Selecteer **Overzicht** en selecteer **vervolgens Apparaatinstelling**.

    ![Apparaatinstellingen selecteren](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Selecteer op de tegel **Activeren** de **optie Toets genereren** om een activeringssleutel te maken. Selecteer het kopieerpictogram om de sleutel te kopiëren en op te slaan voor later gebruik.

    ![Activeringssleutel ophalen](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - De activeringssleutel verloopt drie dagen nadat deze is gegenereerd.
> - Als de sleutel is verlopen, genereert u een nieuwe sleutel. De oudere toegangssleutel is niet langer geldig.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Data Box Edge, zoals:

> [!div class="checklist"]
> * Een nieuwe resource maken
> * De activeringssleutel ophalen

Ga naar de volgende zelfstudie om te lezen hoe u Data Box Edge installeert.

> [!div class="nextstepaction"]
> [Data Box Edge installeren](./data-box-edge-deploy-install.md)



