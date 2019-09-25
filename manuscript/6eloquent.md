# Eloquent, trabalhando com Models

Continuando nosso trabalho com a camada de dados e persitência, vamos subir o nível conhecendo a camada dos models e como podemos trabalhar buscas, inserções, atualizações remoções e até mesmo os relacionamentos da base relacional com o nível do objetos que são nossos models.

Vamos começar primeiro pelas queries e ir crescendo nosso conhecimento no decorrer deste capítulo. Para isto vamos usar nosso controller `PostsController` que se encontra dentro da pasta `Admin` em controllers.

## Os Models!

No Laravel os models são a representação do ponto de vista de objetos das tabelas do nosso banco de dados. Representação essa pensando em uma entidade que represente todos os dados da tabela em questão.

Por exemplo, por convenção do framework, se eu tenho uma tabela chamada `posts` a representação, em model desta tabela, será uma classe chamada de `Post`. Se eu tenho uma tabela `users` sua representação via model será uma classe chamada de `User`.

Quando nós temos entidades/models no singular o Laravel automáticamente tentará, por convenção, resolver sua tabela no plural por ter o pensamento, na base, de uma coleção de dados.

Por exemplo, como posso pegar todos as postagens via Model? Para isto temos um método chamada de `all` que faz este trabalho para nós.

Por exemplo:

```
return \App\Post::all();
```

O resultado do comando acima será uma sql como está:

```
select * from posts
```

Onde o Laravel pegará automáticamente o nome do seu model e tentará resolver ele no plural na execução da query.



## Primeiras queries

