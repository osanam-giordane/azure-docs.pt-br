---
title: Runbooks filhos na Automação do Azure
description: Descreve os diferentes métodos para iniciar um runbook na Automação do Azure de outro runbook e compartilhar informações entre eles.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 582513e7e556859e70c1af9c4f6179e1d60e0139
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216679"
---
# <a name="child-runbooks-in-azure-automation"></a>Runbooks filhos na Automação do Azure

É uma prática recomendada na Automação do Azure escrever runbooks reutilizáveis e modulares com uma função distinta que pode ser usada por outros runbooks. Um runbook pai geralmente chamará um ou mais runbooks filhos para executar o recurso necessário. Há duas maneiras de chamar um runbook filho e cada uma tem diferenças marcantes que você deve compreender para que possa determinar qual será melhor para seus diferentes cenários.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Invocar um runbook filho usando a execução embutida

Para invocar um runbook embutido de outro runbook, use o nome do runbook e forneça valores para os parâmetros exatamente como você faria ao usar uma atividade ou um cmdlet.  Todos os runbooks na mesma conta de automação estão disponíveis para todos os outros para serem usados dessa maneira. O runbook pai aguardará o runbook filho concluir antes de passar para a próxima linha e nenhuma saída é retornada diretamente para o pai.

Quando você chama um runbook embutido, ele é executado no mesmo trabalho que o runbook pai. Não haverá nenhuma indicação no histórico de trabalho do runbook filho de que ele foi executado. Todas as exceções e fluxos de saída do runbook filho serão associados ao pai. Isso resulta em menos trabalhos e torna mais fácil rastrear e solucionar problemas, já que as exceções geradas pelo runbook filho e por sua saída de fluxo são associadas ao trabalho pai.

Quando um runbook é publicado, todos os runbooks filhos que ele chamar já devem ter sido publicados. Isso ocorre porque a Automação do Azure cria uma associação com os runbooks filhos quando um runbook é compilado. Se não tiverem sido,o runbook pai aparecerá para publicação corretamente, mas poderá gerar uma exceção quando for iniciado. Se isso acontecer, você poderá republicar o runbook pai para referenciar corretamente os runbooks filhos. Você não precisa republicar o runbook pai se algum dos runbooks filhos forem alterados porque a associação terá sido criada.

