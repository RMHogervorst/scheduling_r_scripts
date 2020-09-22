# scheduling_r_scripts

![](https://mirrors.creativecommons.org/presskit/buttons/80x15/svg/by.svg)

Authors: Roel M. Hogervorst

*Last change 2020-09-22*

This is an overview of many of the ways you can run an R script. 

This has become a rather large overview but I think could help a lot of R-users. This overview is for you if you want to know how to run your batch script (do one thing without supervision) automatically. This overview does not talk about shiny or plumber, they are both great products that do incredible work, but they both sort of asume they run on a computer that is always on. I'm talking about scripts that you run once every day/week/hour/ etc. If you want more complex workflows look into the [advanced scheduling page](advanced_scheduling.md).  

I'm trying to answer the following questions about all solutions:

* Where are the costs?
* How easy is it to set up and use. and how easy can you transfer your work to your coworker
* how easy it is to change things, the script, changing secrets or frequency? 
* Can you manage your entire configuration in code?
* Is there logging, how easy is it see what exactly went wrong?
* How precise is it and will it auto recover on failure?
* how do you have to deal with secrets? can they leak?
* in what country does it run (if a president of that country gets a tantrum and starts to lock others out)



I'm seperating out 3 usecases:

1. [You run it on your own computer](#own-computer)
2. [You have a server available (a computer that is always on and accesable to you)](#own-a-server)
3. [You use ephemeral services (serverless, pay for use only)](#ephemeral)

# Own computer

*[back to top](#scheduling_r_scripts)*

Think about your laptop / computer. You can run a script, but you can also make the computer run it. So in order of difficulty. 

1. Run it manually (`source("script.R")`)
2. Run it on a schedule

## Running it manually

*[back to top](#scheduling_r_scripts)*

Option 1 is really not sustainable, if you forget, it doesn't run. However the reality is that many companies  rely on such a manual step. So we cannot ignore it completely. Millions of people worldwide copy stuff into an excel file from another excel file, save and send it to someone else. Manual is good for unstable processes, with lots of changing demands. It also helps in applying the best algorithm of all: common sense. 

**Where are the costs?**: High costs in people hours, maintainance of tools and training of other people. Specifically when the person doing this is doing repetitive work that a computer could have done, these are wasted hours. The cost of  computer is already paid for in other ways. 

**How easy is it to set up and use. and how easy can you transfer your work to your coworker**: This is probably a process that evolved over time. Setup and use are unknown, untill someone new is trained to do it.

**how easy it is to change things, the script, changing secrets or frequency? **: Easy to change, some cost in new secrets that someone needs to type in. Changes in frequency only cost time that could have been spent on something else.

**Can you manage your entire configuration in code?**: No, this is manual

**Is there logging, how easy is it see what exactly went wrong?**: In my experience there is a lot of hidden configuration and no logs except for the brain of the user. 

**How precise is it and will it auto recover on failure?**: It can be as precise as you want, but it will cost you. It will auto recover on failure, because the user can retry. 

**how do you have to deal with secrets? can they leak?**: There is absolutely a risk of losing secrets and a risk of choosing easy passwords because you have to type them so much. Password managers are a help here. Leak can come from fishing or reuse of passwords.

**in what country does it run**: In the country where the user is at that moment. 

**Links**:

- why you need  a [password manager](https://www.howtogeek.com/141500/why-you-should-use-a-password-manager-and-how-to-get-started/)



## Running it on a schedule on your computer

*[back to top](#scheduling_r_scripts)*

For linux and mac you have CRON or CRONTAB. A system tool that executes functions on a schedule. For ease of use there  is a package (maybe multiple ones) to help you with setting up your R job called cronR (See links below). For windows systems there is task scheduler which works slightly differently but can do the same thing. There is also an R package to schedule scripts in task scheduler, taskscheduleR (See links below)

This is an easy thing to try out for yourself. And easy to switch from a script that runs manually without your input (except typing source). There are some small snags: on linux the cron process runs as a different user and so it might not have access to the same R library. (you can specify the user if you want) You also have to think about the directory where the R process starts. Many of these tasks are done for you with the two packages I mention at the links of this section. 

**Where are the costs?**: Lower than running it manually after some time investement to make it run for you (see XKCD comic at links of this section). The process, if it doesn't take all your system resources, can run while you are doing other things.

**How easy is it to set up and use. and how easy can you transfer your work to your coworker**: I think the initial setup is quite some work for inexperienced workers, but when it runs you can easily hand it over to a different user and set up in the same way. If your computer is turned off, the process will not run. 

**Can you manage your entire configuration in code?**: In theory yes, in practice not. You need to manually setup the cron task/ task scheduler and check that it works. 

**Is there logging, how easy is it see what exactly went wrong?**:  Cron does have logging, I'm not sure about task scheduler but there must be. Where to find the logs is sometimes difficult to find out. 

**How precise is it and will it auto recover on failure?**: You will not get a message that the job failed and it will not retry with CRON. It will fail and try again the next time the time of execution is there. It is quite precise, if you say start at 0900 than the process will start at 0900. 

**how do you have to deal with secrets? can they leak?**: It runs on your computer so it really depends on where you store the secrets. If you place them in a .Renviron file than it really depends on where you store it. If it stays with the folder where the R process starts than other processes do not have access to it. If you place it in your home folder all the R processes have access to it. If you hardcode the secrets in the script, than anyone who can access the script will have access. 

**in what country does it run**: In the country where the user is at that moment. 

**Links**:

* For linux and mac systems: [CronR (on CRAN)](https://cran.r-project.org/web/packages/cronR/)
* [help in scheduling with CRON use crontab.guru](https://crontab.guru)
* for Windows systems: [taskscheduleR (on CRAN)](https://cran.r-project.org/web/packages/taskscheduleR/vignettes/taskscheduleR.html)
* [XKCD, is it worth your time to automate a task?](https://xkcd.com/1205/ "There is always a relevant XKCD")
* for complex workflows with multiple steps look into the [advanced scheduling page](advanced_scheduling.md).  

# Own a server

*[back to top](#scheduling_r_scripts)*

In this case you own a server. A server is just a 'laptop' (usually without a screen, and sometimes in the cloud). Examples are: a raspberry pi you have lying around, an old laptop that you can use,  an actual server rack in house or office, or a cloud server for instance a virtual machine from one of the cloud providers (See links below). 

The largest issue is how you go from your scripts on your local computer to the server. You need some tools to send the scripts, for instance transfering files with SCP or syncthing or dropbox or something.  Or some sort of release process with git (see for example my git remote shiny server example in the links below)

The choices here are all dependent on how many jobs you run and the flexibility you seek. If you only run a few scripts and they have a fixed time than CRON is still a super useful tool. If you have several actions that depend on the output of each other then you need something else.  See the [advanced scheduling page](advanced_scheduling.md).  

**Where are the costs?**: The running costs of a server. This varies a lot, but is generally cheapest if you run it locally and you pay for size in the cloud environments. Also don't forget the costs in time of keeping the servers updated.

**How easy is it to set up and use. and how easy can you transfer your work to your coworker**: Hard, very hard to transfer to your coworker unless you script everything. 

**Can you manage your entire configuration in code?**: Yes, but in practice no. There are tools like ansible, chef and puppet that can set up your computers for you. But you need to move the files to the computer, make the files executable, set the permissions, setup CRON. 

**Is there logging, how easy is it see what exactly went wrong?**: As above:  Cron does have logging, I'm not sure about task scheduler but there must be. Where to find the logs is sometimes difficult to find out. 

**How precise is it and will it auto recover on failure?**: It is CRON or one of the [advanced options](advanced_scheduling.md). So it depends very much on your setup. But in the simple case, with CRON  there will be no mentioning of failure and no retries.

**how do you have to deal with secrets? can they leak?**: Cloud connected servers with ports open are constantly pommeled by adverseries who want to take over your server and steal secrets or run cryptominers on them, they are not evil but the opportunity is cheap. Servers need to be patched and firewalled. They can become compromised and your secrets will be leaked. Devices that run on your own network and that have no direct open ports to the internet are generally better off. So a raspberry pi or laptop on your network that sometimes calls an API is less at risk than a server that has a shiny server running that the entire internet can access. 

**in what country does it run**: Your own or at your choice dependent on the cloud provider. 

**links**:

* I'm not going to list all the VM (virtual machine) /  VPS (virtual private server) services that are available but there are a lot of options. You can look at for instance: digital ocean, google cloud project, amazon web services, and microsoft's azure cloud.
* [help in scheduling with CRON use crontab.guru](https://crontab.guru)
* git remote example of release on server( [blogpost on how I used this on shiny server](https://blog.rmhogervorst.nl/blog/2018/02/06/setting-up-a-version-controlled-shiny-server/))
* 

# Ephemeral

*[back to top](#scheduling_r_scripts)*

With ephemeral I mean the infrastructure does not really exist for you. You only care that the job is executed and you only get billed for infrastructure used. This is sometimes called serverless, even though it runs on servers. 

For most of these services you use a third party to run your stuff for you. That means configuration (a lot for AWS, Azure and GCP; less for heroku) and registration. 

## Serverless or Function as a Service (FAAS)

These are 'functions' things that run when you want them to run. Usually in response to a request, but they can be triggered by different things like a CRON type of message. 

It runs on cloud providers like Amazon (AWS lambda) , Google (GCP Cloud Functions ) or  Microsoft (Azure Functions) or anywhere with an open source framework  like openfaas.  

**Where are the costs?**: With FAAS you pay for execution, you get a bit for free but pay for use. The advantages are that very fast scripts cost next to nothing and you don't pay for not using it (unlike a server where you pay whether you used it or not). It is also massively parallel, you can run thousands of copies of the 'function' (you would pay for all the executions though) because they are all independent. Long running processes are expensive though.  

**How easy is it to set up and use. and how easy can you transfer your work to your coworker**: Most of the cloud operators have configuration possible with files and APIs, but for your first setup you would probably do it by hand. If you have something running it pays to make this all 'configuration in code', that way you can make variants and hand them to your coworkers. 

**Can you manage your entire configuration in code?**: Yes 

**Is there logging, how easy is it see what exactly went wrong?**: Unknown #TODO

**How precise is it and will it auto recover on failure?**:  FAAS often have things like a cold start and a hot start. They are superfast in hot start (miliseconds sometimes) but if you haven't used the function for a while it goes into storage and triggering it will give it a cold start and that might take 5-10 times longer. Triggering a cold function for your batch job at 0900h will maybe lose you some time but realisticaly it starts within a minute and so how much you care about this is up to you and your application.

**how do you have to deal with secrets? can they leak?** The major cloud providers have their own secrets stores where you can retrieve your keys from. In general these stores are well protected. You could of course lose secrets when you add them to the 

**in what country does it run**: You can set the country/region of your choice. The three cloud providers are worldwide distributed so you are probably fine.

**links**:

* [openfaas (Function as a Service) (I don't have an R tutorial yet #TODO )](https://www.openfaas.com/)
* R on AWS Lambda ([mediumpost](https://medium.com/bakdata/running-r-on-aws-lambda-9d40643551a6) & [AWS lambda R runtime](https://github.com/bakdata/aws-lambda-r-runtime))
* R on GCP Cloud functions ([package 'googleCloudRunner'](https://code.markedmondson.me/googleCloudRunner/))
*  [Azure functions with R](https://github.com/ktaranov/azure-function-r)



## Serverless integrated with version control

Most of the following use Docker or something similar in the background but you usually do not have to care about it.

### Gitlab

*[back to top](#scheduling_r_scripts)*

Gitlab introduced 'runners' years ago. There is a huge collection of runners available. these are docker containers that you can use. 

**Where are the costs?**: 2000 CI/CD minutes a month for free. You can buy 1000 additional CI/CD minutes for 10 dollar. If you self host gitlab and the runners than you pay for the servers, network etc yourself and there is no extra pay for CI/CD.  

**How easy is it to set up and use. and how easy can you transfer your work to your coworker**: I don't know yet #TODO

**Can you manage your entire configuration in code?**:  yes

**Is there logging, how easy is it see what exactly went wrong?**: I don't know yet #TODO

**How precise is it and will it auto recover on failure?**: I don't know yet #TODO

**how do you have to deal with secrets? can they leak?**:I don't know yet #TODO

**in what country does it run**: that depends on if you use a on premise gitlab instance or the public version. I cannot find where the public version lives.

**links**:

* a blogpost describing how to use R on  [gitlab with docker containers for package building ](https://blog.methodsconsultants.com/posts/developing-r-packages-with-usethis-and-gitlab-ci-part-ii/)

### Github

*[back to top](#scheduling_r_scripts)*

Github actions is not really meant for scheduling scripts, but it does support it. 

**Where are the costs?**:  Github actions are free for 2000 actions minutes/month

**How easy is it to set up and use. and how easy can you transfer your work to your coworker**: There are more and more examples but the setup was not super easy because the steps are slow

**Can you manage your entire configuration in code?**: Yes, it is the only way. You create a yaml file with all the configuration. 

**Is there logging, how easy is it see what exactly went wrong?**: There are logs in github actions, visible to everyone with access to the repo. But the feedbackloop is a bit slow if you ask me. 

**How precise is it and will it auto recover on failure?**: That depends. 

**how do you have to deal with secrets? can they leak?**: Similar to cloud services there is a way to store them as variables that are only accessable to the application and you. Everyone with write access to your repo can see the secrets. 

**in what country does it run**: I actually don't know #TODO

**links**:

* I created a blog post about github actions [here] and the github actions code lives [here](https://github.com/RMHogervorst/invertedushape/blob/main/.github/workflows/main.yml)



### Heroku

*[back to top](#scheduling_r_scripts)*

Heroku is not really a version control system. But they did create something that is tightly connected to your git repository. I also did not want to create a new category for heroku only. Heroku is slightly more expensive than other 'cloud' providers but for that money they take over a lot of work for you. 

**Where are the costs?**: You pay for addons and for running of the service. 

**How easy is it to set up and use. and how easy can you transfer your work to your coworker**: It is relatively easy to setup but there are some manual steps that you have to record somewhere. 

**Can you manage your entire configuration in code?**: Almost (the scheduling is not yet possible)

**Is there logging, how easy is it see what exactly went wrong?**: There are no logs, for the cheap version I'm using at leas. 

**How precise is it and will it auto recover on failure?**: It runs around the time of the schedule, not exactly. It fails silently. But if you run it from the command line you do see output. 

**how do you have to deal with secrets? can they leak?**: Similar to cloud services there is a way to store them as variables that are only accessable to the application and you.

**in what country does it run**: By default in the USA, it is possible to run in Europe. Maybe other places too? 

**links**:

* I wrote a blogpost about [running R on heroku](https://blog.rmhogervorst.nl/blog/2018/12/06/running-an-r-script-on-heroku/), and [an update from 2020](https://blog.rmhogervorst.nl/blog/2020/09/21/running-an-r-script-on-a-schedule-heroku/).



# Advice for useRs

*[back to top](#scheduling_r_scripts)*

# Other issues questions and solutions

* Difficulty in finding the correct directory for the process? use something like the [here](https://cran.r-project.org/package=here) or [rprojroot](https://cran.r-project.org/package=rprojroot) package. 
* [Mark Edmondson has a great overview of scheduling R scripts on Google Cloud platform here](http://code.markedmondson.me/4-ways-schedule-r-scripts-on-google-cloud-platform/)

# Typos, mistakes etc

*[back to top](#scheduling_r_scripts)*

Yes please open an issue or pull request to fix mistakes! For additions I would like an issue first to determine if they are within scope.

You spelled CRAN wrong! Distinguish between CRAN the Compehensive R Archive Network and CRON (stands for chonometer or something. the tool in unixes that you can use to schedule things).


# Reuse / licencing of this work
This text is licenced as CC BY 4.0 (creative commons attribiion 4.0 international). 
You are free to copy and redistribute the material in any medium or format, and to adapt, remix transform and build upon it, even commercially.
Just give me credit. 
See Licence file for more info.
