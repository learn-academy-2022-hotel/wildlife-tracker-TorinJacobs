- Accept slack assignment, click link
  https://github.com/learn-academy-2022-hotel/Syllabus/blob/main/rails/rails-api.md
- Wildlife tracker challenges are at the bottom
- cd to your Desktop to store repo
$ rails new wildlife_tracker -d postgresql -T
$ cd wildlife_tracker
$ rails db:create
- Add the remote from GitHub
  $ git remote add origin https://...whatever-your-link-is
- Create the main branch
$ git checkout -b main
- Make an initial commit
- Add all the files
$ git add .
$ git commit -m "initial commit"
$ git push origin main
- Checkout a new branch
$ git checkout -b initial-setup (or whatever you named your first branch after your main branch)
- Add dependencies to gem file
- Gemfiles are stored in 'Gemfile' of the application if you want to view them
$ bundle add rspec-rails
- Add testing files and folders to application
$ rails generate rspec:install
- Open new terminal window
$ rails server
- Verify no errors in terminal after starting server
$ git status
$ git add .
$ git commit -m  "added rspec dependencies and files"
$ git push origin initial-setup
$ git checkout main
$ git pull origin main
$ git branch -d initial-setup
- Open the folder in a text editor
- Code!

- Copy the wildlife challenge into the readme (delete existing info)

Wildlife Tracker Challenge
The Forest Service is considering a proposal to place in conservancy a forest of virgin Douglas fir just outside of Portland, Oregon. Before they give the go ahead, they need to do an environmental impact study. They've asked you to build an API the rangers can use to report wildlife sightings.

Story 1: In order to track wildlife sightings, as a user of the API, I need to manage animals.

Branch: animal-crud-actions

Acceptance Criteria

Create a resource for animal with the following information: common name and scientific binomial

$ rails generate resource Animal common_name:string scientific_binomial:string

$ rails db:migrate

Can see the data response of all the animals
$ rails console and create entries
- Create at least 4 animal entries
> Animal.create(common_name: 'Ba humbugi', scientific_binomial: 'Ba humbugi')
> Animal.create(common_name: 'Tuttu', scientific_binomial: 'Rangifer tarandus')
> Animal.create(common_name: 'Dineega', scientific_binomial: 'Alces alces')
> Animal.create(common_name: 'Sockeye Salmon', scientific_binomial: 'Oncorhynchus nerka')
> Animal.all 

- Add Index endpoint inside controller in application
# INDEX #
class AnimalsController < ApplicationController
  def index
    animals = Animal.all
    render json: animals
  end

- Can create a new animal in the database
# CREATE #
- In animal controller
class AnimalsController < ApplicationController
#...index method...
#...show method...
  def create
    animal = Animal.create(animal_params)
    if animal.valid?
      render json: animal
    else
      render json: animal.errors
    end
  end

  private
  def animal_params
    params.require(:animal).permit(:common_name, :scientific_binomial)
  end
- In Postman:
  POST localhost:3000/animals
  - Input:
  {
    "common_name": "Sexy shrimp",
    "scientific_binomial": "Thor amboinensis"
  }
  -Output: 
  {
    "id": 6,
    "common_name": "Sexy shrimp",
    "scientific_binomial": "Thor amboinensis",
    "created_at": "2023-01-20T19:35:34.459Z",
    "updated_at": "2023-01-20T19:35:34.459Z"
}

- Can update an existing animal in the database
  - In App Controller
class AnimalsController < ApplicationController
 #...index method...
 #...show method...
 #...create method...
  def update
    animal = Animal.find(params[:id])
    animal.update(animal_params)
    if animal.valid?
      render json: animal
    else
      render json: animal.errors
    end
  end
  - In Postman:
  PATCH localhost:3000/animals/1
  Input:
    {
    "common_name": "Ba humbugi snail",
    "scientific_binomial": "Ba humbugi"
    }
  Output:
    {
    "common_name": "Ba humbugi snail",
    "scientific_binomial": "Ba humbugi",
    "id": 1,
    "created_at": "2023-01-20T18:26:34.951Z",
    "updated_at": "2023-01-20T19:55:47.249Z"
    }

- Can remove an animal entry in the database
  - In App Controller
    class AnimalsController < ApplicationController
    #...index method...
    #...show method...
    #...create method...
    #...update method...
    def destroy
    animal = Animal.find(params[:id])
    if animal.destroy
      render json: animal
    else
      render json: animal.errors
    end
  end
  - In Postman: 
  DELETE localhost:3000/animals/6
  Output:
    {
    "id": 6,
    "common_name": "Sexy shrimp",
    "scientific_binomial": "Thor amboinensis",
    "created_at": "2023-01-20T19:35:34.459Z",
    "updated_at": "2023-01-20T19:35:34.459Z"
    }
    
Story 2: In order to track wildlife sightings, as a user of the API, I need to manage animal sightings.

Branch: sighting-crud-actions

Acceptance Criteria

Create a resource for animal sightings with the following information: latitude, longitude, date
Hint: An animal has_many sightings (rails g resource Sighting animal_id:integer ...)
Hint: Date is written in Active Record as yyyy-mm-dd (“2022-07-28") date:
Can create a new animal sighting in the database
Can update an existing animal sighting in the database
Can remove an animal sighting in the database

Story 3: In order to see the wildlife sightings, as a user of the API, I need to run reports on animal sightings.

Branch: animal-sightings-reports

Acceptance Criteria

Can see one animal with all its associated sightings
Hint: Checkout this example on how to include associated records
Can see all the all sightings during a given time period
Hint: Your controller can use a range to look like this:
class SightingsController < ApplicationController
  def index
    sightings = Sighting.where(date: params[:start_date]..params[:end_date])
    render json: sightings
  end
end
Hint: Be sure to add the start_date and end_date to what is permitted in your strong parameters method
Hint: Utilize the params section in Postman to ease the developer experience
Hint: Routes with params
Stretch Challenges
Story 4: In order to see the wildlife sightings contain valid data, as a user of the API, I need to include proper specs.

Branch: animal-sightings-specs

Acceptance Criteria
Validations will require specs in spec/models and the controller methods will require specs in spec/requests.

Can see validation errors if an animal doesn't include a common name and scientific binomial
Can see validation errors if a sighting doesn't include latitude, longitude, or a date
Can see a validation error if an animal's common name exactly matches the scientific binomial
Can see a validation error if the animal's common name and scientific binomial are not unique
Can see a status code of 422 when a post request can not be completed because of validation errors
Hint: Handling Errors in an API Application the Rails Way
Story 5: In order to increase efficiency, as a user of the API, I need to add an animal and a sighting at the same time.

Branch: submit-animal-with-sightings

Acceptance Criteria

Can create new animal along with sighting data in a single API request
Hint: Look into accepts_nested_attributes_for
