# Popurrí de soluciones en Ruby y en Rails 

Popurri de soluciones a cosas de Ruby y Rails.

Repo para la presentación del meetup Ruby Baq del 28 de Marzo.

## Cómo configurar llave primaria como UUID en Rails

Activamos pg_crypto:

```bash
rails g migration enable_pgcrypto_extension
```

Migración normal:

```bash
rails g model Article name:string author:references
```

Pero modificamos la opción del ID:

```ruby
class CreateArticles < ActiveRecord::Migration[6.0]
  def change
    create_table :articles, id: :uuid do |t| # <-- ESTO
      t.references :author, type: :uuid, foreign_key:true # <-- ESTO
      t.string :name
      t.timestamps
    end
  end
end
```

Al modificar columna se usa como tipo de dato:

```ruby
add_column :books, :user_id, :uuid
```

## Cómo escribir rake tasks con argumentos

## Cómo saber si un string tiene números con SQL

## Cómo Configurar GitHub Actions para correr RSpec

## Cómo Correr Migraciones Durante Despliegue de Aplicación Rails en Heroku

## Cómo Reiniciar Base de Datos PostgreSQL en Heroku

## Cómo Agregar Ejecutables al PATH en Linux

## Cómo traducir enums con i18n

## Cómo sobreescribir un método dado por un atributo de ActiveRecord

## Ejecutar otras rake tasks desde una rake task

## rails db:schema:load vs rails db:migrate
