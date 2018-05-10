# saving packages offline
One of the things I found convenient about Windows was that I could save stand-alone setups for programs on a storage device
so that I didn't have to download a shit ton after every fresh install of Windows. 

I tried to find a way to do the same on Ubuntu (after making my desktop look rad and downloading a shit ton) but the search process
for me as a newbie wasn't very... great. 

I tried all the methods here: 
https://help.ubuntu.com/community/InstallingSoftware#Installing_packages_without_an_Internet_connection
but they either failed completely or it wasn't what I was looking for.

Keryx was taking waaay too long to create new projects and I didn't see how I could use it on a fresh install of Ubuntu
when downloading from another computer isn't even an option.
Synaptic Package Manger worked to some extent but I couldn't figure out how to save already installed packages for offline 
installation. Also, it broke sometimes (many times).

Another crazy thing (atleast for a newbie) I tried was copying all the *.deb files from `/var/cahce/apt/archives` to a freshly 
installed Ubuntu 18.04 system and run a `dpkg -i *.deb` followed by `apt-get -f install`. While this did install some of the
packages without any problems, I ran into many problems for the most part. This method wasn't very efficient anyway.

Finally I decided to do things manually (which again, as a newbie, is a scary thought)... 

After some googling I came across this answer - https://stackoverflow.com/a/41428445/4591121

And that was exactly what I needed! After acquiring some basic understanding of bash and testing the above method I came up with
a crude but working solution for myself.

I have the following scripts on an external storage:

### download_package
```
echo Downloading $1
mkdir $1
cd $1

sudo apt-get download $(apt-cache depends --recurse --no-recommends --no-suggests --no-conflicts --no-breaks --no-replaces --no-enhances --no-pre-depends $1 | grep "^\w" | sort -u)

dpkg-scanpackages . | gzip -9c > Packages.gz
```

### add_to_source_list
```
DIRECTORY="$PWD/$1"
echo "deb [allow-insecure=yes] file:$DIRECTORY ./" | sudo tee -a /etc/apt/sources.list
echo "Run sudo apt-get update and then sudo apt-get install <whatever you downloaded>"
```

Now if I have to install, say, vlc on a fresh install of Ubuntu with no internet.

1. `./download_package vlc` (Did this some time in the past)
2. Connect the drive to the computer
3. `./add_to_source_list vlc` 
4. `sudo apt-get update`
5. `sudo apt-get install --allow-unauthenticated vlc`

Not exactly an elegant solution, but works for me. 



