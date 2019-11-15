---
title: 'Zelf studie: de weer gaven van de operator in azure IoT Central aanpassen'
description: 'Zelf studie: als opbouw functie past u de weer gaven van de operator aan in uw Azure IoT Central-toepassing.'
author: sandeeppujar
ms.author: sandeepu
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: d835ce5a1cbdcbd9741c775c4e9bd2eea7c803d7
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112336"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Zelfstudie: de operatorweergave van Azure IoT Central aanpassen

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In deze zelfstudie leert u, als systeembouwer, hoe u de operatorweergave van uw toepassing kunt aanpassen. Wanneer u als systeembouwer een wijziging in de toepassing aanbrengt, kunt u een voorbeeld van de operatorweergave in de Microsoft Azure IoT Central-toepassing bekijken.

In deze zelfstudie past u de toepassing aan om relevante informatie over het verbonden airco-apparaat weer te geven aan een operator. Dankzij uw aanpassingen kan de operator de airco-apparaten beheren die met de toepassing verbonden zijn.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw apparaatdashboard configureren
> * De indeling van uw apparaatinstellingen configureren
> * De indeling van uw apparaateigenschappen configureren
> * Voorbeeld van het apparaat weergeven als operator
> * Uw standaard toepassings dashboard configureren
> * Een voor beeld van het standaard toepassings dashboard als operator

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u de twee vorige zelfstudies uitvoeren:

