# ETL-DW
Este foi um projeto de ETL para um Data Warehouse está bem resumido e dinâmico


Olá gostaria de apresentar um projeto que desenvolvi com alguns dados acadêmicos, neste projeto eu desenvolvi um Data Warehouse através de procedimentos de ETL, com cargas dos bancos de dados origem, para o stage e por fim para o Data Warehouse, e ao final automatizando o projeto. Vamos aos requisitos do projeto!
Requisitos do Projeto

Stakeholder:  Joana Sousa

Contexto:
“No comercial temos uma planilha, a qual preenchemos as vendas que são feitas diariamente no nosso site. Gostaria de ter essas informações no banco de dados para poder ter um carregamento rápido ao importar no Power BI, pois atualmente, quando clico em atualizar dentro do Power BI, passa muito tempo carregando. ”

Atualmente analisamos os seguintes indicadores:
Custo fabricação, Unidades Vendidas, Preco venda, Vendas total, Descontos, Venda Líquida.
Venda Líquida = (Vendas total – Descontos)
“Os indicadores são analisados ao longo do tempo por Produto, Segmento e País.”

Com os requisitos em mãos, vamos partir para modelagem de dados, essa é uma etapa muito importante na criação de um Data Warehouse pois a modelagem será a base das nossas dimensões e da nossa tabela fato! Através do SQL Power Architect, montei uma modelagem Star Schema baseada nos requisitos do negócio:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/0c15e52a-c6d6-4407-b2a4-200184c566ce)

No processo de ETL enviamos os dados da origem para um banco stage, que é um intermediário onde vamos tratar os dados, e temos duas opções, uma é tratar o dado da origem para o banco stage ou tratar os dados do stage para o Data Warehouse. Vamos criar os bancos de stage e Data Warehouse!

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/62e95302-19b3-49cc-aa0b-2fde22b349ae)

Vamos utilizar o SQL Server Integration Services (SSIS) para os nossos procedimentos de ETL, para utilizar o SQL Server Integration Services usei o visual studio 2019, ao iniciar o SQL Server Integration Services e criar um projeto, podemos utilizar a tarefa de fluxo de dados para utilizarmos os Steps para carga e transformação. Um exemplo com a tabela segmento:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/c3a53197-cc49-438a-a0cf-c696139b96b7)

Vamos conectar no banco de dados origem com o componente “Origem OLE DB”, e vamos conectar com o banco de dados e selecionar a tabela da conexão de origem:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/be7e5590-7437-4c70-8600-edb7025c7b0b)

Como os dados estão todos em uma planilha será necessário separar os dados para cada tabela:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/efe2f0b8-086f-4689-9e23-32775a3248d0)

Agora os dados já estão prontos para serem inseridos na tabela de origem, vamos utilizar o componente “Destino OLE DB” e selecionas o banco e criar a tabela de destino:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/a14d825f-4fda-407a-afe8-bee50ecbccb1)

Para criar a tabela destino, basta clicar em nova e através de um comando SQL podemos criar uma tabela no banco de dados sem nem mesmo abri-lo!

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/15ab797c-4999-487c-8aba-76a2d9c461d9)

Ao criar a tabela de destino, é importante mapearmos as tabelas pois assim podemos saber quais as colunas que estão indo para o nosso destino e podemos selecionar as colunas que queremos ou não na nossa tabela destino, um exemplo com a tabela segmento, esse conceito ficará mais claro quando criarmos a fato!

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/baa64f70-2961-4c0e-84a8-24a0d9404a37)

Ao final de todos esses procedimentos nossa carga para o banco stage:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/dcfa5316-e961-4379-b3f3-4869181f06bf)

Muito simples! Podemos repetir os procedimentos para as outras tabelas(País e Produto), ao criar as tarefas de fluxo de dados com as cargas para o stage, podemos liga-los assim ao terminar uma carga ele inicia outra em sequência:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/9a1e0eb2-fca6-47d6-8303-91c2efc7a0b9)

Agora vamos criar a nossa tabela de vendas e carregá-la para o nosso banco stage, vamos configurar a tabela na origem e vamos selecionar apenas as colunas que vamos precisar na tabela de vendas, podemos fazer isso através do mapeamento:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/21b2f74f-23e3-4331-b6cd-d0bda1f63363)

Agora basta criar nossa tabela no componente “Destino OLE DB”:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/8b511b88-7cb3-41e9-9662-4deff3dafb4e)

Após mapeá-la podemos carregar os dados sem problemas! No “Fluxo de Controle” temos um componente que se chama “contêiner de sequência”, vamos colocar as tarefas de fluxos de dados no contêiner de sequência:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/f1bdb183-7aed-4919-8bf1-5bbc797320d9)

Assim todas as tarefas de fluxos de dados podem executar uma atrás da outra mesmo se as tarefas não estiverem conectadas e podemos colocar várias tarefas dentro do contêiner de sequência. Neste projeto vou utilizar um script SQL antes de executar as cargas para Stage, para evitar dados duplicados, mas do stage para o Data Warehouse vamos executar uma carga incremental, por fim, nossas cargas para stage vai ficar assim:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/d58e8e0a-8981-42fa-84b8-0149fae5b3ad)

