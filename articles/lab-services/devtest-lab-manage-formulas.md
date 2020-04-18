---
title: Formules beheren in Azure DevTest Labs om VM's te maken | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u een formule maakt op basis van een basis (aangepaste afbeelding, Marketplace-afbeelding of een andere formule) of een bestaande virtuele machine.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: ce980167cd88adfada1aa294aafa885184565799
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641165"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Azure DevTest Labs-formules beheren

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

In dit artikel wordt uitgelegd hoe u een formule maakt op basis van een basis (aangepaste afbeelding, Marketplace-afbeelding of een andere formule) of een bestaande virtuele machine. Dit artikel begeleidt u ook bij het beheren van bestaande formules.

## <a name="create-a-formula"></a>Een formule maken
Iedereen met de machtigingen van DevTest *Labs-gebruikers* kan VM's maken met behulp van een formule als basis. Er zijn twee manieren om formules te maken: 

* Vanaf een basis - Gebruik wanneer u alle kenmerken van de formule wilt definiëren.
* Vanuit een bestaande lab-vm - Gebruik wanneer u een formule wilt maken op basis van de instellingen van een bestaande vm.

Zie [Eigenaren en gebruikers toevoegen in Azure DevTest Labs voor](./devtest-lab-add-devtest-user.md)meer informatie over het toevoegen van gebruikers en machtigingen.

### <a name="create-a-formula-from-a-base"></a>Een formule maken op basis
De volgende stappen begeleiden u bij het maken van een formule op basis van een aangepaste afbeelding, Marketplace-afbeelding of een andere formule.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.

3. Selecteer in de lijst met laboratoria het gewenste lab.  

4. Selecteer op de pagina van het lab **Formules (herbruikbare bases)** in het linkermenu.
5. Selecteer **op** de pagina Formules de optie **+ Toevoegen**.
   
    ![Een formule toevoegen](./media/devtest-lab-create-formulas/add-formula.png)

6. Selecteer op de **basispagina Een basispagina** kiezen de basis (aangepaste afbeelding of Marketplace-afbeelding) waaruit u de formule wilt maken.
7. Geef op het tabblad **Basisinstellingen** van de pagina **Formule maken** de volgende waarden op:
   
    * **Formulenaam** - Voer een naam voor uw formule in. Deze waarde wordt weergegeven in de lijst met basisafbeeldingen wanneer u een vm maakt. De naam wordt gevalideerd terwijl u deze typt en als deze niet geldig is, geeft een bericht de vereisten voor een geldige naam aan.
    - Voer een optionele **beschrijving** voor de formule in. 
    * **Gebruikersnaam** - Voer een gebruikersnaam in die beheerdersbevoegdheden heeft.
    * **Wachtwoord** - Voer een waarde in of selecteer uit de vervolgkeuzelijst - een waarde die is gekoppeld aan het geheim (wachtwoord) dat u voor de opgegeven gebruiker wilt gebruiken. Zie [Winkelgeheimen in Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md)voor meer informatie over het opslaan van geheimen in een sleutelkluis en deze te gebruiken bij het maken van labbronnen.

        Selecteer **Een opgeslagen geheim gebruiken** als u een geheim uit Azure Key Vault wilt gebruiken in plaats van een wachtwoord te gebruiken. 
    * **Grootte virtuele machine** - Selecteer **Grootte wijzigen** om de grootte van de virtuele machine te wijzigen. 
    - **Type OS-schijftype** - selecteer het type schijf dat u wilt gebruiken (Standard HDD, Standard SSD of Premium SSD).
    * **Artefacten** - Selecteer De pagina **Artefacten toevoegen of verwijderen,** waarin u de artefacten selecteert en configureert die u aan de basisafbeelding wilt toevoegen. Zie [Aangepaste artefacten maken voor uw virtuele Azure DevTest Labs-machine voor](devtest-lab-artifact-author.md)meer informatie over artefacten.

        ![Pagina Basisinstellingen](./media/devtest-lab-create-formulas/basic-settings.png)
8. Ga naar het tabblad **Geavanceerde instellingen** en geef de volgende waarden op:
    - **Virtueel netwerk** - Als u het virtuele netwerk wilt wijzigen, selecteert u **Vnet wijzigen**. 
    - **Subnet** - Als u het subnet wilt wijzigen, selecteert u **Subnet wijzigen**. 
    - **IP-adresconfiguratie** - Geef op of u de openbare, private of gedeelde IP-adressen wilt. Zie [Gedeelde IP-adressen begrijpen in Azure DevTest Labs](./devtest-lab-shared-ip.md)voor meer informatie over gedeelde IP-adressen.
    - **Vervaldatum en -tijd** - U dit veld niet bewerken. 
    - **Maak deze machine claimable** - Het maken van een machine "claimable" betekent dat het niet zal worden toegewezen eigendom op het moment van creatie. In plaats daarvan lab gebruikers in staat zullen zijn om eigendom te nemen ("claim") de machine in de pagina van het lab.  

        ![Pagina Basisinstellingen](./media/devtest-lab-create-formulas/advanced-settings.png)
8. Selecteer **Verzenden** om de formule te maken.

9. Wanneer de formule is gemaakt, wordt deze weergegeven in de lijst op de pagina **Formules.**

### <a name="create-a-formula-from-a-vm"></a>Een formule maken op basis van een virtuele machine
De volgende stappen begeleiden u door het proces van het maken van een formule op basis van een bestaande VM. 

> [!NOTE]
> Als u een formule wilt maken op basis van een vm, moet de VM zijn gemaakt na 30 maart 2016. 
> 
> 

1. Meld u aan bij de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met laboratoria het gewenste lab.  
4. Selecteer op de **pagina Overzicht** van het lab de VM waaruit u de formule wilt maken.
   
    ![Vm's van labs](./media/devtest-lab-create-formulas/my-vms.png)
5. Selecteer op de pagina van de VM de optie **Formule maken (herbruikbare basis)**.
   
    ![Formule maken](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Voer **op** de pagina Formule maken een **naam** en **beschrijving** in voor uw nieuwe formule.
   
    ![Formulepagina maken](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Selecteer **OK** om de formule te maken.

## <a name="modify-a-formula"></a>Een formule wijzigen
Voer de volgende stappen uit om een formule te wijzigen:

1. Meld u aan bij de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met laboratoria het gewenste lab.  
4. Selecteer op de pagina van het lab **formules (herbruikbare bases).**
   
    ![Menu Formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Selecteer op de pagina **Labformules** de formule die u wilt wijzigen.
6. Breng **op** de pagina Formule bijwerken de gewenste bewerkingen uit en selecteer **Bijwerken**.

## <a name="delete-a-formula"></a>Een formule verwijderen
Voer de volgende stappen uit om een formule te verwijderen:

1. Meld u aan bij de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met laboratoria het gewenste lab.  
4. Selecteer **formules**op de pagina **Lab-instellingen** .
   
    ![Menu Formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Selecteer op de pagina **Lab-formules** de ellips rechts van de formule die u wilt verwijderen.
   
    ![Menu Formule](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Selecteer Verwijderen in het contextmenu **van**de formule .
   
    ![Contextmenu Formule](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selecteer **Ja** in het dialoogvenster verwijderingsbevestiging.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Gerelateerde blogberichten
* [Aangepaste afbeeldingen of formules?](devtest-lab-faq.md#what-is-the-difference-between-a-custom-image-and-a-formula)

## <a name="next-steps"></a>Volgende stappen
Zodra u een formule hebt gemaakt voor gebruik bij het maken van een vm, is de volgende stap het [toevoegen van een VM aan uw lab.](devtest-lab-add-vm.md)

