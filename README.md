# Popurrí de workarounds en Ruby y en Rails 

Popurri de soluciones a cosas de Ruby y Rails.

## 1. Cómo configurar llave primaria como UUID en Rails

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
