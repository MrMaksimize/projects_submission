# 22nd Century Media
## Code
* [https://dl.dropboxusercontent.com/u/1009233/DevShare/1_Pecherskiy_Maksim_SD_BEWD_CM22.zip](https://dl.dropboxusercontent.com/u/1009233/DevShare/1_Pecherskiy_Maksim_SD_BEWD_CM22.zip)
* `cm22master` - Master Site
* `cm22slave` - Slave Site

## Links
* Master Site
  * [http://22ndcenturymedia.com](http://22ndcenturymedia.com/)
* Slave Site  (same code, different deployments)
	* [http://www.northbrooktower.com/](http://www.northbrooktower.com/) 
	* [http://www.lockportlegend.com](http://www.lockportlegend.com) 


22nd century media is a newspaper company based in Chicagoland that produces hyper-regional newspapers for various Chicago and Miami suburbs.  

As a Lead Developer / Solutions Architect at Promet Source I lead the development team that created these sites and architected the structure of the project.  The team consisted of myself, my project manager, a designer / themer and one more backend developer.  

I rolled back the code to the last commit I had on the projects, which was sometime around January 2014, and removed the git information.  

## A Little Background on Drupal Challenges
* ***Drupal is extremely difficult to work with on a team, since the system is designed to hold most of the configuration in the database rather than in code***.  Much of this configuration is exportable through code generated custom modules.  However, oftentimes there are major problems--as many components do not support the export of configurations.  In addition, compartmentalizing and modularizing features in a pattern that allows for easy feature maintenance is a challenge in and of itself.  
* ***Drupal uses the LAMP stack, but replicating production environments locally is extremely difficult due to software version differences*** and various performance enhancement systems used in production.
* ***Drupal mixes configuration and content, so keeping features and functionality up to date and rolling new features to production without touching content can be extremely difficult.***

## Key / Interesting Requirements
* As the company that owns 12 newspapers, I would like 12 identical but fully separate sites with different content, but sharing a user base and a sign-on system.
* As a company that plans to acquire / create more newspapers in the future, I need to minimize my costs of starting a new newspaper subscription site.
* As a company, I have one central site for my customers to learn about the organization as a whole: a corporate site.
* As a company, I want to be able to run separate mailing lists for every newspaper in the cluster.
* As a user, when I subscribe for paid access to a newspaper site, based on its region I get access to a set of other newspapers as well.  
* As a user, when I subscribe to a specific newspaper, I want to be able to pick which mailing lists I want to subscribe to in that cluster.
* As a user, I want to log in to the newspaper sites I have access to with the same e-mail and password that I use to manage my account.
* As a user, I have the option to get billed annually or monthly.
* As a company, when my users  subscriptions become delinquent (card expirations, etc), I would like to know about it and notify my users.

## Solutions
### Development Process.
* I created two site "templates" that utilized an open source Drupal build system.  The build system was able to pull in contributed (community) modules, custom modules, and feature modules (code generated custom modules).  One of the templates was for the Master site -- or the central corporate site and the other one was for a Slave site -- or an individual newspaper site.  
* I created a strict naming pattern for module creation across the project in order to differentiate from custom modules, feature modules and contributed modules. 
* As my team created new functionality, we had a code review process to make sure that the naming patterns were followed and we had explicit and strict modularity for all features (the pattern was literally to group by feature).
* In order to create development content and also programmatically (sometimes) push content to staging and production, I used a tool called Kraftwagen Manifests or idempotent migrations of data. 
* We used Vagrant and Chef to make sure we had the necessary similarities between staging, production, and local development environments.

### Meeting Client Requirements
* The build system successfully solves not only the local development problems, but also allows for easy deployment of new slave (newspaper) sites with minor modifications -- therefore reducing cost of spin up.
* Updates to module versions are simple one line changes in make files and easily can be rolled across the master site and slave sites with one deployment (after proper testing of course).
* The Master site maintains a list of current slave sites such as the title, the color scheme, oauth client information and which "pay cluster" they belong to.  
* When a new slave site is being spun up, it can be told to ask the user doing spin-up to pick what site it's going to be by making an API call to master for all available options.  Once the developer makes the choice, the slave site makes another call to get information for that specific instance of the slave site and will know how to configure itself.  This decreases the time to deploy a new slave site to hours rather than days.
* When a user creates a subscription, he/she is doing so through Recurly, an external service used to manage subscription billing.  Once the user successfully subscribes, it is granted access to the pay cluster.  That information is managed on the Master site, and when the user comes back to log in, logs into the newspaper site of choice through the master OAuth server. This allows the user to use the same username / password to log into any site he or she has access to.
* The user at that time is also able to pick which Mailchimp lists he/she wants to subscribe to (only the ones in the cluster are available) and that is also maintained by the Master site.
* When Recurly is unable to charge the user as well, as several other events, it sends a webhook POST to the master site which reacts accordingly -- either by e-mailing the user or by suspending his/her access to the site, as well as their subscription to mailing lists.

## Files of Note
### Master and Slave Codebases
* `README.md` - Developer Instructions.
* `cm22slave.info / cm22master.info` - A file instructing Drupal which dependencies to turn on.
* `cm22slave.make / cm22master.make` - A file instructing the build system which modules were required, what type of project the module was (library, module, theme, etc) and where to locate them.
* `overrides.make` - Since make files can be nested, and we have a strict rule never to modify contributed code, an overrides file is often required to bump versions  of dependencies that may not have been updated in contributed code. 
* `dev/Vagrantfile` - Vagrant configuration file for bring up the development VMs.  
* `modules/custom` - Where custom modules get placed.
* `modules/features` - Where features (code gen modules) get placed.

### Master Codebase - `cm22master/**`
* `modules/custom/specific/cm22ms_mailchimp_list_management` - Module dealing with Mailchimp list management.
* `modules/custom/specific/cm22ms_conf_control` - API providing configuration information to slaves.
* `modules/features/specific/cm22ms_oauth_provider` - OAuth Provider.
*  `modules/features/specific/cm22ms_recurly` - Recurly integration.

###Slave Codebase - `cm22slave/**`

* `modules/custom/oauth_slave` - OAuth Slave callbacks and endpoints in order for users to be able to log in through master.
* `modules/custom/specific/cm22sl_manifests` - Manifests (idempotent migrations) module for creating various content and menu items on spin-up of slave sites.
* `modules/features/specific/cm22sl_control` - Configuration client that receives information from master during spin up.
