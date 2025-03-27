Hello Elixir Phoenix CI
Este projeto configura um pipeline de Integração Contínua (CI) para um aplicativo Elixir Phoenix, com o objetivo de garantir que todo o código passe por formatação, lint e testes automatizados. O pipeline inclui as seguintes etapas:

Formatação de código com mix format

Análise de código estático com Credo

Execução de testes com ExCoveralls e cobertura de testes

Objetivo
O objetivo deste repositório é configurar e verificar um processo CI que:

Formate o código automaticamente.

Verifique a qualidade do código com o Credo.

Execute os testes e gere relatórios de cobertura.

Dependências
Este projeto utiliza as seguintes dependências para garantir que o código esteja formatado corretamente e passe pelos testes:

elixir
Copiar
Editar
{:phoenix, "~> 1.7.20"},
{:phoenix_ecto, "~> 4.5"},
{:ecto_sql, "~> 3.10"},
{:postgrex, ">= 0.0.0"},
{:phoenix_html, "~> 4.1"},
{:phoenix_live_reload, "~> 1.2", only: :dev},
{:phoenix_live_view, "~> 1.0.0"},
{:floki, ">= 0.30.0", only: :test},
{:phoenix_live_dashboard, "~> 0.8.3"},
{:esbuild, "~> 0.8", runtime: Mix.env() == :dev},
{:tailwind, "~> 0.2", runtime: Mix.env() == :dev},
{:swoosh, "~> 1.5"},
{:finch, "~> 0.13"},
{:telemetry_metrics, "~> 1.0"},
{:telemetry_poller, "~> 1.0"},
{:gettext, "~> 0.26"},
{:jason, "~> 1.2"},
{:credo, "~> 1.5", only: [:dev, :test], runtime: false},
{:excoveralls, "~> 0.10", only: :test}
Configuração do CI
1. Formatação de código
O pipeline de CI roda o comando mix format --check-formatted para garantir que todo o código esteja devidamente formatado. A configuração está no arquivo formatter.yml.

yaml
Copiar
Editar
name: Verify format

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main
jobs:
  check_format:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        elixir: [1.17.3]
        otp: [26]
    steps:
      - uses: actions/checkout@v2
      - uses: erlef/setup-elixir@v1
        with:
          elixir-version: ${{ matrix.elixir }}
          otp-version: ${{ matrix.otp }}
      - uses: actions/cache@v3
        id: mix-cache-format
        with:
          path: deps
          key: ${{ runner.os }}-${{ matrix.otp }}-${{ matrix.elixir }}-mix-${{ hashFiles('**/mix.lock') }}
      - name: Install Mix dependencies
        run: mix deps.get
        if: steps.mix-cache-format.outputs.cache-hit != 'true' || steps.mix-cache-format.outputs.cache-hit == ''
      - name: Clear cache
        run: rm -rf deps
      - name: Format
        run: mix format --check-formatted
2. Linting com Credo
O CI também executa o comando mix credo --strict para garantir que o código esteja em conformidade com as boas práticas de estilo e design. O arquivo de configuração está no lint.yml.

yaml
Copiar
Editar
name: Verify Lint

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main
jobs:
  lint:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        elixir: [1.17.3]
        otp: [26]
    steps:
      - uses: actions/checkout@v2
      - uses: erlef/setup-elixir@v1
        with:
          elixir-version: ${{ matrix.elixir }}
          otp-version: ${{ matrix.otp }}
      - uses: actions/cache@v3
        id: mix-cache-lint
        with:
          path: deps
          key: ${{ runner.os }}-${{ matrix.otp }}-${{ matrix.elixir }}-mix-${{ hashFiles('**/mix.lock') }}
      - name: Install Mix dependencies
        run: mix deps.get
        if: steps.mix-cache-lint.outputs.cache-hit != 'true' || steps.mix-cache-format.outputs.cache-hit == ''
      - name: Credo Lint
        run: mix credo --strict
3. Testes e Cobertura
O pipeline também executa os testes do projeto e gera relatórios de cobertura com o ExCoveralls. A configuração está no arquivo tests.yml.

yaml
Copiar
Editar
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        elixir: [1.17.3]
        otp: [26]
    steps:
      - uses: actions/checkout@v2
      - uses: erlef/setup-elixir@v1
        with:
          elixir-version: ${{ matrix.elixir }}
          otp-version: ${{ matrix.otp }}
      - uses: actions/cache@v3
        id: mix-cache-coverage
        with:
          path: deps
          key: ${{ runner.os }}-${{ matrix.otp }}-${{ matrix.elixir }}-mix-${{ hashFiles('**/mix.lock') }}
      - name: Wait for PostgreSQL to be ready
        run: |
          until pg_isready -h localhost -p 5432 -U postgres; do
            echo "Waiting for PostgreSQL..."
            sleep 15
          done
      - name: Install Mix dependencies
        run: |
          mix local.rebar --force
          mix local.hex --force
          mix deps.get
          mix ecto.create
      - name: Verify Coverage and tests
        run: mix coveralls.html
    services:
      postgres:
        image: postgres:17.4-alpine
        ports: ['5432:5432']
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: hello_test
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
Como executar localmente
Pré-requisitos
Certifique-se de que as seguintes ferramentas estão instaladas em seu sistema:

Elixir

Phoenix

PostgreSQL

Executar o projeto
Clone este repositório.

Instale as dependências com:

bash
Copiar
Editar
mix deps.get
Configure o banco de dados (se necessário):

bash
Copiar
Editar
mix ecto.create
Execute os testes e verifique a cobertura:

bash
Copiar
Editar
mix test
Executando o pipeline CI manualmente
Embora o CI seja configurado para ser executado automaticamente, você pode rodar os passos de formatação, lint e testes manualmente com os seguintes comandos:

bash
Copiar
Editar
# Verificar formatação
mix format --check-formatted

# Verificar lint
mix credo --strict

# Executar testes e cobertura
mix test
Contribuindo
Sinta-se à vontade para contribuir para este projeto! Se você encontrar algum bug ou tiver sugestões de melhorias, abra uma issue ou envie um pull request.

Com esse README, você terá uma documentação clara sobre o que é o projeto, como configurar o ambiente localmente e como o pipeline CI está configurado para garantir qualidade e formato consistentes no código.