##Prerequisites

* Git version 1.7.11.3 or higher
* Gradle 1.0-milestone-9 or higher

##Build Steps

* Install the latest [git](http://git-scm.com/book/en/Getting-Started-Installing-Git) and [gradle](http://gradle.org/installation) packages.

*  Get the EVCache source from github
   <pre><code> 
    git init
    git remote add --track master origin git@github.com:Netflix/EVCache.git
    git pull
   </pre></code> 

* Now, build the EVCache by executing the following in the directory where you pulled your sources.

    <pre><code> 
    ./gradlew clean build test
    </pre></code> 

* After a successful build, you can find the following executable jars
     * EVCache Client (./client/build/libs/evcache-client-*.jar )
