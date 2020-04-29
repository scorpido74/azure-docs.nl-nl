---
title: Zelf studie voor het voorbereiden van Azure Portal, Datacenter omgeving om Azure Data Box Edge te implementeren | Microsoft Docs
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79474472"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Zelf studie: de implementatie van Azure Data Box Edge voorbereiden  

Dit is de eerste zelfstudie in de reeks zelfstudies voor implementatie die noodzakelijk zijn voor het voltooien van de implementatie van Azure Data Box Edge. In deze zelfstudie wordt beschreven hoe u Azure Portal voorbereidt voor de implementatie van een Data Box Edge-resource.

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. Het voorbereiden van de portal duurt minder dan 10 minuten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een nieuwe resource maken
> * De activeringssleutel ophalen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

### <a name="get-started"></a>Aan de slag

Raadpleeg de volgende zelfstudies in de voorgeschreven volgorde voor het implementeren van Data Box Edge.

| **#** | **In deze stap** | **Gebruikt u deze documenten** |
| --- | --- | --- | 
| 1. |**[Azure Portal voorbereiden voor Data Box Edge](data-box-edge-deploy-prep.md)** |Maak en configureer uw Data Box Edge-resource voordat u een fysiek Data Box Edge-apparaat installeert. |
| 2. |**[Data Box Edge installeren](data-box-edge-deploy-install.md)**|Het fysieke Data Box Edge-apparaat uitpakken, plaatsen en aansluiten.  |
| 3. |**[Data Box Edge verbinden, instellen en activeren](data-box-edge-deploy-connect-setup-activate.md)** |Maak verbinding met de lokale webinterface, voltooi het instellen van het apparaat en activeer het. Het apparaat is klaar om er SMB- of NFS-shares op in te stellen.  |
| 4. |**[Gegevens overdragen met Data Box Edge](data-box-edge-deploy-add-shares.md)** |Voeg shares toe en maak verbinding met shares via SMB of NFS. |
| 5. |**[Gegevens transformeren met Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Configureer Compute-modules op het apparaat om de gegevens te transformeren wanneer deze naar Azure worden verplaatst. |

U kunt nu Azure Portal gaan instellen.

## <a name="prerequisites"></a>Vereisten

Hier volgen de configuratievereisten voor uw Data Box Edge-resource, uw Data Box Edge-apparaat en het datacenternetwerk.

### <a name="for-the-data-box-edge-resource"></a>Voor de Data Box Edge-resource

Zorg voordat u begint voor het volgende:

* Uw Microsoft Azure-abonnement is ingeschakeld voor een Azure Stack Edge-resource. Zorg ervoor dat u een ondersteund abonnement hebt gebruikt, zoals [micro soft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)of [Microsoft Azure sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* U hebt toegang tot de eigenaar of de Inzender op het niveau van de resource groep voor de resources Data Box Edge/Data Box Gateway, IoT Hub en Azure Storage.
    - Als u een Data Box Edge/Data Box Gateway resource wilt maken, moet u machtigingen hebben als Inzender (of hoger) op het niveau van de resource groep. U moet er ook voor zorgen dat de `Microsoft.DataBoxEdge` provider is geregistreerd. Voor informatie over het registreren gaat u naar [registreren resource provider](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Als u een IoT Hub resource wilt maken, moet u ervoor zorgen dat de provider micro soft. devices is geregistreerd. Voor informatie over het registreren gaat u naar [registreren resource provider](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Als u een opslag account resource wilt maken, moet u op het niveau van de resource groep opnieuw een Inzender of een hogere toegangs scope hebben. Azure Storage is standaard een geregistreerde resource provider.
- U hebt beheerder-of gebruikers toegang tot Microsoft Graph-API. Zie [Microsoft Graph permissions Reference](https://docs.microsoft.com/graph/permissions-reference)(Engelstalig) voor meer informatie.
- U hebt een Microsoft Azure Storage-account met toegangsreferenties.

### <a name="for-the-data-box-edge-device"></a>Voor het Data Box Edge-apparaat

Voordat u een fysiek apparaat implementeert, controleert u of:

- U hebt de beveiligings gegevens die zijn opgenomen in het verzend pakket, gecontroleerd.
- U hebt een 1U-sleuf beschikbaar in een Standard 19-rack in uw Data Center voor rack montage op het apparaat.
- U beschikt over een vlak, stabiel en horizontaal werkoppervlak waar u het apparaat veilig kunt neerleggen.
- De locatie waar u het apparaat wilt neerzetten, beschikt over reguliere wisselstroom van een onafhankelijke bron of over een vermogenseenheid (PDU) met noodvoeding (UPS).
- U hebt toegang tot een fysiek apparaat.


### <a name="for-the-datacenter-network"></a>Voor datacenternetwerk

Zorg voordat u begint voor het volgende:

- Het netwerk in uw datacenter is geconfigureerd volgens de netwerkvereisten voor uw Data Box Edge-apparaat. Zie [Systeemvereisten voor Data Box Edge](data-box-edge-system-requirements.md) voor meer informatie.

- Voor normale bedrijfs omstandigheden van uw Data Box Edge hebt u het volgende nodig:

    - Mini maal 10 Mbps down load bandbreedte om ervoor te zorgen dat het apparaat bijgewerkt blijft.
    - Mini maal 20 Mbps toegewezen upload-en download bandbreedte voor het overdragen van bestanden.

## <a name="create-a-new-resource"></a>Een nieuwe resource maken

Als u al een Data Box Edge-resource hebt voor het beheer van uw fysieke apparaat, kunt u deze stap overslaan en verdergaan met [Activeringscode ophalen](#get-the-activation-key).

Voer de volgende stappen uit in Azure Portal om een Data Box-resource te maken.

1. Uw Microsoft Azure referenties gebruiken om u aan te melden bij 
    
    - De Azure Portal op deze URL: [https://portal.azure.com](https://portal.azure.com).
    - Of de Azure Government Portal op deze URL: [https://portal.azure.us](https://portal.azure.us). Ga voor meer informatie naar [verbinding maken met Azure Government met behulp van de portal](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. Selecteer in het linkerdeel venster **+ een resource maken**. Zoeken naar **Data Box EDGE/Data Box gateway**. Selecteer **Data Box EDGE/Data Box gateway**. Selecteer **Maken**.
3. Kies het abonnement dat u wilt gebruiken voor het Data Box Edge apparaat. Selecteer de regio waar u de Data Box Edge-resource wilt implementeren. Zie [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)voor een lijst met alle regio's waar de Azure stack Edge-resource beschikbaar is.

    Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren. In de regio worden alleen de meta gegevens voor Apparaatbeheer opgeslagen. De werkelijke gegevens kunnen worden opgeslagen in elk opslag account.
    
    Selecteer bij **Maken** bij de optie **Data Box Edge**.

    ![Data Box Edge-service zoeken](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Voer op het tabblad **basis beginselen** de volgende **Project gegevens**in of Selecteer deze.
    
    |Instelling  |Waarde  |
    |---------|---------|
    |Abonnement    |Dit wordt automatisch ingevuld op basis van de eerdere selectie. Abonnement is gekoppeld aan uw factureringsrekening. |
    |Resourcegroep  |Maak een nieuwe groep of selecteer een bestaande groep.<br>Meer informatie over [Azure-resource groepen](../azure-resource-manager/management/overview.md).     |

4. Voer de volgende **exemplaar gegevens**in of Selecteer deze.

    |Instelling  |Waarde  |
    |---------|---------|
    |Naam   | Een beschrijvende naam om de resource aan te duiden.<br>De naam is tussen 2 en 50 tekens lang en kan letters, cijfers en afbreekstreepjes bevatten.<br> De naam begint en eindigt met een letter of cijfer.        |
    |Regio     |Zie [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)voor een lijst met alle regio's waar de Azure stack Edge-resource beschikbaar is. Als u Azure Government gebruikt, zijn alle overheids regio's beschikbaar, zoals weer gegeven in de [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).<br> Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren.|

    ![Details van project en exemplaar](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Selecteer **volgende: verzend adres**.

    - Als u al een apparaat hebt, selecteert u de keuze lijst met invoervak voor **Ik heb een Data Box edge apparaat**.
    - Als dit het nieuwe apparaat is dat u bestelt, voert u de naam van de contact persoon, het bedrijf, het adres voor het verzenden van het apparaat en contact gegevens in.

    ![Verzend adres voor nieuw apparaat](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Selecteer **volgende: controleren + maken**.

7. Controleer op het tabblad **controleren en maken** de **prijs informatie**, de **Gebruiksvoorwaarden**en de details van uw resource. Selecteer de keuze lijst met invoervak voor **Ik heb de privacyverklaringen gecontroleerd**.

    ![Details van de Data Box Edge-resource en de privacyverklaring controleren](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Selecteer **Maken**.

Het maken van de resource duurt enkele minuten. Nadat de resource is gemaakt en geïmplementeerd, wordt u hiervan op de hoogte gebracht. Selecteer **Ga naar resource**.

![Ga naar de Data Box Edge resource](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Nadat de bestelling is geplaatst, controleert micro soft de bestelling en gaat u naar u (via e-mail) met verzend gegevens.

![Melding voor beoordeling van de Data Box Edge order](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>De activeringssleutel ophalen

Nadat de Azure Data Box Edge-resource is geactiveerd, hebt u de activeringssleutel nodig. Deze sleutel wordt gebruikt om uw Data Box Edge-apparaat te activeren en te verbinden met de resource. U kunt deze sleutel nu ophalen, terwijl u Azure Portal geopend hebt.

1. Selecteer de resource die u hebt gemaakt. Selecteer **overzicht** en selecteer vervolgens **apparaat instellen**.

    ![Apparaat-installatie selecteren](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Selecteer op de tegel **activeren** de optie **sleutel genereren** om een activerings sleutel te maken. Selecteer het kopieerpictogram om de sleutel te kopiëren en op te slaan voor later gebruik.

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



