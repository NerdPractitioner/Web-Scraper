# Web-Scraper Live Group Project
Django web-scraper using Beautiful Soup and Selenium packages to pull launch schedule from NASA site post JS loading
## Contents
* [Introduction](#introduction)
* [Space App](#space-app)
* [Integrations](#integrations)
* [Other Skills Learned](#other-skills-learned)

## Introduction
I was involved in a two week team sprint at The Tech Academy. I worked with a group of peers where I experienced what it was like to join a project already in progress and add something of my own. We used Python with the Django framework and the following package dependencies: beautifulsoup4, certifi, chardet, idna, numpy, pytz, requests, selenium, soupsieve, urllib3. I gained an understanding of the scrum framework of the agile methodolodology. This project let me dig my fingers into a live, moving machine and build a piece from scratch without the hand rails. I can't share the whole project but i have included samples from my portion.

*Jump to: [Space App](#space-app), [Integrations](#integrations), [Other Skills](#other-skills-learned), [Page Top](#contents)*

## Space App
I checked out a story for a user interested in an app that could display upcoming space launches from organizations like NASA.

In the following code snippets, I created a model to grab content from the NASA launch schedule. I was initially using urllib and requests to get the page content but realized the page body content wasn't being captured because the NASA site used JavaScript to populate the launch events. I remedied this by using selenium to open a web browser, wait for the JS content to load, capture the content and close the browser. I would be able to make this invisible to the user but the project lead wasn't worried about that for the minimum viable product. I use BeautifulSoup to parse the page content into python objects and then itterate through those objects creating a ziped variable with 3 the content I needed for each event. My space_page view calls my model function and then renders it to the spage_page template.

### From models.py
 

        from django.db import models
        from django.shortcuts import render, get_object_or_404, redirect
        from django.http import HttpResponse
        from bs4 import BeautifulSoup as soup
        from selenium import webdriver


        #Create your models here.

        class LaunchEvent:
            def __init__(self):
                #use selenium to open a browser
                driver = webdriver.Chrome()
                my_url = 'https://www.nasa.gov/launchschedule'
        
                #selenium browser waits 10 seconds allowing JS content to load then grabs page contents
                driver.implicitly_wait(10) # seconds
                driver.get(my_url)

                #selenium driver creates page_html which can be turned to soup
                page_html = driver.page_source
                driver.quit()

                #html parsing
                page_soup = soup(page_html, "html.parser")

                #grabs each launch 
                containers = page_soup.find_all("div", class_="launch-info")
                title_list = []# list to iterate in below for loop.
                date_list = []
                info_list = []

                for i in range(0, 3):
                    title_list.append(containers[i].find(class_="title").get_text())
                    date_list.append(containers[i].find(class_="date").get_text())
                    info_list.append(containers[i].find(class_="description").get_text())

                #group iterations through zip function
                all_list = zip(title_list, date_list, info_list)
                self.all_list = all_list
        
### From Views.py
        from django.shortcuts import render, get_object_or_404, redirect
        from django.http import HttpRequest, HttpResponse
        from .models import LaunchEvent


        #Create your views here.
        def space_page(request):
            launches = LaunchEvent()
            return render(request, 'SpaceApp/space_page.html', {'launches': launches})
    
### From page template    
        {% extends 'base.html' %} <!-- here we are saying that we will be rendering our content inside of the base.html template -->

        {% block title %}| Launch Events{% endblock %} <!-- This will appear in the browser tab -->

        {% block content %} <!-- block content defines where the content will be rendered on the page according to where we placed the same tags in the body of base.html-->
        <h1>Launches from NASA and SpaceX</h1>
         
        {% for item in launches.all_list %}
                <div>
                    <h3 style="background-color: cadetblue"  > {{ item.0 }} </h3><br> <!-- launch title -->
                    <p> {{ item.1 }}    </p><br>                                       <!-- launch date -->
                    <p>{{ item.2 }} </p>  <!-- launch purpose -->
                
                </div>
                <br>
            {% endfor %}

        {% endblock %}   

        
*Jump to: [Introduction](#introduction), [Integrations](#integrations), [Other Skills](#other-skills-learned), [Page Top](#contents)*

## Integrations

Adding this list element to our navbar template, gave access to my page from the anywhere on the site.

        <li class="nav-item pr-3">
          <a class="btn btn-danger" href="{% url 'space_page' %}">Space Launches</a>
        </li>
I also utilized the urls section of our main app to include the paths in my app

        path('spaceapp', include('SpaceApp.urls')),#path to SpaceApp
        
My portion of the project was focused on functionality but I did stick with the style of the overall content using bootstrap for my buttons and display. 
*Jump to: [Introduction](#introduction), [Space App](#space-app), [Other Skills](#other-skills-learned), [Page Top](#contents)*
## Other Skills Learned 
My team met daily for stand-ups and supported each other on a slack channel. We used Microsoft Azure to manage our project user stories board and branch management. I became comfortable using gitbash and setting up a virtual environment locally. I learned how to commit, push and make pull requests while providing ample commentary that the team could understand.

*Jump to: [Introduction](#introduction), [Space App](#space-app), [Integrations](#integrations), [Page Top](#contents)*
