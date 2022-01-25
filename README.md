# Python Live Project

## Introduction
For the last two weeks I worked on a Live Project with the Tech Academy, I worked with my peers in a team using Azure DevOps and was assigned stories to make a fully working MVT app in Django Framework. Working on this project in Django was a great learning opportunity for me to learn how to fix bugs, clean up my code, implement the use of for loops more, scraping website data using BeautifulSoup and working with RESTapi's and to be more familiarized with how Django Framework works and making virtualenv's. There were a lot of challeneges that I faced during this project that could have been a big roadblocks but I was able to work with the Project Managers and a few other people to help me get past those roadblocks and I utilized the tools and examples that were given to me. 

Below are descriptions of the stories I worked on during this project using the CRUD functionality, along with code snippets.

## Create
This story was creating a Schema using a relational database, SQlight3 in Python as our main Database and making sure that we were able to save user input into the Database we created by checking that all of the information the user put in matched the requirements of our forms.

Here is the code from my models.py file and forms.py:

    class Pokemon(models.Model):
      name = models.CharField(max_length=100, default="")
      type = models.CharField(max_length=50, default="", blank=True)
      abilities = models.CharField(max_length=100, default="", blank=True)

      object = models.Manager()

      def __str__(self):
            return self.name
            
      class PokemonForm(ModelForm):

    class Meta:
        model = Pokemon
        fields = '__all__'


## Read
This Section is where I used functions to display the information from my models.py. I had made it so my home html file will display and added some new link for the user to go into the Add Pokemon section of my navigation bar I created and put in the Name, Type, and Abilities of any Pokemon they want from the First Edition Pokemon and save it to the database as long as the information entered in the form was valid. The next function is another html Page where they can go to and see the information they entered. 

    def pokeDexHome(request):
        return render(request, 'PokeDex/PokeDex_home.html')
        
    def addPokemon(request):
    form = PokemonForm(data=request.POST or None)
    if request.method == 'POST':
        if form.is_valid():
            form.save()
            return redirect('PokeDex_home')
    content = { 'form': form }
    return render(request, 'PokeDex/AddPokemon_form.html', content)

    def show_pokemon(request):
          show_pokemon = Pokemon.object.all()

    context = {'show_pokemon': show_pokemon}
    return render(request, "PokeDex/PokeDex_showPokemon.html", context)


## Update and Delete
In this section I had made another html page for the user to click on the Pokemon they have entered and saved to the database and here they can edit any of the information they put in or delete it from the database.

    def pokemon_details(request, pk):
        details = get_object_or_404(Pokemon, pk=pk)
        context = {'details': details}
        return render(request, 'PokeDex/pokemonDetails.html', context)

    def edit_pokemon(request, pk):
        show_pokemon = get_object_or_404(Pokemon, pk=pk)
        form = PokemonForm(data=request.POST or None, instance=show_pokemon)
        if request.method == 'POST':
           if form.is_valid():
              form.save()
              return redirect('show_pokemon')
        context = {'form': form}
        return render(request, 'PokeDex/PokeDex_edit.html', context)

    def delete_pokemon(request, pk):
        show_pokemon = get_object_or_404(Pokemon, pk=pk)
        form = PokemonForm(data=request.POST or None, instance=show_pokemon)
        if request.method == 'POST':
            show_pokemon.delete()
            return redirect('show_pokemon')
        return render(request, 'PokeDex/PokeDex_delete.html', {'show_pokemon': show_pokemon, 'form': form})
        
## Web Scraping
Here I used BeautifulSoup to do web scraping to get the name and ID number of the pokemon from the Pokemon.com Pokedex. On this story I had some issues with using BeautifulSoup for the web scraping as it was my first time using this for anything. However, I was determined to figure out how to get this to work. I ended up coming across a reddit page where someone was trying to accomplish the same thing. I was then able to go through the code that was provided in the answer and figure out what each peice of code was doing to get the information from the same site I was wanting to use. I felt that that this was a good learning experience for me as I had to put in a lot of research to find a solution for scraping the information from the site I used, which helped me build my solution skills and searching skills. I was also able to really go through the code step by step to further understand how it fit into my code and what each line was doing.

    def pokeDex_search(request):
        page = requests.get("https://www.pokemon.com/us/pokedex/")
        soup = BeautifulSoup(page.content, 'html.parser')
        pokemon_list = []
        for i in soup.find_all('li'):
              li_text = i.get_text(strip=True)
              if len(li_text):
                 pokemon_list.append(li_text)
        d = pokemon_list.index('1 - Bulbasaur')
        i = 0
        while i < 898: # number of pokemon
            print(pokemon_list[d + i])
            i += 1
            context = {}
        return render(request, 'PokeDex/PokeDex_search.html', context)
        
        
