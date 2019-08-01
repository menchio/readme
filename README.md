# Sistema de relatorio de disponibilidade de sensores de monitoramento @ Real Protect, a.k.a. PAINEL DE INDICADORES DE DISPONIBILIDADE ou RP-PID.
## Autor: 
1. Daniel Alves

    Security Operation Center / Real Protect

    Tel: 21 2114.2345
    
2. Anderson de Jesus Santos

    Security Operation Center / Real Protect

    Tel: 11 4861.4003 – Ramal 3009

    Skype: anderson7408
    
3. Vinícius Andrade

    Security Operation Center / Real Protect

    Tel: 21 2114.2323

    Skype: vigreenhalgh
    
4. Rodrigo Menchio

    Security Operation Center / Real Protect

    Tel: 21 2114.2345


    

# Lista de Mudancas Principais
08/05/19 - Upload e versionamento fonte prototipo com GIT

09/07/19 - Release alfa makecatalog.py fazendo detecção de objetos de interesse no PRTG

19/07/19 - Restruturação para melhor manutenibilidade: dir. tmp é cache do sistema, cujos dados das empresas são gerados novamente após detecção dos sensores desejados de acordo com as tags configuradas no PRTG.

## Protótipo construído em Fev/2019
## Implantado em produção em XXX/2019.


# Arquitetura

## Estrutura do Fonte:
<br/>
/               -   Raiz do sistema. Base do versionamento.
<br/>
<br/>
index           -   Página default. Faz chamadas apenas a imagens e formulário principal do sistema. Com javascript é feita a chamada para construção do report desejado. Ver mapa de funcionamento do sistema.
<br/>
<br/>
pidhash*        -   credenciais para autenticação na API do PRTG (fora do versionamento)
<br/>
<br/>
img/            -   Diretório com imagens do sistema.
<br/>
<br/>
header          -   Cabeçalho HTML, que faz chamadas aos arquivos de estilo, javasccript , etc. Chamada pelas pagina index inferiores.
<br/>
<br/>
footer          -   Rodapé HTML, que faz chamadas aos arquivos de estilo. Chamada pelas pagina index inferiores.
<br/>
<br/>
skel/		-   Diretório com layouts modulares, que serão utilizados na construção do relatório conforme houverem sensores em cada seção (links, switches, etc)
<br/>
<br/>
MAKEREPORT.PY	-   Backend do sistema, que detecta os sensores conforme tags configuradas no prtg, indexando os respectivos IDs no catalog
<br/>
<br/>
report       -   Inicia a construção do relatório. Faz chamadas ao header e footer do diretório superior para montar o layout básico do relatório, que são compartilhados entre todas as empresas. Chamada para o backend. Ver mapa de funcionamento do sistema.
<br/>
<br/>
tmp/		-   "Work", a.k.a. cache do sistema. Os dados contidos aqui são gerados a cada nova construção de relatório. (dados aqui dentro estão fora do versionamento)
<br/>
<br/>
tmp/"EMPRESA"/      -   Diretório com arquivos temporários relacionados ao último report gerado sobre uma empresa em questão, cujos arquivos abaixo dele são o esqueleto de cada empresa
<br/>
<br/>

## Operacao:
1. Corpo técnico ou gestores acessam o Painel de Indicadores através do navegador.
2. Usuário então seleciona a competência apropriada para o relatório (Default = Último mês), e clica na empresa de que se deseja analisar, neste momento o script makecatalog.py é acionado e o sistema inicia a detecção de sensores e construção dos relatórios
3. Sistema executa uma chamada HTTP à API PRTG, que responde no formato JSON
4. Sistema constrói índice com sensores necessários
5. Sistema prepara os módulos necessários aos grupos de sensores detectados.
5. Sistema constrói relatório e exibe na tela.


### adicionar **tags** a sensores
>> Ação feita através do sistema de monitoramento para definir os sensores que serão reportados

1. Após acessar o PRTG, vá a página de algum id em que você deseje identificar através de uma tag e clique em Settings.

![IMG CONFIG](/img/settings.png)

2. Ao entrar na página, procure pela caixa “Tags” em “Basic Sensor Settings” e insira a tag que desejar. Pronto, seu sensor já tem uma tag acompanhando-o e pode ser encontrado através desta tag.

![IMG TAGS](/img/tags.png)

Nosso script utiliza as seguintes **tags**:

![IMG TABELA](/img/pop.PNG)

**OBS:** É de altissima importância incluir a tag pidxxx nas probes do cliente, caso contrário, a indexação não funcionará.

Exemplo: pidaustral nos ids 29740, 33212 e 36273.

**Sub tags** significam tags que estão atrelados a uma **tag** principal. Por exemplo, a tag **pidlded** indica sensores de links dedicados, que por sua vez possuem mais informações, como a provedor (ISP), o tipo de link e a localidade.

**OBS:** Exclusivamente as tags pidlded e pidlint precisam ser definidas no sensor que se deseja monitorar (RT-XXXX / Ping) pois definir em qualquer parent faria com que todos os demais sensores entrassem na relação de indicadores a ser reportada.

Para fins demonstrativos, observe a imagem abaixo, com as tags definidas em um sensor de link dedicado:

![IMG EXEMPLO](/img/tags-exemplo.png)

