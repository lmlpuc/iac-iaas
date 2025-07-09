# Documentação dos ARM Templates para Implantação de Infraestrutura Web no Azure

Este documento descreve os arquivos ARM (Azure Resource Manager) templates gerados para implantar uma infraestrutura web básica no Azure, incluindo um Grupo de Recursos, uma Máquina Virtual Windows Server com IIS, Rede Virtual, Sub-rede, IP Público e Placa de Rede.

## Conteúdo

- `azuredeploy.json`: O template principal que define os recursos do Azure.
- `azuredeploy.parameters.json`: Um arquivo de parâmetros de exemplo para o template principal.

## Pré-requisitos

Antes de utilizar estes templates, certifique-se de ter:

- Uma assinatura Azure ativa.
- As ferramentas Azure CLI ou Azure PowerShell instaladas e configuradas, ou acesso ao portal do Azure.
- Um Service Principal ou identidade gerenciada configurada no Azure DevOps com permissões para criar e gerenciar recursos na sua assinatura Azure.

## Estrutura do Template (`azuredeploy.json`)

O template `azuredeploy.json` define os seguintes recursos:

- **Rede Virtual (`Microsoft.Network/virtualNetworks`):** Cria uma rede virtual com o prefixo de endereço `10.0.0.0/16`.
- **Sub-rede (`subnets` dentro da VNet):** Cria uma sub-rede dentro da rede virtual com o prefixo de endereço `10.0.0.0/24`.
- **IP Público (`Microsoft.Network/publicIPAddresses`):** Aloca um endereço IP público dinâmico para a máquina virtual.
- **Placa de Rede (`Microsoft.Network/networkInterfaces`):** Cria uma placa de rede e a associa ao IP público e à sub-rede.
- **Máquina Virtual (`Microsoft.Compute/virtualMachines`):** Implanta uma VM Windows Server 2019 Datacenter com o tamanho `Standard_B2ms` (2 vCPUs, 8 GiB de memória).
- **Extensão Custom Script (`Microsoft.Compute/virtualMachines/extensions`):** Instala o IIS (Internet Information Services) na VM após a sua criação e cria um `index.html` de exemplo.
- **Grupo de Segurança de Rede (NSG) (`Microsoft.Network/networkSecurityGroups`):** Cria um NSG e o associa à placa de rede da VM, permitindo tráfego HTTP (porta 80) de entrada.

## Parâmetros do Template

O template `azuredeploy.json` utiliza os seguintes parâmetros, que podem ser fornecidos através do arquivo `azuredeploy.parameters.json` ou diretamente durante a implantação:

| Parâmetro                     | Tipo         | Descrição                                                              | Valor Padrão (se aplicável) |
| :---------------------------- | :----------- | :--------------------------------------------------------------------- | :-------------------------- |
| `resourceGroupName`           | `string`     | Nome do Grupo de Recursos onde os recursos serão implantados.          | `rg-iac-web00001`           |
| `location`                    | `string`     | Localização do Azure para os recursos (ex: `westus3`).                 | `westus3`                   |
| `virtualMachineName`          | `string`     | Nome da Máquina Virtual.                                               | `vm-iac-web00001`           |
| `adminUsername`               | `string`     | Nome de usuário administrador para a VM.                               | `adminwebmanual`            |
| `adminPassword`               | `securestring` | Senha do administrador para a VM.                                      | `adminwebmanual1!o`         |
| `networkInterfaceName`        | `string`     | Nome da Placa de Rede da VM.                                           | `nic-iac-web00001`          |
| `publicIpAddressName`         | `string`     | Nome do Endereço IP Público.                                           | `pip-iac-web00001`          |
| `virtualNetworkName`          | `string`     | Nome da Rede Virtual.                                                  | `net-iac-web00001`          |
| `subnetName`                  | `string`     | Nome da Sub-rede dentro da Rede Virtual.                               | `nset-iac-web00001`         |
| `virtualNetworkAddressPrefix` | `string`     | Prefixo de endereço CIDR para a Rede Virtual.                          | `10.0.0.0/16`               |
| `subnetAddressPrefix`         | `string`     | Prefixo de endereço CIDR para a Sub-rede.                              | `10.0.0.0/24`               |

## Arquivo de Parâmetros (`azuredeploy.parameters.json`)

