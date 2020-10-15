# MqttLogger
create mqtt logger that listens to all traffic on mqtt broker by subscribing to all topics using topic wildcard(#).

the data will be put in an database (eventually).

phoenix webserver will be used to observe logged transactions




# MqttLogger

To start your Phoenix server:

  * Install dependencies with `mix deps.get`
  * Create and migrate your database with `mix ecto.setup`
  * Install Node.js dependencies with `npm install` inside the `assets` directory
  * Start Phoenix endpoint with `mix phx.server`

Now you can visit [`localhost:4000`](http://localhost:4000) from your browser.

## Convenience make tasks
This project includes a couple of convenience `make` tasks. To get the full list
of the tasks run the command `make targets` to see a list of current tasks. For example

```shell
Targets
---------------------------------------------------------------
compile                compile the project
format                 Run formatting tools on the code
lint-compile           check for warnings in functions used in the project
lint-credo             Use credo to ensure formatting styles
lint-format            Check if the project is well formated using elixir formatter
lint                   Check if the project follows set conventions such as formatting
test                   Run the test suite
```

## Deployment to GCP
The deployment is done using docker images with the help of make tasks. We can create a docker image, push it to container registry on gcp and then launch
an instance using this docker image

The docker image is automatically tagged with the application version from your mix file

### Deployment from local machine
**Before you begin:**
- Make sure you have write access to the docker registry
- You will need the necessary permissions to create an instance
- Docker should be installed in your computer and running
- GCloud should be well set up, authenticated and initialised to use docker
- access to production secrets in the `prod.secrets.exs` file (look at `config/prod.sample.exs` to see an example)


#### creating an image for use in your laptop
If you want to create a docker image for use in your laptop then you can use the command
```shell
make docker-image
```

#### Creating an image and pushing to GCP
You can optionally create an image on your laptop and push it up to GCP container registry using the following command
```shell
make push-image-gcp
```
This will create the image and tag it with the current application version then push the created image to GCP

#### creating an image and lauching an instance on GCP
You can also run a server on GCP using the docker image by running the following command
```shell
make push-and-serve-gcp instance-name=<give-the-instance-a-unique-name>
```

If you had created an image before and would like to create a running server using the image run:
```shell
make deploy-existing-image instance-name=<give-the-instance-a-unique-name>
```

The instance name you provide above should be unique and should not be existing on GCP already otherwise you will get an error

#### updating a running instance
If you want to update an already running instance with a different version of the application, you need
to ensure that the image is created and pushed to gcr.io using `make push-image-gcp` after which you can update an instance to use the image.

This is done by specifying the tag to the image you want to use (`image-tag`) and the running instance you want to update (`instance-name`)

```shell
make update-instance instance-name=<existing-instance-name> image-tag=<existing-tag-on-gcr>
```

An example would be:
```shell
make update-instance instance-name=testinstance image-tag=0.5.0
```
#### Custom environment variables
You can set the following custom environment variables when building the image or launching a vm instance

- CLIENT_ID
- MQTT_HOST
- MQTT_PORT
- USER_NAME
- PASSWORD

When running the make commands above you can add any of the variables above that you want to customise for example:

```shell
make deploy-existing-image instance-name=<a-unique-name> CLIENT_ID=<your_new_id> USER_NAME=<prefered_name>
```
### Accessing it from GCP
The above procedures create an instance of twinklymaha
on GCP with the name you gave it.
Using console.cloud.google.compute,
go to your virtual machine instances,
and look up the external ip (a.b.c.d5) of the instance you just created (if you used one of the make commands then the ip address will be listed upon successful startup of the instance).
Note the phoenix webserver is running on port 4000
and the home page is twinkly.
Go to http://a.b.c.d:4000/twinkly
Note it is http not https
