
# work-in-progress: I will remove this comment when done (2017-05-28). Expect it to happen within a few days.


# Building Microsoft Dynamic 365 WebResources with Visual Studio and angular 4

This project demonstrates an easy track to develop Microsoft Dynamic 365 WebResource components with angular 4. 

## How to use, and where will in end up leaving you.

This guide is a step by step guide for building a very simple Visual Studio Solution that will allow you to create a full angular 4 application that can run inside a Dynamic 365 solution, online or on-premises.

Even though a dynamic web resource is a web application, this project is using a simple command line tool as template. The reason for this is the basic nature of a dynamic 365 web resource. It only allow simple html, javascript, css and image files. Nothing else. So any Visual Studio Web template will add things that are not supported anyway, ex. ASP.NET controllers and more.
Secondly, the deployment model of web resources is also very strict. Files need to be uploaded to CRM as WebResources, and eventually they will be deployed as single files (possible in sub folder structure). The angular cli build process is a perfect resource to optimize and prepare as few files as possible to be uploaded. This project will show you how, and even give you the needed code to automate the process. The automation process is included in the command line tool (Deploy).

## The following topics will be covered

1. Creating a solution
1. Create an angular application with angular cli
1. Add the angular application to your solution
1. Deploy the application to your Dynamic 365 
1. Make your angular cli application work with IE.
1. Add a simple typescript xrm service that will allow data access through dynamic 365 WebApi
1. Setup a development environment that allow development and test, directly in Visual Studio, without prior deployment to Dynamic 365

## Creating a solution

This project is a demo reference only, so the best starting point is proberbly not just to download this solution. But in very few steps, you can have your own solution up running.

1. Open visualstudio
1. Create new project, using the template [Other Project Types > Visual Solutions > Blank Solution ], in this guide I assume you are naming i ''''MyAngularSolution''''
1. Add a Command line project to the solution, name it Deploy, because that is what is eventually gonna do.
1. If you wish to use Git or other source control repository, now it is the time to add your solution to source control. Use whatever process you normally use. If you don't add you solution to source control now, you might come in trouble later, because the angular cli will add git source control to your angular project, if the folder is not already under source control.


## Create an angular application with angular cli

If you didn�t already install the angular cli tool, you need to do so now. Follow the guide from this link.

Before you run to fast, just do the installation of angular cli for now. Get back here when you are ready to create a new angular app.

[Angular CLI](https://cli.angular.io/)

Now you have the angular cli tool as part of your development environment. 

Open a command prompt, and navigate the the root of the Deploy project folder. ( de ..whatever.folder is your root ) 

````C:\Projects\MyAngularSolution\Deploy````

```ng new demo```

This will create a sub folder in you solution named demo (give you project whatever name corresponding to your need. It will eventually be used as the root name in your Dynamic 365 WebResource)

Now go back to Visual Studio and add the needed folders to your Visual Studio project:

(Click the Deplop project, use the [Show all files] button in the Solution Explore, hightlight according to below and select Include in Project])


![Add angular app to solution](https://raw.githubusercontent.com/kip-dk/angular-xrm-webresource/master/Documentation/solution-add-angular-application.png)

Just for the sake of confirmation, you can test run your application. This is done by opending a command prompt, navigate to

````C:\Projects\MyAngularSolution\Deploy\Demo````

and enter

```ng server --open```

This will start the ng development server and open a new browser window. You should see something like "app works!" in your browser window.

## Deploy the application to your Dynamic 365

Next step is to deploy the application so it can run under Dynamic 365 as web resource. This is actually quite simple, because the only thing we need to do is to create a build, and then upload the files in the build 
as WebResources in our Dynamic 365 solution. As our project grows in size this can become a time consuming task due to the amount of files that must be uploaded and managed individually. Luckily the dynamic 365 SDK allow us to automate the process.  Below I will take you through the process � step by step, however the Deploy command line tool actually contains all the code for a fully automated process. So if you are a lucky rider, you might just dive directly into the code.

### But first we need to to establish a build process

An angular project is basically just a bunch of html, css and typescript files. Alle these files need to be compiled into a workable web solution to be deployed under Dynamic 365. Dynamic 365 do understand
html and css, but there is no direct support for typescripts. The angular cli build process it the perfect tool to prepare files for upload. Below the manual process for doing this.

Open a command line tool and navigate to your angular page folder

````C:\Projects\MyAngularSolution\Deploy\Demo````

```ng build --prod --output-hashing none```

The --prod flag is telling angular to build an optimized version suiteable for production, and the --output-hasning is to tell the build tool not to hash the file names generated. The later flag is really convinient for the
dynamic 365 scenario, because dynamic 365 is alrady hashing all resources before served to the browser, and new names for all files would be a nightmare to manage when uploading the files to 365.

As a result of this process, ng build will generate a "dist" folder onder the "...\Demo" folder, containing all the files needed to run the application:


![Add angular app to solution](https://raw.githubusercontent.com/kip-dk/angular-xrm-webresource/master/Documentation/angular-first-build.png)

