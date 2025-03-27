# Hello Elixir Phoenix CI

Este projeto configura um pipeline de Integração Contínua (CI) para um aplicativo Elixir Phoenix, com o objetivo de garantir que todo o código passe por formatação, lint e testes automatizados. O pipeline inclui as seguintes etapas:

- **Formatação de código** com `mix format`
- **Análise de código estático** com `Credo`
- **Execução de testes** com `ExCoveralls` e cobertura de testes

## Objetivo

O objetivo deste repositório é configurar e verificar um processo CI que:

1. **Formate o código** automaticamente.
2. **Verifique a qualidade do código** com o `Credo`.
3. **Execute os testes** e gere relatórios de cobertura.

## Dependências

Este projeto utiliza as seguintes dependências para garantir que o código esteja formatado corretamente e passe pelos testes:

```elixir
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
{:tailwind, "~> 0.2", runtime: Mix.env() == :dev"},
{:swoosh, "~> 1.5"},
{:finch, "~> 0.13"},
{:telemetry_metrics, "~> 1.0"},
{:telemetry_poller, "~> 1.0"},
{:gettext, "~> 0.26"},
{:jason, "~> 1.2"},
{:credo, "~> 1.5", only: [:dev, :test], runtime: false},
{:excoveralls, "~> 0.10", only: :test}
