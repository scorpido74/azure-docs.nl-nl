---
title: Toegang tot privésite tot Azure-functies inschakelen
description: Meer informatie over het instellen van azure virtual network private site access for Azure Functions.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78851280"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Zelfstudie: Toegang tot azure-functies voor privé-site instellen

In deze zelfstudie ziet u hoe u [toegang tot privé-site's](./functions-networking-options.md#private-site-access) inschakelen met Azure-functies. Door toegang tot privé-site te gebruiken, u vereisen dat uw functiecode alleen wordt geactiveerd vanuit een specifiek virtueel netwerk.

Toegang tot privé-site's is handig in scenario's waarin de toegang tot de functie-app moet worden beperkt tot een specifiek virtueel netwerk. De functie-app kan bijvoorbeeld alleen van toepassing zijn op werknemers van een specifieke organisatie of services die zich binnen het opgegeven virtuele netwerk bevinden (zoals een andere Azure-functie, Azure Virtual Machine of een AKS-cluster).

Als een Functie-app toegang moet krijgen tot Azure-bronnen binnen het virtuele netwerk of verbonden is via [serviceeindpunten,](../virtual-network/virtual-network-service-endpoints-overview.md)is [virtuele netwerkintegratie](./functions-create-vnet.md) nodig.

In deze zelfstudie leert u hoe u toegang tot privé-site's voor uw functie-app configureert:

> [!div class="checklist"]
> * Een virtuele machine maken
> * Een Azure Bastion-service maken
> * Een Azure Functions-app maken
> * Een eindpunt van een virtuele netwerkservice configureren
> * Een Azure-functie maken en implementeren
> * De functie van buiten en binnen het virtuele netwerk aanroepen

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="topology"></a>Topologie

In het volgende diagram ziet u de architectuur van de te maken oplossing:

![Architectuurdiagram op hoog niveau voor toegang tot privésite-toegang](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie is het belangrijk dat u IP-adressering en subnetting begrijpt. U beginnen met [dit artikel dat de basisprincipes van het aanpakken en subnetting omvat.](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics) Veel meer artikelen en video's zijn online beschikbaar.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

De eerste stap in deze tutorial is het maken van een nieuwe virtuele machine in een virtueel netwerk.  De virtuele machine zal worden gebruikt om toegang te krijgen tot uw functie als u eenmaal hebt beperkt zijn toegang tot alleen beschikbaar zijn vanuit het virtuele netwerk.

1. Selecteer de knop **Een resource maken.**

2. Typ `Windows Server`en selecteer **Windows Server** in het zoekveld in de zoekresultaten.

3. Selecteer **Windows Server 2019 Datacenter** in de lijst met Opties voor Windows Server en druk op de knop **Maken.**

4. Gebruik op het tabblad **Basisbeginselen** de VM-instellingen zoals opgegeven in de tabel onder de afbeelding:

    >[!div class="mx-imgBorder"]
    >![Tabblad Basisbeginselen voor een nieuwe Windows-vm](./media/functions-create-private-site-access/create-vm-3.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waaronder uw resources worden gemaakt. |
    | [**Resourcegroep**](../azure-resource-manager/management/overview.md) | myResourceGroup | Kies de resourcegroep om alle bronnen voor deze zelfstudie te bevatten.  Het gebruik van dezelfde resourcegroep maakt het gemakkelijker om resources op te schonen wanneer u klaar bent met deze zelfstudie. |
    | **Naam virtuele machine** | myVM | De VM-naam moet uniek zijn in de resourcegroep |
    | [**Regio**](https://azure.microsoft.com/regions/) | (VS) Noord Centraal VS | Kies een regio bij u in de buurt of in de buurt van de functies die moeten worden geopend. |
    | **Openbare inkomende poorten** | Geen | Selecteer **Geen** om ervoor te zorgen dat er geen inkomende verbinding met de VM vanaf het internet is. Externe toegang tot de VM wordt geconfigureerd via de Azure Bastion-service. |

5. Kies het tabblad **Netwerken** en selecteer **Nieuw maken** om een nieuw virtueel netwerk te configureren.

    >[!div class="mx-imgBorder"]
    >![Een nieuw virtueel netwerk maken voor de nieuwe virtuele machine](./media/functions-create-private-site-access/create-vm-networking.png)

6. Gebruik **in Virtueel netwerk maken**de instellingen in de tabel onder de afbeelding:

    >[!div class="mx-imgBorder"]
    >![Een nieuw virtueel netwerk maken voor de nieuwe virtuele machine](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Naam** | myResourceGroup-vnet | U de standaardnaam gebruiken die is gegenereerd voor uw virtuele netwerk. |
    | **Adresbereik** | 10.10.0.0/16 | Gebruik één adresbereik voor het virtuele netwerk. |
    | **Subnetnaam** | Zelfstudie | Naam van het subnet. |
    | **Adresbereik** (subnet) | 10.10.1.0/24 | De subnetgrootte bepaalt hoeveel interfaces aan het subnet kunnen worden toegevoegd. Dit subnet wordt gebruikt door de VM. Een `/24` subnet bevat 254 hostadressen. |

7. Selecteer **OK** om het virtuele netwerk te maken.
8. Controleer op het tabblad **Netwerken** of **Geen** is geselecteerd voor **openbaar IP.**
9. Kies het tabblad **Beheer** en kies vervolgens in **diagnostisch opslagaccount**De optie **Nieuw maken** om een nieuw opslagaccount te maken.
10. Laat de standaardwaarden voor de secties **Identiteit,** **Automatisch afsluiten**en **Back-up.**
11. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **Maken**. Het vm-maakproces duurt enkele minuten.

## <a name="configure-azure-bastion"></a>Azure Bastion configureren

[Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) is een volledig beheerde Azure-service die veilige RDP- en SSH-toegang tot virtuele machines rechtstreeks vanuit de Azure-portal biedt. Als u de Azure Bastion-service gebruikt, hoeft u netwerkinstellingen met betrekking tot RDP-toegang te configureren.

1. Kies in de portal **Toevoegen** boven aan de weergave resourcegroep.
2. Typ in het zoekveld 'Bastion'.  Selecteer "Bastion".
3. Selecteer **Maken** om te beginnen met het proces voor het maken van een nieuwe Azure Bastion-bron. U ziet een foutmelding in de sectie **Virtueel** netwerk `AzureBastionSubnet` omdat er nog geen subnet is. Het subnet wordt gemaakt in de volgende stappen. Gebruik de instellingen in de tabel onder de afbeelding:

    >[!div class="mx-imgBorder"]
    >![Beginnen met het maken van Azure Bastion](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Naam** | myBastion | De naam van de nieuwe Bastion-bron |
    | **Regio** | VS - noord-centraal | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |
    | **Virtueel netwerk** | myResourceGroup-vnet | Het virtuele netwerk waarin de Bastion-bron wordt gemaakt in |
    | **Subnet** | AzureBastionSubnet | Het subnet in uw virtuele netwerk waarop de nieuwe Bastion-hostbron wordt geïmplementeerd. U moet een subnet maken `AzureBastionSubnet`met de naamwaarde . Met deze waarde weet Azure naar welk subnet de Bastion-resources moeten worden geïmplementeerd. U moet een subnet `/27` gebruiken van`/27` `/26`ten minste of groter ( , , enzovoort). |

    > [!NOTE]
    > Raadpleeg de zelfstudie [Van een Azure Bastion-host maken](../bastion/bastion-create-host-portal.md) voor een gedetailleerde stapsgewijze handleiding voor het maken van een Azure Bastion-bron.

4. Maak een subnet waarin Azure de Azure Bastion-host kan inrichten. Als u **subnetconfiguratie beheren kiest,** wordt een nieuw deelvenster geopend waarin u een nieuw subnet definiëren.  Kies **+ Subnet** om een nieuw subnet te maken.
5. Het subnet moet van `AzureBastionSubnet` de naam zijn en het `/27`subnetvoorvoeging moet ten minste .  Selecteer **OK** om het subnet te maken.

    >[!div class="mx-imgBorder"]
    >![Subnet maken voor Azure Bastion-host](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. Selecteer **op de** pagina Een Bastion `AzureBastionSubnet` maken de nieuw gemaakte in de lijst met beschikbare subnetten.

    >[!div class="mx-imgBorder"]
    >![Een Azure Bastion-host maken met specifiek subnet](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. Selecteer **Controleren & maken**. Zodra de validatie is voltooid, selecteert u **Maken**. Het duurt enkele minuten voordat de Azure Bastion-bron is gemaakt.

## <a name="create-an-azure-functions-app"></a>Een Azure Functions-app maken

De volgende stap is het maken van een functie-app in Azure met behulp van het [verbruiksplan](functions-scale.md#consumption-plan). U implementeert uw functiecode later in de zelfstudie naar deze bron.

1. Kies in de portal **Toevoegen** boven aan de weergave resourcegroep.
2. De **functie-app Berekenen > selecteren**
3. Gebruik in de sectie **Basisbeginselen** de instellingen van de functie-app zoals aangegeven in de onderstaande tabel.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Resourcegroep** | myResourceGroup | Kies de resourcegroep om alle bronnen voor deze zelfstudie te bevatten.  Met dezelfde resourcegroep voor de functie-app en VM u gemakkelijker resources opschonen wanneer u klaar bent met deze zelfstudie. |
    | **Functie-app-naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn a-z (geval ongevoelig), 0-9 en -. |
    | **Publiceren** | Code | Optie voor het publiceren van codebestanden of een Docker-container. |
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. |
    | **Regio** | VS - noord-centraal | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

    Selecteer de knop **Volgende: Hosting >.**
4. Selecteer **voor** de sectie Hosting het juiste **opslagaccount**, **besturingssysteem**en **Plan** een beschrijving in de volgende tabel.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Opslagaccount** | Wereldwijd unieke naam | Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U ook een bestaand account gebruiken, dat moet voldoen aan de [vereisten voor opslagaccount.](./functions-scale.md#storage-account-requirements) |
    | **Besturingssysteem** | Voorkeursbesturingssysteem | Een besturingssysteem is vooraf voor u geselecteerd op basis van uw runtime stackselectie, maar u de instelling indien nodig wijzigen. |
    | **Plannen** | Verbruik | Het [hostingplan](./functions-scale.md) bepaalt hoe de functie-app wordt geschaald en resources beschikbaar zijn voor elk exemplaar. |
5. Selecteer **Controleren + Maken** om de selecties voor app-configuraties te controleren.
6. Selecteer **Maken** om de functie-app in te richten en te implementeren.

## <a name="configure-access-restrictions"></a>Toegangsbeperkingen configureren

De volgende stap is het configureren van [toegangsbeperkingen](../app-service/app-service-ip-restrictions.md) om ervoor te zorgen dat alleen resources op het virtuele netwerk de functie kunnen aanroepen.

Toegang tot [privé-site's](functions-networking-options.md#private-site-access) is ingeschakeld door een eindpunt van de Azure Virtual Network-service [te](../virtual-network/virtual-network-service-endpoints-overview.md) maken tussen de functie-app en het opgegeven virtuele netwerk. Toegangsbeperkingen worden geïmplementeerd via serviceeindpunten. Serviceeindpunten zorgen ervoor dat alleen verkeer dat afkomstig is van het opgegeven virtuele netwerk toegang heeft tot de aangewezen bron. In dit geval is de aangewezen bron de Azure-functie.

1. Ga in de functie-app naar het tabblad Functies van het **platform.** Selecteer de koppeling **Netwerk** onder de *koptekst netwerksectie* om de sectie Netwerkfunctiestatus te openen.
2. De pagina **Status van netwerkfunctie** is het startpunt voor het configureren van Azure Front Door, het Azure CDN en ook Toegangsbeperkingen. Selecteer **Toegangsbeperkingen configureren** om toegang tot privéplaatsen te configureren.
3. Op de pagina **Toegangsbeperkingen** ziet u alleen de standaardbeperking. De standaardinstelling legt geen beperkingen op aan de toegang tot de functie-app.  Selecteer **Regel toevoegen** om een configuratie van de toegangsbeperking voor privé-site's te maken.
4. Selecteer in het deelvenster **Toegangsbeperking toevoegen** de optie **Virtueel netwerk** in de vervolgkeuzelijst **Type** en selecteer vervolgens het eerder gemaakte virtuele netwerk en subnet.
5. Op de pagina **Toegangsbeperkingen** is nu te zien dat er een nieuwe beperking is. Het kan enkele seconden duren voordat de `Disabled` status `Provisioning` `Enabled` **Endpoint** van door naar .

    >[!IMPORTANT]
    > Elke functie-app heeft een [Kudu-site (Advanced Tool)](../app-service/app-service-ip-restrictions.md#scm-site) die wordt gebruikt om implementaties van functie-apps te beheren. Deze site is toegankelijk via `<FUNCTION_APP_NAME>.scm.azurewebsites.net`een URL zoals: . Omdat toegangsbeperkingen niet zijn ingeschakeld op deze implementatiesite, u uw projectcode nog steeds implementeren vanuit een lokaal ontwikkelaarswerkstation of een buildservice zonder dat u een agent binnen het virtuele netwerk hoeft in te richten.

## <a name="access-the-functions-app"></a>Toegang tot de app Functies

1. Ga terug naar de eerder gemaakte functie-app.  Kopieer in de sectie **Overzicht** de URL.

    >[!div class="mx-imgBorder"]
    >![De URL van de functie-app ophalen](./media/functions-create-private-site-access/access-function-overview.png)

2. Als u de functie-app nu vanaf uw computer buiten uw virtuele netwerk probeert te openen, ontvangt u een HTTP 403-pagina die aangeeft dat de app is gestopt.  De app wordt niet gestopt. Het antwoord is eigenlijk een HTTP 403 IP Verboden status.
3. Nu hebt u toegang tot uw functie vanaf de eerder gemaakte virtuele machine, die is verbonden met uw virtuele netwerk. Als u toegang wilt krijgen tot de site vanaf de VM, moet u verbinding maken met de VM via de Azure Bastion-service.  Selecteer eerst **Verbinding maken** en kies **vervolgens Bastion**.
4. Geef de vereiste gebruikersnaam en wachtwoord op om in te loggen op de virtuele machine.  Selecteer **Verbinden**. Er verschijnt een nieuw browservenster zodat u communiceren met de virtuele machine.
5. Omdat deze VM toegang heeft tot de functie via het virtuele netwerk, is het mogelijk om toegang te krijgen tot de site vanuit de webbrowser op de VM.  Het is belangrijk op te merken dat, terwijl de functie-app is alleen toegankelijk vanuit de aangewezen virtuele netwerk, een openbare DNS-vermelding blijft. Zoals hierboven is aangegeven, resulteert een poging om toegang te krijgen tot de site in een HTTP 403-antwoord.

## <a name="create-a-function"></a>Een functie maken

De volgende stap in deze zelfstudie is het maken van een Azure-functie die door HTTP wordt geactiveerd. Een beroep doen op de functie via een HTTP GET of POST moet resulteren in een reactie van "Hallo, {name}".  

1. Volg een van de volgende snelstarts om uw Azure Functions-app te maken en te implementeren.

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Opdrachtregel](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. Wanneer u uw Azure Functions-project publiceert, kiest u de functie-app-bron die u eerder in deze zelfstudie hebt gemaakt.
3. Controleer of de functie is geïmplementeerd.

    >[!div class="mx-imgBorder"]
    >![Geïmplementeerde functie in lijst met functies](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>De functie rechtstreeks aanroepen

1. Om de toegang tot de functie te testen, moet u de functie-URL kopiëren. Selecteer de geïmplementeerde functie en selecteer **vervolgens</> URL van functie ophalen**. Klik vervolgens op de knop **Kopiëren** om de URL naar het klembord te kopiëren.

    >[!div class="mx-imgBorder"]
    >![De functie-URL kopiëren](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > Wanneer de functie wordt uitgevoerd, ziet u een runtime-fout in de portal waarin staat dat de runtime van de functie niet kan worden gestart. Ondanks de berichttekst wordt de functie-app daadwerkelijk uitgevoerd. De fout is een gevolg van de nieuwe toegangsbeperkingen, waardoor de portal niet kan worden opgevraagd om de runtime te controleren.

2. Plak de URL in een webbrowser. Wanneer u nu probeert toegang te krijgen tot de functie-app vanaf een computer buiten uw virtuele netwerk, ontvangt u een HTTP 403-antwoord dat aangeeft dat de app is gestopt.

## <a name="invoke-the-function-from-the-virtual-network"></a>De functie van het virtuele netwerk aanroepen

Toegang tot de functie via een webbrowser (met behulp van de Azure Bastion-service) op de geconfigureerde VM op het virtuele netwerk resulteert in succes!

>[!div class="mx-imgBorder"]
>![Toegang tot de Azure-functie via Azure Bastion](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen


> [!div class="nextstepaction"]
> [Meer informatie over de netwerkopties in Functies](./functions-networking-options.md)
