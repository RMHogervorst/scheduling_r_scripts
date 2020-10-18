# Advanced scheduling

[back to readme](README.md)

## Upgrading from CRON

You can run CRON on most unix systems (linux,  macos, etc). And CRON is super useful and reliable. But the logs are sometimes difficult to find, CRON will not warn you that a job failed, and if something depends on another thing you have to check yourself. If you have several tasks that depend on eachother 

* and they are all R scripts: use [DRAKE](#drake) , 
* if you have large data transformation steps, different databases to use and tasks in python and SQL too, you might want to look into [Airflow](#airflow) or [Luigi](#luigi) these are python based job schedulers for data transformations. 



## DRAKE

[drake docs](https://docs.ropensci.org/drake/) , [drake user manual](https://books.ropensci.org/drake/), [drake description on CRAN](https://cloud.r-project.org/web/packages/drake/index.html)

From the docs:

>  Data analysis can be slow. A round of scientific computation can take  several minutes, hours, or even days to complete. After it finishes, if  you update your code or data, your hard-earned results may no longer be  valid. How much of that valuable output can you keep, and how much do  you need to update? How much runtime must you endure all over again?

Drake analyses your steps, notices what is done and what is not and even visualises your steps. It also keeps logs of your work. It also has the ability to run on schedules although I do not know for sure. #helpwanted #TODO

There is now an alternative to DRAKE by the same makers: [TARGETS](https://wlandau.github.io/targets/)


# Airflow

[Apache Airflow](https://airflow.apache.org/) is :

>  created by the community to programmatically author, schedule and monitor workflows.

It is written in python and you define tasks in python too, it has monitoring, and a ui that helps you with your tasks. It is also integrated with many many many databases and platforms. Running R scripts is somewhat difficult. You have to use a  system call to make it work which leads to the same issues you have with CRON (where from should it start and as what user?)  Other options are to use a docker operator with the R script in it. 

# Luigi

https://luigi.readthedocs.io/en/stable/index.html

