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
$post->slug        = 'post-salvo-com-eloquent-e-active-record';
$post->is_active   = true;
$post->user_id     = 1;

$post->save(); //Aqui a inserção do post com o conteúdo acima é inserida na tabela.
```

Veja como é simples, inicio uma nova instância de `Post` chamo as colunas como atributos do objeto e por fim, para salver os dados atribuidos a cada um dos atributos utilizo o método `save` do model para realizar a operação de criação desta postagem.

Aqui no livro vou abordar mais conceitos do Eloquent de forma prática e pontuando os comportamentos. Me utilizarei, para salvar e atualizar os dados, do conceito de **Mass Assingment** que explicarei mais a frente mas resumidamente é uma outra forma de inserção e atualização de dados disponível no Laravel por meio Eloquent.

PS.: Antes de prosseguirmos, recomendo fortemente a leitura sobre Active Record caso queira conhecer esse padrão.

## Eloquent na prática

Lembra que já temos um controller para utilização e criação de um CRUD para posts e por meio deste CRUD vamos focar nos pontos mais cruciais para você conhecer o trabalho do Eloquent em nossas aplicações. Nosso controller encontra-se na pasta dos controllers, dentro da pasta `Admin` e o controller `PostsControllers`.

Primeiramente vamos definir nosso método index. Trabalhando do ponto de vista dos models eu consigo realizar operações de busca dos dados em minhas tabelas, por exemplo posso buscar todos os registros do banco de dados por meio do método `all`. Como mostro no trecho abaixo:

```
return \App\Post::all();
```

Ou posso retornar os dados paginados, para exibição em uma tabela na view. Então vamos começar a implementar o método index no controller `PostController` que está na pasta `Admin`. Veja o conteúdo dele abaixo:

```
public function index()
{
	$posts = Post::paginate(15);

	dd($posts); //no próximo capítulo vamos mandar para view...
}
```
Veja o código acima, por enquanto ainda não vamos utilizar a view, retornaremos a ela e os pontos do Blade no próximo capítulo. Voltando ao código acima, perceba que chamei o `Post::paginate` informando que quero 15 postagens, neste caso, os dados vão vir do banco paginados e teremos 15 postagens por cada tela da paginação.

Não esqueça de importar a classe post em seu controller:

```
use App\Post;
```

Vamos lá em nosso arquivo de rotas e realizar uma pequena alteração no que já havíamos feito. Para o conjunto de rotas de post, o que está assim:

```
Route::prefix('admin')->namespace('Admin')->group(function(){

	Route::prefix('posts')->name('posts.')->group(function(){
		Route::get('/create', 'PostController@create')->name('create');
		Route::post('/store', 'PostController@store')->name('store');
	});

});
```

Vamos atualizar para a chamada do controller como recurso. Como vemos abaixo:

```
Route::prefix('admin')->namespace('Admin')->group(function(){

	Route::resource('posts', 'PostController');

});
```
Perceba que agora simplificamos mais ainda nossas rotas dentro do grupo para o admin. Quando trabalhamos com request e os métodos `create` e `store` que já existem no controller tomei o cuidado de já deixá-los dentro do pensamos para o roteamento com o método `resource` para os controllers como recurso.

Se você for ao seu browser agora e acessar `http://127.0.0.1:8000/admin/posts` você verá o resultado abaixo:

![](resources/./images/paginate.png)

Veja que expandi o atributo `items` e seu nivel a dentro, onde temos a coleção de dados retornada. Veja que tivemos 15 itens retornados, em nosso caso 15 posts. Para navegar entre as páginas é bem simples, basta nós informarmos na url o parâmetro `page=2` (como querystring) por exemplo.

Quando formos trabalhar com as views e o blade veremos que temos uma forma simples de criar a páginação no frontend, simplificando esta navegação.

### Buscando apenas um post

Para buscarmos dados podemos trabalhar com diversos métodos. Por exemplo, se você quiser buscar uma postagem pelo id dela você pode usar o método `find`, veja:

```
Post::find(1);
```

