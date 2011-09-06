Mongoid Slug
============

Mongoid Slug generates a URL slug or permalink based on one or more
fields in a Mongoid model. It sits idly on top of [stringex] [1] and
works with non-Latin characters.

[![travis](https://secure.travis-ci.org/hakanensari/mongoid-slug.png)](http://travis-ci.org/hakanensari/mongoid-slug)

Quick Start
-----------

Add mongoid_slug to your Gemfile:

```ruby
gem 'mongoid_slug'
```

Set up some slugs:

```ruby
class Book
  include Mongoid::Document
  include Mongoid::Slug

  field :title
  embeds_many :authors

  slug :title
end

class Author
  include Mongoid::Document
  include Mongoid::Slug

  field :first
  field :last
  embedded_in :book, :inverse_of => :authors

  slug :first, :last, :as => :name
end
```

In your controller, use available finders:

```ruby
# GET /books/a-thousand-plateaus/authors/gilles-deleuze
author = Book.find_by_slug(params[:book_id]).
              authors.
              find_by_name(params[:id])
```

[Read here] [2]
for all available options.

Scoping
-------

To scope a slug by a reference association, pass `:scope`:

```ruby
class Company
  include Mongoid::Document
  references_many :employees
end

class Employee
 include Mongoid::Document
 include Mongoid::Slug
 field :name
 slug  :name, :scope => :company
 referenced_in :company
end
```

In this example, if you create an employee without associating it with
any company, the scope will fall back to the root employees collection.

Currently, if you have an irregular association name, you **must**
specify the `:inverse_of` option on the other side of the assocation.

Embedded objects are automatically scoped by their parent.

If the value of `:scope` is not an association, it should be the name of a field within the model itself:

```ruby
class Employee
 include Mongoid::Document
 include Mongoid::Slug
 field :name
 field :company_id
 slug  :name, :scope => :company_id
end
```

[1]: https://github.com/rsl/stringex/
[2]: https://github.com/hakanensari/mongoid-slug/blob/master/lib/mongoid/slug.rb
