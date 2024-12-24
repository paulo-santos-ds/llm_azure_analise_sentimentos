
# Utilização de Serviços de IA do Azure 

Usar serviços de IA do Azure hospedados no Azure permite que desenvolvedores de aplicativos se concentrem na infraestrutura para seu próprio código enquanto se beneficiam de serviços escaláveis ​​que são gerenciados pela Microsoft. No entanto, em muitos cenários, as organizações exigem mais controle sobre sua infraestrutura de serviço e os dados que são passados ​​entre os serviços.

Muitas das APIs de serviços de IA do Azure podem ser empacotadas e implantadas em um contêiner , permitindo que as organizações hospedem serviços de IA do Azure em sua própria infraestrutura; por exemplo, em servidores Docker locais, instâncias de contêiner do Azure ou clusters de serviços do Azure Kubernetes. Os serviços de IA do Azure em contêineres precisam se comunicar com uma conta de serviços de IA do Azure baseada no Azure para dar suporte ao faturamento; mas os dados do aplicativo não são passados ​​para o serviço de back-end, e as organizações têm maior controle sobre a configuração de implantação de seus contêineres, permitindo soluções personalizadas para autenticação, escalabilidade e outras considerações.

Nota : Há um problema atualmente sendo investigado que alguns usuários encontram onde os contêineres não são implantados corretamente, e as chamadas para esses contêineres falham. Atualizações para este laboratório serão feitas assim que o problema for resolvido.


## Clonar o repositório no Visual Studio Code

Você desenvolverá seu código usando o Visual Studio Code. Os arquivos de código para seu aplicativo foram fornecidos em um repositório do GitHub.

Dica : Se você já clonou o repositório mslearn-ai-services , abra-o no Visual Studio code. Caso contrário, siga estas etapas para cloná-lo para seu ambiente de desenvolvimento.


## Inicie o Visual Studio Code.
Abra a paleta (SHIFT+CTRL+P) e execute o comando Git: Clone para clonar o repositório para uma pasta local (não importa qual pasta).
``` bash
https://github.com/MicrosoftLearning/mslearn-ai-services
```
Quando o repositório tiver sido clonado, abra a pasta no Visual Studio Code.

Aguarde enquanto arquivos adicionais são instalados para dar suporte aos projetos de código C# no repositório, se necessário

Observação : se você for solicitado a adicionar os ativos necessários para compilar e depurar, selecione Agora não .

Expanda a pasta.Labfiles/04-use-a-container

## Provisionar um recurso do Azure AI Services

