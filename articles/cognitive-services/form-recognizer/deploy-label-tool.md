---
title: Het voorbeeldlabelingshulpmiddel Formulierherkenning implementeren
titleSuffix: Azure Cognitive Services
description: Ontdek de verschillende manieren waarop u het labelingshulpmiddel Voor formulierherkenning implementeren om te helpen bij het onder toezicht leren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 7ddb4b2cd465b5e9542d777d33b9bd8cb952becd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531334"
---
# <a name="deploy-the-sample-labeling-tool"></a>Het voorbeeldhulpprogramma voor labelen implementeren

Het voorbeeldlabelingshulpprogramma Form Recognizer is een toepassing die een eenvoudige gebruikersinterface (UI) biedt, waarmee u formulieren (documenten) handmatig labelen met het oog op begeleid leren. In dit artikel geven we links en instructies die je leren hoe je:

* [Het voorbeeldlabelingsgereedschap lokaal uitvoeren](#run-the-sample-labeling-tool-locally)
* [Het voorbeeldlabelingshulpprogramma implementeren in een Azure Container Instance (ACI)](#deploy-with-azure-container-instances-aci)
* [Gebruik en draag bij aan de open-source OCR Form Labeling Tool](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Het voorbeeldlabelingsgereedschap lokaal uitvoeren

De snelste manier om gegevens te labelen is door het voorbeeldlabelingsprogramma lokaal uit te voeren. De volgende quickstart gebruikt de FORM Recognizer REST API en het voorbeeldlabelingsgereedschap om een aangepast model te trainen met handmatig gelabelde gegevens. 

* [Snelstart: labelformulieren, train een model en analyseer een formulier met behulp van het voorbeeldlabelgereedschap](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Implementeren met Azure Container Instances (ACI)

Voordat we aan de slag gaan, is het belangrijk op te merken dat er twee manieren zijn om het voorbeeldlabelingshulpprogramma te implementeren in een Azure Container Instance (ACI). Beide opties worden gebruikt om het voorbeeldlabelingsprogramma met ACI uit te voeren: 

* [Azure Portal gebruiken](#azure-portal)
* [Azure CLI gebruiken](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

Volg deze stappen om een nieuwe bron te maken met de Azure-portal: 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/signin/index/).
2. Selecteer **Een resource maken**. 
3. Selecteer vervolgens **Web-app**. 

   > [!div class="mx-imgBorder"]
   > ![Web-app selecteren](./media/quickstarts/formre-create-web-app.png)
   
4. Controleer eerst of het tabblad **Basisbeginselen** is geselecteerd. Nu moet je wat informatie geven: 

   > [!div class="mx-imgBorder"]
   > ![Basisbeginselen selecteren](./media/quickstarts/formre-select-basics.png)
   * Abonnement - Selecteer een bestaand Azure-abonnement
   * Resourcegroep : u een bestaande resourcegroep opnieuw gebruiken of een nieuwe groep voor dit project maken. Het maken van een nieuwe resourcegroep wordt aanbevolen.
   * Naam - Geef uw web-app een naam. 
   * Publiceren - **Dockercontainer selecteren**
   * Besturingssysteem - Selecteer **Linux**
   * Regio - Kies een regio die voor u zinvol is.
   * Linux-abonnement - Selecteer een prijscategorie/-abonnement voor uw app-service. 

   > [!div class="mx-imgBorder"]
   > ![Uw web-app configureren](./media/quickstarts/formre-select-docker-linux.png)

5. Selecteer vervolgens het tabblad **Docker.** 

   > [!div class="mx-imgBorder"]
   > ![Docker selecteren](./media/quickstarts/formre-select-docker.png)

6. Laten we nu uw Docker-container configureren. Alle velden zijn vereist, tenzij anders vermeld:

   * Opties - Eén **container selecteren**
   * Afbeeldingsbron - **Privéregister selecteren** 
   * Server-URL - Stel dit in op`https://mcr.microsoft.com`
   * Gebruikersnaam (optioneel) - Een gebruikersnaam maken. 
   * Wachtwoord (optioneel) - Maak een veilig wachtwoord dat u zult onthouden.
   * Afbeelding en tag - Stel dit in op`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * Continue implementatie : stel dit in op **Aan** als u automatische updates wilt ontvangen wanneer het ontwikkelteam wijzigingen aanbrengt in het voorbeeldlabelingsprogramma.
   * Opstartopdracht - Stel dit in op`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![Docker configureren](./media/quickstarts/formre-configure-docker.png)

7. Dat is alles. Selecteer vervolgens **Controleren + Maken**en vervolgens **Maken** om uw web-app te implementeren. Als u klaar bent, hebt u toegang tot uw web-app op de URL in het **overzicht** voor uw bron.

> [!NOTE]
> Bij het maken van uw web-app u ook autorisatie/verificatie configureren. Dit is niet nodig om aan de slag te gaan. 

### <a name="azure-cli"></a>Azure CLI

Als alternatief voor het gebruik van de Azure-portal u een bron maken met behulp van de Azure CLI. Voordat u verdergaat, moet u de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)installeren. U deze stap overslaan als u al met de Azure CLI werkt. 

Er zijn een paar dingen die je moet weten over deze opdracht:

* `DNS_NAME_LABEL=aci-demo-$RANDOM`genereert een willekeurige DNS-naam. 
* In dit voorbeeld wordt ervan uitgegaan dat u een resourcegroep hebt die u gebruiken om een resource te maken. Vervang `<resource_group_name>` door een geldige resourcegroep die is gekoppeld aan uw abonnement. 
* U moet opgeven waar u de resource wilt maken. Vervang `<region name>` de gewenste regio voor de web-app. 
* Dit commando accepteert automatisch EULA.

Voer vanuit Azure CLI deze opdracht uit om een web-app-bron voor het voorbeeldlabelingsgereedschap te maken: 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resorunce_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>Verbinding maken met Azure AD voor autorisatie

Het wordt aanbevolen om uw web-app te verbinden met Azure Active Directory. Dit zorgt ervoor dat alleen gebruikers met geldige referenties zich kunnen aanmelden en uw web-app kunnen gebruiken. Volg de instructies in [De app-app configureren](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) om verbinding te maken met Azure Active Directory.

## <a name="open-source-on-github"></a>Open source op GitHub

De OCR Form Labeling Tool is ook beschikbaar als een open-source project op GitHub. De tool is een webapplicatie die is gebouwd met React + Redux en is geschreven in TypeScript. Zie [OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)voor meer informatie of bijdragen.

## <a name="next-steps"></a>Volgende stappen

Gebruik de [trein met labels](./quickstarts/label-tool.md) snel aan de slag om te leren hoe u de tool gebruiken om trainingsgegevens handmatig te labelen en begeleid leren uit te voeren.
