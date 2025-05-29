# Tiddlywiki on fly.io


1. [Create a repository from this template](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template)


2. [Install `flyctl`](https://fly.io/docs/flyctl/install/) - to read this configuration, build it & deploy it to the cloud

> [!NOTE]
> `Dockerfile` defines instructions for creating an operating system and installing `TiddlyWiki` to it. `Fly` reads these instructions, builds it on their cloud infrastructure, and deploys this built operating system to their cloud.


3. Create your `fly` machine

```sh
fly launch
```


4. Create a `TiddlyWiki`
  
**If you don't have an existing `TiddlyWiki` you can skip to step 6** & use the template `wiki.tar` provided here.

> [!NOTE]
> This template was created via the `Tiddlywiki` CLI ...
> 
> ```sh
> npx tiddlywiki ./wiki --init server
> tar -cf wiki.tar wiki/
> ```

**If you do have an existing single-file `TiddlyWiki`** you'll have to convert it for `nodejs`

Install [`nodejs`](https://nodejs.org/en/download)

```sh
npx tiddlywiki --load ./mywiki.html --savewikifolder ./tiddlers
```

> [!NOTE]
> Your wiki should look like ...
> 
> ```
> wiki
> ├── tiddlers
> │   └── $__StoryList.tid
> └── tiddlywiki.info
> ```


5. Zip your `TiddlyWiki` files

So you can copy this zip archive to your [fly.io](https://fly.io) machine.

```sh
tar -cf wiki.tar wiki/
```


6. Copy your zipped `TiddlyWiki` to your [fly.io](https://fly.io) machine via `sftp`

```sh
fly sftp shell
cd /data
put wiki.tar
```

> [!WARNING]
> Can't connect to your fly machine? Checkout [Fix app has no started VMs](#app-has-no-started-vms)


7. Extract your `TiddlyWiki` files via `ssh`

```sh
fly ssh console
cd /data
tar -xf wiki.tar
rm wiki.tar
```

> [!WARNING]
> Can't connect to your fly machine? Checkout [Fix app has no started VMs](#fix-app-has-no-started-vms)


8. Add authentication

Create environment variables `$TWUSER` & `$TWPASS` to define your username & password with the `flyctl` CLI -

```sh
fly secrets set TWUSER=username
fly secrets set TWUSER=password
fly deploy
```

> [!NOTE]
> `Dockerfile` defines a startup command `npx tiddlywiki --listen` which specifies that `TiddlyWiki` look for a username & password in these environment variables


**And we're done.**

**You should now see your TiddlyWiki live on FlyIO & ready to save Tiddlers**


---


## How to

### Fix app has no started VMs 

If you see something like ...

```
Error: app <your-app-name> has no started VMs.
It may be unhealthy or not have been deployed yet.
Try the following command to verify:

fly status
```

... your machine has auto-shutdown, so you'll need to start it back up again if you want to connect to it. You can do so by finding your application's URL either from the prior `fly launch` command, or from your [fly.io](https://fly.io) dashboard.
