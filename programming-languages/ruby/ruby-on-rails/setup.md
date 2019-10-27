# Setup

I like to use Rails with postgres a lot, this is how to make a new Rails project with Postgres as the default database adapter:

```text
rails new myapp --database=postgresql
```

In some cases I use Rails without ActiveRecord \(e.g. [mongoid](https://github.com/mongodb/mongoid) for [MongoDB](https://www.mongodb.com/), like so:

```text
rails new myapp --skip-active-record
```