Para as próximas tarefas de dados, vamos precisar da modelagem para identificar as colunas que irão compor as dimensões, as dimensões País, Produto e Segmento apenas iremos acrescentar nossas surrogate key’s em cada tabela, e executar os mesmos procedimentos, só que agora da stage para nosso Data Warehouse. Vamos gerar um script SQL para a dimensão tempo, ela é muito importante pois através dela podemos armazenar um histórico de dados! A dimensão tempo não terá carga incremental, ela não será alterada até chegar em sua data máxima, esse foi o script que usei, mas foi algo para fins acadêmicos:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/f95819e5-1ff9-490b-953c-817c3e2ca355)

No componente “Origem OLE DB” temos a opção de usar um comando SQL, vamos utilizar essa CTE para popular nossa dimensão tempo:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/b5fce77d-f219-451e-b5b5-5bd774ddbf21)

Visualizando a tabela:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/62bf9eba-50b2-480d-b135-0e2c1135b29e)

Basta mapeá-la e a nossa dimensão tempo já está pronta! Vamos gerar um script para truncar as tabelas dimensões, assim a carga para o DW fica mais rápido, mas é muito importante se atentar as regras de negócio pois se houver slowly changing dimension, não seria possível truncar as tabelas, a slowly changing dimension armazena um histórico do dado mas na dimensão, então muita atenção a isso! Ao final de todo o procedimento teremos as cargas para as nossas tabelas dimensões:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/5a62312f-4bac-458e-a25e-58b5ee1f6162)

Agora vamos carregar nossa tabela fato! Para a nossa tabela fato, temos que pegar a tabela de vendas no banco de dados stage e através de joins buscar as sk’s de cada dimensão, no SQL Server Integration Services temos um componente de pesquisa nele vamos ligar a tabela de vendas com as dimensões e trazer somente a surrogate key, utilizando o id como relacionamento das tabelas para fazer o Join:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/1a5b46d9-1e63-4cd8-83f4-b64087dbe2f3)

Buscando a surrogate key:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/f58f1ec0-f683-4ab4-9078-8c788bbe401a)

Por fim vamos fazer um union, na saída da pesquisa tem duas opções saída correspondente e saída sem correspondente, vamos utilizar uma coluna derivada para tratar os dados, ou seja, se ele não achar o dado correspondente (NULL) então faça a inserção de -1:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/ed98c279-d4b1-4458-bc4c-264ca3369f12)

Ficando assim os processos:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/fbe84970-89b8-4c5f-a1a4-3dbf943918ea)

E utilizamos esse método para as outras dimensões! E por fim criamos a fato e mapeamos os dados para carregarmos apenas as Sk’s e as métricas definidas pela stakeholder!

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/b82b8e3e-37ae-4c37-8558-2f36ef281027)

 Na carga da fato foram apenas os dados de 2013, isso foi feito para exemplificar a carga incremental, com um script sql podemos fazer essas carga de um jeito bem fácil, basta transformar a SKData para data e selecionar a data máxima:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/016eeadd-556c-4cd2-81f0-d2e549680936)

Assim sempre vamos ter o dado mais atualizado! Vamos executar e depois automatizar as cargas no SQL Server!

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/7fc203de-1f0b-48be-94a3-5375ad0ef712)

No SQL Server vamos criar um catálogo para automatizar esse job, para isso é necessário que o SQL Agent esteja executando tudo certinho, caso não esteja basta inicia-lo no SQL Server configuration manager, vamos criar nosso catálogo, não se esqueça de criar uma senha!

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/e6d89917-6025-4349-8249-31302b15516f)

Após cria-lo ele irá aparecer na basta de catálogos do SQL Server:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/aa110b2d-3c9d-4aeb-ab5a-e7feb7971edc)

No Integration Services vamos clicar com o botão direito do mouse em cima do projeto e vamos em implantar ou deploy se estiver em inglês:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/e4b275bb-164d-4cf7-ab30-4f0463bdfb95)

Selecione SQL Server e next:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/27826e7a-3284-40f9-a8ed-9e6d3174a9c1)

Depois vamos conectar no banco, com o nome do servidor e a autenticação, vamos clicar em procurar e encontrar o catálogo que criamos nele vamos criar a pasta do nosso projeto:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/fb6dbfd4-6c25-4699-b89d-dd83b61b2e07)

Depois vamos ter o resumo e vamos implantar!

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/af877779-1e2e-440a-a2ff-202a01fb0cd0)

No SQL Server vamos no SQL Agent > Trabalhos > Novo Trabalho:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/981ab17c-743f-48b0-8ae5-4a7e92c855f7)

Vamos nomear o novo trabalho e logo depois vamos para as etapas de execução:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/21e4e311-949e-44c2-97f5-27c0ba15006a)

Nas etapas vamos selecionar o serviço do integration services e escolher o pacote que vamos automatizar:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/78258d02-4f8d-4ee4-ac93-4f6b7f069aca)

Depois vamos em agendas e agendar a frequência e os dias da semana que o ETL será executado:

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/75c892b3-d0e4-4b14-838e-d8f229e78faf)

Por fim vamos executar!

![image](https://github.com/Miltonmoura011/ETL-DW/assets/110420164/ac3d6622-9f5e-4588-94a6-91450e00a4d7)

Importante destarcar que eu automatizei a carga no meu localhost, o que não é uma boa prática fiz isso apenas para fins didáticos, não é uma boa prática o computador local ele desliga o ideal é automatizar esse processo em um servidor pois ele fica ligado 24h por dia!
























