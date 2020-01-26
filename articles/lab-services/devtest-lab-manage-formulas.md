---
title: Formules in Azure DevTest Labs beheren om Vm's te maken | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een formule maakt vanuit een basis (aangepaste installatie kopie, Marketplace-installatie kopie of een andere formule) of een bestaande virtuele machine.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a668c1f7cf401c109c1041232d7f28dd2accd750
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760399"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Azure DevTest Labs formules beheren

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

In dit artikel wordt beschreven hoe u een formule maakt vanuit een basis (aangepaste installatie kopie, Marketplace-installatie kopie of een andere formule) of een bestaande virtuele machine. In dit artikel wordt u ook begeleid bij het beheren van bestaande formules.

## <a name="create-a-formula"></a>Een formule maken
Iedereen met DevTest Labs- *gebruikers* machtigingen kan vm's maken met behulp van een formule als basis. Er zijn twee manieren om formules te maken: 

* Van een basis-gebruik wanneer u alle kenmerken van de formule wilt definiëren.
* Van een bestaande Lab-VM: gebruiken wanneer u een formule wilt maken op basis van de instellingen van een bestaande virtuele machine.

Zie [eigen aren en gebruikers toevoegen in azure DevTest Labs](./devtest-lab-add-devtest-user.md)voor meer informatie over het toevoegen van gebruikers en machtigingen.

### <a name="create-a-formula-from-a-base"></a>Een formule maken van een basis
De volgende stappen leiden u door het proces van het maken van een formule op basis van een aangepaste installatie kopie, een Marketplace-installatie kopie of een andere formule.

1. Meld u aan bij de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Selecteer **alle services**en selecteer vervolgens **DevTest Labs** in de lijst.

3. Selecteer in de lijst met Labs het gewenste Lab.  

4. Selecteer op de pagina Lab de optie **formules (herbruikbare bases)** .
   
    ![Menu formule](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Selecteer op de pagina **formules** **+ toevoegen**.
   
    ![Een formule toevoegen](./media/devtest-lab-create-formulas/add-formula.png)

6. Selecteer op de pagina **Kies een basis** de basis (aangepaste installatie kopie, Marketplace-installatie kopie of formule) van waaruit u de formule wilt maken.
   
    ![Basis lijst](./media/devtest-lab-create-formulas/base-list.png)

7. Geef op het tabblad **basis instellingen** van de pagina **formule maken** de volgende waarden op:
   
    * **Formule naam** : Voer een naam in voor de formule. Deze waarde wordt weer gegeven in de lijst met basis installatie kopieën wanneer u een VM maakt. De naam wordt gevalideerd terwijl u deze typt en als dit niet het geval is, wordt een bericht met de vereisten voor een geldige naam aangeduid.
    * **Gebruikers naam** : Voer een gebruikers naam in waaraan beheerders bevoegdheden zijn toegekend.
    * **Wacht woord** : Voer in de vervolg keuzelijst een waarde in die is gekoppeld aan het geheim (wacht woord) dat u wilt gebruiken voor de opgegeven gebruiker. Zie [geheimen opslaan in azure Key Vault](devtest-lab-store-secrets-in-key-vault.md)voor meer informatie over het opslaan van geheimen in een sleutel kluis en het gebruik ervan bij het maken van Lab-resources.
    * **VM-grootte** : Selecteer **grootte wijzigen** om de grootte van de virtuele machine te wijzigen. 
    * **Artefacten** : Selecteer de pagina **artefacten toevoegen of verwijderen** , waarin u de artefacten selecteert en configureert die u wilt toevoegen aan de basis installatie kopie. Zie [aangepaste artefacten maken voor uw Azure DevTest Labs virtuele machine](devtest-lab-artifact-author.md)voor meer informatie over artefacten.
8. Ga naar het tabblad **Geavanceerde instellingen** en geef de volgende waarden op:
    - **Virtueel netwerk** : Selecteer **Vnet wijzigen**om het virtuele netwerk te wijzigen. 
    - **Subnet** : Selecteer **subnet wijzigen**om het subnet te wijzigen. 
    - **IP-adres configuratie** : Geef op of u de open bare, persoonlijke of gedeelde IP-adressen wilt. Zie [gedeelde IP-adressen begrijpen in azure DevTest Labs](./devtest-lab-shared-ip.md)voor meer informatie over gedeelde IP-adressen.
    - **Verval datum en-tijd** : Geef de verval datum en-tijd voor de virtuele machine op, zodat de virtuele machine automatisch wordt verwijderd. 
    - **Deze computer claimbaar maken** : het maken van een machine claimbaar betekent dat er geen eigendom wordt toegewezen op het moment van maken. In plaats daarvan kunnen gebruikers met een lab het eigendom (' claim ') op de computer in de test pagina zetten.     
    - **Aantal claim bare instanties** : Geef op hoeveel claim bare instanties u wilt maken. 
8. Selecteer **verzenden** om de formule te maken.

9. Wanneer de formule is gemaakt, wordt deze weer gegeven in de lijst op de pagina **formules** .

### <a name="create-a-formula-from-a-vm"></a>Een formule maken op basis van een virtuele machine
De volgende stappen leiden u door het proces van het maken van een formule op basis van een bestaande virtuele machine. 

> [!NOTE]
> Als u een formule wilt maken op basis van een virtuele machine, moet de virtuele machine na 30 maart 2016 zijn gemaakt. 
> 
> 

1. Meld u aan bij de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **alle services**en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs het gewenste Lab.  
4. Op de **overzichts** pagina van het lab selecteert u de virtuele machine waarvan u de formule wilt maken.
   
    ![Labs-Vm's](./media/devtest-lab-create-formulas/my-vms.png)
5. Selecteer op de pagina van de virtuele machine **formule maken (herbruikbare basis)** .
   
    ![Formule maken](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Voer op de pagina **formule maken** een **naam** en een **Beschrijving** in voor de nieuwe formule.
   
    ![Formule pagina maken](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Selecteer **OK** om de formule te maken.

## <a name="modify-a-formula"></a>Een formule wijzigen
Voer de volgende stappen uit om een formule te wijzigen:

1. Meld u aan bij de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **alle services**en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs het gewenste Lab.  
4. Selecteer op de pagina Lab de optie **formules (herbruikbare bases)** .
   
    ![Menu formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Selecteer op de pagina **Lab-formules** de formule die u wilt wijzigen.
6. Breng de gewenste wijzigingen aan op de pagina **formule bijwerken** en selecteer **bijwerken**.

## <a name="delete-a-formula"></a>Een formule verwijderen
Voer de volgende stappen uit om een formule te verwijderen:

1. Meld u aan bij de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **alle services**en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs het gewenste Lab.  
4. Selecteer op de pagina Lab- **instellingen** de optie **formules**.
   
    ![Menu formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Selecteer op de pagina **Lab-formules** het weglatings teken rechts van de formule die u wilt verwijderen.
   
    ![Menu formule](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Selecteer **verwijderen**in het context menu van de formule.
   
    ![Context menu van formule](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selecteer **Ja** in het bevestigings dialoogvenster voor verwijdering.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Gerelateerde blog berichten
* [Aangepaste afbeeldingen of formules?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Volgende stappen
Wanneer u een formule hebt gemaakt voor het maken van een virtuele machine, is de volgende stap het [toevoegen van een virtuele machine aan uw Lab](devtest-lab-add-vm.md).

