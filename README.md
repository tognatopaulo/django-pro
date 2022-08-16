# django-pro

## Criar repositório (readme, python, mit licence)

## Clonar repositório

## Criar ambiente virtual para o repositório com pipenv

```
pip install pipenv
```

## Instalar pacotes via pipenv para criar pipfile

```
pipenv install django gunicorn
pipenv shell
```

## Iniciar projeto webdev na raiz

```
django-admin startproject webdev .
```

## Criar uma conta no Heroku

## Download Heroku CLI

## Fazer o login no CLI do Heroku

```
heroku login -i
```

## Criar o aplicativo no heroku

```
.\heroku\bin\heroku create django-pythonpro-paulotognato
```

## Conectar a app do heroku com o repositório do git

- Em Settings add buildpack python
- Em Deploy conectar com o repo do git e selecionar Enable Automatic Deploy

## Abrir uma IDE (Pycharm) na pasta do projeto

- CTRL + ALT + A - Adiciona os arquivos para o gerenciados de arquivos
- CTRK + K - Envio os arquivos

## Desabilite a coleta de arquivos estaticos

```
heroku config:set DISABLE_COLLECTSTATIC=1
```

## Criar o arquivo Procfile

```
web: gunicorn webdev.wsgi --log-file -
```

## Criar ISSUE 1 "Implementar integração continua"

## Criar nova branch no pychar "branch 1"

## Instalar o pytest no ambiente de dev

```
pipenv install -d pytest-django
```

## Pycharm Settings >> Python Integrated Tools >> Select Pytest

## Criar arquivo test_home.py

```
from django.test import Client

def test_home_status_code(client:Client):
    resposta = client.get('/')
    assert resposta.status_code == 200
```

## Criar o arquivo setup.cfg na raiz do projeto

```commandline
[tool:pytest]
DJANGO_SETTINGS_MODULE = webdev.settings
# -- recommended but optional:
python_files = tests.py test_*.py *_tests.py
```

## Criar o arquivo home_view.py dentro do projeto

```commandline
def home(request):
    pass
```

## Atualizar o arquivo urls.py dentro do projeto

```commandline
    path('', home)
```

## Atualizar o arquivo home_view.py dentro do projeto

```commandline
def home(request):
    return HttpResponse('Olá Django Pro')
```

## Rodar o pytest via cli

```commandline
pipenv run pytest .\webdev\
```

## Criar .gihub/workflows/django.yml

```commandline
name: Aplicação Django

on: [pull_request]

jobs:
  build:
    env:
      PIPENV_NO_INHERIT: 1
      PIPENV_IGNORE_VIRTUALENVS: 1
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.10.5
      uses: actions/setup-python@v1
      with:
        python-version: 3.10.5
    - name: Instalar Dependências
      run: |
        pip install --upgrade pip
        pip install pipenv
        pipenv sync --dev
    - name: Rodar Testes Automáticos
      run: |
        pipenv run pytest webdev
```

## Criado app

```commandline
python.exe ..\manage.py startapp tarefas
```

## Criado tarefas/tests/test_tarefas_get.py

```commandline
from django.urls import reverse


def test_status_code(client):
    resposta = client.get(reverse('tarefas:home'))
    assert resposta.status_code == 200
```

## criado tarefas/urls.py

```commandline
from django.urls import path

from webdev.tarefas import views

app_name='tarefas'

urlpatterns = [
    path('', views.home, name='home'),
]
```

## apontar o tarefas/urls.py para o arquivo de urls global

```commandline
from django.contrib import admin
from django.urls import path, include

from webdev.home_view import home

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', home),
    path('tarefas/', include('webdev.tarefas.urls'))
]
```

## Incluir os novos testes para formulário e botao submit

```commandline
import pytest
from django.urls import reverse
from pytest_django.asserts import assertContains

@pytest.fixture
def resposta(client):
    resp = client.get(reverse('tarefas:home'))
    return resp


def test_status_code(resposta):
    assert resposta.status_code == 200
```

## Alterar o type do botao para submit, incluir a action e method no formulário e incluir o token csrf no formulário

```commandline
            <form action="{% url 'tarefas:home' %}" method="post">
                {% csrf_token %}
                <div class="input-group mb-3">
                    <input type="text" class="form-control" placeholder="Descreva sua tarefa"
                           aria-label="Descreva sua tarefa"
                           aria-describedby="button-addon2" name="nome">
                    <button type="submit" class="btn btn-primary" id="button-addon2">Salvar</button>
                </div>
            </form>
```

## Criar test_tarefas_post.py
```commandline
import pytest
from django.urls import reverse
from webdev.tarefas.models import Tarefa

@pytest.fixture
def resposta(client):
    resp = client.post(reverse('tarefas:home'), data={'nome': 'Tarefa'})
    return resp


def test_tarefa_existe_no_bd(resposta):
    assert Tarefa.objects.exists
```

## Criar o modelo do banco de dados:
```commandline
from django.db import models

# Create your models here.
class Tarefa(models.Model):
    nome = models.CharField(max_length=128)
    feita = models.BooleanField(default=False)
```

## Criar a marcação para o bd no teste:
```
@pytest.fixture
def resposta(client, db):
    resp = client.post(reverse('tarefas:home'), data={'nome': 'Tarefa'})
    return resp
```

## Criar a migração do banco de dados

## Criar o formulário para testar
```commandline
from django.forms import ModelForm

from webdev.tarefas.models import Tarefa


class TarefaNovaForm(ModelForm):
    class Meta:
        model = Tarefa
        fields = ['nome']
```

## Atualizar a view para uso do formulario de validação
```commandline
from django.shortcuts import render, HttpResponse

# Create your views here.
from webdev.tarefas.forms import TarefaNovaForm


def home(request):
    if request.method == 'POST':
        form = TarefaNovaForm(request.POST)
        if form.is_valid():
            form.save()
    return render(request, 'tarefas/home.html')
```

## aplicar as migrações
```commandline
python manage.py migrate
```

## Incluir novos testes de redirecionamento quando o dado é valido e erro quando o dado é invalido
```commandline
import pytest
from django.urls import reverse
from webdev.tarefas.models import Tarefa

@pytest.fixture
def resposta(client, db):
    resp = client.post(reverse('tarefas:home'), data={'nome': 'Tarefa'})
    return resp


def test_tarefa_existe_no_bd(resposta):
    assert Tarefa.objects.exists()


def test_redirecionamento_depois_do_salvamento(resposta):
    assert resposta.status_code == 302

@pytest.fixture
def resposta_dado_invalido(client, db):
    resp = client.post(reverse('tarefas:home'), data={'nome': ''})
    return resp

def test_tarefa_nao_existe_no_bd(resposta_dado_invalido):
    assert not Tarefa.objects.exists()

def test_pagina_com_dados_invalidos(resposta_dado_invalido):
        assert resposta_dado_invalido.status_code == 400

```

## Incluir retorno da view para quando os dados são validos e invalidos
```commandline
def home(request):
    if request.method == 'POST':
        form = TarefaNovaForm(request.POST)
        if form.is_valid():
            form.save()
            return HttpResponseRedirect(reverse('tarefas:home'))
        else:
            return render(request, 'tarefas/home.html', {'form': form}, status=400)

    return render(request, 'tarefas/home.html')
```

## Atualizar a home.html para incluir a msg de erro
```commandline
                <div class="form-text">{{ form.nome.errors }}</div>
```

## Atualizar o settings.py para mostrar os erros em portugues
```commandline
LANGUAGE_CODE = 'pt-br'
```