---
title: SAP S/4HANA of BW/4HANA implementeren op een Azure VM | Microsoft Documenten
description: SAP S/4HANA of BW/4HANA implementeren op een Azure VM
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: c110a4e0429ba52e01c472097a2241f91d504cf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616211"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>SAP S/4HANA of BW/4HANA implementeren op Azure
In dit artikel wordt beschreven hoe u S/4HANA op Azure implementeert met behulp van de SAP Cloud Appliance Library (SAP CAL) 3.0. Om andere SAP HANA-gebaseerde oplossingen te implementeren, zoals BW/4HANA, volgt u dezelfde stappen.

> [!NOTE]
> Ga voor meer informatie over de SAP CAL naar de SAP [Cloud Appliance Library](https://cal.sap.com/) website. SAP heeft ook een blog over de [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> Vanaf 29 mei 2017 u het Azure Resource Manager-implementatiemodel gebruiken naast het klassieke implementatiemodel met minder voorkeur om de SAP CAL te implementeren. We raden u aan het nieuwe implementatiemodel voor Resource Manager te gebruiken en het klassieke implementatiemodel te negeren.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Stapsgewijs proces om de oplossing te implementeren

In de volgende reeks schermafbeeldingen ziet u hoe u S/4HANA op Azure implementeert met behulp van de SAP CAL. Het proces werkt op dezelfde manier voor andere oplossingen, zoals BW/4HANA.

De pagina **Oplossingen** toont een aantal van de SAP CAL HANA-gebaseerde oplossingen die beschikbaar zijn op Azure. **SAP S/4HANA 1610 FPS01, Volledig geactiveerd toestel** bevindt zich in de middelste rij:

![SAP CAL-oplossingen](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Een account aanmaken in de SAP CAL
1. Als u zich voor het eerst wilt aanmelden bij de SAP CAL, gebruikt u uw SAP S-Gebruiker of andere gebruiker die is geregistreerd bij SAP. Definieer vervolgens een SAP CAL-account dat door de SAP CAL wordt gebruikt om apparaten op Azure te implementeren. In de accountdefinitie moet u:

    a. Selecteer het implementatiemodel in Azure (Resource Manager of klassiek).

    b. Voer uw Azure-abonnement in. Een SAP CAL-account kan slechts aan één abonnement worden toegewezen. Als u meer dan één abonnement nodig hebt, moet u een ander SAP CAL-account aanmaken.

    c. Geef de SAP CAL toestemming om te implementeren in uw Azure-abonnement.

   > [!NOTE]
   >  In de volgende stappen wordt uitgelegd hoe u een SAP CAL-account maakt voor Resource Manager-implementaties. Als u al een SAP CAL-account hebt dat is gekoppeld aan het klassieke implementatiemodel, *moet* u deze stappen volgen om een nieuw SAP CAL-account te maken. Het nieuwe SAP CAL-account moet worden geïmplementeerd in het Resource Manager-model.

1. Maak een nieuw SAP CAL-account aan. Op de pagina **Accounts** worden drie opties voor Azure weergegeven: 

    a. **Microsoft Azure (klassiek)** is het klassieke implementatiemodel en heeft niet langer de voorkeur.

    b. **Microsoft Azure** is het nieuwe Implementatiemodel voor Resource Manager.

    c. **Windows Azure wordt beheerd door 21Vianet** is een optie in China die het klassieke implementatiemodel gebruikt.

    Als u wilt implementeren in het Resource Manager-model, selecteert u **Microsoft Azure**.

    ![SAP CAL-accountgegevens](./media/cal-s4h/s4h-pic-2a.png)

1. Voer de Azure **Subscription ID** in die u vinden op de Azure-portal.

   ![SAP CAL-accounts](./media/cal-s4h/s4h-pic3c.png)

1. Als u de SAP CAL wilt autoriseren om te implementeren in het Azure-abonnement dat u hebt gedefinieerd, klikt u op **Autoriseren.** De volgende pagina wordt weergegeven op het tabblad browser:

   ![Aanmelden voor internet explorer-cloudservices](./media/cal-s4h/s4h-pic4c.png)

1. Als er meer dan één gebruiker wordt vermeld, kiest u het Microsoft-account dat is gekoppeld aan de medebeheerder van het Azure-abonnement dat u hebt geselecteerd. De volgende pagina wordt weergegeven op het tabblad browser:

   ![Bevestiging van Internet Explorer-cloudservices](./media/cal-s4h/s4h-pic5a.png)

1. Klik op **Accepteren**. Als de autorisatie is geslaagd, wordt de definitie van het SAP CAL-account opnieuw weergegeven. Na een korte tijd bevestigt een bericht dat het autorisatieproces is geslaagd.

1. Als u het nieuw gemaakte SAP CAL-account aan uw gebruiker wilt toewijzen, voert u uw **gebruikersnaam** in het tekstvak aan de rechterkant in en klikt u op **Toevoegen**.

   ![Account aan gebruikersvereniging](./media/cal-s4h/s4h-pic8a.png)

1. Als u uw account wilt koppelen aan de gebruiker die u gebruikt om u aan te melden bij de SAP CAL, klikt u op **Controleren**. 
 
1. Als u de koppeling wilt maken tussen uw gebruiker en het nieuw gemaakte SAP CAL-account, klikt u op **Maken**.

   ![Koppeling gebruiker naar SAP CAL-account](./media/cal-s4h/s4h-pic9b.png)

U hebt een SAP CAL-account gemaakt dat in staat is:

- Gebruik het Resource Manager-implementatiemodel.
- Implementeer SAP-systemen in uw Azure-abonnement.

U nu beginnen met het implementeren van S/4HANA in uw gebruikersabonnement in Azure.

> [!NOTE]
> Voordat u verdergaat, bepaalt u of u Azure-vCPU-quota voor VM's uit de Azure H-serie hebt. Op dit moment gebruikt de SAP CAL H-series VM's van Azure om een aantal sap HANA-gebaseerde oplossingen te implementeren. Uw Azure-abonnement heeft mogelijk geen H-series vCPU-quota voor H-series. Als dit het geval is, moet u mogelijk contact opnemen met Azure-ondersteuning om een quotum van vCPU's uit de H-serie te krijgen.
> 
> [!NOTE]
> Wanneer u een oplossing implementeert op Azure in de SAP CAL, u merken dat u slechts één Azure-regio kiezen. Als u wilt implementeren in andere Azure-regio's dan de DOOR de SAP CAL voorgestelde REGIO's, moet u een CAL-abonnement bij SAP aanschaffen. Mogelijk moet u ook een bericht openen met SAP om uw CAL-account in staat te stellen te leveren in andere Azure-regio's dan de aanvankelijk voorgestelde regio's.

### <a name="deploy-a-solution"></a>Een oplossing implementeren

Laten we een oplossing implementeren vanaf de pagina **Oplossingen** van de SAP CAL. De SAP CAL heeft twee sequenties te implementeren:

- Een basisreeks die één pagina gebruikt om het systeem te definiëren dat moet worden geïmplementeerd
- Een geavanceerde reeks die u bepaalde keuzes geeft op VM-formaten 

We tonen de basisweg naar implementatie hier.

1. Op de pagina **Accountgegevens** moet u:

    a. Selecteer een SAP CAL-account. (Gebruik een account dat is gekoppeld aan het implementatiemodel van Resource Manager.)

    b. Voer een **instantienaam**in .

    c. Selecteer een **Azure-gebied**. De SAP CAL suggereert een regio. Als u een andere Azure-regio nodig hebt en u hebt geen SAP CAL-abonnement, moet u een CAL-abonnement bij SAP bestellen.

    d. Voer een **hoofdwachtwoord in** voor de oplossing van acht of negen tekens. Het wachtwoord wordt gebruikt voor de beheerders van de verschillende componenten.

   ![SAP CAL Basic Mode: Instantie maken](./media/cal-s4h/s4h-pic10a.png)

1. Klik **op Maken**en klik in het berichtvak dat wordt weergegeven op **OK**.

   ![SAP CAL Ondersteunde VM-formaten](./media/cal-s4h/s4h-pic10b.png)

1. Klik in het dialoogvenster **Privésleutel** op **Opslaan** om de privésleutel op te slaan in de SAP CAL. Als u wachtwoordbeveiliging wilt gebruiken voor de privésleutel, klikt u op **Downloaden.** 

   ![SAP CAL-privésleutel](./media/cal-s4h/s4h-pic10c.png)

1. Lees het SAP **CAL-waarschuwingsbericht** en klik op **OK**.

   ![SAP CAL-waarschuwing](./media/cal-s4h/s4h-pic10d.png)

    Nu vindt de implementatie plaats. Na enige tijd, afhankelijk van de grootte en complexiteit van de oplossing (de SAP CAL geeft een schatting), wordt de status weergegeven als actief en klaar voor gebruik.

1. Als u de virtuele machines wilt vinden die zijn verzameld met de andere bijbehorende resources in één resourcegroep, gaat u naar de Azure-portal: 

   ![SAP CAL-objecten geïmplementeerd in de nieuwe portal](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. Op de SAP CAL-portal wordt de status weergegeven als **Actief**. Als u verbinding wilt maken met de oplossing, klikt u op **Verbinding maken**. Binnen deze oplossing worden verschillende opties geïmplementeerd om verbinding te maken met de verschillende componenten.

   ![SAP CAL-exemplaren](./media/cal-s4h/active_solution.png)

1. Klik op Aan de **slag met de handleiding**voordat u een van de opties gebruiken om verbinding te maken met de geïmplementeerde systemen. 

   ![Verbinding maken met de instantie](./media/cal-s4h/connect_to_solution.png)

    De documentatie geeft de gebruikers een naam voor elk van de connectiviteitsmethoden. De wachtwoorden voor deze gebruikers zijn ingesteld op het hoofdwachtwoord dat u aan het begin van het implementatieproces hebt gedefinieerd. In de documentatie worden andere meer functionele gebruikers vermeld met hun wachtwoorden, die u gebruiken om u aan te melden bij het geïmplementeerde systeem. 

    Als u bijvoorbeeld de SAP GUI gebruikt die vooraf is geïnstalleerd op de Windows Remote Desktop-machine, ziet het S/4-systeem er mogelijk als volgt uit:

   ![SM50 in de vooraf geïnstalleerde SAP GUI](./media/cal-s4h/gui_sm50.png)

    Of als u de DBACockpit gebruikt, kan de instantie er als volgt uitzien:

   ![SM50 in de DBACockpit SAP GUI](./media/cal-s4h/dbacockpit.png)

Binnen een paar uur wordt een gezond SAP S/4-toestel geïmplementeerd in Azure.

Als u een SAP CAL-abonnement hebt gekocht, ondersteunt SAP implementaties volledig via de SAP CAL op Azure. De ondersteuningswachtrij is BC-VCM-CAL.







