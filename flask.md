## Exercícios

1. [Criando o projeto](#there_you_go2)

1. [Disponibilizando o formulário de contas](#form2)



## <a name="there_you_go2"></a>1. Criando o projeto

1. Abra o Pycharm, vá em `File -> New Project` e crie um novo projeto chamado `banco-web`.

1. Na raiz do projeto, crie um pacote chamado `my_app`. Com o cursor na raiz do projeto, vá em `New -> Python Package` e nomeie com `my_app`. 

1. Agora, vamos incluir a dependência do `flask` no projeto. Vá em `File -> Settings -> Project:<nome-projeto> -> Project Interpreter`. No meu direito, procure por um símbolo de adição (`+`) e clique nele. No campo de busca procure por `flask` e clique em `Install Package`. Aguarde a conclusão da instalação, feche a janela e clique em `OK`.

1. No arquivo `__init__.py`, vamos criar uma instância da classe `Flask` e chamar o método `run()`:
    ```python
    # my_app/__init__.py
    
    app = Flask(__name__)
    
    if __name__ == '__main__':   
        app.run()
    ```
    
1. Rode o arquivo `__init__.py` e veja o console. Uma mensagem `Running on http://127.0.0.1:5000/` deve aparecer. Abra o navegador e teste acessar o endereço `http://localhost:5000`.

1. Uma página com a mensagem abaixo deve aparecer:
    ```
    Not Found

    The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.
    ```
    Isso quer dizer que o flask está funcionando e não encontrou ninguém que responda a essa requisição.
    
1. No arquivo `__init__.py`, vamos criar uma função capaz de responder a requisição. Crie o método `ola_mundo()`:
     ```python
    # my_app/__init__.py
    
    app = Flask(__name__)
    
    def ola_mundo():
        pass
    
    if __name__ == '__main__':   
        app.run()
    ```
    
1. Faça com que o método `ola_mundo()` retorne uma mensagem:
   ```python
    # my_app/__init__.py
    
    app = Flask(__name__)
    
    def ola_mundo():
        return `olá, mundo com Flask!`
    
    if __name__ == '__main__':   
        app.run()
    ```
    
1. Use o decorator `@app_route` para definir uma rota para execução do método devolver a resposta. Vamos configurar a rota para `/`:
    ```python
    # my_app/__init__.py
    
    app = Flask(__name__)
    
    @app.route("/")
    def ola_mundo():
        return `olá, mundo com Flask!`
    
    if __name__ == '__main__':   
        app.run()
    ```
    
    
1. Rode novamente o arquivo `__init__.py` e acesse o endereço http://localhost:5000/ e veja o que acontece.



## <a name="form2"></a>2. Disponibilizando o formulário

Take me where
I guess markdown behaves quite similar to html. I will attach an example below;

[Take me there](#there_you_go)
[Take me where](#here)

I defined two links with anchor names

below is the first anchor

<a name="there_you_go"></a>Take me there

here is the second anchor

<a name="here"></a>Take me where

here is it in practice

Take me there will take you down
Take me where will take you up
/
Take me there
/
blank space for test
/
/
/
/
/
/
blank space end
/
/

So it does work. Hope this helps