![image](https://github.com/paulo-santos-ds/llm_azure/blob/main/Imagem/servicos-azure-ai.png)



Se você ainda não tiver um em sua assinatura, precisará provisionar um recurso do Azure AI Services .

Abra o portal do Azure em e entre usando a conta da Microsoft associada à sua assinatura do Azure.
``` bash
https://portal.azure.com
``` 

Na barra de pesquisa superior, pesquise por Serviços de IA do Azure , selecione Serviços de IA do Azure e crie um recurso de conta multisserviço de Serviços de IA do Azure com as seguintes configurações:

![image](https://github.com/paulo-santos-ds/llm_azure/blob/main/Imagem/criando_servico.png)

- Assinatura : Sua assinatura do Azure
- Grupo de recursos : escolha ou crie um grupo de recursos (se estiver usando uma assinatura restrita, talvez você não tenha permissão para criar um novo grupo de recursos - use o fornecido)
- Região : Escolha qualquer região disponível
- Nome : Insira um nome exclusivo
- Nível de preço : Standard S0

Selecione as caixas de seleção necessárias e crie o recurso.

Aguarde a conclusão da implantação e, em seguida, visualize os detalhes da implantação.

Quando o recurso tiver sido implantado, vá até ele e visualize sua página Keys and Endpoint . Você precisará do endpoint e de uma das chaves desta página no próximo procedimento.

## Implantar e executar um contêiner de análise de sentimento

Muitas APIs de serviços de IA do Azure comumente usadas estão disponíveis em imagens de contêiner. Para uma lista completa, confira a documentação dos serviços de IA do Azure . Neste exercício, você usará a imagem de contêiner para a API de análise de Sentimento do Text Analytics ; mas os princípios são os mesmos para todas as imagens disponíveis.

No portal do Azure, na página inicial , selecione o botão criar um recurso , pesquise por instâncias de contêiner e crie um recurso Instâncias de contêiner com as seguintes configurações:

Noções básicas :

![image](https://github.com/paulo-santos-ds/llm_azure/blob/main/Imagem/criando_servico_container.png)

- Assinatura : Sua assinatura do Azure
- Grupo de recursos : escolha o grupo de recursos que contém seu recurso de serviços de IA do Azure
- Nome do contêiner : Insira um nome exclusivo
- Região : Escolha qualquer região disponível
- Zonas de disponibilidade : Nenhuma
- SKU : Padrão
- Fonte da imagem : Outro Registro
- Tipo de imagem : Pública
- Tipo de SO : Linux
- Tamanho : 1 vcpu, 8 GB de memória
- Imagem :
``` bash
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:latest
``` 

## Rede :

![image](https://github.com/paulo-santos-ds/llm_azure/blob/main/Imagem/criando_servico_rede.png)


- Tipo de rede : Pública
- Rótulo do nome DNS : insira um nome exclusivo para o ponto de extremidade do contêiner
- Portas : Altere a porta TCP de 80 para 5000

## Avançado :

![image](https://github.com/paulo-santos-ds/llm_azure/blob/main/Imagem/criando_servico_avancado.png)

- Política de reinicialização : Em caso de falha
- Variáveis ​​de ambiente:
- Sim	ApiKey	Qualquer chave para o seu recurso de serviços de IA do Azure
- Sim	Billing	O URI do ponto de extremidade para seu recurso de serviços de IA do Azure
- Não	Eula	accept
  
- Substituição de comando : [ ]

- Gerenciamento de chaves : chaves gerenciadas pela Microsoft (MMK)

## Etiquetas :

- Não adicione nenhuma tag

Selecione Revisar + criar e, em seguida, selecione Criar. 

Aguarde a conclusão da implantação e, em seguida, vá para o recurso implantado.

## Observação Observe que a implantação de um contêiner do Azure AI em Instâncias de Contêiner do Azure normalmente leva de 5 a 10 minutos (provisionamento) antes que eles estejam prontos para uso.

Observação Observe que a implantação de um contêiner do Azure AI em Instâncias de Contêiner do Azure normalmente leva de 5 a 10 minutos (provisionamento) antes que eles estejam prontos para uso.


![image](https://github.com/paulo-santos-ds/llm_azure/blob/main/Imagem/criando_servico_instancia.png)


Observe as seguintes propriedades do recurso de instância do contêiner na página Visão geral :

- Status : Deve estar em execução .
- Endereço IP : este é o endereço IP público que você pode usar para acessar suas instâncias de contêiner.
- FQDN : este é o nome de domínio totalmente qualificado do recurso de instâncias do contêiner. Você pode usá-lo para acessar as instâncias do contêiner em vez do endereço IP.
- Observação : neste exercício, você implantou a imagem do contêiner de serviços de IA do Azure para análise de sentimentos em um recurso de Instâncias de Contêiner do Azure (ACI). Você pode usar uma abordagem semelhante para implantá-la em um host do Docker em seu próprio computador ou rede executando o comando a seguir (em uma única linha) para implantar o contêiner de análise de sentimentos em sua instância local do Docker, substituindo <yourEndpoint> e <yourKey> pelo URI do seu ponto de extremidade e qualquer uma das chaves para seu recurso de serviços de IA do Azure. O comando procurará a imagem em sua máquina local e, se não a encontrar lá, a extrairá do registro de imagem mcr.microsoft.com e a implantará em sua instância do Docker. Quando a implantação for concluída, o contêiner será iniciado e escutará as solicitações de entrada na porta 5000.

``` bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:latest Eula=accept Billing=<yourEndpoint> ApiKey=<yourKey>
```

## Use o Container

No seu editor, abra rest-test.cmd e edite o comando curl que ele contém (mostrado abaixo), substituindo <seu_endereço_IP_ACI_ou_FQDN > pelo endereço IP ou FQDN do seu contêiner.

``` bash
curl -X POST "http://<your_ACI_IP_address_or_FQDN>:5000/text/analytics/v3.1/sentiment" -H "Content-Type: application/json" --data-ascii "{'documents':[{'id':1,'text':'The performance was amazing! The sound could have been clearer.'},{'id':2,'text':'The food and service were unacceptable. While the host was nice, the waiter was rude and food was cold.'}]}"
```

Salve suas alterações no script pressionando CTRL+S . Observe que você não precisa especificar o ponto de extremidade ou a chave dos serviços de IA do Azure - a solicitação é processada pelo serviço em contêiner. O contêiner, por sua vez, se comunica periodicamente com o serviço no Azure para relatar o uso para cobrança, mas não envia dados de solicitação.

Digite o seguinte comando para executar o script:

``` bash
./rest-test.cmd
```


Verifique se o comando retorna um documento JSON contendo informações sobre o sentimento detectado nos dois documentos de entrada (que devem ser positivos e negativos, nessa ordem).

![image](https://github.com/paulo-santos-ds/llm_azure/blob/main/Imagem/rest-test_cmd.png)

![image](https://github.com/paulo-santos-ds/llm_azure/blob/main/Imagem/criando_servico_utilizacao.png)



## Limpar

Se você terminou de experimentar sua instância de contêiner, você deve excluí-la.

No portal do Azure, abra o grupo de recursos onde você criou seus recursos para este exercício.
Selecione o recurso de instância do contêiner e exclua-o.

## Limpar recursos

Se você não estiver usando os recursos do Azure criados neste laboratório para outros módulos de treinamento, poderá excluí-los para evitar cobranças adicionais.

Abra o portal do Azure em e, na barra de pesquisa superior, procure os recursos que você criou neste laboratório. 


Na página de recursos, selecione **Delete** e siga as instruções para excluir o recurso. Como alternativa, você pode excluir todo o grupo de recursos para limpar todos os recursos ao mesmo tempo.

## Mais informações

``` bash
https://portal.azure.com
``` 


