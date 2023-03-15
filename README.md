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

[Fuente](https://otroespacioblog.wordpress.com/2022/10/03/como-configurar-llave-primaria-como-uuid-en-rails/).

## Cómo escribir rake tasks con argumentos

La sintaxis es así:

```ruby
task :name, [:arg_1, :arg_2] => :environment do |task, args|
  # some code
end
```

Ejemplo

```ruby
task :name, [:first_name, :last_name] => :environment do |t, args|
  args.with_defaults(:first_name => "John", :last_name => "Dough")
  
  puts "First name is #{args[:first_name]}"
  puts "Last  name is #{args[:last_name]}"
end
```

De esta forma se ejecuta

```bash
rake name[Francisco,Quintero]
```

[Fuente](https://otroespacioblog.wordpress.com/2022/10/01/como-escribir-rake-tasks-con-argumentos/).

## Cómo saber si un string tiene números con SQL

Con un string de esta forma "1" en vez de "I am Cristianu Runalduu"

Primer intento

```sql
SELECT id, form_id, content
FROM answers
WHERE content LIKE '%[0-9]%';
 
 id | form_id | content 
----+---------+---------
(0 rows)
```

Cambiando un poco la forma para asegurarnos que la expresión funciona

```sql
SELECT id, form_id, content
FROM answers
WHERE content NOT LIKE '%[0-9]%'
LIMIT 10;
 
  id  | form_id |                                                            content                                                             
------+---------+--------------------------------------------------------------------------------------------------------------------------------
 1339 |       6 | I have no pain at the moment
 1341 |       6 | I can lift heavy weights but it gives extra pain
 1342 |       6 | Pain prevents me from walking more than 1 mile
 1343 |       6 | I can only sit in my favourite chair as long as I like
 1344 |       6 | Pain prevents me from standing for more than 1 hour
 1345 |       6 | My sleep is occasionally disturbed by pain
```

Pero la solución es de otra forma

```sql
SELECT id,content,REGEXP_MATCHES(content, '[[:digit:]]')
FROM answers
WHERE option_choice_id IS NULL;
 
  id  |                            content                             | regexp_matches 
------+----------------------------------------------------------------+----------------
 1342 | Pain prevents me from walking more than 1 mile                 | {1}
 1344 | Pain prevents me from standing for more than 1 hour            | {1}
   29 | My sleep is slightly disturbed (less than 1 hr sleepless)      | {1}
   24 | I can read as much as I want to with slight pain in my neck 2  | {2}
  276 | 2                                                              | {2}
  287 | 3                                                              | {3}
   44 | Pain prevents me from walking more than 1/2 mile               | {1}
```

De esta forma sí obtenemos resultados.

[Fuente](https://otroespacioblog.wordpress.com/2022/09/30/como-saber-si-un-string-tiene-numeros-con-sql/).

## Cómo Configurar GitHub Actions para correr RSpec

## Cómo Correr Migraciones Durante Despliegue de Aplicación Rails en Heroku

Se hace en el _release phase_ indicando en un archivo Procfile.

```
# Procfile
 
release: bundle exec rails db:migrate
web: bundle exec puma -C config/puma.rb
```

[Fuente](https://otroespacioblog.wordpress.com/2019/11/10/como-correr-migraciones-durante-despliegue-de-aplicacion-rails-en-heroku/).

## Cómo Reiniciar Base de Datos PostgreSQL en Heroku

De esta forma no funciona

```
heroku run rake rails db:drop
```

Se hace así

```
heroku pg:reset DATABASE
```

O así para evitar el paso de confirmación de la app

```
heroku pg:reset DATABASE --confirm NOMBRE-DE-APP
```

[Fuente](https://otroespacioblog.wordpress.com/2019/11/08/como-reiniciar-base-de-datos-postgresql-en-heroku/).

## Cómo Agregar Ejecutables al PATH en Linux

## Cómo traducir enums con i18n

## Cómo sobreescribir un método dado por un atributo de ActiveRecord

## Ejecutar otras rake tasks desde una rake task

## rails db:schema:load vs rails db:migrate
