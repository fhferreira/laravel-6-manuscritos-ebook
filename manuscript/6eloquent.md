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
select * from posts posts
```

Onde o Laravel pegará automáticamente o nome do seu model e tentará resolver ele no plural na execução da query, por exemplo model `Post` tabela `posts`.

Agora vamos conhecer o conteúdo do model `Post` disponivel pós-geração deste. Veja abaixo:

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    //
}

```

Veja nosso model acima, somente com sua definição, bem seca inclusive, já podemos realizar diversos trabalhos e operações em cima de nossa tabela `posts` associada ao model `Post`. Se, por ventura, você quiser utilizar um nome de sua escolhae não quiser que o Laravel resolva o nome da sua tabela, você pode sobscrever o atributo dentro do seu model como mostrado no conteúdo abaixo:

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    protected $table = 'nome_da_sua_tabela';
}

```

Acima ao invés do Laravel tentar encontrar a tabela no plural ele pegará o valor do atributo `$table`.

Deixarei o conteúdo acima para exemplo mas utilizarei a convenção em nossos models sem a utilização do atributo `$table`. Os poderes extras dos models são concedidos pela classe `Model` do Eloquent. Certo mas então o que é o Eloquent?

## Eloquent?

O Eloquent é o ORM padrão do Laravel, é a camada via objetos para manipulação dos dados de seu banco de dados. O ORM é a camada que traduz sua estrutura de objetos, dos models, para a camada relacional e o eloquent se utiliza do active record para prover uma interface de utilização de forma mais simples e direta.

Por exemplo, uma visualização rápida de inserção de um post utilizando esta interface do Active Record:

```
//O códiga poderia está em um método do controller

$post = new Post();
$post->title       = 'Post salvo com eloquent e active record';
$post->description = 'Descrição post';
$post->content     = 'Conteúdo do post';
$post->is_active   = true;
$post->user_id     = 1;

$post->save(); //Aqui a inserção do post com o conteúdo acima é inserida na tabela.
```

Veja como é simples, inicio uma nova instância de `Post` chamo as colunas como atributos do objeto e por fim, para salver os dados atribuidos a cada um dos atributos utilizo o método `save` do model para realizar a operação de criação desta postagem.

Aqui no livro vou abordar mais conceitos do Eloquent de forma prática e pontuando os comportamentos. Me utilizarei para salvar e atualizar os dados do conceito de **Mass Assingments** que explicarei mais a frente mas resumidamente é uma outra forma de inserção e atualização de dados disponível no Laravel por meio Eloquent.

PS.: Antes de prosseguirmos, recomendo fortemente a leitura sobre Active Record caso queira conhecer esse padrão.

## Eloquent e queries na prática

Lembra que já temos um controller para utilização e criação de um CRUD para posts e por meio deste CRUD vamos focar nos pontos mais cruciais para você conhecer o trabalho do Eloquent em nossas aplicações. Nosso controller encontra-se na pasta dos controllers, dentro da pasta `Admin` e o controller `PostsControllers`.

Primeiramente vamos definir nosso método index, na mão mesmo. Veja o conteúdo abaixo:



