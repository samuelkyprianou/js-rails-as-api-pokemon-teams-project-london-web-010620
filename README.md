# Pokemon Teams Project

## Learning Goals

- Combine both a JavaScript frontend with an API-Only build of Rails

## Introduction

The ability to spin up a Rails APIs can actually help us develop our frontend
JavaScript abilities, as the API will give something tangible to work against
as you write your code.

For this project, you will need to build out both a JavaScript frontend and
Rails API backend. There are no tests, but a set of goals for you to try and
achieve.

You are going to help us keep track of Pokemon trainers at Flatiron School.
The data about trainers and Pokemon will be stored on a Rails API, but we can
handle the visual aspects in a JavaScript frontend.

Through their journey, a Pokemon trainer can only have 6 Pokemon on their team.
As you build, the eventual goal of a fully functional Pokemon team
organizer where we can add and remove Pokemon as needed:

![Showing Finished Project Example](/pokemon-teams-frontend/assets/pokemon_teams.gif)

Although we have not explicitly gone over some of the CRUD actions involved in
adding and deleting relationships in this section of content, refer back to what
you learned about CRUD actions in Rails and use your new understanding of 
rendering JSON to try and solve these challenges.

## Goals

- When a user loads the page, they should see all trainers, with their current team of Pokemon.
- Whenever a user hits Add Pokemon and they have space on their team, they should get a new Pokemon.
- Whenever a user hits Release Pokemon on a specific Pokemon team, that specific Pokemon should be 
released from the team.

## Backend

Create a backend from scratch inside this lesson using an API-only Rails build.
Exactly how you choose to structure things and the following guidelines to get
started:

```sh
rails new pokemon-teams-backend --api
```

Once the backend is created, navigate into the new folder. Within this new Rails
build, add the following gem to the Gemfile:

```rb
gem 'faker'
```

While we are in the Gemfile, uncomment `gem 'rack-cors'`, then run 
`bundle install`. If you recall, `rack-cors` is necessary for cross-origin
resource sharing. To get rack-cors working, once the gem is installed, add the
following to `config/application.rb` inside `class Application <
Rails::Application` without replacing any other content:

```rb
config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins '*'
    resource '*', headers: :any, methods: [:get, :post, :delete]
  end
end
```

With these gems installed, use the following resource generators to create
resources for this API:

```sh
rails g resource trainer name
rails g resource pokemon species nickname trainer:references
```

Run `rails db:migrate` to create a schema, models, and controllers. Using
`trainer:references` will set up `belongs_to :trainer` in the `Pokemon` model,
though it won't update the `Trainer` side of the relationship.

In `db/seeds.rb`, add the following and run `rails db:seed`:

```rb
require 'faker'
require 'securerandom'

Trainer.delete_all
Pokemon.delete_all

trainers_name = [
  'Prince',
  'Dick',
  'Garry',
  'Jason',
  'Matt',
  'Noah',
  'Adam',
  'Arthur'
]

trainer_collection = []

trainers_name.each do |name|
  trainer_collection << Trainer.create(name: name)
end

trainer_collection.each do |trainer|
  team_size = (SecureRandom.random_number(6) + 1).floor

  (1..team_size).each do |poke|
    name = Faker::Name.first_name
    species = Faker::Games::Pokemon.name
    Pokemon.create(nickname: name, species: species, trainer_id: trainer.id)
  end
end
```

At this point, you should be able to run `rails console` and confirm that you
have populated your tables with seed data.

## Frontend

Some basic structure is provided in the folder `pokemon-teams-frontend`, including
an HTML, CSS and JS file set up to work together.

### Suggested HTML

As CSS is already provided, you can use this example HTML as a guide when
building out your frontend JavaScript:

```html
<div class="card" data-id="1"><p>Prince</p>
  <button data-trainer-id="1">Add Pokemon</button>
  <ul>
    <li>Jacey (Kakuna) <button class="release" data-pokemon-id="140">Release</button></li>
    <li>Zachariah (Ditto) <button class="release" data-pokemon-id="141">Release</button></li>
    <li>Mittie (Farfetch'd) <button class="release" data-pokemon-id="149">Release</button></li>
    <li>Rosetta (Eevee) <button class="release" data-pokemon-id="150">Release</button></li>
    <li>Rod (Beedrill) <button class="release" data-pokemon-id="151">Release</button></li>
  </ul>
</div>
```

## Example API Requests

To help you shape your data, here are some JSON data structures you should look
to mirror while building out your applications:

### Getting All Trainers and their Pokemon

```text
#=> Example Request
GET /trainers

#=> Example Response
[
  {
    "id":1,
    "name":"Prince",
    "pokemons":[
      {
        "id":140,
        "nickname":"Jacey",
        "species":"Kakuna",
        "trainer_id":1
      },
      {
        "id":141,
        "nickname":"Zachariah",
        "species":"Ditto",
        "trainer_id":1
      },
      // ...
    ]
  }
  // ...
]
```

### Adding a Pokemon

```text
#=> Example Request
POST /pokemons

Required Headers:
{
  'Content-Type': 'application/json'
}

Required Body:
{
  "trainer_id": 1
}

#=> Example Response
{
  "id":147,
  "nickname":"Gunnar",
  "species":"Weepinbell",
  "trainer_id":1
}
```

### Releasing a Pokemon

```text
#=> Example Request
DELETE /pokemons/:pokemon_id

#=> Example Response
{
  "id":147,
  "nickname":"Gunnar",
  "species":"Weepinbell",
  "trainer_id":1
}
```