## API
Here I used the pokeapi.co to get my Pokemon information, with this api the user can get more information about the Pokemon such as it Type(s) and Abilities. I also made it so that whenver the user searches a pokemon it will take the api info and display it on another html page I have made and will display their features plus a picture of the Pokemon and then it will save the name, type, and abilities of the pokemon the user searched automatically for the user to the database and have it in their pokedex. Here as well on this story I was having some struggles with getting the the API information to show up in my HTML file as it was also only my second time working with an api, and first time to also have it save into my Database correctly. I was though able to over come these challenges by speaking with a more experienced developer to get some help on how to go about getting to the API section I wanted to get information from and from doing research on how to further access the API information as well. I than was able to  run it through a for loop to extract the correct information I wanted to display. As for saving the information from the API into my database I was able to do some research and come across this website: https://dev.to/yahaya_hk/how-to-populate-your-database-with-data-from-an-external-api-in-django-398i which had really helped me to understand what I was needing to do for my code to apply the information I had gotten and put it into a new schema and save the information automatically to the database once the search was executed in the "Search Pokemon" section and the api information was returned.

    def more_info(request):
        complete_info = []
        if request.method == "POST": 
            value = request.POST['pokemon'].lower()
            if value == "":
                messages.info(request, 'Please enter in a Pokémon name!')
            else: # else run the code below
                info = requests.get("https://pokeapi.co/api/v2/pokemon/" + str(value)) 
                poke_info = info.json() 
                poke_name = poke_info 
                poke_abilities = poke_name['abilities']
                poke_type = poke_name['types']
                poke_picture = poke_name['sprites'] 
                front_picture = poke_picture['front_default']
                
                pokemon_type = ""
                
                for pokemon in poke_type:
                    pokemon_type = pokemon_type + pokemon['type']['name'] + ", "
                    
                pokemon_ability = ""
                
                for poke in poke_abilities:
                    pokemon_ability = pokemon_ability + poke['ability']['name'] + ", "
                    
                results = {
                    'value': value, # this is the value of the poke name the user put or copied into our search bar in html
                    'species': pokemon_type, # this is the value of the poke types we got from our for loop above
                    'ability': pokemon_ablity, # this is the value of the poke abilites we got from the for loop above
                    'front_picture': front_picture # this is value of our front_picture var above
                }
                complete_info.append(results)
                
                new_pokemon = Pokemon.object.create(
                    name = value,
                    type = pokemon_type,
                    abilities = pokemon_ablity
                )
                new_pokemon.save()
                  
                  
                context = {'complete_info': complete_info}
                return render(request, 'PokeDex/PokeDex_api.html', context) 
                
            else:
                 return render(request, 'PokeDex/PokeDex_api.html')
                 
                 
## Front End Development
In the front end development I was able to design multiple URL Paths to go through all of the function I have created. I have also made it so that when the user goes to search for a pokemon the api picture shows up inside of a Pokeball with it's information displayed to the side of the ball, and when they user goes to look at the saved information about the Pokémon it will change to an actual PokeDex background and there they can edit or delete the information. I have uploaded some screenshots of the page into a folder to show what the offical app looks like. * [Screen Shots of my App](https://github.com/Canadianfaller7/JobPlacementDashboard/tree/master/LiveProjectScreenshots/ "Named link title")


                    
## Skills Acquired

* Working with a group of developers to identify front and back end bugs.
* Improving my code and implementing the use of for loops inside of projects versus hard coding any code.
* Using Git to Create new branches to work on and improve the over all project, commiting the changes made, Merging in new branches and the over all project into one of the created branches, and push files to the Project Manager for review.
* Practice with working in DevOps to see how Agile and Scrum Project Managment styles are implemented in the actual work field and how each coding assignment is assigned to each person. 
* Asking for help when needed
    *I had ran in to some issues with trying to get the Web Scraping information to show up and same with the RESTapi. I learned to do good research to get me on the correct path to figuring out the final solution, but if I had still gotten stuck on something I learned that it was ok to reach out and ask for help to get more guidence in where to go so I could finish my task within the time frame that was given.
