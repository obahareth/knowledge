# Ruby on Rails

[TOC]



## References

- [kubernetes-rails.com](http://kubernetes-rails.com/) by [Marco Colli](http://collimarco.com/) is a goldmine of information on running Rails on Kubernetes. I've archived it using the Wayback Machine just in case [here](https://web.archive.org/web/20191112135115/http://kubernetes-rails.com/). This page is taken from it.



## Deploying a Rails application to Kubernetes

There are many ways to deploy a Ruby on Rails application: one of them is using Docker containers and Kubernetes for orchestration. This guide shows some of the advantages of Kubernetes compared to other solutions and explains how to deploy a Rails application in production using Kubernetes. We focus on the usage of containers for production, rather than development, and we value simple solutions. This guide covers all the common aspects required for running a Rails application in production, including the deployment and continuous delivery of the web application, the configuration of a load balancer and domain, the environment variables and secrets, the compilation of assets, the database migrations, logging and monitoring, the background workers and cron jobs, and how to run maintenance tasks and updates.

## History and alternatives to Kubernetes

The easiest way for deploying a Rails application is probably using a PaaS, like Heroku, which makes the deployment and scaling extremely simple and lets you forget about servers. However:

- as the application scales, the cost may become prohibitive for your kind of business;
- you don't have full control of your application, which is managed by others, and this may raise concerns about uptime;
- you have constraints imposed by the platform;
- your application may become bounded to a specific platform, raising portability concerns.

A cheaper alternative is using a IaaS, like DigitalOcean. You can start with a single server, but soon you will need to scale horizontally on multiple servers. Usually you have at least one load balancer with HAProxy, some web servers with nginx and Puma, a database (probably Postgresql and Redis with replicas) and maybe some separate servers for background processing (e.g. Sidekiq). When you need to scale the application you just create a snapshot of a server and you replicate it. You can also manage or update multiple servers with pssh or using configuration management tools like Chef and the application can be easily deployed with Capistrano. It is not very hard to create and configure a bunch of servers. However:

- the initial setup requires some time and knowledge;
- applying changes to many servers may become painful;
- running the wrong command on a fleet of servers may be difficult to revert;
- you must make sure to keep all the servers updated with the same configuration;
- scaling requires a lot of manual work.

Kubernetes offers the advantages of a PaaS at the cost of a IaaS, so it is a good compromise that you should consider. It is also an open source technology and most cloud providers already offer it as a managed service.

Let's see how to deploy a Rails application in production using Kubernetes.

## Prerequisites

This guide assumes that you already have general knowledge about web development.

We also expect that you already have a development machine with all the necessary applciations installed, including Ruby (e.g. using rbenv), Ruby on Rails, Git, Docker, etc.

You also need to have an account on Docker Hub and DigitalOcean in order to try Kubernetes (or you can use your favorite alternatives).

## The Rails application

You can use an existing Rails application or you can create an example Rails application with this command:

```bash
rails new kubernetes-rails-example
```

Then add a simple page to the example application:

*config/routes.rb**app/controllers/pages_controller.rb*

```ruby
class PagesController < ApplicationController
  def home
  end
end
```

*app/views/pages/home.html.erb*

## The Git repository

Let's save the changes in the local Git repository, which was already initialized by Rails:

```shell
git add .
git commit -m "Initial commit"
```

Then we need to create a Git repository online. Go to Github and create a new repository, then connect the local repository to the remote one and publish the changes:

```shell
git remote add origin https://github.com/username/kubernetes-rails-example.git
git push -u origin master
```

Although a Git repository is not strictly required for Docker and Kubernetes, I mention it here because most CI/CD tools, including Docker Hub, can be connected to your Git repository in order to build the Docker image automatically whenever you push some changes.

## The Docker image

First step for containerization is to create the Docker image. A Docker image is simply a package which contains our application, together with all the dependencies and system libraries needed to run it.

Add this file in the root folder of your Rails application:

*Dockerfile*

First we use `FROM` to tell Docker to download a public image, which is then used as the base for our custom image. In particular we use an image which contains a specific version of Ruby.

Then we use `RUN` to execute a command inside the image that we are building. In particular we use `apt-get` to install some libraries. Note that the libraries available on the default Ubuntu repositories are usually quite old: if you want to get the latest versions you need to update the repository list and tell APT to download the libraries directly from the maintainer's repository. In particular, just before `apt-get`, we can add the following commands to update the Node.js and Yarn repositories:

```dockerfile
RUN curl https://deb.nodesource.com/setup_12.x | bash
RUN curl https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
```

In the next block of code, we copy our Rails application to the image and we install all the required gems using Bundler. The Gemfile is copied before the other application code because Docker can use caching to build the image faster in case there aren't any changes to the Gemfile.

We also run a task to precompile the assets (stylesheets, scripts, etc.).

Finally we configure a default command to be executed on the image.

Before building the image we want to make sure that some files are not copied to it: this is important to exclude *secrets*, for security, and useless directories, like `tmp` or `.git`, which would be a waste of resources. For this we need to create a `.dockerignore` file in the Rails root: you can usually take inspiration from your `.gitignore`, since the aim and syntax are very similar.

It's time to build the image:

```shell
docker build -t username/kubernetes-rails-example:latest .
```

The `-t` option, followed by its argument, is optional: we use it to assign a *name* and a *tag* to the new image. This makes it easier to find the image later. We can also use a *repository* name as the image name, in order to allow push to that repository later. The image name is the part before the colon, while the tag is the part after the colon. Note that we could also omit the tag `latest` since it is the default value used in case the tag is omitted. The last dot is a required argument and indicates the path to the Dockerfile.

Then you can see that the image is actually available on your machine:

```shell
docker image ls
```

You can also use the image ID or its name to run the image (a running image is called a *container*):

```shell
docker run -p 3000:3000 username/kubernetes-rails-example:latest
```

Note that we map the host port 3000 (on the left) to the container port 3000 (on the right). You can also use other ports if you prefer: however, if you change the container port, you also need to update the image to make sure that the Rails server listens on the correct port and you also need to open that port using `EXPOSE`.

You can now see your webiste by visiting `http://localhost:3000`.

Finally we can push the image to the online repository. First you need to sign up to Docker Hub, or to another *registry* and create a *reposotory* there for your image. Then you can push the local image to the remote repository:

```shell
docker push username/kubernetes-rails-example:latest
```

## The Kubernetes cluster

It's time to create the Kubernetes cluster for production. Go to your favorite Kubernetes provider and create a cluster using the dashboard: we will use DigitalOcean for this tutorial.

Once the cluster is created you need to downlooad the credential and cluster configuration to your machine, so that you can connect to the cluster. For example you can move the configuration file to `~/.kube/kubernetes-rails-example-kubeconfig.yaml`. Then you need to pass a `--kubeconfig` option to `kubektl` whenever you invoke a command, or you can set an environment variable:

```shell
export KUBECONFIG=~/.kube/kubernetes-rails-example-kubeconfig.yaml
```

Then you need to make sure that you have Kubernetes installed and that it can connect to the remote cluster. Run this command:

```shell
kubectl version
```

You should see the version of your command line tools and the version of the cluster.

You can also play around with this command:

```shell
kubectl get nodes
```

A *node* is simply a server managed by Kubernetes. Then Kubernetes creates some virtual machines (in a broad sense) called *pods* on each node, based on our configuration. Pods are distributed automatically by Kubernetes on the available nodes and, in case a node fails, Kubernetes will move the pod to a different node. A pod usually contains a single *container*, but it can also have multiple related containers that need to share some resources.

The next step is to run some pods with our Docker image. Since our Docker image is probably hosted in a private repository, we need to give the Docker credentials to Kubernetes, so that it can download the image. Run these commands:

```shell
kubectl create secret docker-registry my-docker-secret --docker-server=DOCKER_REGISTRY_SERVER --docker-username=DOCKER_USER --docker-password=DOCKER_PASSWORD --docker-email=DOCKER_EMAIL
kubectl edit serviceaccounts default
```

And add this to the end after `Secrets`:

```yaml
imagePullSecrets:
- name: my-docker-secret
```

Then you can define the Kubernetes configuration: create a `config/kube` directory in your Rails application.

We start by defining a deploy for the Rails application:

*config/kube/deployment.yml*

The above is a minimal deployment:

- `apiVersion` sets the API version for the configuration file;
- `kind` sets the type of configuration file;
- `metadata` is used to assign a name to the deployment;
- `replicas` tells Kubernetes to spin up a given number of pods;
- `selector` tells Kubernetes what template to use to generate the pods;
- `template` defines the template for a pod;
- `spec` sets the Docker image that we want to run inside the pods and other configurations, like the container port that must be exposed.

Now we also need to forward and distribute the HTTP requests to the pods. Let's create a load balancer:

*config/kube/load_balancer.yml*

Basically we tell the load balancer:

- listen on the default port 80;
- forward the requests on port 3000 of the pods that have the label `rails-app`.

Now we can apply the configuration to Kubernetes, using a *declarative* management:

```shell
kubectl apply -f config/kube
```

It't time to verify that the pods are running properly:

1. `kubectl get pods` shows the pods and their status;
2. the status for all pods should be `Running`;
3. if you see the error `ImagePullBackOff` probably you have not configured properly the secret to download the image from your private repository;
4. you can get more details about any errors by running `kubectl describe pod pod-name`;
5. you can fix the configurations and then regenerate the pods by running `kubectl delete --all pods`.

Now you can get the load balancer IP and other information by running the folowing commands:

```shell
kubectl get services
kubectl describe service service-name
```

In particular you must find the `LoadBalancer Ingress` or `EXTERNAL-IP` and type it in your browser address bar: our website is up and running!

## Domain name and SSL

Probably your users won't access your website using the IP address, so you need to configure a domain name. Add the following record to your DNS:

```
example.com.   A   192.0.2.0
```

Obviously you need to replace the domain name with your domain and the IP address with the *external IP* of the load balancer (you can get it using `kubectl get services`).

An SSL certificate can be added to your website using proprietary YAML configurations, but the easiest way, if you are using DigitalOcean, is to use their dashboard to configure the SSL certificate (i.e. go to the load balancer settings).

## Environment variables

There are different solutions for storing environment variables:

- you can define the env variables inside your Rails configuration;
- you can define the env variables inside your Dockerfile;
- you can define the env variables using Kubernetes.

I suggest that you use Kubernetes for managing the environment variables for production: that makes it easy to change them without having to build a new image each time. Also Kubernetes has more information available about your environment and it can set some variables dinamically for you (e.g. it can set a variable with the pod name or IP). Finally it offers more granularity when you have multiple deployments (e.g. Puma and Sidekiq), since you can set different values for each deployment.

Add the following attribute to your *container* definition (e.g. after the `image` attribute) inside *config/kube/deployment.yml*:

```yaml
env:
- name: EXAMPLE
  value: This env variable is defined by Kubernetes.
```

Then you can update the Kubernetes cluster by running this command:

```shell
kubectl apply -f config/kube
```

If you want to make sure that everything works, you can try to print the env variable inside your app.

For development and test you can use a gem called *dotenv* to easily define the env variables.

For production you can also use Kubernetes *ConfigMaps* to define the env variables. The advantage of using this method is that you can define the env variables once and then use them for different *Deployments* or *Pods*. For example a Rails application usually requires the followig variables:

*config/kube/env.yml*

Then add the following attribute inside a *container* definition (e.g. after the `image` attribute):

*config/kube/deployment.yml*

Remember that it is not safe to store secrets, like `SECRET_KEY_BASE`, in your Git repository! In the next section we will see how to use Rails credentials to safely store your secrets.

## Secrets

You can store your secrets either in your Rails configuration or using Kubernetes secrets. I suggest that you use the Rails *credentials* to store your secrets: we will use Kubernetes secrets only to store the master key. Basically we store all our credentials in the Git repository, along with our application, but this is safe because we encrypt them with a master key. Then we use the master key, not stored in the Git repository, to access those secrets.

Enable this option inside *config/environments/production.rb*:

```ruby
config.require_master_key = true
```

Then run this command to edit your credentials:

```shell
EDITOR="vi" rails credentials:edit
```

While editing the file add the following line and then save and close:

```ruby
example_secret: foobar
```

Then inside your app you can try to print the secret:

*app/views/pages/home.html.erb*

Note that starting from Rails 6 you can also define different credentials for different environments.

If you run your website locally you can see the secret displayed properly. The last thing that we need to do is to give the master key to Kubernetes in a secure way:

```shell
kubectl create secret generic rails-secrets --from-literal=rails_master_key='example'
```

Your master key is usually stored in *config/master.key*.

Finally we need to pass the Kubernetes secret as an environment variable to our containers. Add this variable to your `env` inside *config/kube/deployment.yml*:

```yaml
- name: RAILS_MASTER_KEY
  valueFrom:
    secretKeyRef:
      name: rails-secrets
      key: rails_master_key
```

In order to test if everything works, you can rebuild the Docker image and deploy the new configuration: you should see the example secret (not a real secret) displayed on your homepage.

## Logging

There are two different strategies for logging:

- send the logs directly from your Rails app to a centralized logging service;
- log to *stdout* and let Docker and Kubernetes collect the logs on the node.

The first option is simple, but you don't collect the Kubernetes logs and might be less efficient. In any case you can use a gem like *logstash-logger* for this.

If you want to use the second option, you can enable logging to stdout for your Rails app by settings the env variable `RAILS_LOG_TO_STDOUT` to `enabled`.

Then you can see the latest logs using this command:

```shell
kubectl logs -l app=rails-app
```

Basically, when you run the command, the Kubernetes master node gets the latest logs from the nodes (only for pods with label `rails-app`) and displays them. This is useful for getting started, however logs are not persistent and you need to make them searchable. For this reason you need to send them to a centralized logging service: we can use Logz.io for example, which offers a managed ELK stack. In order to send the logs from Kubernetes to ELK we use Fluentd, which is a log collector written in Ruby and a CNCF graduated project.

This is how logging works:

1. your Rails application and other Kubernetes components write the logs to stdout;
2. Kubernetes collects and store the logs on the nodes;
3. you use a Kubernetes DaemonSet to run a Fluentd pod on each node;
4. Fluentd reads the logs from the node and sends them to the centralized logging service;
5. you can use the logging service to read, visualize and search all the logs.

You can install Fluentd on your cluster with these simple commands:

```shell
kubectl create secret generic logzio-secrets --from-literal=logzio_token='MY_LOGZIO_TOKEN' --from-literal=logzio_url='MY_LOGZIO_URL' -n kube-system

kubectl apply -f https://raw.githubusercontent.com/collimarco/logzio-k8s/use_k8s_secrets/logzio-daemonset-rbc.yaml
```

If you need custom configurations, you can download the file and edit it before running `kubectl apply`. Note that if you use services different from Logz.io the strategy is very similar and you can find many configuration examples on the Github repository *fluent/fluentd-kubernetes-daemonset*.

You can verify if everything works by visiting your website and then checking the logs.

## Background jobs

Let's see how to run Sidekiq on Kubernetes, in order to have some background workers.

First of all you need to add Sidekiq to your Rails application:

*Gemfile*

Then run `bundle install` and create an example worker:

*app/jobs/hard_worker.rb*

Finally add the following line to your `PagesController#home` method (or anywhere else) to create a background job every time a request is made:

```ruby
HardWorker.perform_async
```

Now the interesting part: we need to add a new deployment to Kubernetes for running Sidekiq. The deployment is very similar to what we have already done for the web application: however, instead of running Puma as the main process of the container, we want to run Sidekiq. Here's the configuration:

*config/kube/sidekiq.yml*

Basically we define a new deployment with two pods: each pod runs our standard image that contains the Rails application. The most interesting part is that we set a `command` which overrides the default command defined in the Docker image. You can also pass some arguments to Sidekiq using an `args` key.

Also note that we define a `REDIS_URL` variable, so that Sidekiq and Rails can connect to Redis to get and process the jobs. You should also add the same env variable to your web deployment, so that your Rails application can connect to Redis and schedule the jobs. For Redis itself you can use Kubernetes *StatefulSets*, you can install it on a custom server or use a managed solution: although it is easy to manage a single instance of Redis, scaling a Redis cluster is not straightforward and if you need scalability and reliability probably you should consider a managed solution.

As always, you can apply the new configuration to Kubernetes with `kubectl apply -f config/kube`.

Finally you can try to visit your website and make sure that everything works: when you load your homepage, the example job is scheduled and you should see *It works!* in your logs.

## Cron jobs

There are different strategies to create a cron job when you deploy to Kubernetes:

- use Kubernetes built-in cron jobs to run a container periodically;
- use some Ruby background processes to schedule and perform the jobs.

A problem with the first approach is that you have to define a Kubernetes config file for each cron job. If you want to use this solution you can use *Kubernetes CronJobs* in combination with *rake tasks* or *rails runner*.

If you use the second method, you can schedule the cron jobs easily using Ruby. Basically you need a Ruby process that is always running in background and takes care to create the jobs when the current time matches a cron pattern. For example you can install the *rufus-scheduler* gem and then run a dedicated container: however in this case you have a single point of failure and if the pod is rescheduled a job may be lost. In order to have a more distributed and relaible environment, we can use a gem like *sidekiq-cron*: it runs a scheduler thread on each sidekiq server process and it uses Redis in order to make sure that the same job is not scheduled multiple times. For example, if you have N sidekiq replicas, then there are N processes that check the schedule every minute and if the current time matches a cron line, then they try to get a Redis lock: if a thread manages to get the lock, it means that it is responsible for scheduling the Sidekiq jobs for that time, otherwise it simply does nothing. Finally the Sidekiq jobs are executed normally, as the other background jobs, and thus can easily scale horizontally on the existing pods and get a reliable processing with retries.

Let's add this gem to the Rails application:

*Gemfile*

Then run `bundle install` and create an initializer:

*config/initializers/sidekiq.rb*

Finally define a schedule:

*config/schedule.yml*

Then when you start Sidekiq you will see that the worker is executed once every minute, regardless of the number of pods running.

## Console

You can connect to a pod by running this command:

```shell
kubectl exec -it my-pod-name bash
```

Basically we start the bash process inside the container and we attach our interactive input to it using the `-it` options.

If you need a list of the pod names you can use `kubectl get pods`.

Altough you can connect to any pod, I find it useful to create a single pod named `terminal` for maintenance tasks. Create the following file and then run `kubectl apply -f kube/config`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: terminal
spec:
  containers:
  - name: terminal
    image: username/kubernetes-rails-example:latest
    command: ['sleep']
    args: ['infinity']
    env:
    - name: EXAMPLE
      value: This env variable is defined by Kubernetes.
```

All containers must have a main running process, otherwise they exit and Kubernetes considers that as a crash. Running the default command for the image, the Rails server, would be a waste of resources, since the pod is not connected to the load balancer: instead we use `sleep infinity`, which is basically a no-op that consumes less resources and keeps the container running.

Once you are connected to the bash console of a pod you can easily run any command. If you want to run only a single command, you can also start it directly. For example, if you want to start the *Rails console* inside a pod named `terminal`, you can run this command:

```shell
kubectl exec -it terminal rails console
```

If you need to pass additional arguments to the process you can use `--` to separate the Kubernetes arguments from the command arguments. For example:

```shell
kubectl exec -it terminal -- rails console -e production
```

## Rake tasks

There are different ways to run a rake task on Kubernetes:

- you can create a Kubernetes Job to run the rake task in a dedicated container;
- you can connect to an existing pod and run the rake task.

For simplicity I prefer the second alternative.

Run the following command to list all the pods available:

```shell
kubectl get pods
```

Then you can run a task with this command:

```shell
kubectl exec my-pod-name rake task-name
```

Note that `kubectl exec` returns the status code of the command executed (i.e. `0` if the rake task is executed successfully).

## Database migrations

Migrating the database without downtime when you deploy a new version of your application is not a simple task. The origin of most problems is that both deploying the new code to all pods and running the database migration are long tasks that take some time to be completed. Basically they are not instant and atomic operations, and during that time you have at least one of the following situations:

- old code is running with the new database schema;
- new code is running with the old database schema.

Let's analyse in more detail some strategies:

- **Downtime**: If you could afford some downtime during migrations, than you would simply scale down your replicas to zero, run the migrations with a Kubernetes Job and then scale up your application again. **Pros**: simple, with no special requirements in your application code; no runtime errors during deployment due to different schemas. **Cons**: some minutes of downtime.
- **Deploy new code, then migrate**: You deploy the new code, updating the images on all pods, and then, when everything is finished, you run the migration. At first this seems to works perfectly if you can make your new code support the old schema (which is not always easy). However when the new database schema is applied, you still have to deal with ActiveRecord caching the old database schema in the Ruby process (thus making an additional restart required). If you choose this trategy, you can deploy your new code and then simply connect to one of the pods and run `rake db:migrate`. **Pros:** zero downtime; deployment is very simple. **Cons**: it is very difficult to make code backward compatible; you probably need an additional restart after the migration.
- **Migrate, then deploy new code**: This is the most common approach and it is used by Capistrano, Heroku and other CI/CD tools. The problem is that rolling out an update to many pods takes time and during that period you have the old code running with the new database schema. In order to avoid transient errors, you need to make the new schema backward compatible, so that it can run with both the old code and the new code: however it is not alwasy easy to write *zero downtime migrations* and there are many pitfalls. Also, in order to avoid additional problems, you should use a different Docker tag for each image (and not just the tag `latest`), otherwise an automatic reschedule may fetch the new image before the migration is complete. If you choose this strategy, you need to use a *Kubernetes Job* to deploy a single pod with the new image and run the migration and then, if the migration succeeds, update the image on all pods. **Pros**: common and reliable strategy. **Cons**: if you don't write backward compatible migrations, some errors may occur while the new code is being rolled out; you need to use different Docker tags for each image version if you want to prevent accidental situations where the new code runs before the migration.

If we choose the latest solution, we must define a job like the following:

*config/kube/migrate.yml*

Then you can run the migration using this command:

```shell
kubectl apply -f config/kube/migrate.yml
```

Then you can see the status of the migration:

```shell
kubectl describe job migrate
```

The above command also displays the name of the pod where the migration took place. You can then see the logs:

```shell
kubectl logs pod-name
```

When the job is completed you can delete it, so that you free the resources and you can run it again in the future:

```shell
kubectl delete job migrate
```

## Continuous delivery

In the previous sections we have configured the Kubernetes cluster and deployed the code manually. However it would be useful to have a simple command to deploy new versions of your app whenever you want.

Create the following file and make it executable (using `chmod +x deploy.sh`):

*deploy.sh*

Then you can easiliy release a new version with this command:

```shell
./deploy.sh
```

The above command executes the following steps:

1. use sh as the interpreter and set options to print each command and exit on failure;
2. build and publish the docker image;
3. run the migrations and then wait the completion of the job and delete it;
4. finally release the new code / image.

Also remember that if you change the Kubernetes configuration you need to run this command:

```shell
kubectl apply -f kube/config
```

## Monitoring

You need to monitor the Kubernetes cluster for various reasons, for example:

- understand the resource usage and scale the cluster accordingly;
- check if there are anomalies in the usage of resources, like pods that are using too many resources;
- check if all the pods are running properly or if there are some failures.

Usually the Kubernetes provider already offers a *dashboard* with many useful stats, like CPU usage, load average, memory usage, disk usage and bandwidth usage across all the nodes. Usually they collect the stats using a *DaemonSet*, in a way similar to what we have previously described for logging. If you prefer, you can also install custom monitoring agents on all nodes: you can use open source products like Prometheus or services like Datadog.

Other ways to monitor your application performance are:

- installing the Kubernetes *metrics-server* that stores the stats in memory and allows you to use commands like `kubectl top nodes/pods`;
- using stats from the load balancer;
- collecting synthetic metrics generated by ad-hoc requests sent from an external service to your application, for example in order to measure the response time from an external point of view;
- collecting stats directly from your Rails application using a gem for *application performance monitoring*, like Datadog APM or New Relic APM.

## Security updates

There is a misconception that you can forget about security updates when you use containers. That is not true. Even if Docker and containers add an additional layer of isolation, in particular from the host and from other containers, and they are also ephemeral, which is good for security, they still need to be updated in order to avoid application exploits. Note that an attack at the application layer it is also possible when there is a security bug at a lower layer, for example in the OS or inside libraries included in the base image.

If you run Rails on Kubernetes remeber to apply updates to the following layers:

- **Kubernetes and nodes**: most Kubernetes providers will apply the updates for you to the underlying nodes and to Kubernetes, so that you can forget about this layer. However you may need to enable an option for automatic updates: for example, if you use DigitalOcean, remember to go the Kubernetes settings from the dashboard and enable the automatic updates option.
- **Docker and containers**: you need to keep your containers updated. In particular make sure that you are using an updated version of the base image. If you use Ruby as the base image, use a tag like `2.5` instead of `2.5.1`, so that you don't forget to increase the patch version when there is a new patch available. However that is not enough: when a new OS patch is available, the Ruby maintainers release a new version of the image, with the same tag (for example the image with tag `2.5` is not always the same). This means that you should ckeck Docker Hub frequently to see if the base image has received some updates (or subscribe to the official security mailing lists for Ruby, Ubuntu, etc.): if there are new updates, build your image again and deploy.
- **Rails application and dependencies**: remember to update the versions of Ruby, Rails, Gems and Yarn packages used by your application, and any other dependencies.
- **Other**: you also need to update the database and other services outside Kubernetes. Usually it is useful to use managed databases so that your provider applies the security patches automatically and you can forget about this layer.

Basically, if you use managed services (for Kubernetes and database) and you deploy your application frequently, you don't need to do anything special: just keep your Rails app updated. However, if you don't release your application frequently, remember to rebuild the image and don't run your application for months on an outdated base image.

## Conclusion

We have covered all the aspects required for deploying a Ruby on Rails application in production using Kubernetes.

Scaling the application or updating the configuration across hundreds of nodes is now a simple operation that can be managed by a single DevOp. Thanks to the widespread support for Kubernetes you also get a better pricing and portability, compared to PaaS solutions like Heroku.

Remember that in order to achieve availability and world-scale scalability you need to avoid bottlenecks and single points of failure, in particular:

- **the load balancer**, when it is a simple server, may become a bottleneck; you can use better hardware if available, but finally you will have to use *Round-Robin DNS* to increase capacity, by distributing the clients over different load balancers and deployments; if you use a global network like CloudFlare, they can even perform health checks on your load balancers, protect them from DDoS attacks and cache most requests;
- **the database** hosted on a single server may become a bottleneck; you can use better hardware and *hot standby* servers, but finally you may have to move to a DBMS that supports *sharding*, meaning that the data is distributed automatically across different database instances, each one managing a range of keys; the database clients (e.g. inside your Rails app) first query a server in the database cluster to understand the current cluster configuration and then query the correct database instances directly, thus avoiding any kind of bottleneck; moreover each *shard* is usually replicated in order to preserve data in case of hardware failures, and thus it is called a *sharded replica*; strategies similar to what we have described are provided for example by *MongoDB* and *Redis Cluster* and there are many managed solutions available on the market.