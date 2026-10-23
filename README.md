# Sistema de Gestão de Oficina Mecânica

Aplicação web acadêmica para gerenciamento de uma oficina mecânica. O sistema permite autenticar usuários e administrar clientes, veículos, serviços e agendamentos por meio de uma interface web e de uma API JSON.

## Funcionalidades

- **Autenticação:** login e logout com sessão e expiração após 30 minutos de inatividade/tempo de sessão.
- **Clientes:** cadastro, listagem, pesquisa, edição e exclusão.
- **Veículos:** cadastro, listagem, edição e exclusão, vinculando cada veículo a um cliente.
- **Serviços:** consulta dos serviços cadastrados e seus valores.
- **Agendamentos:** criação, consulta, edição de data/status/observação e exclusão.
- **Validações:** validação de campos, formato de placa, ano do veículo, referências entre registros e prevenção de exclusões que deixariam vínculos inválidos.
- **Persistência:** banco de dados MySQL.
- **Segurança de dados:** senhas de usuários e CPFs de clientes são armazenados como hashes bcrypt no código da aplicação.

## Tecnologias utilizadas

- Python
- Flask 3.0.3
- MySQL 8 ou superior
- `mysql-connector-python` 9.0.0
- `bcrypt` 4.2.0
- HTML, CSS e JavaScript

## Estrutura do projeto

```text
sistema/
├── app.py                  # Inicialização da aplicação Flask
├── rotas.py                # Páginas web e endpoints da API
├── autenticacao.py         # Autenticação, hashes e controle de sessão
├── db.py                   # Conexão e operações com MySQL
├── clientes.py             # Regras de negócio de clientes
├── veiculos.py             # Regras de negócio de veículos
├── agendamento.py          # Regras de negócio de serviços e agendamentos
├── requirements.txt        # Dependências Python
├── saep_db.sql             # Criação e carga inicial do banco de dados
├── templates/
│   ├── login.html
│   ├── principal.html
│   ├── clientes.html
│   ├── veiculos.html
│   └── agendamentos.html
└── static/
    ├── app.js
    └── style.css
```

## Pré-requisitos

Antes de iniciar, instale:

1. Python 3.10 ou superior.
2. MySQL Server 8.0 ou superior.
3. Git, caso vá clonar o projeto.

## Instalação e execução

Os comandos abaixo são para Windows usando o Prompt de Comando ou PowerShell. Execute-os dentro da pasta `sistema`.

### 1. Criar e ativar um ambiente virtual

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
```

No Prompt de Comando (`cmd.exe`), a ativação é:

```bat
.venv\Scripts\activate.bat
```

### 2. Instalar as dependências

```powershell
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 3. Configurar o MySQL

Inicie o servidor MySQL e execute o script de criação do banco:

```bash
mysql -u root -p < saep_db.sql
```

O script cria o banco `saep_db_v1`, as tabelas e alguns registros de demonstração. **Atenção:** o script contém `DROP DATABASE IF EXISTS saep_db`; revise-o antes de executar em qualquer ambiente com dados importantes. Não execute scripts de inicialização sem verificar os comandos destrutivos.

### 4. Configurar as variáveis de ambiente

A aplicação lê as seguintes variáveis:

| Variável | Finalidade | Valor padrão no código |
|---|---|---|
| `SECRET_KEY` | Chave usada para assinar a sessão Flask | `troque-esta-chav-em-producao` |
| `DB_HOST` | Servidor MySQL | `localhost` |
| `DB_PORT` | Porta MySQL | `3306` |
| `DB_USER` | Usuário MySQL | `root` |
| `DB_PASSWORD` | Senha do usuário MySQL | Valor definido no código; configure a sua |
| `DB_NAME` | Nome do banco | `saep_db_v1` |

Configure `SECRET_KEY` e `DB_PASSWORD` no ambiente antes de iniciar. Não publique credenciais reais nem envie arquivos `.env` com senhas para o Git. Para um ambiente de produção, use uma chave secreta forte, credenciais de banco com permissões mínimas e configuração de depuração desativada.

Exemplo no PowerShell, válido apenas para a sessão atual:

```powershell
$env:SECRET_KEY = "substitua-por-uma-chave-longa-e-aleatoria"
$env:DB_HOST = "localhost"
$env:DB_PORT = "3306"
$env:DB_USER = "seu_usuario_mysql"
$env:DB_PASSWORD = "sua_senha_mysql"
$env:DB_NAME = "saep_db_v1"
```

### 5. Iniciar a aplicação

```powershell
python app.py
```

A aplicação será iniciada na porta `5000`. Acesse no navegador:

- Aplicação: http://127.0.0.1:5000/
- Login: http://127.0.0.1:5000/login

O código atual inicia o Flask com `debug=True`; isso é apropriado somente para desenvolvimento local. Desative o modo debug antes de qualquer implantação.

## Usuários de demonstração

