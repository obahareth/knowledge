# Gotchas

## ActiveRecord

* `find` raises an exception that if not handled, will result in controller endpoints returning 404.
* `find_by!` is a variant that can be used to get the same behavior of `find` for `find_by`.

## database.yml

If you try to have a generic database configuration controlled by environment variables, your database actions will run twice in development.

e.g.

```yaml
default: &default
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV['DATABASE_POOL'] %>
  host: <%= ENV['DATABASE_HOST'] %>
  database: <%= ENV['DATABASE_NAME'] %>
  username: <%= ENV['DATABASE_USER'] %>
  password: <%= ENV['DATABASE_PASSWORD'] %>

development:
  <<: *default

test:
  <<: *default
```

Since Rails by default runs migrations for the `test` environment when you're on `development` , when it tries to run them the `RAILS_ENV` will be `development` so it will actually run all the database actions on the development database again.

Instead ensure that you give specific and unique names for the development and test environments:

```yaml
development:
  <<: *default
  database: my_app_development

test:
  <<: *default
  database: my_app_test
```