* [Een nieuw apparaattype definiëren in uw Azure IoT Central-toepassing](tutorial-define-device-type.md).
* [Regels en acties voor uw apparaat configureren](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Uw apparaatdashboard configureren

Als systeembouwer kunt u bepalen welke informatie in een apparaatdashboard wordt weergegeven. In de zelfstudie [Een nieuw apparaattype definiëren in uw toepassing](tutorial-define-device-type.md) hebt u een lijndiagram en andere informatie toegevoegd aan het dashboard **Connected Air Conditioner**.

1. Kies **Apparaatbeheer** in het linkerdeel venster om de **aangesloten airconditioning** te bewerken:

    ![De pagina Apparaatsjablonen](media/tutorial-customize-operator/devicetemplates.png)

2. Als u het dash board van uw apparaat wilt aanpassen, selecteert u het apparaat sjabloon apparaat **voor verbonden Air-voor waardeer (1.0.0)** dat u hebt gemaakt in het [type een nieuw apparaat definiëren in](tutorial-define-device-type.md) de zelf studie voor uw toepassing.

3. Als u het dashboard wilt bewerken, selecteert u het tabblad **Dashboard**.

4. Als u een KPI-tegel (Key Performance Indicator) wilt toevoegen aan het dashboard, kiest u **KPI**:

    Voor het definiëren van de KPI kunt u de informatie in de volgende tabel gebruiken:

    | Instelling     | Waarde |
    | ----------- | ----- |
    | Naam        | Maximale temperatuur |
    | Tijdsbereik  | Afgelopen week 1 |
    | Metingtype | Telemetrie |
    | Meting | temperatuur |
    | Aggregatie | Maximum |
    | Zichtbaarheid  | Ingeschakeld |

    ![KPI toevoegen](media/tutorial-customize-operator/addkpi.png)

5. Selecteer **Opslaan**. U ziet nu de KPI-tegel in het dashboard:

    ![KPI-tegel](media/tutorial-customize-operator/temperaturekpi.png)

6. Als u een tegel in het dashboard wilt verplaatsen of het formaat ervan wilt wijzigen, houdt u de muisaanwijzer boven de tegel. U kunt de tegel naar een nieuwe locatie slepen of het formaat van de tegel wijzigen.

## <a name="configure-your-settings-layout"></a>De indeling van uw instellingen configureren

Als systeembouwer kunt u ook de operatorweergave van de apparaatinstellingen configureren. Een operator gebruikt het tabblad Apparaatinstellingen om een apparaat te configureren. Een operator gebruikt het tabblad Instellingen bijvoorbeeld om de doeltemperatuur voor het verbonden airco-apparaat in te stellen.

1. Als u de indeling van de instellingen voor het verbonden airco-apparaat wilt bewerken, kiest u het tabblad **Instellingen**.

2. U kunt de tegels voor instellingen verplaatsen en vergroten of verkleinen:

    ![De indeling van de instellingen bewerken](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>De indeling van de eigenschappen configureren

Naast het dashboard en instellingen kunt u ook de operatorweergave van de apparaateigenschappen configureren. Een operator maakt gebruik van het tabblad Apparaateigenschappen voor het beheren van metagegevens van apparaten. Een operator maakt bijvoorbeeld gebruik van het tabblad Eigenschappen om een serienummer van een apparaat te bekijken of om de contactgegevens van de fabrikant bij te werken.

1. Als u de indeling van de eigenschappen voor het verbonden airco-apparaat wilt bewerken, kiest u het tabblad **Eigenschappen**.

2. U kunt de eigenschapvelden verplaatsen en vergroten of verkleinen:

    ![De indeling van de eigenschappen bewerken](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>Voor beeld van het apparaat

U gebruikt de pagina **Apparaatsjablonen** om het dashboard, de instellingen, en het tabblad eigenschappen voor een operator aan te passen. U kunt de pagina **Apparaat** gebruiken om de apparaatsjabloon weer te geven en te gebruiken.

1. Als u het verbonden airco-apparaat als een operator wilt gebruiken, navigeert u naar de pagina **Device Explorer** en kiest u het gesimuleerde apparaat dat met IoT Central is gegenereerd uit de sjabloon:

    ![De apparaatsjabloon weergeven en gebruiken](media/tutorial-customize-operator/usetemplate.png)

2. Als u de locatie van dit apparaat wilt bijwerken, kiest u **Eigenschappen** en bewerkt u de waarde in de tegel Locatie. Selecteer vervolgens **Opslaan**:

    ![Een eigenschapswaarde wijzigen](media/tutorial-customize-operator/editproperty.png)

3. Als u een instelling naar uw verbonden airco-apparaat wilt verzenden, kiest u **Instellingen**, wijzigt u een instellingswaarde in een tegel en kiest u **Bijwerken**:

    ![Instelling naar apparaat verzenden](media/tutorial-customize-operator/sendsetting.png)

    Wanneer het apparaat de waarde van de nieuwe instelling bevestigt, wordt de instelling weergegeven als **gesynchroniseerd** op de tegel.

4. Als operator kunt u het dashboard van het apparaat weergeven zoals deze is geconfigureerd door de systeembouwer:

    ![Operatorweergave van het dashboard van het apparaat](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Het standaard dashboard configureren

Wanneer een Builder of operator zich aanmeldt bij een Azure IoT Central-toepassing, zien ze het dash board van de toepassing. Als opbouw functie kunt u de inhoud van het standaard dashboard configureren zodat de meest nuttige en relevante inhoud voor een operator wordt meegenomen.

> [!NOTE]
> Gebruikers kunnen ook hun eigen persoonlijke Dash boards maken en er een kiezen als standaard.

1. Als u het standaard toepassings dashboard wilt aanpassen, gaat u naar de pagina **dash board** en selecteert u **bewerken** rechtsboven op de pagina. Er wordt een paneel met een bibliotheek met objecten weer gegeven die u aan het dash board kunt toevoegen.

    ![Dashboard pagina](media/tutorial-customize-operator/builderhome.png)

2. Voeg tegels uit de **bibliotheek**toe om het dash board aan te passen. Kies **Koppeling** en voeg gegevens toe van de website van uw organisatie. Kies vervolgens **Opslaan**:

    ![Koppeling naar het dash board toevoegen](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > U kunt ook koppelingen toevoegen voor pagina's in uw Azure IoT Central-toepassing. U kunt bijvoorbeeld een koppeling toevoegen voor een apparaatdashboard of instellingenpagina.

3. Kies eventueel **afbeelding** en upload een afbeelding om weer te geven op uw dash board. Een afbeelding kan een URL bevatten waarnaar u navigeert wanneer u deze selecteert:

    ![Een afbeelding toevoegen aan het dash board](media/tutorial-customize-operator/addimage.png)

    Zie [Afbeeldingen voorbereiden en uploaden naar uw Azure IoT Central-toepassing](howto-prepare-images.md) voor meer informatie.

## <a name="preview-the-dashboard"></a>Het dash board bekijken

Als u een voor beeld van het toepassings dashboard wilt weer geven als een operator **, selecteert u** in de rechter bovenhoek van de pagina.

![De ontwerpmodus in- en uitschakelen](media/tutorial-customize-operator/operatorviewhome.png)

U kunt de tegels koppeling en afbeelding selecteren om te navigeren naar de Url's die u als een opbouw functie hebt ingesteld.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de operatorweergave van de toepassing kunt aanpassen.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Uw apparaatdashboard configureren
> * De indeling van uw apparaatinstellingen configureren
> * De indeling van uw apparaateigenschappen configureren
> * Voorbeeld van het apparaat weergeven als operator
> * De standaardstartpagina configureren
> * Voorbeeld van de standaardstartpagina weergeven als operator

Nu u hebt geleerd hoe u de operatorweergave van de toepassing kunt aanpassen, zijn dit de voorgestelde volgende stappen:

* [Uw apparaten bewaken (als operator)](tutorial-monitor-devices.md)
* [Een nieuw apparaat toevoegen aan uw toepassing (als operator en ontwikkelaar van het apparaat)](tutorial-add-device.md)