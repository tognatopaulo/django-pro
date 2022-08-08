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

## Criar .gihub/workflows/django.iml
```commandline

```