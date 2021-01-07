---
title: "Metaprogramming 101"
tags: [machine learning, quality]
toc: true
lang: pt_BR
ref: metaprogramming-101
excerpt: Metaclasses e Metaprogramação em Python
header:
  image: "/images/metaprogramming/post.png"
---

Antes de falarmos e entendermos mais sobre metaprogramação, que tal um pequeno "refresco" sobre o que são classes, objetos e metaclasses em Python?

## Relembrando conceitos...

### Objetos em Python

Em Python, [como você deve saber](https://www.codingninjas.com/blog/2020/08/27/how-everything-in-python-is-an-object/), tudo é um objeto. Para exercitarmos e entendermos um pouco sobre esta afirmação vamos utilizar `type` em algumas funções e variáveis para que fique um pouco mais óbvio:

```python
variable = 25
type(variable)
# <class 'int'>

variable_string = "hello-world"
type(variable_string)
# <class 'str'>

import inspect

inspect.isclass(str)
# True

inspect.isclass(int)
# True
```

A função `type` nos ajuda a obter a classe de um objeto. Mas.... se tudo é um objeto em Python, o que é uma `class`?

```python
class WhatIsLove:
    pass

type(WhatIsLove)
# <class 'type'>

import inspect

inspect.isclass(WhatIsLove)
# True

inspect.isclass(type(WhatIsLove))
# True
```

Conforme vimos acima, uma classe em Python *também é um objeto*, bem estranho né? Na verdade, quando usamos `type` como função, conseguimos descobrir a classe de nossas classes (*inception*?) e encontramos a classe `type` (*inception* de novo?). Vamos tornar as coisas ainda mais confusas:

```python
isinstance(WhatIsLove, type)
# True

isinstance(type, type)
# True

isinstance(WhatIsLove, WhatIsLove)
# False
```

Então.... Uma classe é uma instância de `type` e a classe `type` também é uma instância de `type`? Agora, isso sim é estranho!

O que é essa função e classe *type*, então? Na documentação da linguagem você vai encontrar mais informações (e, na verdade, muito mais informação) sobre esse tema, mas eu vou apontar pra um [artigo relacionado mais específico](https://docs.python.org/3/reference/datamodel.html#metaclasses). Basicamente, **todas as classes são construídas utilizando `type`**, que significa que todas as classes são derivadas de `type`:

```python
isinstance(int, type)
# True

isinstance(55, type)
# True
```

OK, OK. Vamos dar um tempo sobre esse assunto em específico para que ele se encaixe melhor na nossa cabeça.

### Classes e Metaclasses

(Se você precisa de uma revisa de [POO em Python](https://www.programiz.com/python-programming/object-oriented-programming))

Bem, você deve saber que quando estamos construindo uma classe nós utilizamos a função `__init__` para inicializar os atributos dos nossos objetos:

```python
class Painting:
 
    def __init__(self, author, date, name, height, width, cost="unknown"):
        self.author = author
        self.date = date
        self.name = name
        self.height = height
        self.width = width
        self.cost = cost
    
    def __repr__(self):
        return f"Paiting '{self.name}' by {self.author} ({self.date}). USD {self.cost}. Dimensions {self.height} X {self.width}"
    
    def __str__(self):
        return self.__repr__
    
    def get_area(self):
        return self.height * self.width
```

Acima, podemos ver uma implementação muito simples de uma classe de Pintura. Vamos ver o que acontece por detrás dos panos:

```python
Painting.__dict__
# {'__module__': '__main__', '__init__': <function Painting.__init__ at 0x10be9a3b0>, '__repr__': <function Painting.__repr__ at 0x10be9a440>, '__str__': <function Painting.__str__ at 0x10be9a4d0>, 'area': <property object at 0x10bd899b0>, '__dict__': <attribute '__dict__' of 'Painting' objects>, '__weakref__': <attribute '__weakref__' of 'Painting' objects>, '__doc__': None}

starry_night = Painting(
    author="Vincent van Gogh",
    date=1889,
    name="The Starry Night",
    height=73.7,
    width=92.1
)

starry_night.__dict__
# {'author': 'Vincent van Gogh', 'date': 1889, 'name': 'The Starry Night', 'height': 73.7, 'width': 92.1, 'cost': 'unknown'}
```

Você consegue ver como a classe Painting mantém todos os métodos e métodos mágicos? Uma vez que instanciamos a classe, todos os seus atributos são populados resultando em um objeto da classe Painting.

Mas ainda, como isso tudo acontece? Bem, conforme vimos anteriormente, lá vem o `type`.

```python
# Podemos usar type para descobrir o tipo de uma variável
variable = "123"
type(variable)
# <class 'str'>

# Ou para criar classes!
def get_area(self):
    return self.height * self.width


Painting = type("Painting", (), {"author": "Vincent van Gogh", "date": 1889, "name": "The Starry Night", "height": 73.7, "width": 92.1, "get_area": get_area})

print(Painting)
# <class '__main__.Painting'>

starry_night = Painting()
starry_night.author
# 'Vincent van Gogh'
starry_night.name
# 'The Starry Night'
starry_night.get_area()
# 6787.7699999999995

# Também podemos derivar de uma classe base!
class SaoPauloState:
    def __init__(self):
        self.state = "Sao Paulo"
        self.country = "Brasil"


SaoPauloCity = type("SaoPauloCity", (SaoPauloState), {"city": "Sao Paulo"})

sao_paulo = SaoPauloCity()
sao_paulo.city
# 'Sao Paulo'

sao_paulo.state
# 'Sao Paulo'

sao_paulo.country
# 'Brazil'
```

Na prática, o que acontece por detrás dos panos é que quando instanciamos uma nova classe, dinamicamente (ou *on-the-fly*) o interpretador Python reúne todas as classes base necessárias (geralmente `object`, quando não declaramos uma classe para extender) e também o 'corpo' da classe (atributos, métodos, ...).

E isso, amigos, é o que uma `metaclass` realmente faz: **ela cria classes**! Assim como uma classe cria objetos (e customizam os objetos, de acordo com o código), **metaclasses criam classes** (e customizam as classes, de acordo com o código)!

#### OK, OK... Como isso tudo funciona?

Basicamente, toda vez que criamos e instanciamos uma classe isso aqui acontece:

- Python checa a metaclasse da classe (isso acontece tanto a nível da classe quanto ao nível do módulo em que a classe foi declarada, [aqui tem mais informações sobre o processo](https://www.python.org/dev/peps/pep-3115/))
  - Se não existir nenhuma metaclasse definida, `type` - a padrão - é utilizada
- Usando a metaclasse, Python cria um objeto de classe em memória - lembre-se que a metaclasse **cria um objeto classe e não uma instância da classe**!
- Finalmente, uma instância da classe é criada, ou um objeto da classe!

Uma coisa muito legal de saber é que as metaclasses não precisam necessariamente serem classes; podemos usar funções como metaclasses também!

Vamos ver um exemplo muito simples e rápido de como isso funciona:

```python
import re


def camel_case_attributes(future_class_name, future_class_parents, future_class_attributes):
    """Retorna um objeto classe com seus atributos tranformados em camelCase."""
    camelCase_attributes = {
        re.sub(r'_([a-z])', lambda m: m.group(1).upper(), attribute): value
        for attribute, value in future_class_attributes.items()
    }
    
    # Aqui usamos type para criar um objeto classe
    return type(future_class_name, future_class_parents, camelCase_attributes)


class WhatIsLove(metaclass=camel_case_attributes):
    baby_dont_hurt_me = "no more"


WhatIsLove.baby_dont_hurt_me
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# AttributeError: type object 'WhatIsLove' has no attribute 'baby_dont_hurt_me'

WhatIsLove.babyDontHurtMe
# 'no more'

haddaway = WhatIsLove()
haddaway.babyDontHurtMe
# 'no more'
```

Isso é muito legal, hein? Nossa função foi usada como metaclasse e dessa forma pudemos fazer modificações na variável de classe antes de instanciar o objeto!

## Finalmente, Metaprogramação

Agora que temos uma melhor ideia geral do que são metaclasses em Python e de como elas funcionam podemos conversar sobre metaprogramação que é escrever um programa que manipula outros programas. Isso geralmente é um pouco diferente do que geralmente fazemos ou desenvolvemos porque nossos programas manipulam dados; aqui, em metaprogramação, estamos manipulando código (se você sabe um pouco sobre LISP, por exemplo, deve ter uma noção maior de como manipular códigos como se fossem dados).

Vou dar dois exemplos bem curiosos sobre metaprogramação pra que você entenda melhor do que estou falando:

```shell
#!/bin/bash
# retirado daqui -> https://en.wikipedia.org/wiki/Metaprogramming#Code_generation

# Criamos um shebang para executar o programa
echo '#!/bin/bash' > program
# Instruções para o programar escrever os números 1-100
for i in $(seq 100)
do
	echo "echo $i" >> program
done
# Transformando o programa em executável
chmod +x program
```

Legal, o código bem simples acima é um programa que cria um programa que escreve na tela números de 1 à 100!

```python
#!/usr/bin/env python3
# Isso é um quine, um programa auto-replicável! Um código que escreve a si mesmo, que tal?
# Retirado daqui -> https://www.youtube.com/watch?v=a-zEbokJAgY&list=UUSHZKyawb77ixDdsGog4iWA
s='s=%r;print(s%%s)';print(s%s)
```

Esse é um exemplo de um [*quine*](https://pt.wikipedia.org/wiki/Quine_(inform%C3%A1tica)#:~:text=Em%20inform%C3%A1tica%2C%20um%20quine%20%C3%A9,pr%C3%B3prio%20c%C3%B3digo%20fonte%20do%20programa.) em Python, um código que quando executado se auto-replica (ou descreve seu código-fonte), tente executá-lo em seu interpretador Python!

This example is of a Python quine, a code that when executed replicates itself, try running it on your interpreter!

### Um exemplo mais útil

Bom, caso você não saiba, se você alguma vez criou um [Python decorator](https://realpython.com/primer-on-python-decorators/), você usou metaprogramação (tome um cafezinho, você merece!). Apenas para te relembrar, um decorador em Python é uma função que "embrulha" uma outra função ou uma classe e "muda seu comportamento/funcionamento". Vamos ver um exemplo bem simples:

```python
from functools import wraps
from urllib.request import urlopen, Request
import random

URL = "https://raw.githubusercontent.com/EugeneKay/git-jokes/lulz/Jokes.txt"
request = Request(URL)
response = urlopen(request)
jokes = response.read().decode("utf-8")
response.close()
jokes = jokes.split("\n")


def print_jokes(n_jokes=1):
    def inner(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            print("Some random jokes for you:\n")
            print("\n".join(random.choices(jokes, k=n_jokes)), sep="\n")
            return func(*args, **kwargs)
        return wrapper
    return inner


def helloworld():
    print("Hello World")

helloworld()
# Hello World

helloworld = print_jokes(n_jokes=5)(helloworld)

helloworld()
# Some random jokes for you:\n
# git off!
# What we push in life echoes in eternity
# May the forks be with you
# You can store your code in it!
# The problem with Git jokes ? well, everyone has their own version :)
# Hello World
```

Legal! Essa é a nossa primeira tentaiva de utilizar metaprogramação; como você pode ver, nós escrevemos um código que manipulava outro código, assim como se este fosse um "dado". E nós podemos fazer isso com classes também, e não somente com funções:

```python
def cls_wrapper(cls):
    for name, val in vars(cls).items():
        if callable(val):
            setattr(cls, name, print_jokes(n_jokes=2)(val))
    return cls


@cls_wrapper
class HelloWorld:
    def method1(self):
        print("method1 was called!")
    def method2(self):
        print("method2 was called!")
    def method3(self):
        print("method3 was called!")


hellow = HelloWorld()

hellow.method1()
# Some random jokes for you:\n
# git pull a day keeps the conflicts away
# I bet you thought this was a joke. Nope, git-testa.
# method1 was called!

hellow.method2()
# Some random jokes for you:\n\n
# hellow.method3()git-stash: The sock drawer of version control
# git-bisect: The good, the bad and the... uhh... skip
# method2 was called!

hellow.method3()
# Some random jokes for you:\n
# Don't forgit to bring a towel
# Be careful when rewriting history. It may push you to use the dark side of the --force
# method3 was called!
```

Viu? Conseguimos manipular uma classe usando um decorador de classes? Quão legal é isso?

### De volta às metaclasses

Até agora já conseguimos ver alguns casos de uso para metaclasses muito simples, mas como metaclasses **realmente são utilizadas** para metaprogramação em Python? Como que, por que usaríamos metaclasses ou metaprogramação?

Pra ser muito honesto, para maioria das aplicações, utilizar metaclasses provavelmente vai ser um exagero:(. Lembra do criador do "The Zen of Python" (PEP 20 ou `import this`), Tim Peter? Ele tem uma frase muito famosa sobre metaclasses (que honestamente, não consegui rastrear sua origem):

> “Metaclasses são magias profundas que 99% dos usuários não deveriam se preocupar. Se você se pergunta se precisa deles, você não precisa (as pessoas que realmente precisam deles sabem com certeza que precisam deles e não precisam de uma explicação sobre o porquê). ”
>
> Tim Peter (adaptado)

Mas, claro, existem alguns casos em que metaclasses e/ou metaprogramação são realmente bem-vindas e vou tentar fazer uma pequena lista delas

#### Design Patterns: Singleton

OK. Então você quer implementar o pattern Singleton em Python. Mas como você faz isso? Eu já vi alguns usos realmente muito espertos da implementação de singletons utilizando arquivos separados (módulos) e sempre importando deste módulo. Mas o problema aqui é que o seu objeto Singleton não consegue ser instanciado de forma *lazy*:

```python
#!/usr/bin/env python3
# file: singleton.py

class Singleton:
    pass

singleton = Singleton()

# Em outros arquivos/módulos você importaria assim
from singleton import singleton

# Agora basta usar a variável singleton como quiser ;D
```

Eu gosto bastante dessa estratégia porque "simples é melhor do que complexo" (PEP 20), mas vamos dar um passo pra trás e pensar nesta solução de um ponto de vista de metaprogramação: nós queremos, em tempo de execução, sempre retornar o mesmo objeto em vez de criar um novo quando instanciamos utilizando uma classe (ou, manipular código como se fosse um "dado")...

```python
class SingletonMetaclass(type):
    """Para criar uma metaclasse, você deve derivar de `type`"""
    _instances = {}
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            instance = super().__call__(*args, **kwargs)
            cls._instances[cls] = instance
        return cls._instances[cls]
    
    
class Singleton(metaclass=SingletonMetaclass):
    pass


singleton1 = Singleton()
singleton2 = Singleton()

assert singleton1 is singleton2, "Ooops, your singleton is bad and you should feel bad"
```

Perfeito! Conseguimos criar uma metaclasse que armazena em uma variável de classe suas instâncias e retorna um objeto se ele já existir. Justamente o que queremos quando pensamos em Singletons. O melhor é que esta metaclasse pode ser utilizada para diferentes Singletons!

### ABC Metaclasses

Dependendo do seu passado técnico, você já deve ter se perguntando como definir uma interface em Python. Pra ser 100% honesto, não existe uma coisa como "interface" em Python, principalmente porque a linguagem possui coisas como *duck typing* e *multi herança*. Mas, algumas vezes você quer garantir ou enforçar que usuários definam alguns métodos obrigatórios em uma classe, para isso nós utilizamos `ABC` ou [Classes Base Abstratas](https://docs.python.org/3.7/library/abc.html).

```python
from abc import ABCMeta, abstractmethod


class Shape(metaclass=ABCMeta):
    @abstractmethod
    def area(self):
        pass


class Square(Shape):
    def __init__(self, size):
        self.size = size


square = Square(size=5)
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# TypeError: Can't instantiate abstract class Square with abstract methods area

class Square(Shape):
    def __init__(self, size):
        self.size = size
    def area(self):
        return self.size ** 2


square = Square(size=5)
```

Viu? Agora usuários não conseguem instanciar objetos de uma classe que derive da nossa classe base `Shape` sem implementarem o método `area`!

### Registrando Objetos

Imagine um cenário onde você precisa gerencial múltiplos conectores de banco de dados em uma estrutura comum (um dicionário). Você pode usar metaclasses para automaticamente registrar novos conectores e previnir alguns erros de registro que antes deveriam ser feitos de forma manual.

```python
import inspect

_connectors = {}


def get_connection_handler_class(db):
    if db in _connectors:
        return _connectors[db]
    raise NotImplementedError(f"Could not find a connector for the {db}")


class RegisterMetaclass(type):
    """Automaticamente registra um novo conector"""
    def __new__(meta, name, bases, attrs):
        # Cria instância de classe do connector
        cls = type.__new__(meta, name, bases, attrs)
        # Registra o novo conector!
        _connectors[attrs["_db_connector"]] = cls
        return cls

   
class TeradataConnector(metaclass=RegisterMetaclass):
    _db_connector = "teradata"


class RedshiftConnector(metaclass=RegisterMetaclass):
    _db_connector = "redshift"


teradata = get_connection_handler_class(db="teradata")
assert inspect.isclass(teradata, TeradataConnector)


melilake = get_connection_handler_class(db="melilake")
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
#   File "<stdin>", line 4, in get_connection_handler_class
# NotImplementedError: Could not find a connector for the melilake
```

Fantástico, manter esse código agora é um pouco mais fácil, já que não precisamos registrar conectores nós mesmos, isso agora acontece automaticamente!

### APIs

Então, esta é uma das aplicações mais legais de metaprogramação e metaclasses. Geralmente, quando estamos criando um *framework* ou uma biblioteca, queremos reduzir a quantidade e a necessidade de código *boilerplate*, mantendo uma boa interface para a API. Você pode então usar metaclasses e metaprogramação para consegur isso. Ao invés de escrever código, desta vez vamos ver como essa técnica é utilizada "na natureza":

#### Django ORM

Se você já usou Django alguma vez, já deve estar familiarizado de como é fácil, simples e limpo definir um modelo ORM:

```python
from django.db import models


class Painting(models.Model):
    author = models.CharField(max_length=140)
    date = models.IntegerField()
    name = models.CharField(max_length=140)
    height = models.FloatField()
    width = models.FloatField()
    cost = models.FloatField()


painting = Painting(
    author="Vincent van Gogh",
    date=1889,
    name="The Starry Night",
    height=73.7,
    width=92.1
)

painting.author
# 'Vincent van Gogh'
```

Você pode encontrar mais detalhes [aqui](https://github.com/django/django/blob/2.1.7/django/db/models/base.py#L61), mas a interface para o usuário é tão legal! Em tempo de execução, Django precisa lidar com um monte de coisa, como:

- Ler esta definição de classe do modelo de dados e preparar novas classes especializadas para lidar com a funcionalidade necessária para o ORM
- Ligar todas as relações transversais implícitas e explícitas entre os diferentes modelos
- Lidar com todos os diferentes tipos de classes diferentes (proxys, abstracts, etc.)
- Lidar com as configurações nos metadados que vem do arquivo `settings.py` ou da classe `Meta` interna
- ...

Como podemos ver, tem muita coisa acontecendo debaixo do capô, mas, novamente, a interface para o usuário é tão simples! Essa é a magia da utilização de metaclasses e metaprogramação. Tem um post ótimo e muito detalhado de como Django utiliza metaclasses [aqui](https://medium.com/swlh/how-django-use-data-descriptors-metaclasses-for-data-modelling-14b307280fce).

### Linguagem Específica de Domínio (DSL)

DSLs (Linguagem Específica de Domínio) são amplamente utilizadas porque melhoram a comunicação entre desenvolvedores e especialistas de domínio. Em contraste com linguagens de programação "genéricas" (como Python e Java), DSLs são desenvolvidas para resolverem um problema específico. Alguns exemplos bem legais disso são SQL, XML, CSS e HTML. Você pode até mesmo enxergar na seção anterior os ORMs do Django como DSL.

Um exemplo muito interessante é o `argo-python-dsl` que foi criado para ajudar desenvolvedores a programaticamente definerem Argo Workflows em Python, que são então traduzidos para especificações/objetos Argo/Kubernetes.

```python
# Construindo um Workflow simples: um passo
from argo.workflows.dsl import Workflow
from argo.workflows.dsl import template

from argo.workflows.dsl.templates import V1Container


class HelloWorld(Workflow):

    entrypoint = "whalesay"

    @template
    def whalesay(self) -> V1Container:
        container = V1Container(
            image="docker/whalesay:latest",
            name="whalesay",
            command=["cowsay"],
            args=["hello world"]
        )

        return container
```

Este DSL traduz o código neste YAML:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  name: hello-world
  generateName: hello-world-
spec:
  entrypoint: whalesay
  templates:
  - name: whalesay
    container:
      name: whalesay
      image: docker/whalesay:latest
      command: [cowsay]
      args: ["hello world"]
```

Bem legal, hein? Você pode encontrar exemplos mais complexos no [repositório do GitHub](https://github.com/argoproj-labs/argo-python-dsl) da aplicação.

## Conclusão

Bem, aqui vimos um pouquinho sobre metaclasses em Python, mais especificamente `type`, e também vimos um pouco sobre casos de uso de metaprogramação. Eu espero que tenha sido uma boa introdução neste tema e que seja um conhecimento adquirido que possa ter algum bom uso!

## Referências

Kansal, S. Metaprogramming in Python. Disponível em: https://developer.ibm.com/technologies/analytics/tutorials/ba-metaprogramming-python/

StackOverflow, What are metaclasses in Python. Disponível em: https://stackoverflow.com/questions/100003/what-are-metaclasses-in-python

Sturtz, J. Python Metaclasses. Disponível em: https://realpython.com/python-metaclasses/

Bennet, J. Contributing to classiness (in Django). Disponível em: https://www.b-list.org/weblog/2019/mar/04/class/
