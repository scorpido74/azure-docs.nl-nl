---
title: Toegang tot Azure Functions van privé-site inschakelen
description: Meer informatie over het instellen van toegang tot de persoonlijke site van Azure Virtual Network voor Azure Functions.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851280"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Zelf studie: toegang tot Azure Functions privé-site tot stand brengen

In deze zelf studie wordt uitgelegd hoe u [toegang tot de persoonlijke site](./functions-networking-options.md#private-site-access) inschakelt met Azure functions. Door toegang tot de persoonlijke site te gebruiken, kunt u vereisen dat uw functie code alleen wordt geactiveerd vanuit een specifiek virtueel netwerk.

Toegang tot de persoonlijke site is handig in scenario's wanneer de toegang tot de functie-app moet worden beperkt tot een specifiek virtueel netwerk. De functie-app kan bijvoorbeeld alleen worden toegepast op werk nemers van een specifieke organisatie of services die zich binnen het opgegeven virtuele netwerk bevinden (zoals een andere Azure-functie, een virtuele Azure-machine of een AKS-cluster).

Als een functions-app toegang moet hebben tot Azure-resources in het virtuele netwerk of verbonden zijn via [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md), is [integratie met virtueel netwerk](./functions-create-vnet.md) nodig.

In deze zelf studie leert u hoe u toegang tot persoonlijke sites kunt configureren voor uw functie-app:

> [!div class="checklist"]
> * Een virtuele machine maken
> * Een Azure Bastion-service maken
> * Een Azure Functions-app maken
> * Een service-eind punt voor een virtueel netwerk configureren
> * Een Azure-functie maken en implementeren
> * De functie van buiten en binnen het virtuele netwerk aanroepen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="topology"></a>Topologie

In het volgende diagram ziet u de architectuur van de oplossing die moet worden gemaakt:

![Architectuur diagram op hoog niveau voor de toegangs oplossing van een particuliere site](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Vereisten

Voor deze zelf studie is het belang rijk dat u inzicht hebt in IP-adres sering en-subnetten. U kunt beginnen met [dit artikel voor informatie over de basis principes van adres sering en subnetten](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Er zijn veel meer artikelen en Video's online beschikbaar.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

De eerste stap in deze zelf studie is het maken van een nieuwe virtuele machine in een virtueel netwerk.  De virtuele machine wordt gebruikt om toegang te krijgen tot uw functie zodra u de toegang tot de app hebt beperkt, zodat deze alleen beschikbaar is in het virtuele netwerk.

1. Selecteer de knop **een resource maken** .

2. Typ `Windows Server`in het zoek veld en selecteer **Windows Server** in de zoek resultaten.

3. Selecteer **Windows server 2019 Data Center** in de lijst met opties voor Windows Server en klik op de knop **maken** .

4. Op het tabblad **basis beginselen** gebruikt u de VM-instellingen zoals opgegeven in de tabel onder de installatie kopie:

    >[!div class="mx-imgBorder"]
    >![Tabblad basis principes voor een nieuwe Windows-VM](./media/functions-create-private-site-access/create-vm-3.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee uw resources worden gemaakt. |
    | [**Resourcegroep**](../azure-resource-manager/management/overview.md) | myResourceGroup | Kies de resource groep om alle resources voor deze zelf studie te bevatten.  Door gebruik te maken van dezelfde resource groep kunt u gemakkelijker resources opschonen wanneer u met deze zelf studie klaar bent. |
    | **Naam van de virtuele machine** | myVM | De naam van de virtuele machine moet uniek zijn in de resource groep |
    | [**Regio**](https://azure.microsoft.com/regions/) | VS VS Noord-Centraal | Kies een regio bij u in de buurt of in de buurt van de functies die u wilt openen. |
    | **Open bare binnenkomende poorten** | Geen | Selecteer **geen** om er zeker van te zijn dat er geen inkomende verbinding met de virtuele machine via internet is. Externe toegang tot de virtuele machine wordt geconfigureerd via de Azure Bastion-service. |

5. Kies het tabblad **netwerken** en selecteer **nieuwe maken** om een nieuw virtueel netwerk te configureren.

    >[!div class="mx-imgBorder"]
    >![Een nieuw virtueel netwerk maken voor de nieuwe virtuele machine](./media/functions-create-private-site-access/create-vm-networking.png)

6. In **virtueel netwerk maken**gebruikt u de instellingen in de tabel onder de installatie kopie:

    >[!div class="mx-imgBorder"]
    >![Een nieuw virtueel netwerk maken voor de nieuwe virtuele machine](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Naam** | myResourceGroup-vnet | U kunt de standaard naam die voor het virtuele netwerk is gegenereerd, gebruiken. |
    | **Adresbereik** | 10.10.0.0/16 | Gebruik één adres bereik voor het virtuele netwerk. |
    | **Subnetnaam** | Zelfstudie | De naam van het subnet. |
    | **Adres bereik** (subnet) | 10.10.1.0/24 | De subnet grootte definieert het aantal interfaces dat aan het subnet kan worden toegevoegd. Dit subnet wordt gebruikt door de virtuele machine. Een `/24` subnet biedt 254 hostadressen. |

7. Selecteer **OK** om het virtuele netwerk te maken.
8. Schakel op het tabblad **netwerken** de waarde **geen** is geselecteerd voor **openbaar IP-adres**.
9. Kies het tabblad **beheer** en kies in **Diagnostische opslag account** **nieuwe maken** om een nieuw opslag account te maken.
10. Behoud de standaard waarden voor de secties **identiteit**, **automatisch afsluiten**en **back-up** .
11. Selecteer **controleren + maken**. Nadat de validatie is voltooid, selecteert u **maken**. Het proces voor het maken van de virtuele machine duurt een paar minuten.

## <a name="configure-azure-bastion"></a>Azure-Bastion configureren

[Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) is een volledig beheerde Azure-service die rechtstreeks vanuit de Azure Portal beveiligde RDP-en SSH-toegang biedt tot virtuele machines. Als u de Azure Bastion-service gebruikt, wordt de nood zaak om netwerk instellingen te configureren met betrekking tot RDP-toegang.

1. Klik in de portal op **toevoegen** boven aan de weer gave resource groep.
2. Typ ' Bastion ' in het zoek veld.  Selecteer ' Bastion '.
3. Selecteer **maken** om te beginnen met het proces van het maken van een nieuwe Azure Bastion-resource. Er wordt een fout bericht weer gegeven in de sectie **virtueel netwerk** omdat er nog geen `AzureBastionSubnet` subnet is. Het subnet wordt in de volgende stappen gemaakt. Gebruik de instellingen in de tabel onder de afbeelding:

    >[!div class="mx-imgBorder"]
    >![Starten met het maken van Azure Bastion](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Naam** | myBastion | De naam van de nieuwe Bastion-resource |
    | **Regio** | VS - noord-centraal | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |
    | **Virtueel netwerk** | myResourceGroup-vnet | Het virtuele netwerk waarin de Bastion-resource wordt gemaakt |
    | **Subnetrouter** | AzureBastionSubnet | Het subnet in het virtuele netwerk waarop de nieuwe bastion-host wordt geïmplementeerd. U moet een subnet maken met behulp van `AzureBastionSubnet`de naam waarde. Met deze waarde kan Azure weten met welk subnet de Bastion-resources moeten worden geïmplementeerd. U moet een subnet van Mini maal `/27` of groter gebruiken (`/27`, `/26`, enzovoort). |

    > [!NOTE]
    > Raadpleeg de zelf studie [een Azure bastion-host maken](../bastion/bastion-create-host-portal.md) voor een gedetailleerde, stapsgewijze hand leiding voor het maken van een Azure Bastion-resource.

4. Maak een subnet waarin Azure de Azure bastion-host kan inrichten. Als u **subnet-configuratie beheren** kiest, wordt er een nieuw deel venster geopend waarin u een nieuw subnet kunt definiëren.  Kies **+ subnet** om een nieuw subnet te maken.
5. Het subnet moet de naam `AzureBastionSubnet` hebben en het voor voegsel van het subnet moet mini `/27`maal zijn.  Selecteer **OK** om het subnet te maken.

    >[!div class="mx-imgBorder"]
    >![Subnet maken voor Azure bastion-host](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. Selecteer op de pagina **een bastion maken** de nieuwe `AzureBastionSubnet` in de lijst met beschik bare subnetten.

    >[!div class="mx-imgBorder"]
    >![Een Azure bastion-host met een specifiek subnet maken](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. Selecteer **controleren & maken**. Wanneer de validatie is voltooid, selecteert u **maken**. Het duurt enkele minuten voordat de Azure Bastion-resource is gemaakt.

## <a name="create-an-azure-functions-app"></a>Een Azure Functions-app maken

De volgende stap is het maken van een functie-app in azure met behulp van het [verbruiks abonnement](functions-scale.md#consumption-plan). Verderop in de zelf studie implementeert u uw functie code in deze resource.

1. Klik in de portal op **toevoegen** boven aan de weer gave resource groep.
2. **Reken > selecteren functie-app**
3. Gebruik in de sectie **basis beginselen** de instellingen van de functie-app, zoals is opgegeven in de volgende tabel.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Resource groep** | myResourceGroup | Kies de resource groep om alle resources voor deze zelf studie te bevatten.  Als u dezelfde resource groep gebruikt voor de functie-app en de virtuele machine, kunt u resources gemakkelijker opschonen wanneer u met deze zelf studie klaar bent. |
    | **functie-app naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn a-z (niet hoofdletter gevoelig), 0-9 en-. |
    | **Publiceren** | Code | Optie voor het publiceren van codebestanden of een Docker-container. |
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. |
    | **Regio** | VS - noord-centraal | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

    Selecteer de knop **volgende: hosten >** .
4. Voor de sectie **Hosting** selecteert u het juiste **opslag account**, **besturings systeem**en **plant** u een beschrijving in de volgende tabel.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Storage-account** | Wereldwijd unieke naam | Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken dat moet voldoen aan de [vereisten voor het opslag account](./functions-scale.md#storage-account-requirements). |
    | **Besturingssysteem** | Voor keur besturings systeem | Er wordt vooraf een besturings systeem geselecteerd voor u op basis van de selectie van de runtime stack, maar u kunt de instelling wijzigen, indien nodig. |
    | **Plannen** | Verbruik | Het [hosting plan](./functions-scale.md) bepaalt hoe de functie-app wordt geschaald en welke resources beschikbaar zijn voor elk exemplaar. |
5. Selecteer **controleren + maken** om de selecties van de app-configuratie te controleren.
6. Selecteer **Maken** om de functie-app in te richten en te implementeren.

## <a name="configure-access-restrictions"></a>Toegangs beperkingen configureren

De volgende stap is het configureren van [toegangs beperkingen](../app-service/app-service-ip-restrictions.md) om ervoor te zorgen dat alleen bronnen in het virtuele netwerk de functie kunnen aanroepen.

Toegang tot de [persoonlijke site](functions-networking-options.md#private-site-access) wordt ingeschakeld door een Azure Virtual Network [service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) te maken tussen de functie-app en het opgegeven virtuele netwerk. Toegangs beperkingen worden geïmplementeerd via service-eind punten. Service-eind punten zorgen ervoor dat alleen verkeer dat afkomstig is van binnen het opgegeven virtuele netwerk toegang heeft tot de aangewezen resource. In dit geval is de aangewezen resource de Azure-functie.

1. Ga in de functie-app naar het tabblad **platform functies** . Selecteer de **netwerk koppeling onder** de sectie *netwerk* -header om de sectie status van de netwerk functie te openen.
2. De pagina **status van netwerk functie** is het begin punt voor het configureren van de Azure-front-deur, het Azure CDN en ook toegangs beperkingen. Selecteer **toegangs beperkingen configureren** om toegang tot de persoonlijke site te configureren.
3. Op de pagina **toegangs beperkingen** ziet u alleen de standaard beperking in plaats. De standaard instelling bevat geen beperkingen voor toegang tot de functie-app.  Selecteer **regel toevoegen** om een configuratie voor toegangs beperking van een persoonlijke site te maken.
4. Selecteer in het deel venster **toegangs beperking toevoegen** **Virtual Network** in de vervolg keuzelijst **type** en selecteer vervolgens het eerder gemaakte virtuele netwerk en subnet.
5. Op de pagina **toegangs beperkingen** ziet u nu dat er een nieuwe beperking is. Het kan een paar seconden duren voordat de **eindpunt status** wordt gewijzigd van `Disabled` `Provisioning` naar `Enabled`.

    >[!IMPORTANT]
    > Elke functie-app beschikt over een [geavanceerde hulp programma-site (kudu)](../app-service/app-service-ip-restrictions.md#scm-site) die wordt gebruikt voor het beheren van implementaties van functie-apps. Deze site wordt geopend vanuit een URL zoals: `<FUNCTION_APP_NAME>.scm.azurewebsites.net`. Omdat de toegangs beperkingen niet zijn ingeschakeld op deze implementatie site, kunt u uw project code nog steeds implementeren vanuit een lokaal ontwikkelaars werkstation of een service bouwen zonder een agent in te richten in het virtuele netwerk.

## <a name="access-the-functions-app"></a>De functie-app openen

1. Ga terug naar de eerder gemaakte functie-app.  Kopieer de URL in de sectie **overzicht** .

    >[!div class="mx-imgBorder"]
    >![De URL van de functie-app ophalen](./media/functions-create-private-site-access/access-function-overview.png)

2. Als u nu toegang probeert te krijgen tot de functie-app vanaf uw computer buiten het virtuele netwerk, ontvangt u een HTTP 403-pagina die aangeeft dat de app is gestopt.  De app is niet gestopt. De reactie is in feite een HTTP 403 IP verboden status.
3. U hebt nu toegang tot uw functie vanaf de eerder gemaakte virtuele machine, die is verbonden met uw virtuele netwerk. Als u toegang wilt krijgen tot de site via de VM, moet u verbinding maken met de virtuele machine via de Azure Bastion-service.  Selecteer eerst **verbinding maken** en kies vervolgens **Bastion**.
4. Geef de vereiste gebruikers naam en het wacht woord op om u aan te melden bij de virtuele machine.  Selecteer **Verbinden**. Er wordt een nieuw browser venster weer gegeven waarin u kunt communiceren met de virtuele machine.
5. Omdat deze virtuele machine toegang heeft tot de functie via het virtuele netwerk, is het mogelijk om toegang te krijgen tot de site vanuit de webbrowser op de virtuele machine.  Het is belang rijk te weten dat wanneer de functie-app alleen toegankelijk is vanuit het aangewezen virtuele netwerk, een open bare DNS-vermelding blijft. Zoals hierboven wordt weer gegeven, resulteert het uitvoeren van een poging om toegang te krijgen tot de site tot een HTTP 403-antwoord.

## <a name="create-a-function"></a>Een functie maken

De volgende stap in deze zelf studie is het maken van een Azure-functie die door HTTP wordt geactiveerd. Het aanroepen van de functie via een HTTP GET of POST moet resulteren in een antwoord van ' Hello, {name} '.  

1. Volg een van de volgende Quick starts om uw Azure Functions-app te maken en te implementeren.

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Opdracht regel](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. Wanneer u uw Azure Functions-project publiceert, kiest u de resource van de functie-app die u eerder in deze zelf studie hebt gemaakt.
3. Controleer of de functie is geïmplementeerd.

    >[!div class="mx-imgBorder"]
    >![Geïmplementeerde functie in lijst met functies](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>De functie rechtstreeks aanroepen

1. Als u de toegang tot de functie wilt testen, moet u de functie-URL kopiëren. Selecteer de geïmplementeerde functie en selecteer vervolgens **</> functie-URL ophalen**. Klik vervolgens op de knop **kopiëren** om de URL naar het klem bord te kopiëren.

    >[!div class="mx-imgBorder"]
    >![De functie-URL kopiëren](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > Wanneer de functie wordt uitgevoerd, wordt er een runtime-fout weer gegeven in de portal, met de mede deling dat de functie-runtime niet kan worden gestart. Ondanks de bericht tekst wordt de functie-app in feite uitgevoerd. De fout treedt op als gevolg van de nieuwe toegangs beperkingen, waarmee wordt voor komen dat de portal een query uitvoert om de runtime te controleren.

2. Plak de URL in een webbrowser. Wanneer u nu probeert toegang te krijgen tot de functie-app vanaf een computer buiten uw virtuele netwerk, ontvangt u een HTTP 403-antwoord met de melding dat de app is gestopt.

## <a name="invoke-the-function-from-the-virtual-network"></a>De functie aanroepen vanuit het virtuele netwerk

Als u de functie opent via een webbrowser (door gebruik te maken van de Azure Bastion-service) op de geconfigureerde VM in het virtuele netwerk, resulteert dit in geslaagde pogingen.

>[!div class="mx-imgBorder"]
>![Toegang tot de Azure-functie via Azure Bastion](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen


> [!div class="nextstepaction"]
> [Meer informatie over de netwerk opties in functions](./functions-networking-options.md)
