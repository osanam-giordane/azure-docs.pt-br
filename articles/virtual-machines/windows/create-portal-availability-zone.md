---
title: Crie uma VM do Windows zoneada com o Portal do Azure | Microsoft Docs
description: Crie uma VM do Windows em uma Região de Disponibilidade com o portal do Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 3d3561cf1ad760930821fabeef9839c25d55f2a9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
ms.locfileid: "30321981"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Crie uma máquina virtual do Windows em uma Região de Disponibilidade com o portal do Azure

Este artigo aborda usando o portal do Azure para criar uma máquina virtual em uma região de disponibilidade do Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona fisicamente separada em uma região do Azure. Use zonas de disponibilidade para proteger seus aplicativos e dados de uma improvável falha ou perda de um datacenter inteiro.

Para usar uma zona de disponibilidade, crie a máquina virtual em uma [região do Azure com suporte](../../availability-zones/az-overview.md#regions-that-support-availability-zones).

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar máquina virtual

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.

2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 

3. Insira as informações da máquina virtual. O nome do usuário e a senha inseridos aqui são usados para fazer logon na máquina virtual. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm). Escolha um local como Leste dos EUA 2 que oferece suporte a zonas de disponibilidade. Ao concluir, clique em **OK**.

    ![Insira as informações básicas sobre sua VM na folha do portal](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Escolha um tamanho para a VM. Selecione um tamanho recomendado ou filtro com base nos recursos. Confirme se que o tamanho está disponível na zona que você deseja usar.

    ![Selecione um tamanho de VM](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Em **Configurações** > **Alta Disponibilidade**, selecione uma das zonas de numeração da **Zona de disponibilidade** suspensa, mantenha os padrões restantes, e Clique em **Ok**.

    ![Selecione uma zona de disponibilidade](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na página de resumo, clique em **Criar** para iniciar a implantação da máquina virtual.

7. A VM será fixada ao painel do portal do Azure. Depois que a implantação for concluída, a folha de resumo da VM abrirá automaticamente.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confirme a zona do disco gerenciado e endereço IP

Quando a VM é implantada em uma zona de disponibilidade, um disco gerenciado para a VM sé criado na mesma zona de disponibilidade. Por padrão, um endereço IP público também é criado nessa região.

Você pode confirmar as configurações de zona para esses recursos no portal.  

1. Clique em **Grupos de recursos** e, em seguida, no nome do grupo de recursos da VM, como *myResourceGroup*.

2. Clique no nome do recurso de disco. A página **Visão geral** inclui detalhes sobre a zona local e de disponibilidade do recurso.

    ![Zona de disponibilidade do disco gerenciado](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Clique no nome do recurso de endereço IP público. A página **Visão geral** inclui detalhes sobre a zona local e de disponibilidade do recurso.

    ![Zona de disponibilidade do endereço IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar uma VM em uma zona de disponibilidade. Saiba mais sobre [regiões e disponibilidade](regions-and-availability.md) para máquinas virtuais do Azure.