Os parâmetros de um runbook filho com chamada embutida podem ser de qualquer tipo de dados, incluindo objetos complexos, e não há nenhuma [serialização JSON](automation-starting-a-runbook.md#runbook-parameters) como quando você inicia o runbook usando o Portal do Azure ou o cmdlet Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Tipos de runbook

Quais tipos podem chamar um ao outro:

* Um [runbook do PowerShell ](automation-runbook-types.md#powershell-runbooks) e os [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) podem chamar uns aos outros em linha (ambos são baseados no PowerShell).
* Um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e runbooks gráficos do Fluxo de Trabalho do PowerShell podem chamar uns aos outros de forma embutida (ambos são baseados no Fluxo de Trabalho do PowerShell)
* Os tipos de PowerShell e os tipos de Fluxo de Trabalho do PowerShell não podem chamar uns aos outros de forma embutida e precisam usar Start-AzureRmAutomationRunbook.

Quando a ordem de publicação é importante:

* a ordem de publicação de runbooks é importante apenas para runbooks do Fluxo de Trabalho do PowerShell e runbooks gráficos do Fluxo de Trabalho do PowerShell.

Quando você chama um runbook filho Gráfico ou de Fluxo de Trabalho do PowerShell usando execução embutida, você usa apenas o nome do runbook.  Quando chamar um runbook filho do PowerShell, você deverá preceder seu nome com *.\\* para especificar que o script seja localizado no diretório local. 

### <a name="example"></a>Exemplo

O exemplo a seguir invoca um runbook filho de teste que aceita três parâmetros, um objeto complexo, um número inteiro e um valor booleano. A saída do runbook filho é atribuída a uma variável.  Nesse caso, o runbook filho é um runbook de Fluxo de Trabalho do PowerShell.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

A seguir, temos o mesmo exemplo usando um runbook do PowerShell como o filho.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Iniciar um runbook filho usando cmdlet

Você pode usar o cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) para iniciar um runbook, como descrito em [Para iniciar um runbook com o Windows PowerShell](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Há dois modos de uso para esse cmdlet.  Em um modo, o cmdlet retorna a ID do trabalho assim que o trabalho filho é criado para o runbook filho.  No outro modo, que você habilita especificando o parâmetro **-wait** , o cmdlet aguarda até que o trabalho filho seja concluído e retorna a saída do runbook filho.

O trabalho de um runbook filho iniciado com um cmdlet executará um trabalho separado do runbook pai. Isso resulta em mais trabalhos do que invocar o runbook embutido e torna o acompanhamento mais difícil de rastrear. O pai pode iniciar vários runbooks filhos assincronamente sem esperar a conclusão de cada um. Para a mesma variante de execução paralela que chama runbooks filho embutidos, o runbook pai precisaria usar a [palavra-chave paralela](automation-powershell-workflow.md#parallel-processing).

A saída dos runbooks filho não é retornada para o runbook pai confiavelmente devido ao intervalo. Além disso, determinadas variáveis como $VerbosePreference, $WarningPreference e outras podem não ser propagadas para os runbooks filho. Para evitar esses problemas, é possível invocar os runbooks filho como trabalhos de Automação separadas usando o cmdlet `Start-AzureRmAutomationRunbook` com a opção `-Wait`. Isso bloqueia o runbook pai até que o runbook filho seja concluído.

Se você não quiser que o runbook pai seja bloqueado em espera, poderá invocar o runbook filho usando o cmdlet `Start-AzureRmAutomationRunbook` sem a opção `-Wait`. É necessário usar `Get-AzureRmAutomationJob` para aguardar a conclusão do trabalho e `Get-AzureRmAutomationJobOutput` e `Get-AzureRmAutomationJobOutputRecord` para recuperar os resultados.

Parâmetros de um runbook filho iniciados com um cmdlet são fornecidos como uma tabela de hash, conforme descrito em [Parâmetros de Runbook](automation-starting-a-runbook.md#runbook-parameters). Somente tipos de dados simples podem ser usados. Se o runbook tiver um parâmetro com um tipo de dados complexos, ele deve ser chamado embutido.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um runbook filho com parâmetros e aguarda a sua conclusão usando o parâmetro Start-AzureRmAutomationRunbook -wait. Depois de concluído, sua saída é coletada do runbook filho. Para usar `Start-AzureRmAutomationRunbook` é necessário autenticar-se na assinatura do Azure.

```azurepowershell-interactive
# Connect to Azure with RunAs account
$conn = Get-AutomationConnection -Name "AzureRunAsConnection"

$null = Add-AzureRmAccount `
  -ServicePrincipal `
  -TenantId $conn.TenantId `
  -ApplicationId $conn.ApplicationId `
  -CertificateThumbprint $conn.CertificateThumbprint

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}
$joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResourceGroupName "LabRG" –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Comparação de métodos para chamar um runbook filho
A tabela a seguir resume as diferenças entre os dois métodos para chamar um runbook de outro runbook.

|  | Embutido | Cmdlet |
|:--- |:--- |:--- |
| Trabalho |Os runbooks filhos são executados no mesmo trabalho que o pai. |Um trabalho separado é criado para o runbook filho. |
| Execução |O runbook pai aguarda a conclusão do runbook filho antes de continuar. |O runbook pai continua imediatamente após o runbook filho ser iniciado *ou* o runbook pai aguarda a conclusão do trabalho filho. |
| Saída |O runbook pai pode obter saída diretamente do runbook filho. |O runbook pai deve recuperar a saída do trabalho do runbook filho *ou* o runbook pai pode obter a saída diretamente do runbook filho. |
| parâmetros |Os valores para os parâmetros de runbook filho são especificados separadamente e podem usar qualquer tipo de dados. |Os valores para os parâmetros de runbook filho devem ser combinados em uma única tabela de hash e somente podem tipos de dados simples, de matriz e de objeto que aproveitem a serialização JSON. |
| Conta de Automação |O runbook pai somente pode usar o runbook filho na mesma conta de automação. |O runbook pai pode usar runbook filho de qualquer conta de automação da mesma assinatura do Azure e até mesmo de uma assinatura diferente se você tiver uma conexão com ele. |
| Publicação |O runbook filho deve ser publicado antes da publicação do runbook pai. |O runbook filho deve ser publicado antes da inicialização do runbook pai. |

## <a name="next-steps"></a>Próximas etapas

* [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md)
* [Saída de runbook e mensagens na Automação do Azure](automation-runbook-output-and-messages.md)
