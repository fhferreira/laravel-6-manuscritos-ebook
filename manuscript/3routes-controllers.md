# Rotas & Controllers

As rotas e controllers são integrantes bem importantes em nossas aplicações Laravel. Neste capítulo vamos conhecer as opções que as rotas nos proporcionam e como podemos trabalhar com o controllers conhecendo um pouco além do que vimos no capítulo passado.

Vamos as rotas então!

## Rotas

As rotas em nossa aplicação Laravel nos ajudam a termos mais previsibilidade sobre nossas urls. Como nós mapeamos nossas URLs dentro dos arquivos de rotas fica mais fácil termos controle do que será exposto e também fica mais fácil de customizarmos as rotas como queremos.

Dentro do Laravel temos os arquivos de rotas bem separados o que nos ajuda a organizarmos melhor tais rotas que dependendo da aplicação podem se tornar bem grande no quesito de definições dentro do arquivo de rotas em questão.

O Laravel possui os seguintes arquivos de rotas: `web.php`, `api.php`, `channels.php` e `console.php`.

##### web.php

O arquivo web.php conterá as rotas de sua aplicação com as interfaces para o usuário. Todas as rotas que têm esse fim deverão ser definidas neste arquivo.

##### api.php

Se você for trabalhar com APIs, expondo endpoints para que outras aplicações possam consumir seus recursos você deverá definir suas rotas com este fim no arquivo api.php.

##### channels.php

Se você for trabalhar com eventos de Broadcasting, suas rotas deverão ser definidas neste arquivo.

##### console.php

Arquivo para registro de comandos para o console e execução no artisan.

### Definição de Rotas

Como vimos no último capítulo, abordei duas formas de definição de rotas em nosso Hello World. Uma era a rota que já existia no arquivo web.php e a outra foi nossa definição de rota para nosso Hello World. Vamos da uma revisada, uma relembrada:

```
Route::get('/', function () {
    return view('welcome');
});
```

e 

```
Route::get('hello-world', 'HelloWorldController@index');
```

Acima temos duas formas de definição para rotas, no quesito do que será executado. Lembrando que o primeiro parâmetro do método get é a rota em questão e o segundo parâmetro será um callable: uma função anônima ou uma string que respeite `Controller@método` que no fim das contas também virará um callable dada a instância do controller e a chamada do método deste controller.

Na rota inicial vemos a utilização da função anônima e em nossa rota usamos a definição de chamada do controller e seu método diretamente.

Um primeiro ponto que podemos abordar sobre os métodos do `Route`, como vimos o `get` até o momento, é que teremos métodos respeitando os verbos http, como: 

- GET;
- POST;
- PUT;
- PATCH;
- DELETE;
- OPTIONS;

Podendo utilizá-los da seguinte maneira:

- Route::get($route, $callback);
- Route::post($route, $callback);
- Route::put($route, $callback);
- Route::patch($route, $callback);
- Route::delete($route, $callback);
- Route::options($route, $callback);

Podemos usar os métodos conforme os verbos http mostrados, tendo sempre o primeiro parâmetro, a rota em si, e o segundo parâmetro o callable ou executável para esta rota ao ser acessada.

### Route match e Route any

Se precisarmos usar uma rota que responda a determinados tipos de verbos http, podemos usar o método `match` do `Route`. Como abaixo:

```

Route::match(['get', 'post'], 'posts/create', function(){
	return 'Esta rota bate com o verbo GET e POST';
});

```

Caso queira que uma rota responda para todos os verbos ao mesmo tempo, você pode usar o método `any` do `Route`:

```

Route::any('posts', function(){
	return 'Esta rota bate com todos os verbos HTTP mencionados anteriormente';
});

```


### Parâmetros dinâmicos 

Continuando vamos conhecer um ponto bem importante sobre rotas que é a possibilidade de informamos parâmetros dinâmicos em nossas rotas. Parâmetro este que pode servir para identificar determinado recurso como uma postagem em um blog por exemplo. 

Veja a rota definida abaixo:

```
Route::get('/post/{slug}', function($slug) {
    return $slug;
});

```

Temos a rota `/post/` após isso definimos um parâmetro dinâmico chamado `slug` dentro de chaves como é solicitado pelo componenete de rotas. Em nossa função anônima passamos o parâmetro `$slug` que receberá o valor dinâmico e poderemos utilizar ele dentro da nossa função. Se eu estiver usando um controller e seu método, basta informarmos no método o parâmetro correspondente como informamos na função anônima e utilizarmos tranquilamente.

Com esta rota defininda em nosso arquivo `web.php` e nosso server levantado, podemos acessar em nosso browser a seguinte url: `http://127.0.0.1:8000/post/teste-parametro-dinamico`.

Resultado:

![](resources/./images/parametro-dinamico-rota-laravel.png)

Como retornamos o parâmetro teremos o valor dinâmico exibido em nosa tela como mostra a imagem acima.

### Apelido para rotas

Outro ponto bem importante em nossas rotas são seus apelidos. Mas para que servem? Até agora conhecemos o valor real ou nome real da rota mas podemos chama-las por meio de seus apelidos também, isso nos ajuda quando precisamos, em um futuro, alterar o nome real das rotas.

Quando fazemos referência aos apelidos, podemos alterar tranquilamente o nome real da rota que o peso desta modificação não será tão impactante assim no quesito negativo. E como utilizar este apelido?

Vamos pegar nossa última rota do parâmetro dinâmico:


```
Route::get('/post/{slug}', function($slug) {
    return $slug;
})
->name('post.single');

```

