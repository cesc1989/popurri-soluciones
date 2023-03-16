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

```yaml
env:
  POSTGRES_USER: postgres
  POSTGRES_PASSWORD: postgres
  RAILS_ENV: test
 
name: Rails tests
on: [push, pull_request]
jobs:
  rspec-test:
    name: RSpec
    runs-on: ubuntu-18.04
    services:
      postgres:
        image: postgres:latest
        ports:
          - 5432:5432
        options: >-
          --mount type=tmpfs,destination=/var/lib/postgresql/data
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: nsac_testing
    steps:
      - uses: actions/checkout@v1
      - uses: ruby/setup-ruby@v1
        with:
          ruby-version: 2.7.1
          bundler-cache: true
      - name: Install postgres client
        run: sudo apt-get install libpq-dev
      - name: Prepare database
        run: bundler exec rails db:prepare RAILS_ENV=test
      - name: Run tests
        run: bundler exec rake
```

[Fuente](https://otroespacioblog.wordpress.com/2021/04/28/como-configurar-github-actions-para-correr-rspec/).

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

Primero que nada, tener configurado el idioma en `config/application.rb`

```ruby
config.i18n.default_locale = :es
```

Para un enum:

```ruby
class Pqr < ApplicationRecord
  enum status: { unassigned: 0, assigned: 1, closed: 2 }
end
```

Se configura la traducción así:

```ruby

# config/locales/es.yml
es:
  pqr_status:
    unassigned: 'Sin asignar'
    assigned: 'Asignado'
    closed: 'Cerrado'
```

Y se usan en la vista:

```ruby
<%= I18n.t(:"pqr_status.#{pqr.status}") %>
```

## Cómo sobreescribir un método dado por un atributo de ActiveRecord

## Ejecutar otras rake tasks desde una rake task

Así:

```ruby
namespace :production_bootstrap do
  desc 'Setup production'
  task :run_all do
    Rake::Task['categories:run_all'].invoke
    Rake::Task['categories:create_for_suppliers'].invoke
    Rake::Task['infractions:create_types'].invoke
    Rake::Task['vehicles:run_all'].invoke
    Rake::Task['cities:create_basic_cities'].invoke
    Rake::Task['countries:create_countries'].invoke
    Rake::Task['admins_and_owners:create_admins'].invoke
    Rake::Task['admins_and_owners:create_owners'].invoke
  end
end
```

[Fuente](https://stackoverflow.com/a/1290119/1407371).

## rails db:schema:load vs rails db:migrate

¿Qué diferencia hay entre estos dos comandos al momento de iniciar/configurar un proyecto?

La principal diferencia es que `rails db:schema:load` debe usarse unicamente la primera vez que se inicie el sistema. Sea local o en producción.

**Este comando es destructivo.** Si lo ejecutas en un ambiente con datos, vas a perder todo lo que haya en la base de datos.

Usa `rails db:migrate` para ir agregando nuevas modificaciones a la base de datos.

**Conclusión**

- Primer vez que carga el sistema: `rails db:schema:load`
- De ahí en adelante, solo `rails db:migrate`

[Fuente](https://stackoverflow.com/a/5905958/1407371).

## has_many sintaxis y bloque para extender el macro

La sintaxis

```ruby
has_many(name, scope = nil, **options, &extension) public
```

Para darle un scope por defecto a la asociación

```ruby
has_many :comments, -> { where(author_id: 1) }
```

Extender su comportamiento para hacer algo más complejo

```ruby
has_many :children, :dependent => :destroy do
  def at(time)
    proxy_owner.children.find_with_deleted :all, :conditions => [
      "created_at <= :time AND (deleted_at > :time OR deleted_at IS NULL)", { :time => time }
    ]        
  end      
end
```