O arquivo `saep_db.sql` insere usuários de teste:

| Login | Senha de demonstração |
|---|---|
| `admin` | `admin123` |
| `carlos` | `carlos123` |
| `ana` | `ana12345` |

Use essas credenciais apenas no ambiente de demonstração. Altere ou remova os usuários de teste antes de disponibilizar o sistema a outras pessoas.

## Principais rotas da API

As rotas de API abaixo, exceto as rotas de autenticação e teste de conexão, exigem sessão autenticada.

| Método | Endpoint | Descrição |
|---|---|---|
| `POST` | `/api/login` | Autentica usuário |
| `POST` | `/api/logout` | Encerra a sessão |
| `GET` | `/api/me` | Retorna o usuário da sessão |
| `GET` | `/api/teste-conexao` | Testa a conexão com o banco |
| `GET` | `/api/clientes?q=termo` | Lista/pesquisa clientes |
| `POST` | `/api/clientes` | Cadastra cliente |
| `PUT` | `/api/clientes/<id>` | Edita cliente |
| `DELETE` | `/api/clientes/<id>` | Exclui cliente |
| `GET` | `/api/veiculos` | Lista veículos |
| `POST` | `/api/veiculos` | Cadastra veículo |
| `PUT` | `/api/veiculos/<id>` | Edita veículo |
| `DELETE` | `/api/veiculos/<id>` | Exclui veículo |
| `GET` | `/api/servicos` | Lista serviços |
| `GET` | `/api/agendamentos` | Lista agendamentos |
| `POST` | `/api/agendamentos` | Cadastra agendamento |
| `PUT` | `/api/agendamentos/<id>` | Edita data, status e observação |
| `DELETE` | `/api/agendamentos/<id>` | Exclui agendamento |

As operações de cadastro e edição recebem dados em JSON. Em caso de erro de validação, a API retorna um objeto JSON com a propriedade `erro` e o código HTTP correspondente.

### Status dos agendamentos

- `AGENDADO`
- `EM_ANDAMENTO`
- `CONCLUIDO`
- `CANCELADO`

## Banco de dados

O banco `saep_db_v1` utiliza as tabelas:

- `usuarios`: contas de acesso.
- `clientes`: dados de clientes e hash do CPF.
- `veiculos`: veículos associados a clientes.
- `servicos`: catálogo de serviços e valores.
- `agendamentos`: data, status, observações e vínculos com veículo e serviço.

O CPF é armazenado com bcrypt. Como o bcrypt gera hashes com salt, eles não podem ser consultados com `LIKE`; a implementação compara o CPF informado com os hashes existentes. A validação de CPF no código verifica apenas se há 11 dígitos, não os dígitos verificadores.

## Verificação da conexão

Com o ambiente virtual ativo e as variáveis de banco configuradas, execute:

```powershell
python testar_conexao.py
```

O projeto também contém `db_exemplo.py`, que pode servir como referência para testar operações de banco. Confira o conteúdo desses scripts antes de executá-los.

## Segurança e limitações conhecidas

Este projeto é acadêmico e precisa de revisão antes de ser usado em produção.

- O `app.py` usa uma chave de sessão padrão se `SECRET_KEY` não estiver configurada e inicia com `debug=True`.
- O módulo `db.py` define uma senha padrão para o banco. Configure credenciais por ambiente e evite armazenar segredos no repositório.
- O script SQL inclui usuários, clientes, veículos e agendamentos de demonstração. Use apenas dados fictícios em ambientes de teste.
- A validação do CPF verifica a quantidade de dígitos, não a validade matemática dos dígitos verificadores.
- A busca por CPF compara hashes bcrypt individualmente e pode ficar lenta com muitos clientes.
- O uso de hash bcrypt para CPF protege contra armazenamento em texto puro, mas não permite recuperar o CPF original; avalie os requisitos de consulta e proteção de dados antes de usar em um cenário real.
- O script SQL contém um comando de exclusão de banco (`DROP DATABASE IF EXISTS saep_db`). Revise o arquivo e faça backup antes de executar scripts de banco.
- O servidor de desenvolvimento Flask não deve ser exposto diretamente à internet.

## Solução de problemas

**Erro de conexão com o MySQL**
- Confirme que o serviço MySQL está ativo.
- Verifique `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASSWORD` e `DB_NAME`.
- Confirme que o banco `saep_db_v1` foi criado.

**Módulo Python não encontrado**
- Ative o ambiente virtual e execute `pip install -r requirements.txt`.

**Redirecionamento para o login**
- Faça login novamente; a sessão tem duração limitada.

**Erro ao excluir cliente ou veículo**
- Um cliente com veículos vinculados não pode ser excluído.
- Um veículo com agendamentos vinculados não pode ser excluído.

## Licença

Nenhuma licença específica foi identificada nos arquivos analisados. Consulte o responsável pelo projeto antes de reutilizar ou distribuir o código.
