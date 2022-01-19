# Python Live Project

## Introduction
For the last two weeks I worked on a Live Project with the Tech Academy, I worked with my peers in a team using Azure DevOps and was assigned stories to make a fully working MVT app in Django Framework. Working on this project in Django was a great learning oppertunity for me to learn how to fix bugs, clean up my code, implement the use of for loops more, scraping website data using BeautifulSoup and working with RESTapi's. There were a lot of challeneges that I faced during this project that could have been a big roadblocks but I was able to work with the Project Managers and a few other people to help me get past those roadblocks and I utilized the tools and examples that were given to me. 

Below are descriptions of the stories I worked on during this project, along with code snippets.

## Create
This story was creating a Schema using SQlight3 in Python as our main Database and making sure that we were able to save user input into the Database we created by checking that all of the information the user put in matched the requirements of our forms.

      class Pokemon(models.Model):
    name = models.CharField(max_length=100, default="")
    type = models.CharField(max_length=50, default="", blank=True)
    abilities = models.CharField(max_length=100, default="", blank=True)

    object = models.Manager()

    def __str__(self):
        return self.name


