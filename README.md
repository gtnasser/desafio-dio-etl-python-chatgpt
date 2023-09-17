# desafio-dio-etl-python-chatgpt
Desafio de projeto da plataforma [DIO](https://web.dio.me) - Explorando IA Generativa em um Pipeline de TEL com Python

# Desafio DIO - Projeto Pipeline de ETL com Python

Olá, este é um projeto realizado para o curso Explorando IA Generativa em um Pipeline de TEL com Python, do [Bootcamp DIO Santander Bootcamp 2023 - Ciência de Dados com Python](https://web.dio.me/track/71477949-f762-43c6-9bf2-9cf3d7f61d4a)

No curso foram usados como referência um Notebook criado via Google Colab e uma API desenvolvida para o Santander Dev Week 2023.

* [colab.research.google.com](https://colab.research.google.com/drive/1SF_Q3AybFPozCcoFBptDSFbMk-6IVGF-?usp=sharing): Notebook criado via Google Colab com todo o código-fonte desenvolvido neste Desafio de Projeto (Lab)

* [github.com/digitalinnovationone/santander-dev-week-2023-api](https://github.com/digitalinnovationone/santander-dev-week-2023-api): GitHub com a API desenvolvida para a Santander Dev Week 2023 com informações úteis. Relevante para quem quiser saber mais sobre o processo de criação da API RESTful consumi neste Lab.

* Documentação [Swagger](https://sdw-2023-prd.up.railway.app/swagger-ui/index.html) da API.

O objetivo deste projeto é usar o Python para ler uma lista de usuários, buscar informações sobre os mesmos utilizando a API, completar o cadastro com informações coletadas utiizando o Chat GPT, e atualizar o cadastro do usuário usando a mesma API.

## Pré-requisitos

## 1. Python

Vamos utilizar o Google Colab para executar o Python, assim não temos que instalar nada em nosso equipamento. Isso agiliza bastante o desenvolvimento. Se quiser utilizar o meu Notebook, estou compartilhando ele [aqui](), ele contém todos os comandos que estou utilizando ao longo deste tutorial. 

## 2. Chat GTP

É necessário ter um cadastro no site da [OpenAI](https://openai.com/chatgpt) para utilizar o Chat GPT. Vamos acessar através da API, então é necessário criar um token para validar as requisições, da seguinte maneira:
* 1. crie uma conta na OpenAI
* 2. navegue por API > Personal > VIew API Keys ou pelo [link](https://platform.openai.com/account/api-keys)
* 3. Clique em **+ Create New Secret Key**, e confirme a criação da Secret Key
* 4. Copie a chave antes de fechar a mensagem, você não poderá acessá-la novamente.

A API do Chat GPT pode ser utilizada gratuitamente até 30 dias após o cadastro. Se você tem um cadastro antigo e não consegue mais realizar as requisições gratuitas, tem 2 caminhos: cadastrar seu cartão de crédito e pagar pelas requisições (alguns centavos de dolar para realizar este desafio), ou realizar um novo cadastro utilizando outro email.

## 3. API

A API utilizada é a que foi desenvolvida para a Santander Dev Week 2023, ela foi disponibilizada pela DIO e pode ser acessada publicamente. Então não dá para confiar que os dados cadastrados anteriormente ainda existem e permanecem íntegros. Então vamos cadastrar alguns usuários para este desafio. Consulte a documentação [Swagger](https://sdw-2023-prd.up.railway.app/swagger-ui/index.html) para se familiarizar com o endpoint */users*. 

Eu utilizei o [curl](https://curl.se/docs/manpage.html) para cadastrar os usuários, mas voce pode utilizar a ferramenta que preferir ou que tiver mais familiaridade, como por exemplo, instalando o [Postman](https://www.postman.com) ou utilizando o [REQBIN](https://reqbin.com/post-online) online. Os arquivos utilizados com os dados dos 6 usuários estão compartilhados no meu [Github](https://github.com/gtnasser/desafio-dio-etl-python). 

Para cadastrar usuários utilizando o ```curl```, abra o Terminal e execute na linha de comando:
```sh
C:\Users\t4005027\Downloads\desafio-dio-etl-python>curl -X POST -d @user1.json https://sdw-2023-prd.up.railway.app/users -H "accept: */*" -H "Content-Type: application/json"
{"id":2800,"name":"Deodoro da Fonseca","account":{"id":2961,"number":"171-1889-1891","agency":"0055","balance":0.00,"limit":500.00},"card":{"id":2719,"number":"**** **** **** 1889","limit":1000.00},"features":[],"news":[]}

C:\Users\t4005027\Downloads\desafio-dio-etl-python>curl -X POST -d @user2.json https://sdw-2023-prd.up.railway.app/users -H "accept: */*" -H "Content-Type: application/json"
{"id":2801,"name":"Floriano Peixoto","account":{"id":2962,"number":"1891-1894","agency":"0055","balance":0,"limit":500},"card":{"id":2720,"number":"**** **** **** 1891","limit":1000},"features":[],"news":[]}

C:\Users\t4005027\Downloads\desafio-dio-etl-python>curl -X POST -d @user3.json https://sdw-2023-prd.up.railway.app/users -H "accept: */*" -H "Content-Type: application/json"
{"id":2802,"name":"Prudente de Morais","account":{"id":2963,"number":"1894-1898","agency":"0055","balance":0,"limit":500},"card":{"id":2721,"number":"**** **** **** 1894","limit":1000},"features":[],"news":[]}

C:\Users\t4005027\Downloads\desafio-dio-etl-python>curl -X POST -d @user4.json https://sdw-2023-prd.up.railway.app/users -H "accept: */*" -H "Content-Type: application/json"
{"id":2803,"name":"Campos Salles","account":{"id":2964,"number":"1898-1902","agency":"0055","balance":0,"limit":500},"card":{"id":2722,"number":"**** **** **** 1898","limit":1000},"features":[],"news":[]}

C:\Users\t4005027\Downloads\desafio-dio-etl-python>curl -X POST -d @user5.json https://sdw-2023-prd.up.railway.app/users -H "accept: */*" -H "Content-Type: application/json"
{"id":2804,"name":"Rodrigues Alves","account":{"id":2965,"number":"1902-1906","agency":"0055","balance":0,"limit":500},"card":{"id":2723,"number":"**** **** **** 1902","limit":1000},"features":[],"news":[]}
```

4. Lista de ID de Usuários

Com base nas mensagens de retosno da API, vamos montar o arquivo 'SDW2023.csv' com a lista de ID dos usuários:
```
UserID
2800
2801
2802
```

Apesar criar vários usuários, limitamos a lista com apenas 3 IDs, porque a versão utilizada para acessar o Chat GPT é a gratuita e tem limitação de 3 requisições por minuto. Assim não teremos nenhuma mensagem de erro ao trabalhar com as requisições que faremos a seguir.

## ETL

Como todos nós já sabemos, um ETL é formado pelas 3 etapas: Extract (extração de dados), Transform (limpeza e transformação) e Load (carga para o repositório final). Neste desafio, executaremos as atividades em cada etapa:
* Extract : ler uma lista de usuários e buscar informações sobre os mesmos utilizando a API
* Transform: completar o cadastro com informações coletadas utiizando o Chat GPT
* Load: atualizar o cadastro do usuário usando a API

Os comandos a seguir serão executados no Google Colab, 

Definir a URL da API que será utilizada:

```py
SDW23_API_URL = 'https://sdw-2023-prd.up.railway.app/'
```

## Extract

Usaremos a biblioteca **pandas** para ler o arquivo com a ID dos usuários. 

```py
# Extrair os IDs da lista 
import pandas as pd

df = pd.read_csv('SDW2023.csv')
user_ids = df['UserID'].tolist()

print(user_ids)
```
>[2800, 2801, 2802, 2803, 2804]


Em seguida buscaremos com uma requisição GET as informações de cada usuário da lista, utilizando a API

```py
import requests
import json

# busca dado do usuario especifico
def get_user(id):
  response = requests.get(f'{SDW23_API_URL}users/{id}')
  return response.json() if response.status_code == 200 else None

# buscar dados de cada usuario da lista
users = [user for id in user_ids if (user := get_user(id)) is not None]

print(json.dumps(users, indent=2))
```

>[
  {
    "id": 2800,
    "name": "Deodoro da Fonseca",
    "account": {
      "id": 2961,
      "number": "171-1889-1891",
      "agency": "0055",
      "balance": 0.0,
      "limit": 500.0
    },
    "card": {
      "id": 2719,
      "number": "**** **** **** 1889",
      "limit": 1000.0
    },
    "features": [],
    "news": []
  },
  ...


## Transform

Usaremos a biblioteca do **Chat GPT** para acessá-lo, mas antes precisaremos instalá-la no Google Colab.

```py
# instalar biblioteca para usar o Chat GPT
!pip install openai
```

Vamos também associar a chave criada anteriormente na sua conta, para usar nas requisições.

Agora vamos fazer uma requisição para cada usuário, pedindo ao Chat GPT que gere uma recomendação de investimento personalizada, e vamos armazenar esta recomendação no cadastro de cada usuário.

Vamos usar como base o código disponibilizado na [documentação de uso](https://platform.openai.com/docs/api-reference/chat/create?lang=python) da API do Chat GPT.


```py
import openai

# chave API Key
openai.api_key = "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"

# solicita ao Chat GPT uma recomendação de investimento
def generate_ai_news(user):
  completion = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
      {
          "role": "system", 
          "content": "Voce é um especialista em marketing bancário."
      },
      {
          "role": "user", 
          "content": f"Crie uma mensagem para {user['name']} oferecendo algum produto financeiro do Santander (máximo de 200 caracteres)"
      }
    ]
  )

  # as vezes o Chat GPT encapsula a resposta em aspas duplas, usamos strip para retirá-las
  return completion.choices[0].message.content.strip('\"')

# buscar recomendacao de investimento para cada usuario da lista
for user in users:
  news = generate_ai_news(user)
  user['news'].append({
    "icon": "https://digitalinnovationone.github.io/santander-dev-week-2023-api/icons/credit.svg",
    "description": news
  })
  print(news)
```

>Sr. Deodoro, o Santander apresenta o Cartão de Crédito Prime, exclusivo para clientes VIP como o senhor. Aproveite benefícios exclusivos e flexibilidade de pagamento. Solicite já!
>
>Floriano Peixoto, conheça nossa oferta exclusiva de empréstimo pessoal Santander, com taxas especiais. Acesse santander.com.br e faça sua simulação agora!
>
>Prudente de Morais, conheça o Santander e aproveite nossos produtos financeiros exclusivos para você. Venha ser nosso cliente e conquiste as suas metas!

Avançando na *brincadeira* com o Chat GPT, vamos pedir que ele informe o principal feito de cada presidente. Opa, como assim, não tinha percebido que os usuários que eu cadastrei foram os primeiros presidentes do Brasil?

```py
# solicita ao Chat GPT info do presidente
def generate_ai_news(user):
  completion = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
      {
          "role": "system", 
          "content": "Voce é um especialista em marketing bancário."
      },
      {
          "role": "user", 
          "content": f"Cite o período que {user['name']} foi presidente, e o seu principal feito"
      }
    ]
  )
  return completion.choices[0].message.content.strip('\"')

# buscar info para cada usuario da lista
for user in users:
  news = generate_ai_news(user)
  user['news'].append({
    "icon": "https://digitalinnovationone.github.io/santander-dev-week-2023-api/icons/credit.svg",
    "description": news
  })
  print(news)
```
>Deodoro da Fonseca foi presidente do Brasil de 15 de novembro de 1889 a 23 de novembro de 1891. Foi o primeiro presidente do Brasil após a Proclamação da República.
>
>Floriano Peixoto foi presidente do Brasil de 23 de novembro de 1891 a 15 de novembro de 1894. Ele assumiu o cargo após a renúncia de Deodoro da Fonseca e foi o segundo presidente do período conhecido como República Velha.
>
>Prudente de Morais foi presidente do Brasil de 1894 a 1898.

## Load

Agora vamos usar a API para gravar os dados dos usuários com as novas informações.

```py
# atualiza os dados do usuario
def update_user(user):
  response = requests.put(f"{SDW23_API_URL}users/{user['id']}", json=user)
  return True if response.status_code == 200 else False

for user in users:
  success = update_user(user)
  print(f"Usuario {user['name']} atualizado? {success}")
```

## Verificação Final

Vamos dar uma conferida se o processo de ETL foi feito com sucesso: extrair info dos usuários, transformar os dados, e armazená-los.

```py
for user in users:
  print(user['name'])
  for info in user['news']:
    print('-',info['description'])
```

>Deodoro da Fonseca
>- Deodoro da Fonseca, conheça nosso Cartão de Crédito Santander! Mais facilidade e segurança em suas transações financeiras. Saiba mais!
>- Deodoro da Fonseca foi presidente do Brasil de 15 de novembro de 1889 a 23 de novembro de 1891. Foi o primeiro presidente do Brasil após a Proclamação da República.

>Floriano Peixoto
>- Floriano, o Santander tem diversas soluções financeiras especialmente para você. Venha descobrir as vantagens dos nossos produtos e conquiste seus objetivos! #SantanderParaVocê
>- Floriano Peixoto foi presidente do Brasil de 23 de novembro de 1891 a 15 de novembro de 1894. Ele assumiu o cargo após a renúncia de Deodoro da Fonseca e foi o segundo presidente do período conhecido como República Velha.

>Prudente de Morais
>- Aproveite as vantagens exclusivas do Santander e conquiste sua estabilidade financeira. Venha conhecer nossos produtos e invista no seu futuro!
>- Prudente de Morais foi presidente do Brasil de 1894 a 1898.