O arquivo `azuredeploy.parameters.json` fornece um exemplo de como passar os valores para os parâmetros do template. Você pode modificar este arquivo para ajustar as configurações conforme suas necessidades.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "resourceGroupName": {
            "value": "rg-iac-web00001"
        },
        "location": {
            "value": "westus3"
        },
        "virtualMachineName": {
            "value": "vm-iac-web00001"
        },
        "adminUsername": {
            "value": "adminwebmanual"
        },
        "adminPassword": {
            "value": "adminwebmanual1!o"
        },
        "networkInterfaceName": {
            "value": "nic-iac-web00001"
        },
        "publicIpAddressName": {
            "value": "pip-iac-web00001"
        },
        "virtualNetworkName": {
            "value": "net-iac-web00001"
        },
        "subnetName": {
            "value": "nset-iac-web00001"
        },
        "virtualNetworkAddressPrefix": {
            "value": "10.0.0.0/16"
        },
        "subnetAddressPrefix": {
            "value": "10.0.0.0/24"
        }
    }
}
```

## Como Implantar

Você pode implantar esses templates usando o Azure CLI, Azure PowerShell ou através de um pipeline de CI/CD (como Azure DevOps).

### Usando Azure CLI

1.  **Navegue até o diretório** onde os arquivos `azuredeploy.json` e `azuredeploy.parameters.json` estão localizados.

2.  **Faça login no Azure** (se ainda não o fez):

    ```bash
    az login
    ```

3.  **Implante o Grupo de Recursos e os recursos:**

    ```bash
    az deployment group create \
      --name WebInfraDeployment \
      --resource-group rg-iac-web00001 \
      --template-file azuredeploy.json \
      --parameters @azuredeploy.parameters.json
    ```

    *   Substitua `WebInfraDeployment` pelo nome que desejar para a sua implantação.
    *   Substitua `rg-iac-web00001` pelo nome do seu grupo de recursos (se for diferente do padrão no arquivo de parâmetros).

### Usando Azure PowerShell

1.  **Navegue até o diretório** onde os arquivos `azuredeploy.json` e `azuredeploy.parameters.json` estão localizados.

2.  **Faça login no Azure** (se ainda não o fez):

    ```powershell
    Connect-AzAccount
    ```

3.  **Implante o Grupo de Recursos e os recursos:**

    ```powershell
    New-AzResourceGroupDeployment `
      -Name 


WebInfraDeployment `
      -ResourceGroupName rg-iac-web00001 `
      -TemplateFile azuredeploy.json `
      -TemplateParameterFile azuredeploy.parameters.json
    ```

    *   Substitua `WebInfraDeployment` pelo nome que desejar para a sua implantação.
    *   Substitua `rg-iac-web00001` pelo nome do seu grupo de recursos (se for diferente do padrão no arquivo de parâmetros).

### Usando Azure DevOps Pipeline (Exemplo Básico de `azure-pipelines.yml`)

Para automatizar a implantação, você pode usar um pipeline YAML no Azure DevOps. Este é um exemplo básico de como você pode configurar seu `azure-pipelines.yml` para implantar o ARM template.

Certifique-se de ter uma `Service Connection` configurada no seu projeto Azure DevOps que tenha permissões para implantar recursos na sua assinatura Azure.

```yaml
# azure-pipelines.yml

trigger:
- main

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy Azure Web Infrastructure'
  inputs:
    azureResourceManagerConnection: '<Nome da sua Service Connection>'
    resourceGroupName: 'rg-iac-web00001'
    location: 'westus3'
    templateLocation: 'Linked artifact'
    csmFile: 'azuredeploy.json'
    csmParametersFile: 'azuredeploy.parameters.json'
    overrideParameters: '-virtualMachineName vm-iac-web00001 -adminUsername adminwebmanual -adminPassword adminwebmanual1!o -networkInterfaceName nic-iac-web00001 -publicIpAddressName pip-iac-web00001 -virtualNetworkName net-iac-web00001 -subnetName nset-iac-web00001'

```

**Observações sobre o Pipeline YAML:**

-   **`trigger: - main`**: Este pipeline será acionado sempre que houver um push para o branch `main`.
-   **`pool: vmImage: 'windows-latest'`**: O pipeline será executado em um agente hospedado pela Microsoft com a imagem mais recente do Windows.
-   **`AzureResourceGroupDeployment@2`**: Esta é a tarefa do Azure DevOps para implantar grupos de recursos. Você precisará especificar:
    -   `azureResourceManagerConnection`: O nome da sua Service Connection configurada no Azure DevOps.
    -   `resourceGroupName`: O nome do grupo de recursos. Pode ser o mesmo definido no `azuredeploy.parameters.json` ou pode ser sobrescrito aqui.
    -   `location`: A localização do Azure.
    -   `csmFile`: O caminho para o seu arquivo `azuredeploy.json`.
    -   `csmParametersFile`: O caminho para o seu arquivo `azuredeploy.parameters.json`.
    -   `overrideParameters`: Você pode sobrescrever parâmetros definidos no `azuredeploy.parameters.json` diretamente aqui. É uma boa prática para parâmetros sensíveis como senhas, usar variáveis de pipeline seguras (secret variables) em vez de hardcodar no YAML.

## Próximos Passos

1.  **Faça o upload** dos arquivos `azuredeploy.json`, `azuredeploy.parameters.json` e `README.md` para o seu repositório GitHub.
2.  **Crie um novo pipeline** no Azure DevOps, selecionando a opção 


para usar um repositório Git existente e aponte para o seu repositório GitHub.
3.  **Selecione a opção** para configurar um pipeline existente e aponte para o arquivo `azure-pipelines.yml` que você fará o upload.
4.  **Execute o pipeline** para implantar sua infraestrutura.

Lembre-se de que a senha do administrador da VM (`adminPassword`) é um parâmetro sensível. Em um ambiente de produção, é altamente recomendável que você utilize variáveis de pipeline seguras no Azure DevOps para gerenciar senhas e outras informações confidenciais, em vez de incluí-las diretamente no arquivo de parâmetros ou no YAML do pipeline.

---

*Documentação gerada por Manus AI em 07/09/2025.*

