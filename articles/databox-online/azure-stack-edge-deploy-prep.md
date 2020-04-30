---
title: Zelf studie voor het voorbereiden van Azure Portal, Datacenter omgeving om Azure Stack Edge te implementeren | Microsoft Docs
description: De eerste zelf studie over het implementeren van Azure Stack Edge omvat het voorbereiden van de Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: e453723692e0e6c08c1bd03301318688082201c2
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570949"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge"></a>Zelf studie: de implementatie van Azure Stack Edge voorbereiden  

Dit is de eerste zelf studie in de reeks implementatie-zelf studies die vereist zijn om Azure Stack Edge volledig te implementeren. In deze zelf studie wordt beschreven hoe u de Azure Portal voorbereidt voor het implementeren van een Azure Stack Edge-resource.

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. Het voorbereiden van de portal duurt minder dan 10 minuten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe resource maken
> * De activeringssleutel ophalen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

### <a name="get-started"></a>Aan de slag

Als u Azure Stack Edge wilt implementeren, raadpleegt u de volgende zelf studies in de voorgeschreven volg orde.

| **#** | **In deze stap** | **Gebruikt u deze documenten** |
| --- | --- | --- | 
| 1. |**[De Azure Portal voorbereiden voor Azure Stack Edge](azure-stack-edge-deploy-prep.md)** |Maak en configureer uw Azure Stack Edge-resource voordat u een Azure Stack Box edge-apparaat installeert. |
| 2. |**[Azure Stack rand installeren](azure-stack-edge-deploy-install.md)**|Het fysieke apparaat van de Azure Stack rand uitpakken, op het rek bekabelen en aansluiten.  |
| 3. |**[Azure Stack Edge verbinden, instellen en activeren](azure-stack-edge-deploy-connect-setup-activate.md)** |Maak verbinding met de lokale webinterface, voltooi het instellen van het apparaat en activeer het. Het apparaat is klaar om er SMB- of NFS-shares op in te stellen.  |
| 4. |**[Gegevens overdragen met Azure Stack Edge](azure-stack-edge-deploy-add-shares.md)** |Voeg shares toe en maak verbinding met shares via SMB of NFS. |
| 5. |**[Gegevens transformeren met Azure Stack rand](azure-stack-edge-deploy-configure-compute.md)** |Configureer Compute-modules op het apparaat om de gegevens te transformeren wanneer deze naar Azure worden verplaatst. |

U kunt nu Azure Portal gaan instellen.

## <a name="prerequisites"></a>Vereisten

Hieronder vindt u de configuratie vereisten voor uw Azure Stack Edge-resource, uw Azure Stack edge-apparaat en het Data Center-netwerk.

### <a name="for-the-azure-stack-edge-resource"></a>Voor de Azure Stack Edge-resource

Zorg voordat u begint voor het volgende:

