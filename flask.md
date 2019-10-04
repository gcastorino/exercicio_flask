## <a name="exercicios"></a>Exercícios

### introdução ao Flask

1. [Criando o projeto](#projeto)

1. [Disponibilizando o formulário de contas](#form2)

1. [Organizando o projeto](#organizando)

1. [Abrindo uma conexão com banco de dados](#connection)

1. [Disponibilizando a lista de contas](#lista)

### flask-api

1. [Configurando a API e refatorndo o projeto](#init_api)

1. [Iniciando a API com flask-restplus](#restplus)

1. [Mostrando a lista do banco de dados](#sqlalchemy)
## <a name="projeto"></a>1. Criando o projeto

1. Abra o Pycharm, vá em `File -> New Project` e crie um novo projeto chamado `banco-web`.

1. Na raiz do projeto, crie um pacote chamado `my_app`. Com o cursor na raiz do projeto, vá em `New -> Python Package` e nomeie com `my_app`. 

1. Agora, vamos incluir a dependência do `flask` no projeto. Vá em `File -> Settings -> Project:<nome-projeto> -> Project Interpreter`. No meu direito, procure por um símbolo de adição (`+`) e clique nele. No campo de busca procure por `flask` e clique em `Install Package`. Aguarde a conclusão da instalação, feche a janela e clique em `OK`.

1. No arquivo `__init__.py`, vamos criar uma instância da classe `Flask` e chamar o método `run()`:
    ```python
    # my_app/__init__.py
    from flask import Flask
    
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
    from flask import Flask
    
    app = Flask(__name__)
    
    def ola_mundo():
        pass
    
    if __name__ == '__main__':   
        app.run()
    ```
    
1. Faça com que o método `ola_mundo()` retorne uma mensagem:
   ```python
    # my_app/__init__.py
    from flask import Flask
    
    app = Flask(__name__)
    
    def ola_mundo():
        return `olá, mundo com Flask!`
    
    if __name__ == '__main__':   
        app.run()
    ```
    
1. Use o decorator `@app_route` para definir uma rota para execução do método devolver a resposta. Vamos configurar a rota para `/`:
    ```python
    # my_app/__init__.py
    from flask import Flask
    
    app = Flask(__name__)
    
    @app.route("/")
    def ola_mundo():
        return 'olá, mundo com Flask!'
    
    if __name__ == '__main__':   
        app.run()
    ```
    
    
1. Rode novamente o arquivo `__init__.py` e acesse o endereço `http://localhost:5000/` e veja o que acontece.

voltar ao [topo](#exercicios)

## <a name="form2"></a>2. Disponibilizando o formulário de contas

1. Na pasta `my_app` crie um diretório chamado `templates`.

1. Entre no link do arquivos do curso(https://drive.google.com/drive/folders/1YtwKBjOHSKVidVKUTDdErfnYRtL9gjtf?usp=sharing) e faça o download da pasta `web` e extraia seu conteúdo para o `Desktop`.

1. Copie o conteúdo pasta `html` da pasta `web` para a pasta `template` do projeto:
    ```
    my_app/
        templates/
            form.html
            contas.html
    ```
1. Dentro da pasta `my_app` crie um diretório chamado `static`.

1. Copie os arquivos da pasta `css` da pasta `web` para a pasta `static`do projeto:
    ```
    my_app/
        templates/
            form.html
            contas.html
        static/
            bootstrap.min.css  
    ```

1. Agora vamos escrever uma rota para disponibilizar o formulário. No arquivo `__init__.py` crie o método `formulario()` decorato com `app.route` e defina a rota para `/form`:
    ```python
    # my_app/__init__.py
    from flask import Flask
    
    app = Flask(__name__)
    
    @app.route("/")
    def ola_mundo():
        return 'olá, mundo com Flask!'
    
    @app.route("/form")
    def formulario():
        pass
    
    if __name__ == '__main__':   
        app.run()
    ```

1. Para retornar o formulário, precisamos importar a função `render_template()` do módulo `flask`, responsável por renderizar a página do formulário como resposta:
    ```python
    # my_app/__init__.py
    from flask import Flask, render_template
    
    app = Flask(__name__)
    
    @app.route("/")
    def ola_mundo():
        return 'olá, mundo com Flask!'
    
    @app.route("/form")
    def formulario():
        return render_template('form.html')
    
    if __name__ == '__main__':   
        app.run()
    ```

1. Rode novamente o arquivo `__init__.py` e acesse o endereço `http://localhost:5000/form` pelo navegador. O formulário de contas deve aparecer como resposta.

voltar ao [topo](#exercicios)

## <a name="organizando"></a> 3. Organizando o projeto

1. A aplicação pode vir a crescer e ter muitas rotas definidas. Não é uma boa prática deixar todas as rotas em um único arquivo. Vamos separar disponibilizadades. Na pasta `my_app`, crie um arquivo chamado `views.py`.

1. Apague os método `ola_mundo()` e copie o método `formulario()` para o arquivo `views.py`:
    ```python
    # my_app/__init__.py
    from flask import Flask, render_template
    
    app = Flask(__name__)
    
    if __name__ == '__main__':   
        app.run()
    ```
    
    ```python
    # my_app/views.py
    from flask import render_template
    
    @app.route("/form")
    def formulario():
        return render_template('form.html')
    ```
    
1. Vamos modularizar o projeto com _blueprints_. Dentro do arquivo `views.py`, importe a classe `Blueprint` do módulo `flask`. Crie uma instância de `Bluenprint` chamada `contasbp` definida no contexto `"/contas":
    ```python
    # my_app/views.py
    from flask import render_template, Blueprint
    
    contasbp = Blueprint('contas', __name__, url_prefix='/contas')
    
    @app.route("/form")
    def formulario():
        return render_template('form.html')
    ```


1. No decorador do método, troque `app` pela _blueprint_ criada:
    ```python
    # my_app/views.py
    from flask import render_template, Blueprint
    
    contasbp = Blueprint('contas', __name__, url_prefix='/contas')
    
    @contasbp.route("/form")
    def formulario():
        return render_template('form.html')
    ```
    
1. Agora precisamos registrar o contexto de nossa _blueprint_ na aplicação `flask`. No arquivo `__init__.py`, chame o método `register_blueprint()` do objeto `app` passando nossa `contasbp`. Não esqueça de importar `contasbp`do módulo `views`:
    ```python
    # my_app/__init__.py
    from flask import Flask, render_template
    from my_app.views import contasbp
    
    app = Flask(__name__)
    app.register_blueprint(contasbp)
    
    if __name__ == '__main__':   
        app.run()
    ```
    
1. Rode novamente a aplicação e acesse o endereço `http://localhost:5000/contas/form` e veja que o formulário é disponibilizado como antes. Agora, usaremos o arquivo `views`para as rotas das contas.



## <a name="organizando"></a>4. Abrindo uma conexão com banco de dados

1. Precisamos instalar a dependência de um DB API para conexão com o bando de dados MySQL. Vamos utilizar o `mysql-connector-python` como driver de conexão. Vá em `Flie -> Settings -> Project:<nome-projeto> -> Project Interpreter`. No meu direito, procure por um símbolo de adição (`+`) e clique nele. No campo de busca procure por `mysql-connector-python` e clique em `Install Package`. Aguarde a conclusão da instalação, feche a janela e clique em `OK`

1. Na pasta `my_app`, crie o arquivo `db.py`.

1. Importe o módulo `mysql.connector` e crie uma função chamada `get_connection()` que deve retornar uma conexão com banco de dados aberta através do método `connect()`:
    ```python
    my_app/db.py
    import mysql.connector

    def get_connection():
        return mysql.connector.connect(
            host='localhost',
            user='root',
            password='',
            database='banco')

    ```
    
1. Para testas se o código funciona, vamos testar o método `get_connection()`: 
    ```python
    my_app/db.py
    import mysql.connector

    def get_connection():
        return mysql.connector.connect(
            host='localhost',
            user='root',
            password='',
            database='banco')
            
    if __name__ == '__main__':
        connection = get_connection()
        print(connection)

        print('conexão aberta')

        connection.close()  # fecha a conexão
    ```

1. Ao rodar o arquivo `db.py` um erro deve ocorrer:
    ```python
    mysql.connector.errors.ProgrammingError: 1049 (42000): Unknown database 'banco'
    ```

1. O erro dz que a base de dados `banco` é desconhecida. Precisamos criar a base de dados no banco. Abra o terminal e acesse o console do MySQL pelo comando abaixo:
    ```bash
    mysql -u root
    ```
    
1. Com o console do MySQl aberto, digite o comando para a criação de uma base de dados chamada `banco`:
    ```mysql
    create database banco;
    ```
    
1. Rode novamente o arquivo `db.py` e a conexão deve ser aberta.

voltar ao [topo](#exercicios)

## <a name="connection"></a>4. Inserindo uma conta no banco

1. Na pasta `my_app`, crie um arquivo chamado `models.py` e copie o conteúdo do arquivo `contas.py` da pasta `web` extraída para o `Desktop` em exercício anterior.
    ```python
    #my_app/models.py
    
    class Conta:

        def __init__(self, numero, titular, saldo, limite=1000.0, id=None):
            self._id = id
            self._numero = numero
            self._titular = titular
            self._saldo = saldo
            self._limite = limite

        @property
        def id(self):
            return self._id

        @property
        def numero(self):
            return self._numero

        @numero.setter
        def numero(self, numero):
            self._numero = numero

        @property
        def titular(self):
            return self._titular.capitalize()

        @titular.setter
        def titular(self, titular):
            self._titular = titular

        @property
        def saldo(self):
            return self._saldo

        @property
        def limite(self):
            return self._limite

        @limite.setter
        def limite(self, limite):
            self._limite = limite

        def deposita(self, valor):
            self._saldo -= valor

        def saca(self, valor):
            self._saldo -= valor

        def transfere_para(self, destino, valor):
            self.saca(valor)
            destino.deposita(valor)

        def __str__(self):
            return 'Conta: [id: {}, numero: {}, titular: {}, saldo: {}, limite: {}]'.format(self._id, self._numero, self._titular, self._saldo, self._limite)
    ```    

1. No arquivo `views.py` vamos criar o método para criação de uma conta com a rota da ação do formulário `/cria_conta`:

    ```python
    #my_app.py
    
    # restante do código omitido
    
    @contasbp.route('/cria_conta', methods=['POST'])
    def cria():
        pass
    ```
    
1. Primeiro precisamos pegar os parâmetros vindos da requisição. Importe o objeto `request` do módulo `flask` para que seja possível pegar os atributos do formulário através do parâmetro `form`:
    ```python
    #my_app/views.py
    from flask import Flask, render_template, Blueprint, request
    # restante do código omitido
    
    @contasbp.route('/cria_conta', methods=['POST'])
    def cria():
        numero = request.form.get('numero')
        titular = request.form.get('titular')
        saldo = float(request.form.get('saldo'))
        limite = float(request.form.get('numero'))        
    ```
    
    Não esqueça de converter os parâmetros `saldo`e `limite` para `float`.
    
1. Agora precisamos criar uma contas com os valores dos atributos obtidos pela requisição. Importe a classe `Conta` do módulo `models` e instancie uma conta no método `cria()`:
    ```python
    #my_app/views.py
    from flask import Flask, render_template, Blueprint, request
    from my_app.models impórt Conta
    
    # restante do código omitido
    
    @contasbp.route('/cria_conta', methods=['POST'])
    def cria():
        numero = request.form.get('numero')
        titular = request.form.get('titular')
        saldo = float(request.form.get('saldo'))
        limite = float(request.form.get('numero'))
        
        conta = Conta(numero, titular, saldo, limite)
    ```

1. Agora precisamos criar uma conta no banco de dados. Primeiro, precisamos criar a tabela de contas dentro da base de dados `banco` no MySQL. 
    - Abra o terminal e acesse o MySQL
        ```bash
        mysql -u root
        ```
    
    - No console do MySQL, acesse a base de dados `banco`
        ```
        use banco;
        ```
    
    - Digite o comando abaixo para criar a tabela `contas` no banco:
        ```mysql
         create table contas ( 
            id BIGINT NOT NULL AUTO_INCREMENT,
            numero varchar(255), 
            titular varchar(255), 
            saldo double, 
            limite double, 
            primary key(id));
        ```

1. Criada a tabela, vamos precisar da conexão aberta para inserir uma conta no banco de dados. No arquivo `views.py` importe o método `get_connection()` e abra uma conexão dentro do método `cria()`
    ```python
    #my_app/views.py
    from flask import Flask, render_template, Blueprint, request
    from my_app.models import Conta
    from my_app.db import get_connection

    contasbp = Blueprint('contas', __name__, url_prefix='/contas')

    @contasbp.route("/form")
    def formulario():
        return render_template("form.html")
    
    @contasbp.route('/cria_conta', methods=['POST'])
    def cria():
        numero = request.form.get('numero')
        titular = request.form.get('titular')
        saldo = float(request.form.get('saldo'))
        limite = float(request.form.get('numero'))
        
        conta = Conta(numero, titular, saldo, limite)
        
        connetion = get_connection()
    ```

1. Agora que temos uma conexão aberta, podemos enviar o comando SQL para inserir uma conta em nossa tabela `contas` no banco de dados. Ao final, precisaremos confirmar a ação no banco de dados através do métoso `commit()` de `connection`:
    ```python
    #my_app/views.py
    from flask import Flask, render_template, Blueprint, request
    from my_app.models import Conta
    from my_app.db import get_connection

    contasbp = Blueprint('contas', __name__, url_prefix='/contas')

    @contasbp.route("/form")
    def formulario():
        return render_template("form.html")
    
    @contasbp.route('/cria_conta', methods=['POST'])
    def cria():
        numero = request.form.get('numero')
        titular = request.form.get('titular')
        saldo = float(request.form.get('saldo'))
        limite = float(request.form.get('numero'))
        
        conta = Conta(numero, titular, saldo, limite)
        
        connetion = get_connection()
        
        sql = 'insert into contas (numero, titular, saldo, limite) values (%s, %s, %s, %s)'
    valores = (conta.numero, conta.titular, conta.saldo, conta.limite)

        cursor = connction.cursor()
        cursor.execute(sql, valores)
        
        connction.commit()
        connection.close()

        return "conta cadastrada com sucesso"
    ```
    
1. Rode novamente o arquivo `__init__.py`. Acesse o formulário pela requisição `http://localhost:5000/contas/form`, preencha os dados de uma conta e clique no botão `cria conta`. Se nenhum erro ocorrer a mensagem "conta cadastrada com sucesso" deve aparecer.

1. Para verificar se a conta foi realmente salva no banco, vamos acessar o MySQl:
    - Acesse o MySQl via terminal
        ```bash
        mysql -u root
        ```
    
    - Acesse a base de dados `banco`:
        ```mysql
        use banco;
        ```
        
    - Digite o comando SQL abaixo para listar todas as contas da tabela `contas`:
        ```mysql
        select * from contas;
        ```
        
1. Insira mais algumas contas no banco de dados para usarmos no próximo exercício.

voltar ao [topo](#exercicios)

## <a name="lista"></a>5. Disponibilizando a lista de contas

1. Agora vamos trazer todas as contas do banco e disponibilizar para o template `lista.html` mostrar os dados. Crie uma nova função chamada `lista_contas()` no arquivo `views.py` definida com a rota `/lista`.
    ```python
    #my_app/views.py
    
    # código restante omitido
    
    @contasbp.route("/lista")
    def lista_contas():
        pass
    ```

1. Este método deve ir até o banco pegar as contas cadastradas para disponibilizar para a página responsãvel por listar as contas (`lista.html`). Vamos usar a connection para enviar o comando que deve buscar todas as contas e armazenar em uma lista:
    ```python
    #my_app/views.py
    
    # código restante omitido
    
    @contasbp.route('/lista')
    def lista_contas():
        connetion = get_connection()
        
        sql = 'select * from contas'

        cursor = connction.cursor()
        cursor.execute(sql)
        resultado = cursor.fetchall()
       
        contas = []
        for registro in resultado:
            conta = Conta(registro[1], registro[2], registro[3], registro[4], registro[0])
            contas.append(conta)
        
        connection.close()

        return render_template('lista.html', contas=contas)
    ```
    
1. Rode novamente o arquivo `__init__.py` e acesse a requisição `http://localhost:5000/contas/lista` e veja o resultado.

1. Para melhorar ainda mais, podemos fazer com que o formulário, após a submissão dos dados de uma conta, redirecione para a listagem de contas. Para isso, usaremos a função `redirect()` do `flask` junto com a `url_for()` no retorno do método `cria()`:
    ```python
    #my_app/views.py
    from flask import Flask, render_template, Blueprint, request, redirect, url_for
    from my_app.models import Conta
    from my_app.db import get_connection
    
    # código restante omitido
    
    @contasbp.route('/cria_conta', methods=['POST'])
    def cria():
        # código restante omitido
        return redirect(url_for('contas.lista_contas'))
        
    @contasbp.route('/lista')
    def lista_contas():
        # código omitido
    ```
    
1. Rode novamente o arquivo `__init__.py` e adicione uma nova conta e veja que agora é redirecionado para a página de listagem das contas.


voltar ao [topo](#exercicios)

## flask-api

## <a name="init_api"></a>1.Configurando a API e refatorando o projeto

1. Vamos criar uma nova aplicação no projeto chamada de `my_api`. Na raiz do projeto crie um novo pacote chamado `my_api`. Com o cursor no projeto, clique com o botão direito do mouse e vá em `New -> Python Package`. Nomeie o pacote com `my_api` e clique em `OK`.

1. Como o projeto já possui uma instância de `flask`, vamos aproveitar a mesma para as duas aplicações (`my_app` e `my_api`). Crie o arquivo `run.py` na raiz do projeto. Esse arquivo será responsável por instanciar e rodar a aplicação.
    ```
    banco-web/
     my_api/
        __init__.py
     my_app/
        /templates
        /static
        __init__.py
        db.py
        models.py
        views.py
     run.py
    ```

1. Copie o conteúdo do arquivo `my_app/__init__.py` para o arquivo `run.py`:
    ```python
    # banco_web/run.py
    from flask import Flask, render_template
    from my_app.views import contasbp

    app = Flask(__name__)
    app.register_blueprint(contasbp)

    if __name__ == '__main__':
        app.run()
    ```
    Apague o conteúdo do arquivo `my_app/__init__.py`
    
1. Mas, ao fazer isso, nossa aplicação pára de funcionar já que o flask não encontra as pastas `templates` e `static`. Vamos ensinar ao `flask`encontrar essas pastas definindo os atributos `template_folder` e `static_url_path` de `app`:
    ```python
    # banco_web/run.py
    from flask import Flask, render_template
    from my_app.views import contasbp

    app = Flask(__name__)
    app.template_folder = "my_app/template
    app.static_url_path = 'my_app/static' 
    
    app.register_blueprint(contasbp)

    if __name__ == '__main__':
        app.run()
    ```
    Obs.: Você também poderia arrastar as pastas `templates` e `static` para a raiz do projeto sem precisar configurar essas propriedades. Essa seria uma melhor solução caso mais de uma aplicação utilize templates em seu projeto.
    
1. Rode o arquivo `run.py` e veja se tudo funciona como antes.

1. Agora vamos definir os diretórios do projeto de nossa `api`. Dentro da pasta `my_api` crie os diretórios `models`, `resources` e `utils`.
    ```
    my_api/
        models/
        resources/
        utils/
        __init__.py
    ```
    
1. Dentro de models crie o arquivo `conta.py` e copie o conteúdo de `my_app/models` para o módulo `contas.py`
     ```python
    #my_api/models/conta.py
    
    class Conta:

        def __init__(self, numero, titular, saldo, limite=1000.0, id=None):
            self._id = id
            self._numero = numero
            self._titular = titular
            self._saldo = saldo
            self._limite = limite

        @property
        def id(self):
            return self._id

        @property
        def numero(self):
            return self._numero

        @numero.setter
        def numero(self, numero):
            self._numero = numero

        @property
        def titular(self):
            return self._titular.capitalize()

        @titular.setter
        def titular(self, titular):
            self._titular = titular

        @property
        def saldo(self):
            return self._saldo

        @property
        def limite(self):
            return self._limite

        @limite.setter
        def limite(self, limite):
            self._limite = limite

        def deposita(self, valor):
            self._saldo -= valor

        def saca(self, valor):
            self._saldo -= valor

        def transfere_para(self, destino, valor):
            self.saca(valor)
            destino.deposita(valor)

        def __str__(self):
            return 'Conta: [id: {}, numero: {}, titular: {}, saldo: {}, limite: {}]'.format(self._id, self._numero, self._titular, self._saldo, self._limite)
    ```

1. Agora vamos criar a rota principal que definirá o contexto de nossa API. Assim como fizemos com nossa aplicação `my_app`, criaremos uma _blueprint_ para definir seu contexto no arquivo `run.py`:
    ```python
    # banco_web/run.py
    from flask import Flask, render_template
    from my_app.views import contasbp

    app = Flask(__name__)
    app.template_folder = "my_app/template
    app.static_url_path = 'my_app/static' 
    
    app.register_blueprint(contasbp)
    app.register_blueprint(contas_api)
    
    if __name__ == '__main__':
        app.run()
    ```

1. Esse arquivo vai acusar erro já que não encontra a variável `contas_api`. Vamos inicializar essa _blueprint_ no arquivo `__init__.py` de `my_api`:
    ```python
    # my_api/__init__.py
    from flask import Blueprint
    
    contas_api = Blueprint('api', __name__, url_prefix='/api')
    ```
    
1. Vamos aproveitar e criar uma rota para testar se vai funcionar:
    ```python
    # my_api/__init__.py
    from flask import Blueprint
    
    contas_api = Blueprint('api', __name__, url_prefix='/api')
    
    @contas_api.route("/")
    def teste():
        return "OK"
    ```

1 Importe a _blueprint_ `contas_api` dentro do arquivo `run.py`:
    ```python
    # banco_web/run.py
    from flask import Flask, render_template
    from my_app.views import contasbp
    from my_api import contas_api
    
    app = Flask(__name__)
    app.template_folder = "my_app/template
    app.static_url_path = 'my_app/static' 
    
    app.register_blueprint(contasbp)
    app.register_blueprint(contas_api)
    
    if __name__ == '__main__':
        app.run()
    ```

1. Rode o arquivo `run.py` e acesse a endereço `http://localhost:5000/api/` e veja se a mensagem `OK` aparece na tela. 

voltar ao [topo](#exercicios)
    
## <a name="restplus"></a>2. Iniciando a API com flask-restplus

1. Contaremos com a ajuda do pacote `flask-restplus` para construir nossa API. Precisamos adicionar esta dependência ao projeto. Vá em `File -> Settings -> Project:<nome-projeto> -> Project Interpreter`. No meu direito, procure por um símbolo de adição (`+`) e clique nele. No campo de busca procure por `flask-restplus` e clique em `Install Package`. Aguarde a conclusão da instalação, feche a janela e clique em `OK`.

1. No arquivo `my_api/__init__.py`, apague o método de teste e crie uma instância de `Api` e inicie a api passando a _blueprint_ `contas_api`:
    ```python
    # my_api/__init__.py
    from flask import Blueprint
    from flask_restplus import Api
    
    contas_api = Blueprint('api', __name__, url_prefix='/api')
    
    api = Api(version='1.0', title='Contas API',
          description='Api de Contas do curso PY-14')
    api.init_app(contas_api)
    ```
    Não esqueça de importar `Api` do módulo `flask_restplut`.
    
1. Além de ajudar na construção de nossa API, o `flask-restplus` gera uma documetanção automática integrada com o Swagger. Rode novamentes a aplicação (`run.py`) e acesse o endereço `http://localhost:5000/api/` e veja o resultado. Deve aparecer a página da documentação com as informções que definimos no objeto `api`.

1. Agora vamos criar a nossa primeira rota. Segundo o protocolo HTTP, todo conteúdo que iremos disponibilizar é um recurso em nossa aplicação. O `flask-restplus` possui uma classe interna que define um recurso e criaremos um recurso para as nossas contas. Dentro da pasta `resources` crie o arquivo `conta_resource.py`.

1. Dentro do arquivo `conta_resource.py`crie uma classe chamada `ContasResource`:
    ```my_api/resources/conta_resource.py
    class ContasResource:
        pass
    ```
    
1. Para definir métodos que serão mapeados com as rotas, devemos fazer nossa classe herdar da classe `Resoruce` do módulo `flask-restplus`:
    ```my_api/resources/conta_resource.py
    from flask_restplus import Resource
    
    class ContasResource(Resource):
        pass
    ```
    
1. Ao herdar da classe `Resource`, herdamos vários métodos pré-definidos dentro dela que expõe vários _endpoints_ de acordo com os mátodos do protocolo HTTP. A classe `Resource`, por sua vez, herda da classe `MethodView` do `flask`. Nossa primeira rota será para expor uma requisição utilizando o método `GET` do protocolo HTTP para disponibilizar uma lista de contas.
    ```my_api/resources/conta_resource.py
    from flask_restplus import Resource
    
    class ContasResource(Resource):
        
        def get():
            pass
    ```
    
1. Vamos criar umas lista com algumas contas para retornar no método `get()` para testar:
    ```my_api/resources/conta_resource.py
    from flask_restplus import Resource
    from my_api.models.conta import Conta
    
    conta1 = Conta('123-4', 'João', 1200.0, 1000.0, 1)
    conta2 = Conta('123-5', 'José', 1500.0, 1000.0, 2)
    conta3 = Conta('123-6', 'Maria', 2200.0, 1000.0, 3)

    contas = [conta1, conta2, conta3] 
    
    class ContasResource(Resource):
        
        def get():
            return contas
    ```

1. Mas ainda precisamos definir uma rota. o contexto de nossa API já está configurado como `/api/` pela _blueprint_. Agora, precisamos registrar nosso recurso `ContasResource` em nossa api. Para isso, criaremos um namespace "/contas" para este recurso e incluiremos no objeto `api`. Para criar um namespace, utilizamos a classe `Namespace` do mádulo `flask-restplus`. O namespace definido será usado para definir a rota para nosso recurso com o decorator `ns.route()`:
    ```my_api/resources/conta_resource.py
    from flask_restplus import Resource. Namespace
    from my_api.models.conta import Conta
    
    ns = ns =  Namespace('contas', description='operações das contas')
    
    conta1 = Conta('123-4', 'João', 1200.0, 1000.0, 1)
    conta2 = Conta('123-5', 'José', 1500.0, 1000.0, 2)
    conta3 = Conta('123-6', 'Maria', 2200.0, 1000.0, 3)

    contas = [conta1, conta2, conta3] 
    
    @ns.route("/")
    class ContasResource(Resource):
        
        def get():
            return contas
    ```

1. Agora precisamos importar o namespace criado para nosso arquivo `my_api/__init__.py` e registrá-lo no objeto `api` com a rota desejada através do método `add_namespace()`:
    ```python
    # my_api/__init__.py
    from flask import Blueprint
    from flask_restplus import Api
    from my_api.resources.conta_resource import ns as conta_resource
    
    contas_api = Blueprint('api', __name__, url_prefix='/api')
    
    api = Api(version='1.0', title='Contas API',
          description='Api de Contas do curso PY-14')
    api.init_app(contas_api)
    

    api.add_namespace(conta_resource, path='/contas')
    ```

1. Rode novamente o arquivo `run.py` e acesse o endereço `http://localhost:5000/api/contas`. Um erro acontece.

1. Abra o console e veja a mensagem do erro:
    ```python
    TypeError: Object of type 'Conta' is not JSON serializable
    ```
    Ou seja, o objeto do tipo `COnta` não é serializável para o formato JSON.
    
1. Precisamos de uma maneira de converter nossos objetos do tipo conta para JSON. Como um dicionário tem uma estrutura parecida com um JSON, o Python possui um módulo chamado `json` que consegue fazer essa conversão. Basta adicionarmos um método que devolve nossa conta como um objeto do tipo funcionário ou usar seu `__dict__` interno para isso, mas nos daria muito trabalho toda vez para parsear o objeto. O `flask-restplus`já provê os recursos necessários para esta tarefa. Na pasta `utils`, criaremos o arquivo `serializers.py` e ensinaremos ao `flask-restplus`como queremos que nossa conta seja parseada para JSON:
    ```
    #my_api/utils/serializers.py
    from flask_restplus import fields
    from my_api import api

    conta_dto = api.model('conta', {
        'id': fields.Integer,
        'numero': fields.String,
        'titular': fields.String,
        'saldo': fields.Float,
        'limite': fields.Float
    })
    ```
    Através do método `model()` do objeto `api`, ensinamos os `flask-restplus` como uma conta deve ser parseada para JSON. O objeto `fileds` do módulo `flask-restplus` é bastante poderoso e podemos definir com precisão o tipo de cada atributo de nossa conta.
    
1. Agor que já temos nosso conversor pronto, vamos pedir para o `flask-restplus`  converter a conta no retorno do método `get()` de nossa `ContasResoruces`. Para isso, usamos o decorador `marshal_with` passando o objeto `conta_dto`definido no módulo `serializers.py`:
    ```my_api/resources/conta_resource.py
    from flask_restplus import Resource. Namespace, marshal_with
    from my_api.models.conta import Conta
    from my_api.utils.serializers import conta_dto
    
    ns = ns =  Namespace('contas', description='operações das contas')
    
    conta1 = Conta('123-4', 'João', 1200.0, 1000.0, 1)
    conta2 = Conta('123-5', 'José', 1500.0, 1000.0, 2)
    conta3 = Conta('123-6', 'Maria', 2200.0, 1000.0, 3)

    contas = [conta1, conta2, conta3] 
    
    @ns.route("/")
    class ContasResource(Resource):
        
        @marshal_with(conta_dto)
        def get():
            return contas
    ```

1. Rode novamente o arquivo `run.py`, acesse `http://localhost:5000/api/contas` pelo navegador e veja que agora tudo funciona como esperado.

1. Acesse a documentação pelo endereço `http://localhost:5000/api`. Clique em `contas` e veja que o _endpoint_ foi adicionado automaticamente e pode ser testado de maneira iterativa cicando em `Try it out` e `Execute`.
    
voltar ao [topo](#exercicios)

## <a name="sqlalchemy"></a>3. Mostrando a lista do banco de dados.

Para que a APi trabalhe com dados reais, precisamos adicionar algumas contas no banco de dados e, em seguida, buscá-las. Precisamos, como fizemos na outra aplicação, de uma conexão com o banco de dados e executar as queries necessárias. Vimos como isso dá muito trabalho e, portanto, vamos utilizar um framework para a parte de persitência chamado SQLAlchemy. Existe um pacote que faz a integração do SQLAlchemy com o Flask nos poupando trabalho repetitivo.

1. Primeiro vamos instalar o pacote `flask-sqlalchemy`. Vá em `File -> Settings -> Project:<nome-projeto> -> Project Interpreter`. No meu direito, procure por um símbolo de adição (`+`) e clique nele. No campo de busca procure por `Flask-SQLAlchemy` e clique em `Install Package`. Aguarde a conclusão da instalação, feche a janela e clique em `OK`.

1. No arquivo `run.py`, vamos cria uma instância de `SQLAlchemy`, classe chave para iniciar a camada de persitência da aplicação dentro do contexto do `flask`:  
    ```python
    # banco_web/run.py
    from flask import Flask, render_template
    from my_app.views import contasbp
    from my_api import contas_api
    from flask_sqlalchemy import SQLAlchemy  
    
    app = Flask(__name__)
    app.template_folder = "my_app/template
    app.static_url_path = 'my_app/static' 
    
    app.register_blueprint(contasbp)
    app.register_blueprint(contas_api)
    
    db = SQLAlchemy(app) 
    
    if __name__ == '__main__':
        app.run()
    ```

1. Mas ao rodar novamente o módulo `run.py` recebemos um erro:
    ```python
    'Neither SQLALCHEMY_DATABASE_URI nor SQLALCHEMY_BINDS is set. '
    ```
    Faz sentido! Não tem como o SQLALchemy saber onde fica nosso banco de dados, ou mesmo qual o usuário e a senha para estabelecer uma conexão. Precisamos passar isso a ele, ou melhor, para o `flask` que está no controle na aplicação. A instância `app` de `FLask` possui vários atributos de configuração, dentre eles os de configuração do banco de dados. Por exemlo, podemos passar através do atributo `config` a url de conexão com o banco:
    ```
    app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+mysqlconnector://root@localhost:3306/banco_api'
    ```
    Mas qualquer outra configuração adicional que fizermos, lotaremos nosso módulo `run.py` com linhas de código de configurações. Por este motivo que optaremos por colocar todas as configurações em um arquivo chamado `config.py` na raiz da aplicação e em apenas uma linha definiremos todas as configurações contidas neste arquivo.
    
1. Crie o arquivo `config.py`na raiz do projeto e acrescente a seguinte linha e código:
    ```
    # banco_web/config.py
    
    # sql_alchemy
    SQLALCHEMY_DATABASE_URI = "mysql+mysqlconnector://root@localhost:3306/banco_api"
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    ```

1. Acrescente a seguinte linha de código no arquivo `run.py` para definir as configurações de nosso arquivo `config.py`:
    ```
    app.config.from_object('config') 
    ```
    
    Por fim, nosso código ficará assim:
    ```python
    # banco_web/run.py
    from flask import Flask, render_template
    from my_app.views import contasbp
    from my_api import contas_api
    from flask_sqlalchemy import SQLAlchemy  
    
    app = Flask(__name__)
    app.config.from_object('config') 
    
    app.template_folder = "my_app/template
    app.static_url_path = 'my_app/static' 
    
    app.register_blueprint(contasbp)
    app.register_blueprint(contas_api)
    
    db = SQLAlchemy(app) 
    
    if __name__ == '__main__':
        app.run()
    ```

1. Agora nenhum erro aparece ao rodar novamente a aplicação. Com o SQLAlchemy configurado, vamos colocá-lo em ação. Vamos pedir que ele crie a tabela de contas na base de dados `banco_api`. Para isso, precisamos fazer com que nossa classe `Conta` herde da classe `Model` do SQLAlchemy:
    ```
    #my_api/models.conta.py
    from run import db
    
    class Conta(db.Model):
        # código omitido
    ```
   
1. Mas ao rodar a plaicação novamente recebemos um erro:
    ```
    sqlalchemy.exc.ArgumentError: Mapper mapped class Conta->conta could not assemble any primary key columns for mapped table 'conta'
    ```
    Ou seja, o SQLAlchemy ñão conseguiu mapear nossa classe para um objeto que deve ser persistido pois ele não possui uma chave primária. Devemos avisar ao SQLAlchemy qual atributo deve ser a chave primária (que será o `id`).
    
1. Para ensinar como queremos que nossa classe `Conta` seja persistida, devemos incluir as seguintes linhas dentro de nossa classe:
    ```
     #my_api/models.conta.py
    from run import db
    from sqlalchemy import Column, Integer, String, Numeric
    
    class Conta(db.Model):   
    
        _id = Column('id', Integer(), primary_key=True)
        _numero = Column('numero', String(255), nullable=False, unique=True)
        _titular = Column('titular', String(255), nullable=False)
        _saldo = Column('saldo', Numeric(), nullable=False)
        _limite = Column('limite', Numeric(), nullable=False)
        
        # restante do código omitido
    ```
    Dessa maneira o SQLAlchemy sabe como queremos salvar cada atributo no banco, com seu respectivo tipo e regras de nulidade ou unicidade.
    
1. Mas isso não basta para que ele crie a tabela. Precisamos pedir para ele criar tudo e confirmar as ações no banco com o método `create_all()`:
    ```python
    # banco_web/run.py
    from flask import Flask, render_template
    from my_app.views import contasbp
    from my_api import contas_api
    from flask_sqlalchemy import SQLAlchemy  
    
    app = Flask(__name__)
    app.config.from_object('config') 
    
    app.template_folder = "my_app/template
    app.static_url_path = 'my_app/static' 
    
    app.register_blueprint(contasbp)
    app.register_blueprint(contas_api)
    
    db = SQLAlchemy(app) 
    db.create_all()
    
    if __name__ == '__main__':
        app.run()
    ```
1. Mas aqui teremos problemas com import circulares. Vamos isolar a criação da instância de `SQLAlchemy` para outro módulo chamdo de `database.py` dentro do diretório raiz:
    ```
    # bancoweb/database.py
    from flask_sqlalchemy import SQlAlchemy
    
    db = SQLAlchemy()
    ```
    
1. E isolamos a criação da aplicação em um método `create_app()` e a inicialização do banco apenas de o contexto da aplicação existir em um método `setup_database()`: 
     ```python
    # banco_web/run.py
    from flask import Flask, render_template
    from my_app.views import contasbp
    from my_api import contas_api
    from database import db  
    
    def create_app():
        app = Flask(__name__)
        app.config.from_object('config') 
        
        db.init_app(app)
        
        app.template_folder = "my_app/template
        app.static_url_path = 'my_app/static' 
    
        app.register_blueprint(contasbp)
        app.register_blueprint(contas_api)
    
    def setup_database(app):
        with app.app_context():
            db.create_all()
        
    if __name__ == '__main__':
        app = create_app()
        setup_database(app)
        app.run()
    ```
    Não esqueça de modificar o import de `db` no módulo `conta.py`.
    
1. Mas ao rodar recebemos outro erro:
    ```
    sqlalchemy.exc.ProgrammingError: (mysql.connector.errors.ProgrammingError) 1049 (42000): Unknown database 'banco_api'
    ```
    Ou seja, não encontrou a base de dados `banco_api`. Precisamos criá-la no banco de dados e rodar novamente.
    
1. Acesse o MySQL, crie uma base de dados chamada `banco_api` e rode novamente a aplicação.

1. Precisamos executar alguma operação para que o SQLAlchemy abra uma conexão. Vamos criar o método `post()` em nossa `ContasResource` par definir a ação de criação de uma nova conta:
    ```python
    my_api/resources/conta_resource.py
    from flask_restplus import Resource. Namespace, marshal_with
    from my_api.models.conta import Conta
    from my_api.utils.serializers import conta_dto
    
    ns = ns =  Namespace('contas', description='operações das contas')
    
    conta1 = Conta('123-4', 'João', 1200.0, 1000.0, 1)
    conta2 = Conta('123-5', 'José', 1500.0, 1000.0, 2)
    conta3 = Conta('123-6', 'Maria', 2200.0, 1000.0, 3)

    contas = [conta1, conta2, conta3] 
    
    @ns.route("/")
    class ContasResource(Resource):
        
        @marshal_with(conta_dto)
        def get():
            return contas
            
        def post():
            pass
    ```
    
1. Este método deve pegar os atributos da requisição como fizemos na aplicação anterior. Mas os atributos não chegam mais via formulário mas em formato JSON. Novamente, devemos ensinar ao flask como esperamos receber este objeto. Dentro do arquivo serializar, crie o modelo esperado do json em uma variável `conta_input_dto`. E modifique o anterior para `conta_output_dto`:
    ```python
    # my_api/utils/serializers.py
    from flask_restplus import fields
    from my_api import api

    conta_output_dto = api.model('conta', {
        'id': fields.Integer,
        'numero': fields.String,
        'titular': fields.String,
        'saldo': fields.Float,
        'limite': fields.Float
    })

    conta_input_dto = api.model('conta', {
        'numero': fields.String,
        'titular': fields.String,
        'saldo': fields.Float,
        'limite': fields.Float
    })
    ```
  
1. No arquivo `conta_resource.py` arrume os imports da módulo `serializer.py` e utilize  decorator `ns.expect()` passando o DTO de input para avisar que espermos esse objeto pela request:
    ```python
    my_api/resources/conta_resource.py
    from flask_restplus import Resource. Namespace, marshal_with
    from my_api.models.conta import Conta
    from my_api.utils.serializers import conta_output_dto, conta_input_dto
    
    ns = ns =  Namespace('contas', description='operações das contas')
    
    conta1 = Conta('123-4', 'João', 1200.0, 1000.0, 1)
    conta2 = Conta('123-5', 'José', 1500.0, 1000.0, 2)
    conta3 = Conta('123-6', 'Maria', 2200.0, 1000.0, 3)

    contas = [conta1, conta2, conta3] 
    
    @ns.route("/")
    class ContasResource(Resource):
        
        @marshal_with(conta_output_dto)
        def get():
            return contas
        
        @ns.expect(conta_input_dto)
        def post():
            pass
    ```

1. Import o objeto `request` de `flask-restplus` e utilize o método `get_json()` para obter os parametros da requiição. Depois construa uma conta com os parâmetros e chame o método `add()` do objeto `session` do `SQLAlchemy` para adicionar uma conta no banco. Não esqueça de chamar o `comiit()` para confirmar a operação.
    ```python
    @ns.route("/")
    class ContasResource(Resource):
        
        @marshal_with(conta_output_dto)
        def get():
            return contas
            
        @marhal_with(conta_output_dto)
        @ns.expect(conta_input_dto)
        def post():
            data = request.get_json()
            numero = data.get('numero')
            titular = data.get('titular')
            saldo = float(data.get('saldo'))
            limite = float(data.get('limite'))

            conta = Conta(numero, titular, saldo, limite)

            db.session.add(conta)
            db.session.commit()
            return conta
    ```

1. Rode novamente o arquivo `run.py`, acesse o endreço `http://localhost:5000/api/` e acesse o método `POST`da `conta` na documentação do swagger. Teste inserir uma conta por lá.

1. Modifique o método `get()` de `ContasResource` para que ele retorne a lista das contas no banco de dados:
    ```python
    @ns.route("/")
    class ContasResource(Resource):
        
        @marshal_with(conta_output_dto)
        def get():
            return return Conta.query.all()
        
        @marhal_with(conta_output_dto)
        @ns.expect(conta_input_dto)
        def post():
            data = request.get_json()
            numero = data.get('numero')
            titular = data.get('titular')
            saldo = float(data.get('saldo'))
            limite = float(data.get('limite'))

            conta = Conta(numero, titular, saldo, limite)

            db.session.add(conta)
            db.session.commit()
            
            return conta
    ```
    Apague as contas e a lista de contas criadas no arquivo `conta_resource.py` já que não estamos mais utilizando.
    
voltar ao [topo](#exercicios)