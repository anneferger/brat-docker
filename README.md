# In progress: Running the docker image

Run brat-docker with docker installed using the following command:
```bash
$ docker run --name=brat -d -p 80:80 -v {/path/to/your/cloned/data/git/folder}:/bratdata -e BRAT_USERNAME=brat -e BRAT_PASSWORD=brat -e BRAT_EMAIL=brat@example.com anneferger/brat
```
`{/path/to/your/cloned/data/git/folder}` needs to be replaced with the local path to your data folder containing the files you want to annotate (it can be a git clone but doesn't have to be).
To run the container you need to specify a username, password and email address for BRAT as environment variables when you start the container. This user will have editor permissions. 

Visit http://localhost:80, login with your supplied username and password and start annotating. Brat automatically creates and changes `.ann` files containing the annotation. Those changes can be committed using git in the respective folder as usual.

To use another port for the BRAT installation use e.g. `-p 8081:80` and point the browser to http://localhost:8081.

The `annotation.conf` file containing the BRAT annotation specification should be supplied in the respective data folder on the top-level of the sub-folders. If you added or made changes to the `annotation.conf` you can restart the docker container using `docker stop brat` and `docker start brat` for the annotation changes to be available in brat. 

To remove the brat-docker container stop it using `docker stop brat` and delete it using `docker container rm brat`.

# NOTE

I am no longer doing anything with brat and am not maintaining this at all. 

# brat docker

This is a docker container deploying an instance of [brat](http://brat.nlplab.org/).


### Installation

You will need two volumes to pass annotation data and user configuration to the container. 
Start by creating a named volume for each of them like this:

```bash
$ docker volume create --name brat-data
$ docker volume create --name brat-cfg
```

The `brat-data` volume should be linked to your annotation data, and the `brat-cfg` volume should contain a file called `users.json`.
To add multiple users to the server use `users.json` to list your users and their passwords like so:

```javascript
{
    "user1": "password",
    "user2": "password",
    ...
}
```

The data in these directories will persist even after stopping or removing the container.
You can then start another brat container as above and you should see the same data. 
Note that if you are using `docker < 1.9.0` named volumes are not available and 
you'll have to use a data-only container and `--volumes-from` instead.

You can also add data and edit the users from within the container. To add some data directly inside the container do something like:
``` bash
$ docker run --name=brat-tmp -it -v brat-data:/bratdata cassj/brat /bin/bash
$ cd /bratdata
$ wget http://my.url/some-dataset.tgz
$ tar -xvzf some-dataset.tgz
$ exit  
$ docker rm brat-tmp
```

Or, if you have data on the host machine, you can check where docker is keeping the named volume with: 

```bash
$ docker volume inspect brat-data 
```
and you can just copy the data into there from your host.


### Running

To run the container you need to specify a username, password and email address for BRAT as environment variables when you start the container. This user will have editor permissions.
```bash
$ docker run --name=brat -d -p 80:80 -v brat-data:/bratdata -v brat-cfg:/bratcfg -e BRAT_USERNAME=brat -e BRAT_PASSWORD=brat -e BRAT_EMAIL=brat@example.com cassj/brat
```
