# Projeto da DIO no Bootcamp do Banco PAN Java Developer com Amazon DynamoDB

### Comandos para execução

 - Criando a tabela
 
```
 aws dynamodb create-table \
--table-name Game \
--attribute-definitions \
    AttributeName=Companie,AttributeType=S \
--key-schema \
    AttributeName=Companie,KeyType=HASH \
    AttributeName=GameTitle,KeyType=RANGE \
--provisioned-throughput \
    ReadCapacityUnits=10,WriteCapacityUnit=5
```

- Inserindo um item

```
aws dynamodb put-item \
    --table-name Game \
    --item file://itemgame.json \
```

- Inserindo multiplos itens

```
aws dynamodb batch-write-item \
    --request-items file://batchgame.json
```

- Criando um index global baseado no title do game

```
aws dynamodb update-table \
    --table-name Game \
    --attribute-definitions AttributeName=GameTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"GameTitle-index\",\"KeySchema\":[{\"AttributeName\":\"GameTitle\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Criando um index global baseado na companie e no game title

```
aws dynamodb update-table \
    --table-name Game \
    --attribute-definitions\
        AttributeName=Companie,AttributeType=S \
        AttributeName=GameTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"CompanieGameTitle-index\",\"KeySchema\":[{\"AttributeName\":\"Companie\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"GameTitle\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Criando um index secundário baseado no game title e no game year

```
aws dynamodb update-table \
    --table-name Game \
    --attribute-definitions\
        AttributeName=GameTitle,AttributeType=S \
        AttributeName=GameYear,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"GameTitleYear-index\",\"KeySchema\":[{\"AttributeName\":\"SongTitle\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"SongYear\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Pesquisando item por companie

```
aws dynamodb query \
    --table-name Game \
    --key-condition-expression "Companie = :companie" \
    --expression-attribute-values  '{":companie":{"S":"Nintendo"}}'
```

- Pesquisando pelo index secundário baseado no title game

```
aws dynamodb query \
    --table-name Game \
    --index-name AlbumTitle-index \
    --key-condition-expression "GameTitle = :name" \
    --expression-attribute-values  '{":name":{"S":"The Legend Of Zelda - Breath Of The Wild"}}'
```

- Pesquisando pelo index secundario baseado na companie e no title game  

```
aws dynamodb query \
    --table-name Music \
    --index-name CompanieGameTitle-index \
    --key-condition-expression "Companie :v_companie and GameTitle = :v_title" \
    --expression-attribute-values  '{":v_conpanie":{"S":"Nintendo"},":v_title":{"S":"The Legend Of Zelda - Breath Of The Wild"} }'
```

- Pesquisando pelo index secundário baseado no game title e no game year

```
aws dynamodb query \
    --table-name Game \
    --index-name GameTitleYear-index \
    --key-condition-expression "GameTitle = :v_game and GameYear = :v_year" \
    --expression-attribute-values  '{":v_game":{"S":"Wasting LoveThe Legend Of Zelda - Breath Of The Wild"},":v_year":{"S":"2017"} }'
```








