# User Interfaces

## Phoenix Web Interfaces

Phoenix makes an excellent companion to Nerves applications by offering an easy-to-use, powerful framework to create user interfaces in parallel with Nerves device code. The easiest way to handle this is to layout your application as an Umbrella. Lets get started...

First, generate a new umbrella app, called `nervy` in this case:

```
$ mix new nervy --umbrella
```

Next, create your sub-applications for Nerves and for Phoenix:

```
$ cd nervy/apps
$ mix nerves.new fw --target rpi3
...
$ mix phoenix.new ui --database sqlite
...
```

Now, add the Phoenix `ui` app and the `nerves_networking` library to the firmware app as dependencies:

```
defp deps do
  [{:ui, in_umbrella: true},
   {:nerves_networking, github: "nerves-project/nerves_networking"}]
end
```

In order to build the `ui` Phoenix application into the nerves firmware app, you need to add some configuration to your firmware config:

```elixir
# nervy/apps/firmware/config/config.exs

use Mix.Config

config :ui, Ui.Endpoint,
  http: [port: 80],
  url: [host: "localhost", port: 80],
  secret_key_base: "#############################",
  root: Path.dirname(__DIR__),
  server: true,
  render_errors: [accepts: ~w(html json)],
  pubsub: [name: Nerves.PubSub]

config :logger, level: :debug

# Configure your database
config :ui, Ui.Repo,
  adapter: Sqlite.Ecto,
  database: "/root/nerves.sqlite",
  pool_size: 20
```

There you have it! A Phoenix web application ready for your Nerves device. By separating the Phoenix application from the Nerves application, you can distribute the development between resources and continue to leverage the features we have all come to love from Phoenix, like live code reloading.

When developing your UI, you can simply run the phoenix server from the UI application:

```
# nervy/apps/ui
$ mix phoenix.server
```

When it's time to create your firmware:
```
# nervy/apps/firmware
$ mix firmware
```
