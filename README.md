# Listar os 100 primeiros arquivos e pastas em seu Google Drive

*Estou seguindo o tutorial http://g.co/codelabs/gsuite-apis-intro usando o link https://developers.google.com/drive/api/v3/quickstart/python como apoio*

## Pré-requisitos

Rodar o seguinte comando no terminal:

*pip install --upgrade google-api-python-client google-auth-httplib2 google-auth-oauthlib*

## Código

Pacotes necessários


```python
from __future__ import print_function
import os.path
from googleapiclient.discovery import build
from google_auth_oauthlib.flow import InstalledAppFlow
from google.auth.transport.requests import Request
from google.oauth2.credentials import Credentials
```

Instanciando objeto de Array para listar o scopo da API


```python
SCOPES = ['https://www.googleapis.com/auth/drive.metadata.readonly']
```

Instanciaremos objeto de acesso às credenciais da API por OAuth


```python
flow = InstalledAppFlow.from_client_secrets_file('client_secret.json', SCOPES)
```

Instanciaremos objeto de credencial, seja por arquivo ou por OAuth


```python
creds = None
if os.path.exists('token.json'):
    creds = Credentials.from_authorized_user_file('token.json', SCOPES)
if not creds or not creds.valid:
    if creds and creds.expired and creds.refresh_token:
        creds.refresh(Request())
    else:
        creds = flow.run_local_server(port=0)
```

Salvando as credenciais para a próxima vez que rodarmos o código


```python
with open('token.json', 'w') as token:
    token.write(creds.to_json())
```

Finalmente instanciaremos o objeto de acesso à Google Workspace API


```python
service = build('drive', 'v3', credentials=creds)
```

Instanciaremos o objeto de acesso à api de listagem dos arquivos


```python
results = service.files().list(pageSize=10, fields="nextPageToken, files(id, name)").execute()
```

Instanciaremos os objetos de Array contendo a lista de arquivos no meu Google Drive


```python
items = results.get('files', [])
```

Imprimiremos os nomes dos arquivos e os códigos, se houver


```python
if not items:
    print('No files found.')
else:
    print('Files:')
    for item in items:
        print(u'{0} ({1})'.format(item['name'], item['id']))
```

    Files:
    DIP3E_Chapter10_Art.pptx (1fJ5U2dLsTOg3XzA3W213WWCfYhfFs9SB)
    1583474114110 (1).pdf (1n4ecsBhOGFecF3yqRDtUwURoe0lA7A3K)
    Currículo do Sistema de Currículos Lattes (Yoshio Mori)_compressed.pdf (1gjj4xpnP26C60HYBq_UffPfLs9fIsVWM)
    README (1B7jXqIblGg_exDg2DGQLY_nmOKVcxBUK4VY9XF1wyM4)
    