Perceba a adição simples que fiz após o método `get` antes de fechar com o `;`. Chamei o método `name` que me permite adicionar um apelido para a rota em questão, neste caso agora posso chamar o apelido `post_single` toda vez que eu precisar usar a rota `post/{slug}`. 

Em um link em nossa view ao invés de usarmos desta maneira:

```
<a href="/post/primeiro-post">Primeiro Post</a>
```

Vamos utilizar desta maneira:

```
<a href="{{route('post.single', ['slug' => 'primeiro-post'])}}">Primeiro Post</a>
```

Perceba acima que estamos em uma suposta view e utilizamos um método helper do Blade que é o `route` em nosso atributo href da âncora. O primeiro parâmetro do `route` é o apelido da rota e se a rota tiver parâmetros dinâmicos, que é o nosso caso, agente informa isso dentro de um array no segundo parâmetro informando o nome do parâmetro e o valor para este parâmero.

O método route irá gerar a url correta informando o parâmetro dinâmico no local correto. Com isso fica mais simples se precisarmos futuramente modificar o nome real da rota por esta questão, de chamarmos a rota pelo apelido ao invés de seu nome real.

### Grupo de Rotas & Prefixo

Podemos definir determinadas configurações para um grupo especifico de rotas, para conhecermos o poder do group decidir mostrar ele aqui com a definição de um prefixo, método existente no Route também.

Vamos ao código abaixo:

```

Route::prefix('posts')->group(function(){

    Route::get('/', 'PostController@index')->name('posts.index');
    
    Route::get('/create', 'PostController@create')->name('posts.create');
    
    Route::post('/save', 'PostController@save')->name('posts.save');

});

```

Perceba no set de rotas acima que utilizei incialmente o método `prefix` e me utilizei do método `group` para definir esse prefixo para um grupo de rotas especifico. Esse grupo de rotas é adicionado dentro do método `group` por meio de uma função anônima.

Agora, as rotas dentro do group serão prefixadas com o `posts`, ficando desta maneira:

- **/posts/**;
- **/posts/create**;
- **/posts/save**.

Duas rotas acessivéis via GET e uma acessível via POST. 

O grupo nos permite esse tipo de configuração, quando precisamos organizar melhor determinadas configurações que se repetirão para mais de um set de rota. Isso melhora até a escrita dos nossos arquivos de rotas e definições.

### Grupo de Rotas & Apelidos

Vamos melhorar ainda mais nosso set de rotas do momento passado. Podemos, também, definir um apelido base para um grupo de rotas então vamos melhorar nosso grupo anterior.

Veja como ficou:

```

Route::prefix('posts')->name('posts.')->group(function(){

    Route::get('/', 'PostController@index')->name('index');
    
    Route::get('/create', 'PostController@create')->name('create');
    
    Route::post('/save', 'PostController@save')->name('save');

});

```

Perceba que agora isolei a parte `posts.` referente ao apelido das rotas após a definição do prefixo. Agora as rotas deste grupo além de receber um prefixo irá receber um apelido base que será concatenado com os apelidos de cada rota do grupo.

Os apelidos ficarão desta forma:

- **posts.index**;
- **posts.create**;
- **posts.save**.

Esses serão os apelidos da rota gerados, o mesmo que seria anteriormente mas agora com o detalhe de termos organizado e isolado o que era repetido, ou seja, o  `posts.`.

### Grupo de Rotas & Namespaces

O namespace base do Laravel é `App`, e o namespace base dos controllers é `App\Http\Controllers`. Esse namespace é adicionado automaticamente pelo Laravel quando chamamos uma rota referenciando um método de um Controller mas podem existir casos em que você queira criar mais um nível de namespace para um determinado grupo de controllers dentro de sua aplicação.

Por exemplo, podemos ter controllers especificos de um painel administrativo. Suponhamos que temos dentro da pasta controllers uma pasta `Admin` (que também representará mais um nivel de namespace) e dentro desta pasta `Admin` temos um `PostController`, um `UserController` ambos referentes ao gerenciamento de posts e usuários de nosso painel administrativo.

Como podemos referir o namespace `Admin` durante o set de rotas?

Vejamos as duas rotas abaixo:

```
Route::get('admin/users/', 'Admin\\UserController@index')->name('users.index');


Route::get('admin/posts/', 'Admin\\PostController@index')->name('posts.index');

```

Perceba como eu informei o namespace extra, `Admin`, nas duas rotas acima que supostamente levam para a listagem de usuários e posts dentro do nosso admin.

Perceba que temos repetições nas duas rotas, vamos nos focar em organizar o namespace inicialmente.

Organizando o namespace por grupos podemos chegar no resultado abaixo:

```
Route::namespace('Admin')->group(function(){

    Route::get('admin/users/', 'UserController@index')->name('users.index');


    Route::get('admin/posts/', 'PostController@index')->name('posts.index');

});

```

Agora isolamos o namespace através do método do `Route` chamado `namespace` e todas as rotas deste grupo irão receber este namespace. 

Podemos ainda organizar os prefixos perceba a repetição do `admin` nos nomes reais das rotas. Organizando fica assim:

```
Route::namespace('Admin')->prefix('admin')->group(function(){

    Route::get('/users/', 'UserController@index')->name('users.index');


    Route::get('/posts/', 'PostController@index')->name('posts.index');

});

```
Agora as coisas começam a ficar mais organizadas. Podemos organizar e agrupar nossas rotas conforme nossa necessidade, então, sempre que estiver escrevendo suas rotas analise o que pode ser organizado usando o método `group` e as opções disponiveis para as configurações em questão.

## Controllers


