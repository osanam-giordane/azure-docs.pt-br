---
title: Solucionar problemas de Arquivos do Azure no Linux | Microsoft Docs
description: Solução de problemas de Arquivos do Azure no Linux
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffpatt
ms.component: files
ms.openlocfilehash: 0f99913ab252b94d475f920bd734e68ff5f3b3d3
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525113"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Solucionar problemas de Arquivos do Azure no Linux

Este artigo lista os problemas comuns relacionados aos Arquivos do Microsoft Azure quando você se conecta de clientes Linux. Também fornece as possíveis causas e resoluções para esses problemas.

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>“[permissão negada] Cota de disco excedida” ao tentar abrir um arquivo

No Linux, você recebe uma mensagem de erro semelhante à seguinte:

**<filename> [permissão negada] Cota de disco excedida**

### <a name="cause"></a>Causa

Você atingiu o limite máximo de identificadores abertos simultâneos permitidos para um arquivo.

### <a name="solution"></a>Solução

Reduza o número de identificadores abertos simultâneos fechando alguns deles e repita a operação. Para obter mais informações, consulte [Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Cópia de arquivos bidirecional lenta dos Arquivos do Azure no Linux

- Se você não tiver um requisito mínimo de tamanho de E / S específico, recomendamos usar 1 MiB como o tamanho de E / S para um desempenho ideal.
- Se você souber o tamanho final de um arquivo que está sendo estendido usando gravações e seu software não apresentar problemas de compatibilidade quando uma parte não escrita do arquivo contiver zeros, defina o tamanho do arquivo antecipadamente em vez de fazer com que cada gravação seja estendida.
- Use o método de cópia correto:
    - Use o [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para todas as transferências entre dois compartilhamentos de arquivo.
    - Use o [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) entre compartilhamentos de arquivos e um computador local.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>“Erro de montagem (112): o host está inativo” devido a um tempo limite de reconexão

Um erro de montagem “112” ocorre no cliente Linux quando o cliente ficou ocioso por um longo período. Após um tempo ocioso estendido, o cliente se desconecta e a conexão atinge o tempo limite.  

### <a name="cause"></a>Causa

A conexão pode ficar ociosa pelos seguintes motivos:

-   Falhas de comunicação de rede que impedem o restabelecimento de uma conexão TCP com o servidor quando a opção de montagem "soft" padrão é usada
-   Correções de reconexão recentes que não estão presentes nos kernels mais antigos

### <a name="solution"></a>Solução

Esse problema de reconexão no kernel do Linux agora foi corrigido como parte das seguintes alterações:

- [Corrigir a reconexão para não adiar a sessão smb3 por muito tempo após a reconexão do soquete](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Chamar o serviço de eco imediatamente após a reconexão do soquete](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: corrigir uma possível corrupção de memória durante a reconexão](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: corrigir um possível bloqueio duplo de mutex durante a reconexão (para kernel v4.9 e posterior)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

No entanto, essas alterações ainda podem não ter sido portadas para todas as distribuições do Linux. Essa correção e outras correções de reconexão foram feitas nos seguintes kernels populares do Linux: 4.4.40, 4.8.16 e 4.9.1. Obtenha essa correção fazendo upgrade para uma dessas versões recomendadas de kernel.

### <a name="workaround"></a>Solução alternativa

Resolva esse problema especificando uma montagem rígida. Isso força o cliente a esperar até que uma conexão seja estabelecida ou até que seja explicitamente interrompida e possa ser usada para evitar erros devido a tempos limites de rede. No entanto, essa solução alternativa pode causar esperas indefinidas. Esteja preparado para interromper conexões, conforme necessário.

Se você não pode atualizar para as últimas versões do kernel, resolva esse problema mantendo um arquivo no compartilhamento de arquivos do Azure no qual se grava a cada 30 segundos ou menos. Essa deve ser uma operação de gravação, como regravar a data de criação ou de modificação no arquivo. Caso contrário, você poderá obter resultados em cache e a operação poderá não disparar a reconexão.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>“Erro de montagem (115): operação em andamento” durante a montagem dos Arquivos do Azure usando o SMB 3.0

### <a name="cause"></a>Causa

Algumas distribuições do Linux ainda não dão suporte para os recursos de criptografia do SMB 3.0, por isso os usuários poderão receber uma mensagem de erro “115” se tentarem montar os Arquivos do Azure usando o SMB 3.0, devido a um recurso ausente. Só há suporte para SMB 3.0 com criptografia completa no momento ao usar o Ubuntu 16.04 ou posterior.

### <a name="solution"></a>Solução

O recurso de criptografia do SMB 3.0 para Linux foi introduzido no kernel 4.11. Este recurso permite a montagem do Compartilhamento de Arquivos do Azure do local ou de uma região diferente do Azure. No momento da publicação deste artigo, essa funcionalidade foi retrocompatibilizada para o Ubuntu 17.04 e Ubuntu 16.10. Se seu cliente SMB do Linux não der suporte à criptografia, monte os Arquivos do Azure usando o SMB 2.1 em uma VM Linux do Azure que esteja no mesmo datacenter que a conta de Armazenamento de Arquivos.

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Desempenho lento em um compartilhamento de arquivos do Azure montado em uma VM Linux

### <a name="cause"></a>Causa

Uma possível causa da lentidão no desempenho é o cache desabilitado.

### <a name="solution"></a>Solução

Para verificar se o cache está desabilitado, procure a entrada **cache=**. 

**Cache=none** indica que o cache está desabilitado.  Remonte o compartilhamento usando o comando de montagem padrão ou adicionando explicitamente a opção **cache=strict** ao comando de montagem para garantir que o modo de cache padrão ou de cache “strict” seja habilitado.

Em alguns cenários, a opção de montagem **serverino** pode fazer com que o comando **ls** execute stat em cada entrada do diretório. Esse comportamento resulta na degradação do desempenho quando você está listando um diretório grande. Verifique as opções de montagem na entrada **/etc/fstab**:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Também verifique se as opções corretas estão sendo usadas executando o comando **sudo mount | grep cifs** e verificando sua saída, como na seguinte saída de exemplo:

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Se a opção **cache=strict** ou **serverino** não estiver presente, desmonte e monte os Arquivos do Azure novamente executando o comando de montagem da [documentação](../storage-how-to-use-files-linux.md). Em seguida, verifique novamente se a entrada **/etc/fstab** tem as opções corretas.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Os carimbos de data/hora foram perdidos durante a cópia de arquivos do Windows para o Linux

Nas plataformas Unix/Linux, o comando **cp -p** falhará se os arquivos 1 e 2 pertencerem a diferentes usuários.

### <a name="cause"></a>Causa

O sinalizador de força **f** em COPYFILE resulta na execução de **cp -p -f** no Unix. Esse comando também não preserva o carimbo de data/hora do arquivo que você não possui.

### <a name="workaround"></a>Solução alternativa

Use o usuário da conta de armazenamento para copiar os arquivos:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-or-mount-an-azure-file-share"></a>Não é possível conectar-se a um compartilhamento de arquivos do Azure ou montá-lo

### <a name="cause"></a>Causa

As causas comuns desse problema são:


- Você está usando um cliente de distribuição Linux incompatível. Recomendamos que você use as seguintes distribuições Linux para se conectar ao compartilhamento de arquivos do Azure:

* **Versões mínimas recomendadas com recursos de montagem correspondentes (SMB versão 2.1 vs SMB versão 3.0)**    
    
    |   | SMB 2.1 <br>(Montagens em VMs na mesma região do Azure) | SMB 3.0 <br>(Montagens de local e entre regiões) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- Utilitários CIFS não estão instalados no cliente.
- O SMB/CIFS mínimo a versão 2.1 não está instalado no cliente.
- Não há suporte para a criptografia do SMB 3.0 no cliente. A criptografia do SMB 3.0 está disponível no Ubuntu 16.4 e versões posteriores, bem como no SUSE 12.3 e versões posteriores. Outras distribuições exigem kernel 4.11 e versões posteriores.
- Você está tentando se conectar a uma conta de armazenamento usando a porta TCP 445, que não tem suporte.
- Você está tentando se conectar ao compartilhamento de arquivos do Azure de uma VM do Azure, e a VM não está localizada na mesma região que a conta de armazenamento.

### <a name="solution"></a>Solução

Para resolver o problema, use a [Ferramenta de solução de problemas para erros de montagem de Arquivos do Azure no Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Essa ferramenta ajuda a validar o ambiente de execução do cliente, detectar a configuração de cliente incompatível que causaria falha de acesso para os arquivos do Azure, fornece orientação prescritiva sobre a correção automática e coleta os rastreamentos de diagnóstico.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: não é possível acessar '&lt;caminho&gt;': erro de entrada/saída

Quando você tenta listar arquivos em um compartilhamento de arquivos do Azure usando o comando ls, o comando Is trava ao listar arquivos, você recebe o seguinte erro:

**ls: não é possível acessar '&lt;caminho&gt;': erro de entrada/saída**


### <a name="solution"></a>Solução
Atualize o kernel do Linux para as seguintes versões que têm correção para esse problema:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Todas as versões que sejam maiores ou iguais a 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Não é possível criar links simbólicos - ln: falhou ao criar link simbólico 't': Operação não suportada

### <a name="cause"></a>Causa
Por padrão, a montagem de compartilhamentos de arquivos do Azure no Linux usando o CIFS não permite o suporte a links simbólicos. Você verá um erro vincular isso:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Solução
O cliente CIFS do Linux não suporta a criação de links simbólicos ao estilo do Windows sobre o protocolo SMB2 / 3. Atualmente, o cliente Linux suporta outro estilo de links simbólicos chamado [Mishall + French symlinks] (https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) para operações de criação e acompanhamento. Clientes que precisam de links simbólicos podem usar a opção de montagem "mfsymlinks". “Mfsymlinks” geralmente são recomendados porque esse também é o formato usado pelos Macs.

Para poder usar links simbólicos, adicione o seguinte ao final do seu comando de montagem CIFS:

```
,mfsymlinks
```

Portanto, o comando será algo parecido com:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsynlinks
```

Uma vez adicionado, você poderá criar links simbólicos conforme sugerido no [Wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Caso ainda precise de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
