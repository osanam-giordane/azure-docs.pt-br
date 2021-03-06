---
title: Migrar máquinas locais para o Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como migrar máquinas locais para o Azure, usando o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: bc04483c35162c0b461fd03c63aaa894b1bc199a
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070670"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrar máquinas locais para o Azure

Além de usar o serviço [Azure Site Recovery](site-recovery-overview.md) para gerenciar e coordenar a recuperação de desastres de máquinas locais e VMs do Azure para fins de continuidade de negócios e recuperação de desastres (BCDR), você também pode usar o Site Recovery para gerenciar a migração de computadores locais para o Azure.


Este tutorial mostra como migrar VMs locais e servidores físicos para o Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Selecione uma meta de replicação
> * Configure o ambiente de origem e de destino
> * Configurar uma política de replicação
> * Habilitar a replicação
> * Execute uma migração de teste para verificar se tudo está funcionando como esperado
> * Executar um failover avulso para o Azure

Este é o terceiro tutorial de uma série. Este tutorial presume que você já tenha concluído as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. Preparar [VMware](vmware-azure-tutorial-prepare-on-premises.md) local ou servidores [Hyper-V] (hyper-v-prepare-on-premises-tutorial.md).

Antes de começar, é aconselhável examinar as arquiteturas do [VMware](vmware-azure-architecture.md) ou [Hyper-V](hyper-v-azure-architecture.md) quanto à recuperação de desastre.


## <a name="prerequisites"></a>Pré-requisitos

- Não há suporte para dispositivos exportados por drivers paravirtualizados.
 
> [!WARNING]
> É possível migrar as VMs em outras plataformas de virtualização (que não sejam VMware, Hyper-V), como o XenServer, tratando as VMs como Servidores Físicos. Essa abordagem no entanto, ainda não foi testada e validada pela Microsoft e pode ou não funcionar. Por exemplo, as VMs em execução na plataforma do XenServer não podem executar no Azure, a menos que as ferramentas do XenServer e armazenamento para-virtualizados e drivers de rede são desinstalados da VM antes de iniciar a migração.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. Entre no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **Criar um recurso** > **Monitoramento e Gerenciamento** > **Backup e Site Recovery**.
3. Em **Nome**, especifique o nome amigável **ContosoVMVault**. Se você tiver mais de uma assinatura, selecione uma delas.
4. Crie um grupo de recursos **ContosoRG**.
5. Especifique uma região do Azure. Para verificar as regiões com suporte, confira a disponibilidade geográfica nos [Detalhes dos Preços de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Para acessar rapidamente o cofre no painel, clique em **Fixar no painel** e em **Criar**.

   ![Novo cofre](./media/migrate-tutorial-on-premises-azure/onprem-to-azure-vault.png)

O novo cofre é adicionado ao **Painel** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.



## <a name="select-a-replication-goal"></a>Selecione uma meta de replicação

Selecione o que você deseja replicar e para onde deseja replicar.
1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu Recursos, clique em **Site Recovery** > **Preparar Infraestrutura** > **Meta de proteção**.
3. Em **Objetivo de proteção**, selecione o que você deseja migrar.
    - **VMware**: selecione **Para o Azure** > **Sim, com o VMWare vSphere Hypervisor**.
    - **Computador físico**: selecione **Para o Azure** > **Não virtualizados/outros**.
    - **Hyper-V**: selecione **Para o Azure** > **Sim, com o Hyper-V**. Se as VMs do Hyper-V são gerenciadas pelo VMM, selecione **Sim**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

- [Configure](vmware-azure-tutorial.md#set-up-the-source-environment) o ambiente de origem das VMs VMware.
- [Configure](physical-azure-disaster-recovery.md#set-up-the-source-environment) o ambiente de origem para os servidores físicos.
- [Configure](hyper-v-azure-tutorial.md#set-up-the-source-environment) o ambiente de origem para VMs do Hyper-V.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique o modelo de implantação do Gerenciador de Recursos.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

- [Configure uma política de replicação](vmware-azure-tutorial.md#create-a-replication-policy) para VMs VMware.
- [Configure uma política de replicação](physical-azure-disaster-recovery.md#create-a-replication-policy) para servidores físicos.
- [Configure uma política de replicação](hyper-v-azure-tutorial.md#set-up-a-replication-policy) para VMs do Hyper-V.


## <a name="enable-replication"></a>Habilitar a replicação

- [Habilite a replicação](vmware-azure-tutorial.md#enable-replication) para VMs VMware.
- [Habilite a replicação](physical-azure-disaster-recovery.md#enable-replication) para servidores físicos.
- Habilite a replicação para VMs Hyper-V [com](hyper-v-vmm-azure-tutorial.md#enable-replication) ou [sem VMM](hyper-v-azure-tutorial.md#enable-replication).


## <a name="run-a-test-migration"></a>Execute um teste de migração

Execute um [failover de teste](tutorial-dr-drill-azure.md) para o Azure, para verificar se tudo está funcionando conforme o esperado.


## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute um failover para as máquinas que você deseja migrar.

1. Em **Configurações** > **Itens replicados** clique no computador > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Selecione o ponto de recuperação mais recente.
3. A configuração de chave de criptografia não é relevante para esse cenário.
4. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tentará desligar máquinas virtuais antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
5. Verifique se a VM do Azure aparece no Azure, conforme o esperado.
6. Em **Itens replicados**, clique com o botão direito do mouse em VM > **Concluir Migração**. Isso conclui o processo de migração, interrompe a replicação da VM e interrompe a cobrança do Site Recovery para a VM.

    ![Migração completa](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Não cancelar um failover em andamento**: a replicação da VM é interrompida antes do início do failover. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. É possível observar um tempo de failover de teste mais longo para servidores físicos, máquinas VMware Linux, VMs VMware que não têm o serviço DHCP habilitado e VMs VMware que não têm os seguintes drivers de inicialização: storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você migrou máquinas virtuais locais para máquinas virtuais do Azure. Agora que você migrou máquinas virtuais com êxito:
- [Configurar a recuperação de desastre](azure-to-azure-replicate-after-migration.md) para as VMs migradas.
- Tire proveito dos recursos [Seguros e bem-gerenciados na nuvem](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) do Azure para gerenciar suas VMs no Azure.
  
