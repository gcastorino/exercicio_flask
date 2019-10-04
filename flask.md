## Exercícios

### introdução ao Flask

1. [Criando o projeto](#there_you_go2)

1. [Disponibilizando o formulário de contas](#form2)

1. [Organizando o projeto](#organizando)

1. [Abrindo uma conexão com banco de dados](#connection)

1. [Disponibilizando a lista de contas](#lista)

### flask-api
    1. 
## <a name="there_you_go2"></a>1. Criando o projeto

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


## flask-api

    

