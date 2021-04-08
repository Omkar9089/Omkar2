# Part-1. Get Docker
Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.

## Part-2. Sample application

For the rest of this tutorial, we will be working with a simple todo list manager that is running in Node.js. If you’re not familiar with Node.js, don’t worry! No real JavaScript experience is needed!

At this point, your development team is quite small and you’re simply building an app to prove out your MVP (minimum viable product). You want to show how it works and what it’s capable of doing without needing to think about how it will work for a large team, multiple developers, etc.

![](https://docs.docker.com/get-started/images/todo-list-sample.png)

## Get the app
Before we can run the application, we need to get the application source code onto our machine. For real projects, you will typically clone the repo. But, for this tutorial, we have created a ZIP file containing the application.

Download the App contents. You can either pull the entire project or download it as a zip and extract the app folder out to get started with

Once extracted, use your favorite code editor to open the project. If you’re in need of an editor, you can use Visual Studio Code. You should see the package.json and two subdirectories (src and spec).
![](https://docs.docker.com/get-started/images/ide-screenshot.png)

## Build the app’s container image
In order to build the application, we need to use a Dockerfile. A Dockerfile is simply a text-based script of instructions that is used to create a container image. If you’ve created Dockerfiles before, you might see a few flaws in the Dockerfile below. But, don’t worry! We’ll go over them.

* 1. Create a file named Dockerfile in the same folder as the file package.json with the following contents.

` FROM node:12-alpine
 RUN apk add --no-cache python g++ make
 WORKDIR /app
 COPY . .
 RUN yarn install --production
 CMD ["node", "src/index.js"]
`

Please check that the file Dockerfile has no file extension like .txt. Some editors may append this file extension automatically and this would result in an error in the next step.

* 2. If you haven’t already done so, open a terminal and go to the app directory with the Dockerfile. Now build the container image using the docker build command.

` docker build -t getting-started .
`

This command used the Dockerfile to build a new container image. You might have noticed that a lot of “layers” were downloaded. This is because we instructed the builder that we wanted to start from the node:12-alpine image. But, since we didn’t have that on our machine, that image needed to be downloaded.

After the image was downloaded, we copied in our application and used yarn to install our application’s dependencies. The CMD directive specifies the default command to run when starting a container from this image.

Finally, the -t flag tags our image. Think of this simply as a human-readable name for the final image. Since we named the image getting-started, we can refer to that image when we run a container.

Then at the end of the docker build command tells that Docker should look for the Dockerfile in the current directory.

## Start an app container
Now that we have an image, let’s run the application! To do so, we will use the docker run command (remember that from earlier?).

* 1. Start your container using the docker run command and specify the name of the image we just created:

` docker run -dp 3000:3000 getting-started
`
Remember the -d and -p flags? We’re running the new container in “detached” mode (in the background) and creating a mapping between the host’s port 3000 to the container’s port 3000. Without the port mapping, we wouldn’t be able to access the application.

* 2. After a few seconds, open your web browser to http://localhost:3000. You should see our app!
![](https://docs.docker.com/get-started/images/todo-list-empty.png)

* 3. Go ahead and add an item or two and see that it works as you expect. You can mark items as complete and remove items. Your frontend is successfully storing items in the backend! Pretty quick and easy, huh?

At this point, you should have a running todo list manager with a few items, all built by you! Now, let’s make a few changes and learn about managing our containers.

If you take a quick look at the Docker Dashboard, you should see your two containers running now (this tutorial and your freshly launched app container)!
![](https://docs.docker.com/get-started/images/dashboard-two-containers.png)

## Part-3. Update the application
## Update the source code🔗

1. In the `src/static/js/app.js` file, update line 56 to use the new empty text.

` -                <p className="text-center">No items yet! Add one above!</p>`

` +                <p className="text-center">You have no todo items yet! Add one above!</p>`


2. Let’s build our updated version of the image, using the same command we used before.
` docker build -t getting-started .
`
3. Let’s start a new container using the updated code.
` docker run -dp 3000:3000 getting-started`

Here we got some error.
So, what happened? We aren’t able to start the new container because our old container is still running. The reason this is a problem is because that container is using the host’s port 3000 and only one process on the machine (containers included) can listen to a specific port. To fix this, we need to remove the old container.

## Replace the old container🔗
To remove a container, it first needs to be stopped. Once it has stopped, it can be removed. We have two ways that we can remove the old container. Feel free to choose the path that you’re most comfortable with.

## Remove a container using the CLI

* Get the ID of the container by using the `docker ps` command.
* Use the `docker stop` command to stop the container.
* Once the container has stopped, you can remove it by using the `docker rm` command.

## Remove a container using the Docker Dashboard
If you open the Docker dashboard, you can remove a container with two clicks! It’s certainly much easier than having to look up the container ID and remove it.

* With the dashboard opened, hover over the app container and you’ll see a collection of action buttons appear on the right.

* Click on the trash can icon to delete the container.

* Confirm the removal and you’re done!
![](https://docs.docker.com/get-started/images/dashboard-removing-container.png)

## Start the updated app container🔗

1. Now, start your updated app.
`docker run -dp 3000:3000 getting-started`

2. Refresh your browser on http://localhost:3000 and you should see your updated help text!
![](https://docs.docker.com/get-started/images/todo-list-updated-empty-text.png)

## Part-4. Share the application

## Create a repo🔗
To push an image, we first need to create a repository on Docker Hub.

* Sign up and share images using Docker Hub.
z
* Sign in to Docker Hub.

* Click the Create Repository button.

* For the repo name, use getting-started. Make sure the Visibility is Public.

* Click the Create button!

## Push the image🔗

* In the command line, try running the push command you see on Docker Hub. Note that your command will be using your namespace, not “docker”.
`
 $ docker push docker/getting-started
 The push refers to repository [docker.io/docker/getting-started]
 An image does not exist locally with the tag: docker/getting-started
`

>* Command to push the Image
` docker push YOUR-USER-NAME/getting-started`

## Run the image on a new instance

* Open your browser to Play with Docker.

* Click Login and then select docker from the drop-down list.

* Connect with your Docker Hub account.

## Part-5. Persist the DB

## The container’s filesystem🔗

When a container runs, it uses the various layers from an image for its filesystem. Each container also gets its own “scratch space” to create/update/remove files. Any changes won’t be seen in another container, even if they are using the same image.

## Container volumes🔗

Volumes provide the ability to connect specific filesystem paths of the container back to the host machine. If a directory in the container is mounted, changes in that directory are also seen on the host machine. If we mount that same directory across container restarts, we’d see the same files.

There are two main types of volumes. We will eventually use both, but we will start with named volumes.

## Persist the todo data🔗

By default, the todo app stores its data in a SQLite Database at /etc/todos/todo.db. If you’re not familiar with SQLite, no worries! It’s simply a relational database in which all of the data is stored in a single file. While this isn’t the best for large-scale applications, it works for small demos. We’ll talk about switching this to a different database engine later.

With the database being a single file, if we can persist that file on the host and make it available to the next container, it should be able to pick up where the last one left off. By creating a volume and attaching (often called “mounting”) it to the directory the data is stored in, we can persist the data. As our container writes to the todo.db file, it will be persisted to the host in the volume.

* 1.Create a volume by using the `docker volume create` command.

* 2.Stop the todo app container once again in the Dashboard (or with `docker rm -f <id>`), as it is still running without using the persistent volume.

* 3.Start the todo app container, but add the -v flag to specify a volume mount. We will use the named volume and mount it to `/etc/todos`, which will capture all files created at the path.

* 4.Once the container starts up, open the app and add a few items to your todo list.
![](https://docs.docker.com/get-started/images/items-added.png)

* 5.Remove the container for the todo app. Use the Dashboard or docker ps to get the ID and then docker rm -f <id> to remove it.

* 6.Start a new container using the same command from above.

* 7.Open the app. You should see your items still in your list!

* 8.Go ahead and remove the container when you’re done checking out your list.

## Dive into the volume
A lot of people frequently ask “Where is Docker actually storing my data when I use a named volume?” If you want to know, you can use the docker volume inspect command.

`docker volume inspect todo-db

[
    {
        "CreatedAt": "2019-09-26T02:18:36Z",

        "Driver": "local",

        "Labels": {},

        "Mountpoint": "/var/lib/docker/volumes/todo-db/_data",

        "Name": "todo-db",

        "Options": {},

        "Scope": "local"
    }
]`







