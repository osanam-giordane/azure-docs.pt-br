---
title: Como restaurar um servidor no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve como restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: b4fe1151337dc6f5874e6ad102c6e905e90fd963
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412749"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure

## <a name="backup-happens-automatically"></a>O backup ocorre automaticamente
O backup do Banco de Dados do Azure para servidores PostgreSQL é feito periodicamente para habilitar os recursos de restauração. Com esse recurso de backup automático, você pode restaurar o servidor e todos os seus bancos de dados para um ponto anterior em um novo servidor.

## <a name="set-backup-configuration"></a>Definir configuração de backup

Escolha entre configurar o servidor para backups com redundância local ou backups com redundância geográfica na criação do servidor, na janela **Tipo de Preço**.

> [!NOTE]
> Depois que um servidor é criado, o tipo de redundância que ele tem, geográfica ou local, não pode ser alternado.
>

Ao criar um servidor por meio do portal do Azure, a janela **Tipo de Preço** é onde você seleciona backups **Com Redundância Local** ou **Com Redundância Geográfica** para o servidor. Essa janela também é onde você seleciona o **Período de Retenção de Backup**: quanto tempo (em número de dias) você deseja que os backups de servidor sejam armazenados.

   ![Tipo de preço - Escolher redundância de backup](./media/howto-restore-server-portal/pricing-tier.png)

Para saber mais sobre como definir esses valores de durante a criação, confira o [guia de início rápido do Banco de Dados do Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md).

O período de retenção de backup de um servidor pode ser alterado por meio das seguintes etapas:
1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL. Essa ação abre a página **Visão geral** do runbook.
3. Selecione **Tipo de Preço** no menu, em **CONFIGURAÇÕES**. Usando o controle deslizante, você pode alterar o **Período de Retenção de Backup** entre 7 e 35 dias, conforme a sua preferência.
Na captura de tela abaixo, ele foi aumentado para 34 dias.
![Período de retenção de backup aumentado](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Clique em **OK** para confirmar a alteração.

O período de retenção de backup determina até quando a restauração de pontos anteriores pode ser feita, já que ele se baseia em backups disponíveis. A Restauração pontual é descrita mais detalhadamente na seção a seguir. 

## <a name="point-in-time-restore"></a>Restauração pontual
O Banco de Dados do Azure para PostgreSQL permite a restauração do servidor em um ponto anterior no tempo e em uma nova cópia do servidor. Você pode usar esse novo servidor para recuperar seus dados ou fazer seu aplicativo cliente apontar para esse novo servidor.

Por exemplo, se uma tabela for acidentalmente descartada ao meio-dia de hoje, você poderá restaurar em um momento logo antes do meio-dia e recuperar a tabela e os dados dessa nova cópia do servidor. A restauração pontual está no nível do servidor, não no nível do banco de dados.

As etapas a seguir restauram o exemplo de servidor para um ponto anterior:
1. No Portal do Azure, selecione o servidor do Banco de Dados do Azure para PostgreSQL. 

2. Na barra de ferramentas da página **Visão geral** do servidor, selecione **Restaurar**.

   ![Banco de Dados do Azure para PostgreSQL - Visão geral - botão Restaurar](./media/howto-restore-server-portal/2-server.png)

3. Preencha o formulário Restaurar com as informações necessárias:

   ![Banco de Dados do Azure para PostgreSQL - Informações de restauração ](./media/howto-restore-server-portal/3-restore.png)
  - **Ponto de restauração**: selecione o ponto para o qual você deseja restaurar.
  - **Servidor de destino**: forneça um nome para o novo servidor.
  - **Local**: não é possível selecionar a região. Por padrão, é o mesmo que o servidor de origem.
  - **Tipo de preço**: você não pode alterar esses parâmetros ao fazer uma restauração pontual. Ele é igual ao servidor de origem. 

4. Clique em **OK** para restaurar o servidor em um ponto anterior. 

5. Após a conclusão da restauração, localize o novo servidor criado para verificar se os dados foram restaurados conforme o esperado.

>[!Note]
>O novo servidor criado pela restauração pontual tem o mesmo nome de logon e senha do administrador válidos para o servidor existente no ponto escolhido. Você pode alterar a senha na página **Visão geral** do novo servidor.

## <a name="geo-restore"></a>Restauração geográfica
Se você configurou seu servidor para backups com redundância geográfica, um novo servidor pode ser criado do backup do servidor existente. Esse novo servidor pode ser criado em qualquer região em que o Banco de Dados do Azure para PostgreSQL esteja disponível.  

1. Selecione o botão **Criar um recurso** (+) no canto superior esquerdo do portal. Selecione **Bancos de Dados** > **Banco de Dados do Azure para PostgreSQL**.

   ![A opção “Banco de Dados do Azure para PostgreSQL”](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. Na lista suspensa **Selecionar origem** do formulário, escolha **Backup**. Essa ação carrega uma lista de servidores que têm backups com redundância geográfica habilitados. Selecione um desses backups para ser a origem do novo servidor.
   ![Selecionar origem: backup e lista de backups com redundância geográfica](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Quando um servidor é criado pela primeira vez, talvez não fique imediatamente disponível para restauração geográfica. Pode demorar algumas horas para que os metadados necessários sejam preenchidos.
   >

3. Preencha o restante do formulário com suas preferências. Você pode selecionar qualquer **Local**. Depois de selecionar o local, você pode selecionar o **Tipo de Preço**. Por padrão, os parâmetros do servidor existente de onde você está restaurando são exibidos. Você pode clicar em **OK** sem fazer nenhuma alteração para herdar as configurações. Ou pode alterar **Geração de Computação** (se disponível na região escolhida), número de **vCores**, **Período de Retenção de Backup** e **Opção de Redundância de Backup**. Não há suporte para a alteração do **Tipo de Preço** (Básico, Uso Geral ou Otimizado para Memória) ou do tamanho de **Armazenamento** durante a restauração.

>[!Note]
>O novo servidor criado pela restauração geográfica tem o mesmo nome de logon e senha do administrador válidos para o servidor existente no momento em que a restauração foi iniciada. A senha pode ser alterada na página **Visão geral** do servidor.


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [backups](concepts-backup.md) do serviço.
- Saiba mais sobre as opções de [continuidade dos negócios](concepts-business-continuity.md).
