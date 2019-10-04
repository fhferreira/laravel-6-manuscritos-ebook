# Relacionamentos com Eloquent

Olá, tudo bem? Espero que sim!

Vamos começar agora um trecho que é necessário bastante atenção e cuidado, vamos falar sobre relacionamentos de uma base relacional (nosso banco de dados) e a representação destes relacionamentos do ponto de vista de Objetos, representadados por nossos Models junto com o Eloquent.

Como venho fazendo vamos mostrar os relacionamentos e suas nuances aplicados em nosso blog onde teremos as seguintes representações:

- Relacionamento 1:N(Um para Muitos): Autor x Postagens;
- Relacionamento N:N(Muitos para Muitos: Postagens & Categorias;
- Relacionamento 1:1 (Um para 1): Autor e Perfil.

Já temos definido na base o primeiro relacionamento da listagem acima e vamos começar por ela, para cada relacionamento restante(os dois última da lista acima) vou criar os insumos(Models, Controllers e etc) no momento que forem necessários, até para darmos uma relembrada.

Então vamos lá, vamos a obra! Quer dizer mão aos teclados!

## Relacionamento 1:N (Um para Muitos e Inverso)

Primeiramente vamos definir nossa relação entre Autor(User)  e suas Postagens(Post). Para isto precisamos definir métodos dentro de cada model que representem esta ligação.

Primeiramente vamos criar o método do ponto de vista de Post em relação ao nosso User. Veja o método abaixo adicionado ao model Post:

```
public function user()
{
    return $this->belongsTo(User::class);
}
```

Definimos o método acima para representar a ligação entre nossos models, neste caso quando precisarmos acessar dados desta relação, criar um dados por meio desta relação vamos chamar o método `user` do ponto de vista de Post.

O método `belongsTo` vêm do Eloquent e indica que o model Post pertence a(belongsTo) User, por isso passei o nome qualificado do model User no parâmetro do método `belongsTo`.

Outro ponto a ressaltar aqui é que, o Laravel vai tentar resolver o nome da referência na tabela posts por meio do nome do método, se coloquei `user` ele vai buscar dentro da tabela post, no banco de dados, pela referência `user_id`.

Se por ventura você usou um nome de coluna, que representa a referência na sua tabela, por exemplo, não usou o `user_id` mas sim `author_id`. Neste caso você precisa informar para o Eloquent o nome da coluna, veja um trecho abaixo:

```
return $this->belongsTo(User::class,  'author_id');
```

Desta forma quando o Eloquent for acessar suas tabelas e gerar as queries sql dos acessos irá buscar pela coluna `author_id`. Lembrando isso vale para você ter usado o nome da coluna que recebe a chave estrangeira que não seja `user_id`.

Agora como digo que o model User têm muitas postagens, vamos lá no model `User` e definir o método abaixo:

```
public function posts()
{
    return $this->hasMany(Post::class);
}
```

Se indiquei que o Post pertence a User por meio do método belongsTo, dentro de User eu indico que ele têm muitos(has Many) por meio do método `hasMany`. Agora toda vez que eu precisar acessar as postagens de um usuário eu irei acessar pormeio do método `posts`.

As definições do ponto de vista do Model são estas para entendermos melhor vamos realizar algumas queries para testarmos esta relação.

Vamos exibir lá na listagem de posts o autor da postagem. Para isto adicione mais uma coluna no `thead`, depois da coluna do id(#):

```
<th>Autor</th>
```

No `tbody` vamos adicionar o conteúdo para esta coluna, veja abaixo:

```
<td>{{$post->user->name}}</td>
```

Antes de vermos o resultado no browser, vamos ver na íntegra a view de posts agora, com esta alteração:

```
@extends('layouts.app')

@section('content')
    <div class="row">
        <div class="col-sm-12">
            <a href="{{route('posts.create')}}" class="btn btn-success float-right">Criar Postagem</a>
            <h2>Postagens Blog</h2>
            <div class="clearfix"></div>
        </div>
    </div>
    <table class="table table-striped">
        <thead>
            <tr>
                <th>#</th>
                <th>Autor</th>
                <th>Titulo</th>
                <th>Status</th>
                <th>Criado em</th>
                <th>Ações</th>
            </tr>
        </thead>
        <tbody>
        @forelse($posts as $post)
            <tr>
                <td>{{$post->id}}</td>
                <td>{{$post->user->name}}</td>
                <td>{{$post->title}}</td>
                <td>
                    @if($post->is_active)
                        <span class="badge badge-success">Ativo</span>
                    @else
                        <span class="badge badge-danger">Inativo</span>
                    @endif
                </td>
                <td>{{date('d/m/Y H:i:s', strtotime($post->created_at))}}</td>
                <td>
                    <div class="btn-group">
                        <a href="{{route('posts.show', ['post' => $post->id])}}" class="btn btn-sm btn-primary">
                            Editar
                        </a>
                        <form action="{{route('posts.destroy', ['post' => $post->id])}}" method="post">
                            @csrf
                            @method('DELETE')
                            <button type="submit" class="btn btn-sm btn-danger">Remover</button>
                        </form>
                    </div>
                </td>
            </tr>
        @empty
            <tr>
                <td colspan="4">Nada encontrado!</td>
            </tr>
        @endforelse
        </tbody>
    </table>

    {{$posts->links()}}
@endsection
```

Veja o resultado:

![](resources/./images/autor-post-index.png)

Por meio da definição do método `user` em `Post` e `posts` em `User` criamos as relações entre os objetos dos dois pontos de vistas. Acessei o auto da postagem do ponto de vista de Post, quando exibimos na tabela:

```
{{$post->user->name}}
```

Pegando a ligação que está em Post, `user`, e pegando o atributo name do usuário. Agora você pode está se perguntando, cara tu acessou `user` como se fosse um atributo mas tu definiu um método lá no model.

Vou explicar isso agora, vamos lá!

Aqui tecnicamente é bem simples, quando você acessar a ligação como se fosse um atributo uma coleção é retornada ou 