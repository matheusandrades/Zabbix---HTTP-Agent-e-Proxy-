# Zabbix---HTTP-Agent-e-Proxy-

### Fala pessoal, tudo beleza? Hoje vamos falar de HTTP Agent e Proxy no zabbix.
### Em grande empresas por questões de seguranças para evitar ataques DDOS entre outros motivos os servidores do Zabbix acabam sendo restritos a conexao com a internet, por conter ### varias informações de toda topologia de uma rede e tambem por ser um ferramenta essencial para ter um monitoramento de toda a rede.
### Quando você se depara com uma situação semelhante a essa e muitas das vezes precisa trabalhar para obter alguma informação atravez de API consultando na internet, como faria ?
### A função Type: HTTP Agent e HTTP Proxye, veio para resolver esse tipo de "problema". 
### Na verdade o type: HTTP Agent serve para você monitorar diversos cenarios via HTTP, mas vou citar um exemplo bem simples e auto-explicativa abaixo onde iremos fazer uma  consulta via API no Bgp View, trazendo informação do ASN que fecham peer com um roteador de borda (BGP).
### O agente HTTP oferece suporte a HTTP e HTTPS.

#### Para realizar uma consulta via api HTTP ou HTTPS no exemplo que vamos usar irmeos consultar atraves do link https://api.bgpview.io/asn/AS6507, a baixo teremos o resultado dessa requisição em formato json :

{
   "status":"ok",
   "status_message":"Query was successful",
   "data":{
      "asn":6507,
      "name":"RIOT-NA1",
      "description_short":"Riot Games, Inc",
      "description_full":[
         "Riot Games, Inc"
      ],
      "country_code":"US",
      "website":"http:\/\/www.riotgames.com",
      "email_contacts":[
         "noc@riotgames.com",
         "abuse-report@riotgames.com",
         "peering@riotgames.com"
      ],
      "abuse_contacts":[
         "abuse-report@riotgames.com"
      ],
      "looking_glass":null,
      "traffic_estimation":"500-1000Gbps",
      "traffic_ratio":"Mostly Outbound",
      "owner_address":[
         "12333 West Olympic Blvd",
         "Los Angeles",
         "CA",
         "90025",
         "US"
      ],
      "rir_allocation":{
         "rir_name":"ARIN",
         "country_code":"US",
         "date_allocated":"2012-11-27 00:00:00",
         "allocation_status":"assigned"
      },
      "iana_assignment":{
         "assignment_status":"assigned",
         "description":"Assigned by ARIN",
         "whois_server":"whois.arin.net",
         "date_assigned":null
      },
      "date_updated":"2021-05-03 08:15:23"
   },
   "@meta":{
      "time_zone":"UTC",
      "api_version":1,
      "execution_time":"24.85 ms"
   }
}
....


### Agora que validamos o resultado que estamos recebendo vamos criar um item no zabbix, para isso iremos seguir os seguintes passos:


#### > Vá para: Configuração → Hosts
#### > Clique em Discovery Rules
#### > Clique em Create discovery rule
#### > Insira os parâmetros no formulario

![Discovery rule](https://github.com/matheusandrades/Zabbix--HTTP-Agent-e-Proxy/main/discovery.jpg)

### Na imagem acima adicionamos uma descoberta via snmp dos numeros dos ASN, onde o resultado sera armazenado na vareavel {#ASNUM}.

### Feito isso vamos seguir os seguintes passos;

#### > Clique em Item prototype 
#### > Clique em Creat Item prototype
#### > Insira os parâmetros do item no formulario

![Item](https://github.com/matheusandrades/Zabbix--HTTP-Agent-e-Proxy/main/item.jpg)

##### No campo URL, vamos inserir a API que iremos realizar a consulta em seguida da vamos adicionar ao final da url a vareavel {#ASNUM} que sera substituidas por todos ASN que existem no roteador de borda(BGP).
##### Em seguida voce ira preencher o endereço do HTTP proxy que tem a conexão com a internet que recebera a requisição do zabbix server.
##### Feito isso precisamos alterar o Type Information para Text pois os valores que estamos recebendo sao textos em JSON.

##### Agora que temos nosso formulario preenchido vamos criar um Preprocessing JSONPath, onde iremos passar o parametro para coletar somente a tag "name" que se encontra dnetro do "data".

![JSONPath](https://github.com/matheusandrades/Zabbix--HTTP-Agent-e-Proxy/main/jsonpath.jpg)

##### Para isso vamos adicionar $.data.["name"]   .. (Para saber mais sobre JSONPath acesse o link da documentação oficial do zabbix https://www.zabbix.com/documentation/4.0/manual/appendix/items/jsonpath ).
##### Agora ja podemos salvar nosso item prototype e realizar nossas coletas, depois disso iremos em Monitoring no canto esquerdo do navegador, e iremos conferir o resultado no Latest data...

![JSONPath](https://github.com/matheusandrades/Zabbix--HTTP-Agent-e-Proxy/main/resultado.jpg)



#### Pronto temos nosso resultado da consulta via api HTTP.

##### Espero que tenham gostado do conteudo, qualquer duvida fique a vontade para me mandar um email admin@andradesolutions.com.br