- Uw Microsoft Azure-abonnement is ingeschakeld voor een Azure Stack Edge-resource. Betalen per gebruik-abonnementen worden niet ondersteund.
- U hebt toegang tot de eigenaar of de Inzender op het niveau van de resource groep voor de Azure Stack EDGE/Data Box Gateway, IoT Hub en Azure Storage resources.

    - Als u een Azure Stack EDGE/Data Box Gateway resource wilt maken, moet u machtigingen hebben als Inzender (of hoger) op het niveau van de resource groep. U moet er ook voor zorgen dat de `Microsoft.DataBoxEdge` provider is geregistreerd. Voor informatie over het registreren gaat u naar [registreren resource provider](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Als u een IoT Hub resource wilt maken, moet u ervoor zorgen dat de provider micro soft. devices is geregistreerd. Voor informatie over het registreren gaat u naar [registreren resource provider](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Als u een opslag account resource wilt maken, moet u op het niveau van de resource groep opnieuw een Inzender of een hogere toegangs scope hebben. Azure Storage is standaard een geregistreerde resource provider.
- U hebt beheerder-of gebruikers toegang tot Azure Active Directory Graph API. Zie [Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)voor meer informatie.
- U hebt een Microsoft Azure Storage-account met toegangsreferenties.

### <a name="for-the-azure-stack-edge-device"></a>Voor het Azure Stack edge-apparaat

Voordat u een fysiek apparaat implementeert, controleert u of:

- U hebt de beveiligings gegevens die zijn opgenomen in het verzend pakket, gecontroleerd.
- U hebt een 1U-sleuf beschikbaar in een Standard 19-rack in uw Data Center voor rack montage op het apparaat.
- U beschikt over een vlak, stabiel en horizontaal werkoppervlak waar u het apparaat veilig kunt neerleggen.
- De locatie waar u het apparaat wilt neerzetten, beschikt over reguliere wisselstroom van een onafhankelijke bron of over een vermogenseenheid (PDU) met noodvoeding (UPS).
- U hebt toegang tot een fysiek apparaat.


### <a name="for-the-datacenter-network"></a>Voor datacenternetwerk

Zorg voordat u begint voor het volgende:

- Het netwerk in uw Data Center wordt geconfigureerd volgens de netwerk vereisten voor uw Azure Stack edge-apparaat. Zie [Azure stack Edge-systeem vereisten](azure-stack-edge-system-requirements.md)voor meer informatie.

- Voor normale bedrijfs omstandigheden van uw Azure Stack Edge hebt u het volgende nodig:

    - Mini maal 10 Mbps down load bandbreedte om ervoor te zorgen dat het apparaat bijgewerkt blijft.
    - Mini maal 20 Mbps toegewezen upload-en download bandbreedte voor het overdragen van bestanden.

## <a name="create-a-new-resource"></a>Een nieuwe resource maken

Als u een bestaande Azure Stack Edge-resource hebt om uw fysieke apparaat te beheren, slaat u deze stap over en gaat u naar [de activerings sleutel ophalen](#get-the-activation-key).

Als u een Azure Stack-rand resource wilt maken, voert u de volgende stappen uit in de Azure Portal.

1. Uw Microsoft Azure referenties gebruiken om u aan te melden bij 
    
    - De Azure Portal op deze URL: [https://portal.azure.com](https://portal.azure.com).
    - Of de Azure Government Portal op deze URL: [https://portal.azure.us](https://portal.azure.us). Ga voor meer informatie naar [verbinding maken met Azure Government met behulp van de portal](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. Selecteer in het linkerdeel venster **+ een resource maken**. Zoek en selecteer **Azure stack rand/data Box gateway**. Selecteer **Maken**.
3. Kies het abonnement dat u wilt gebruiken voor het Azure Stack edge-apparaat. Selecteer de regio waar u de resource voor de Azure Stack rand wilt implementeren. Zie [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)voor een lijst met alle regio's waar de Azure stack Edge-resource beschikbaar is.

    Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren. In de regio worden alleen de meta gegevens voor Apparaatbeheer opgeslagen. De werkelijke gegevens kunnen worden opgeslagen in elk opslag account.
    
    Selecteer **maken**in de optie **Azure stack Edge** .

    ![Azure Stack Edge-service zoeken](media/azure-stack-edge-deploy-prep/data-box-edge-sku.png)

3. Voer op het tabblad **basis beginselen** de volgende **Project gegevens**in of Selecteer deze.
    
    |Instelling  |Waarde  |
    |---------|---------|
    |Abonnement    |Dit wordt automatisch ingevuld op basis van de eerdere selectie. Abonnement is gekoppeld aan uw factureringsrekening. |
    |Resourcegroep  |Maak een nieuwe groep of selecteer een bestaande groep.<br>Meer informatie over [Azure-resource groepen](../azure-resource-manager/resource-group-overview.md).     |

4. Voer de volgende **exemplaar gegevens**in of Selecteer deze.

    |Instelling  |Waarde  |
    |---------|---------|
    |Naam   | Een beschrijvende naam om de resource aan te duiden.<br>De naam is tussen 2 en 50 tekens lang en kan letters, cijfers en afbreekstreepjes bevatten.<br> De naam begint en eindigt met een letter of cijfer.        |
    |Regio     |Zie [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)voor een lijst met alle regio's waar de Azure stack Edge-resource beschikbaar is. Als u Azure Government gebruikt, zijn alle overheids regio's beschikbaar, zoals weer gegeven in de [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).<br> Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren.|

    ![Details van project en exemplaar](media/azure-stack-edge-deploy-prep/data-box-edge-resource.png)

5. Selecteer **volgende: verzend adres**.

    - Als u al een apparaat hebt, selecteert u de keuze lijst met invoervak voor **Ik heb een Azure stack edge-apparaat**.
    - Als dit het nieuwe apparaat is dat u bestelt, voert u de naam van de contact persoon, het bedrijf, het adres voor het verzenden van het apparaat en contact gegevens in.

    ![Verzend adres voor nieuw apparaat](media/azure-stack-edge-deploy-prep/data-box-edge-resource1.png)

6. Selecteer **volgende: controleren + maken**.

7. Controleer op het tabblad **controleren en maken** de **prijs informatie**, de **Gebruiksvoorwaarden**en de details van uw resource. Selecteer de keuze lijst met invoervak voor **Ik heb de privacyverklaringen gecontroleerd**.

    ![Details van de resource en de privacyverklaring van Azure Stack-rand controleren](media/azure-stack-edge-deploy-prep/data-box-edge-resource2.png)

8. Selecteer **Maken**.

Het maken van de resource duurt enkele minuten. Nadat de resource is gemaakt en geïmplementeerd, wordt u hiervan op de hoogte gebracht. Selecteer **Ga naar resource**.

![Ga naar de Azure Stack Edge-resource](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

Nadat de bestelling is geplaatst, controleert micro soft de bestelling en gaat u naar u (via e-mail) met verzend gegevens.

![Melding voor beoordeling van de volg orde van de Azure Stack rand](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>De activeringssleutel ophalen

Nadat de Azure Stack-rand resource actief is, moet u de activerings sleutel ophalen. Deze sleutel wordt gebruikt om uw Azure Stack edge-apparaat te activeren en er verbinding mee te maken met de resource. U kunt deze sleutel nu ophalen, terwijl u Azure Portal geopend hebt.

1. Selecteer de resource die u hebt gemaakt. Selecteer **overzicht** en selecteer vervolgens **apparaat instellen**.

    ![Apparaat-installatie selecteren](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Selecteer op de tegel **activeren** de optie **sleutel genereren** om een activerings sleutel te maken. Selecteer het kopieerpictogram om de sleutel te kopiëren en op te slaan voor later gebruik.

    ![Activeringssleutel ophalen](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - De activeringssleutel verloopt drie dagen nadat deze is gegenereerd.
> - Als de sleutel is verlopen, genereert u een nieuwe sleutel. De oudere toegangssleutel is niet langer geldig.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd over Azure Stack Edge-onderwerpen, zoals:

> [!div class="checklist"]
> * Een nieuwe resource maken
> * De activeringssleutel ophalen

Ga naar de volgende zelf studie voor meer informatie over het installeren van Azure Stack Edge.

> [!div class="nextstepaction"]
> [Azure Stack rand installeren](./azure-stack-edge-deploy-install.md)



