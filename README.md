---
title: OTPCL (Open Telecom Platform Command Language)
---
# What is it?

OTPCL is a scripting language designed to integrate with and extend applications written in BEAM-based languages (Erlang, Elixir, LFE, etc.).

# How do I use it?

## Standalone

* Make sure you've installed a relatively recent Erlang/OTP distribution (currently tested and developed against Erlang/OTP 21, but older versions should work to some extent)

* Clone the OTPCL git repo: `git clone https://github.com/otpcl/otpcl.git && cd otpcl`

* Build OTPCL: `rebar3 compile`

* Try it out:

```
$ bin/otpcl
OTPCL Shell (WIP!)

otpcl> print "Hello, world!~n"
Hello, world!
ok
```

## Within an Erlang application (using rebar3)

* Add OTPCL to your `rebar.config`:

```erlang
{erl_opts, [debug_info]}.
{deps, [{otpcl, "0.1.0"}]}.

{shell, [
  % {config, "config/sys.config"},
    {apps, [mylib]}
]}.
```

* Try it out:

```
$ rebar3 shell
Eshell V10.0 (abort with ^G)
1> otpcl:eval("import io; format {Hello, world!~n}").
Hello, world!
[... a bunch of interpreter state ...]
```

## Within an Elixir application (using Mix)

* Add OTPCL to your `mix.exs`:

```elixir
defmodule OtpclMixTest.MixProject do
  use Mix.Project

  def project do
    [
      app: :otpcl_mix_test,
      version: "0.1.0",
      elixir: "~> 1.7",
      start_permanent: Mix.env() == :prod,
      deps: deps()
    ]
  end

  # Run "mix help compile.app" to learn about applications.
  def application do
    [
      extra_applications: [:logger]
    ]
  end

  # Run "mix help deps" to learn about dependencies.
  defp deps do
    [
      {:otpcl, "~> 0.1.0"}
    ]
  end
end
```

* Get dependencies: `mix do deps.get, deps.compile`

* Try it out:

```
$ iex -S mix
[... mix compilation output ...]
Interactive Elixir (1.7.3) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> :otpcl.eval "import Elixir.IO; puts {Hello, world!}"
Hello, world!
[... a bunch of interpreter state ...]
```