Note que o sensor possui as **sub tags** chamadas *isp:algar*, *tipodolink:pap*, *site:rj-dc*, que deverão ser definidas junto com a tag *pidlded*, no processo de adição de um novo ativo.





## Requisitos:
+ Microsoft Visual C++ 2017 Redistributable
+ XAMPP v7.3.1 \
    * Apache 2.4
    * PHP v7.3.1
+ PRTG (Login + Passhash) - Chave restrita às cópias locais e portanto fora do versionamento. Ver "Passhash"!
+ Python 3 (e lib requests)
+ FONTE: https://github.com/dbeniciorj/RP-PID 
<br/>
**OBS:** Para instalação basta:
<br/>
1 - instalar os requisitos acima e disponibilizar o código no servidor de páginas.
<br/>
2 - criar arquivos pidhash
<br/>
3 - configurar permissionamento de escrita ao servidor de páginas na pasta TMP ,  para habilitar o sistema a criar/sobrescrever os dados necessários à criação dos reports
<br/>
<br/>


## DEV:
Para sincronizar o fonte, efetuar os passos abaixo (ou preferencialmente utilizar uma ferramenta gráfica para operação do GIT. Ex.: Git GUI - vem junto com o pacote GIT BASH, ou GIT-DESKTOP):
<br/>
1. Baixe e instale o GIT BASH a partir do site  https://git-scm.com/download/win
2. Acesse a raiz do servidor de páginas, conforme descrito anteriormente.
3. Clone o repositório com o comando
<br/>


```bash
git clone https://github.com/dbeniciorj/RP-PID
```


<br/>    
OBS: Mais instruções para operações com GIT em https://rogerdudler.github.io/git-guide/ e https://guides.github.com/features/mastering-markdown/
<br/>    
<br/>

Para IDE, sugestões de uso: PyCharm Community e Netbeans.
<br/>    
<br/>   

Por fim, localmente o arquivo "exemplo.gitignore" DEVE ser copiado com nome ".gitignore" (ou ".gitignore." se for no Windows) para evitar subir credenciais e/ou lixo para o repositório nos próximos commits.
<br/>    
<br/> 

## Passhash:
Autenticação do sistema na API PRTG armazenada na raiz do servidor de páginas e fora do versionamento por questão de segurança, no arquivo pidhash.php e pidhash.py, cujos exemplos seguem abaixo.\
Cada "Empresa" faz uma consulta ao arquivo com a chave de acesso, conforme código abaixo, e cujo valor em PASSHASH pode ser obtido com a seguinte chamada a API:
```
https://PRTG_hostname_or_IP_address/api/getpasshash.htm?username=myuser&password=mypass 
```

Exemplos:
<br/>
pidhash.php:
```php
<?php	

	$USERNAME = "rppid";
	$PASSHASH = "XXXXXXXXXXXXXXXXXXXXX";
	
?>
```
<br/>
pidhash.py:

```bash
username = "rppid"
passhash = "XXXXXXXXXXXXXXXXXXXXX"
```
**OBS:** No caso de instalação do sistema em uma subpasta no servidor de páginas (ou seja, acesso ocorre pelo endereço http://XXXXX/RP-PID ao invés de  http://XXXXX ), o pidhash.py DEVE ficar na mesma pasta que o script makereport.py .
<br/>
<br/>
O arquivo 

## Funcionamento do sistema:
![IMG MAPA](/img/MAP.png)

## Detecção de sensores:

BACKEND: O script makereport.py, escrito em Python 3, é chamado pelo report.php e gera um arquivo tmp/EMPRESA/catalog.php com intuito de indexar os sensores que precisam ser reportados. Além disso, conforme a existência de sensores em cada seção de ativos uma cópia do respectivo layout também é feita para o diretório (ex.: tmp/EMPRESA/splunk.php), modularizando o relatório que será gerado a seguir incluindo ou não determinada seção no arquivo tmp/EMPRESA/mod.php.

No FRONTEND o PHP monta uma página web com o relatório dos ativos cujos ids foram indexados. Após conclusão do script anterior, é carregado o arquivo tmp/EMPRESA/mod.php que referencia cada seção que será usada na montagem da página. AS SEÇÕES (tmp/EMPRESA/dedicados.php, outros.php, etc) CONSULTAM A API DO PRTG A CADA SENSOR INDEXADO NO ARQUIVO catalog.php POR INFORMAÇÕES PARA EXIBIÇÃO NO RELATÓRIO.

## Troubleshooting:

Atualmente, a execução manual do script (para fins de diagnóstico / simulação do backend) é feita conforme o exemplo abaixo, passando a empresa (em uma única palavra) como parâmetro:

```bash
python makereport.py austral
```

A partir deste ponto, o script busca pelas **tags** já pré-definidas e constroi o relatório, baseado no parâmetro "empresa" que você indicar.


### Códigos de erro conhecidos

***"Erro na detecção de sensores. Simular automação para identificar falha na execução do script."***
* O report.php não recebeu uma resposta de sucesso (cód.0) durante a execução do makereport.py
	* Falha de autenticação
	* Erro na estrutura do script
	* Falta de permissionamento de escrita para o serviço de páginas no diretório TMP

<br/>

***"Erro na criação do relatório. Nenhum sensor encontrado!"***
* O report.php não encontrou informação de sensores no catálogo.
	* Falha da configuração de tags




