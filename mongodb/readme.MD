NOTES
------
when a container is started for the first time it will execute files with extenstions .sh and .js that are found in /docker-entrypoint-initdb.d. files will be executed in alphabatical order .js files will be executed by mongosh