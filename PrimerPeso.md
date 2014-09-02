# Primer Peso

## Code
* [http://codeforamerica.org/primerpeso](http://codeforamerica.org/primerpeso)

## Links
*  [http://www.primerpeso.com](http://www.primerpeso.com)

## Background
As part of my fellowship at Code For America, my team and I are working with the government of Puerto Rico in the area of Economic Development.  My team consists of myself, an anthropologist and a visual designer.  I am the technical lead on the team and have been in charge of all the development we have done so far.  

We spent all of February in Puerto Rico researching and understanding the economic development efforts underway there.  Through our research we decided  to help small business owners find, filter and select financing opportunities provided by the Puerto Rican and Federal government. In addition, we decided to provide the government with data useful in tailoring their programs.

We set about building an application that would:

* Allow agencies to log in, create, edit, delete and list various programs they offer.
* Allow users to enter the site, answer a few simple questions and get a list of programs they may be eligible for. 
* Store and aggregate the data and provide insight to agencies about what people are looking for vs. what they're offering (gaps).

In addition, I knew that I needed to make the application approachable to my team (in terms of being able to test and work on it), and I wanted to be able to deploy to staging and production very quickly.  

Some key points:

## Architecture
* The entire application is written in JavaScript, using Node and Express for the backend.
* The code is separated into models, views and controllers.  Most of the functionality is built into models for easier unit testing.
* I used an ORM called Sequelize - [http://sequelizejs.com](http://sequelizejs.com) to allow me to create models and let me manage storage and migrations on Postgres.  
  * [See Models - https://github.com/codeforamerica/primerpeso/tree/master/models](https://github.com/codeforamerica/primerpeso/tree/master/models)
* Since an open source tool was not available for generating the admin interface, I wrote one that introspects my models and builds an admin interface (soon to be open-source on npm). This allows easy generation and modification of the admin forms based the data structure.  
  * [Admin Controller - https://github.com/codeforamerica/primerpeso/blob/master/controllers/admin.js](https://github.com/codeforamerica/primerpeso/blob/master/controllers/admin.js)
  * [Jade Mixins for creating forms - https://github.com/codeforamerica/primerpeso/blob/master/views/mixins/forms.jade](https://github.com/codeforamerica/primerpeso/blob/master/views/mixins/forms.jade)
* On the user side, I'm using a wizard to collect data from the business owner.  The data is then submitted to an Express callback, which based on the choices creates a query, executes it and returns a set of results. 
  * [See SearchQuery - https://github.com/codeforamerica/primerpeso/blob/master/lib/SearchQuery.js](https://github.com/codeforamerica/primerpeso/blob/master/lib/SearchQuery.js)  
* The user is then able to pick programs and "add them to a cart".  I used backbone on the front end to build this functionality.  
  * [SearchShop - https://github.com/codeforamerica/primerpeso/blob/master/public/src/js/searchShop.js](https://github.com/codeforamerica/primerpeso/blob/master/public/src/js/searchShop.js)
  * [Search folder - https://github.com/codeforamerica/primerpeso/blob/master/public/src/js/search](https://github.com/codeforamerica/primerpeso/blob/master/public/src/js/search)
* I'm using Mocha and Chai for testing. 
  * [See Test - https://github.com/codeforamerica/primerpeso/tree/master/test](https://github.com/codeforamerica/primerpeso/tree/master/test).
* For templating, I'm using Jade, since it's much easier to read than templating languages that are based on HTML.
* For CSS, I'm using LESS because it's a nice well-known CSS preprocessor, already being used by Twitter Bootstrap, which is much of our front-end architecture.
* I used Mandrill for sending e-mails because it offsets a lot of the hard work of delivery.  

### Work Process and Deployment.
In order to make life easier for my team that does not like to mess with migrations, dependencies and port settings, I created a set of Gulp tasks.  

They still need to install the Postgres.app, Redis and Node, and that's something I would like to fix for future developers with Docker. 

After they install node and run `npm install` all they need to do is run `gulp` in their terminal.  That will spin up a local environment for them, run any needed migrations, start all the proper services and update itself every time they make changes.  
[Gulp Tasks - https://github.com/codeforamerica/primerpeso/tree/master/gulp/tasks](https://github.com/codeforamerica/primerpeso/tree/master/gulp/tasks)
[Readme - https://github.com/codeforamerica/primerpeso/blob/master/README.md](https://github.com/codeforamerica/primerpeso/blob/master/README.md)

Travis-CI runs continous integration.  Every pull request is automatically tested.  Once a pull requested is accepted into the master branch, Travis picks it up, compiles and minifies all LESS into one CSS file, and all JavaScript into two files, compresses the images and uploads all these static assets to an S3 bucket.  It deploys the new code to our staging Heroku app, runs any needed migrations and restarts the dynos.  

Once we review the latest code on staging, we then merge master into the production branch,  Travis repeats the same steps but deploys the code to the production Heroku application.    See [Travis File - https://github.com/codeforamerica/primerpeso/blob/master/.travis.yml](https://github.com/codeforamerica/primerpeso/blob/master/.travis.yml)

### Next Steps
I am definitely looking forward to refactoring the code more.  As I develop, I am very careful to make decisions that balance the accrual of technical debt vs. delivery time.  

I am looking forward to adding a Docker process to the repository in order to allow people to easily jump into development. 

Currently, JavaScript and CSS magnification is not turned on because it's not critical yet, and I want to be able to debug.  The front-end code could definitely use some cleanup, as well as some of the controllers and models. 

In addition, we are still developing new features on a tight timeline.  I would say this is the typical living state of any application - not yet perfect, but always working towards getting there.