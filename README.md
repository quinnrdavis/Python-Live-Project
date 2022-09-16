# Python Live Project
## Introduction
At the end of the Python course at The Tech Academy, I worked alongside other students creating a CRUD app with Django. My app was created solely by me but had to fit into the larger project site that housed all of our apps. The project was meant to imitate a real work environment and was run following Agile and SCRUM principles with a sprint planning session, daily stand up meetings, and retrospectives at the end of both weeks of the two week sprint. Below is a description of the functionality with code snippets of what I created.

## Acquired Skills
- Agile/Scrum
- Programming in a team environment
- Azure DevOps
- Producing a web app from scratch with Django
- Working with a remote repository with git

## CRUD Functionality
My app's purpose is to allow users to create a list of their favorite film directors.
#### It uses Django Models to create records in the database when the user fills out a form.
```
class Director(models.Model):
    name = models.CharField(max_length=100, default="", null=False)
    numberOfMovies = models.IntegerField()
    yearOfFirstMovie = models.IntegerField()
    primaryGenre = models.CharField(max_length=100, default="")
    deceased = models.BooleanField(default=False)

    objects = models.Manager()

    def __str__(self):
        return self.name

```
#### ...And Django's Views to render templates for each different page in the app.
```
# Render the home page
def directors_home(request):
    return render(request, 'MovieDirectors/directors_home.html')


# Render page with form to add records to the database
def directors_add(request):
    form = DirectorForm(request.POST or None)
    if form.is_valid():
        form.save()
        return redirect('directors_view')
    else:
        print(form.errors)
        form = DirectorForm()
    context = {
        'form': form,
    }
    return render(request, 'MovieDirectors/directors_add.html', context)


# Render page with a list of all entered directors
def directors_view(request):
    directors = Director.objects.all()
    return render(request, 'MovieDirectors/directors_view.html', {'directors': directors})


# Render a page with details about a specific director when selected from the list
def directors_details(request, pk):
    pk = int(pk)
    director = get_object_or_404(Director, pk=pk)
    context = {
        'director': director,
    }
    return render(request, 'MovieDirectors/directors_details.html', context)


# Render a page that allows the user to edit an existing record
def directors_edit(request, pk):
    pk = int(pk)
    director = get_object_or_404(Director, pk=pk)
    form = DirectorForm(data=request.POST or None, instance=director)
    if request.method == 'POST':
        if form.is_valid():
            form.save()
            return redirect('directors_view')
    context = {
        'form': form,
        'director': director,
    }
    return render(request, 'MovieDirectors/directors_edit.html', context)


# Delete selected record from the database
def directors_delete(request, pk):
    pk = int(pk)
    director = get_object_or_404(Director, pk=pk)
    director.delete()
    return redirect('directors_view')
```
## Pages include:
#### A home page:
![Home Page](https://user-images.githubusercontent.com/22021323/190713621-f90c76ca-6585-42b6-adae-3328fc7cb355.jpg)
#### A form to add new records:
![Add Form](https://user-images.githubusercontent.com/22021323/190713647-247c3e38-dfa1-42e0-9184-0879f20cc224.jpg)
#### A list view to show all records in the database:
![List View](https://user-images.githubusercontent.com/22021323/190714199-2c47ee5b-e09a-4201-9b35-eab3ecdbd6a9.jpg)
#### And a details page that shows all properties of a selected record, along with edit and delete buttons to update or remove that record:
![Details](https://user-images.githubusercontent.com/22021323/190714964-f3db872a-9963-40b9-802b-39ceb24beca8.jpg)

## Connecting to an API
One of the challenge stories after completing the main CRUD functionality was to connect to an API. I found a free API that provides info about every episode of *Bob's Burgers* and parsed the JSON object it provides to display a random "Burger of the Day".
```
def directors_api(request):
    # A free Bob's Burgers API, I am getting a random burger of the day of the 333 available
    num = random.randrange(1, 333)
    response = requests.get('https://bobsburgers-api.herokuapp.com/burgerOfTheDay/{}'.format(num))
    context = response.json()
    return render(request, 'MovieDirectors/directors_api.html', context)
```

## Scraping Content with Beautiful Soup
The other optional challenge story was to scrape a paragraph from a web page using the Beautiful Soup library. I wrote this to scrape and print the first body paragraph from the wikipedia page about web scraping.
```
def directors_beautifulsoup(request):
    # Scrape and print the first paragraph from the wikipedia page on web scraping
    page = requests.get('https://en.wikipedia.org/wiki/Web_scraping')
    soup = BeautifulSoup(page.content, 'html.parser')
    html = list(soup.children)[2]
    body = list(html.children)[3]
    div1 = list(body.children)[4]
    div2 = list(div1.children)[9]
    div3 = list(div2.children)[13]
    div4 = list(div3.children)[0]
    p = list(div4.children)[8].get_text()
    context = {
        'p': p,
    }
    return render(request, 'MovieDirectors/directors_beautifulsoup.html', context)
    ```