Ou ainda, buscando pelo id, você pode usar o método `findOrFail` que caso não encontre o dado em questão irá lançar uma Exception. Podemos usar desta maneira:

```
Post::findOrFail(1);
```

Só pra frizar, o parâmetro informado nos métodos acima são o id da postagem desejada. Vamos criar mais método em nosso controller `PostController` para recuperação de uma post em questão, para nossa futura tela de edição. Veja o conteúdo do método `show` e já adicione ele em seu controler:

```
public function show($id)
{
	$post = Post::findOrFail($id);

	dd($post); //em breve mandaremos pra view
}
```

Usarei o `findOrFail`, para mais a frente tratarmos melhor estas exceptions com blocos `try` e `catch`.  Se você acessar a postagem de id 1 em seu browser pelo link `http://127.0.0.1:8000/admin/posts/1` você terá o resultado do dump, abaixo na imagem eu destaco só o atributo `original` que traz o dados escolhido, a postagem única em questão:

![](resources/./images/find.png)

Nosso controller até o momento está desta maneira, com os dois métodos criados no capítulo sobre requests e mais o `index` e o `show`. Veja na íntegra:

```
<?php

namespace App\Http\Controllers\Admin;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use App\Post;

class PostController extends Controller
{
	public function index()
	{
		$posts = Post::paginate(15);

		dd($posts); //no próximo capítulo vamos mandar para view...
	}

	public function show($id)
	{
		$post = Post::findOrFail($id);

		dd($post);
	}

    public function create()
    {
		return view('posts.create');
    }

    public function store(Request $request)
    {
		if($request->hasAny(['title', 'content', 'slug'])) {
			var_dump($request->except(['title']));
		}

		return back()->withInput();
    }
}

```

Agora vamos ao método `store` pois nele vamos trabalhar a inserção de dados propriamente dita!

## Inserindo dados com Eloquent

Como mencionei anteriormente, poderíamos utilizar o método via Active Record para salvar os dados, por meio da referência dos atributos dinâmicos, baseados nas colunar do banco para aquela tabela (posts)  e por meio do método `save` criar um dado ou atualizar um caso quisessemos.

Como estamos tratando aqui de criação, vou mostrar a título de conhecimento o salvar dos dados usando Active Record, veja como ficaria, vamos adicionar dentro do método store o código abaixo comente o remova o conteúdo anterior já existente neste método:

```
 public function store(Request $request)
 {
    $data = $request->all();

    $post = new Post();
    
    $post->title       = $data['title'];
    $post->description = $data['description'];
    $post->content     = $data['content'];
    $post->slug        = $data['slug'];
    $post->is_active   = true;
    $post->user_id     = 1;

    dd($post->save()); //veja o resultado no browser
}
```
Veja que agora pego os dados de campo da request, vindas do formulário e repasso para cada atributo para na chamada do método `save` nós criarmos este registro na base.

Para testarmos vamos ao nosso formulário no link `http://127.0.0.1/admin/posts/create` e enviar uma informação de lá. Veja o resultado na imagem abaixo:

![](resources/./images/inserindo-post-ar.png)

Perceba que o resultado do método `save` foi o valor boleano true, confirmando assim a criação do registro em nossa base. Se você quiser atualizar um registro usando Active Record, basta, ao invés de instanciar um model post, passar o resultado de um find por exemplo:

```
$post = Post::find(1);
    
$post->title       = $data['title'];
$post->description = $data['description'];
$post->content     = $data['content'];
$post->slug        = $data['slug'];
$post->is_active   = true;
$post->user_id     = 1;

dd($post->save());
```

Como temos a referência em post agora de um dado vindo da base, ao chamarmos o método `save` o Eloquent irá atualizar este registro ao invés de criar um novo. 

Este trecho foi um rápido desmontrativo do active record no Eloquent, quero te mostrar uma técnica mais direta e que é mais utilizada hoje em dia dentro do Laravel, via Eloquent. Esta técnica é o que chamaos de `Mass Assignment` ou Atribuição em Massa.

Vamos conhecer esta técnica.

## Mass Assignment


