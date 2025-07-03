# Vehicle-Insurance-Project
End-2-End Vehicle Insurance Prediction in ML

# Roadmap
1. Create project template by executing template.py file
2. Write the code on setup.py and pyproject.toml file to import local packages
   >> Find more about "setup.py and pyproject.toml" at crashcourse.txt
3. Create a virtual env, activate it and install the requirements from requirements.txt
   conda create -n vehicle python=3.10 -y
   conda activate vehicle
   add required modules to requirements.txt
   Do "pip install -r requirements.txt"
4. Do a "pip list" on terminal to make sure you have local packages installed.

----------------------------------------------- MongoDB Setup -----------------------------------------------
5. Sign up to MongoDB Atlas and create a new project by just providing it a name then next next create.
6. From "Create a cluster" screen, hit "create", Select M0 service keeping other services as default, hit "create deployment"
7. Setup the username and password and then create DB user.
8. Go to "network access" and add ip address - "0.0.0.0/0" so that we can access it from anywhere
9. Go back to project >> "Get Connection String" >> "Drivers" >> {Driver:Python, Version:3.6 or later} 
   >> copy and save the connection string with you(replace password). >> Done.
10. Create folder "notebook" >> do step 11 >>  create file "mongoDB_demo.ipynb" >> select kernal>python kernal>vehicle>>
11. Dataset added to notebook folder
12. Push your data to mongoDB database from your python notebook.
13. Go to mongoDB Atlas >> Database >> browse collection >> see your data in key value format

-------------------------------------- logging, exception and notebooks --------------------------------------
14. Write the logger file and test it on demo.py
15. Write the exception file and test it on demo.py
16. EDA and Feature Engg notebook added.

----------------------------------------------- Data Ingestion -----------------------------------------------
17. Before we work on "Data Ingestion" component >> Declare variables within constants.__init__.py file >> 
    add code to configuration.mongo_db_connections.py file and define the func for mondodb connection >> 
    Inside "data_access" folder, add code to proj1_data that will use mongo_db_connections.py
    to connect with DB, fetch data in key-val format and transform that to df >>
    add code to entity.config_entity.py file till DataIngestionConfig class >>
    add code to entity.artifact_entity.py file till DataIngestionArtifact class >>
    add code to components.data_ingestion.py file >> add code to training pipeline >> 
    run demo.py (set mongodb connection url first, see next step)
18. To setup the connection url on mac(also work for windows), open bash/powershell terminal and run below command:
                        *** For Bash ***
    set: export MONGODB_URL="mongodb+srv://<username>:<password>......"
    check: echo $MONGODB_URL
                        *** For Powershell ***
    set: $env:MONGODB_URL = "mongodb+srv://<username>:<password>......"
    check: echo $env:MONGODB_URL

    To setup the connection url on Windows, open env variable setting option and add a new variable:
    Name: MONGODB_URL, Value = <url>
    Also add "artifact" dir to .gitignore file

---------------------------- Data Validation, Data Transformation & Model Trainer ----------------------------

19. Complete the work on utils.main_utils.py and config.schema.yaml file (add entire info about dataset for data validation step)
20. Now work on the "Data Validation" component the way we did in step 17 for Data Ingestion. (Workflow mentioned below)
21. Now work on the "Data Transformation" component the way we did in above step. (add estimator.py to entity folder)
22. Now work on the "Model Trainer" component the way we did in above step. (add class to estimator.py in entity folder)

23. Before moving to next component of Model Evaluation, some AWS services setup is needed:
      * Login to AWS console.
      * Keep region set as - us-east-1
      * Go to IAM >> Create new user (name: firstproj)
      * Attach policy >> select AdministratorAccess >> next >> create user
      * Go to the user >> Security Credentials >> Access Keys >> Create access key
      * Select CLI >> agree to condition >> next >> Create Access Key >> download csv file
      * Set env variables with above csv values using below method:
      ====================================================================================
         >> Set env var from bash terminal: <<
         export AWS_ACCESS_KEY_ID="AWS_ACCESS_KEY_ID"
         export AWS_SECRET_ACCESS_KEY="AWS_SECRET_ACCESS_KEY"
         >> Check env var from bash terminal: <<
         echo $AWS_ACCESS_KEY_ID
         echo $AWS_SECRET_ACCESS_KEY

         >> Set env var from powershell terminal: <<
         $env:AWS_ACCESS_KEY_ID="AWS_ACCESS_KEY_ID"
         $env:AWS_SECRET_ACCESS_KEY="AWS_SECRET_ACCESS_KEY"
         >> Check env var from powershell terminal: <<
         echo $env:AWS_ACCESS_KEY_ID
         echo $env:AWS_SECRET_ACCESS_KEY
      ====================================================================================
      * Now add the access key, secret key, region name to constants.__init__.py
      * Add code to src.configuration.aws_connection.py file (To work with AWS S3 service)
      * Ensure below info in constants.__init__.py file:
            MODEL_EVALUATION_CHANGED_THRESHOLD_SCORE: float = 0.02
            MODEL_BUCKET_NAME = "my-model-mlopsproj"
            MODEL_PUSHER_S3_KEY = "model-registry"
      * Go to S3 service >> Create bucket >> Region: us-east-1 >> General purpose >>
        Bucket Name: "my-model-mlopsproj" >> uncheck: "Block all public access" and acknowledge >>
        Hit Create Bucket
      * Now inside "src.aws_storage" code needs to be added for the configurations needed to pull 
        and push model from AWS S3 bucket. 
      * Inside "entity" dir we will have an "s3_estimator.py" file containing all the func to pull/push
        data from s3 bucket.

24. Now we will start our work on "Model Evaluation" and "Model Pusher" component.

25. Create the code structure of "Prediction Pipeline" and setup your app.py
26. Add "static" and "template" dir to the project.

27. Getting started with CI-CD process:
      * Setup the dockerfile and .dockerignore file
      * Setup the .github\workflows dir and aws.yaml file within
      * Go to AWS console and create a new IAM user exactly the way we did earlier (name: "usvisa-user") >>
        Go inside user >> Security Credentials >> Access Keys >> create access key >> CLI >> check agreement
        >> next >> create access key >> download csv
      * Now create one ECR repo to store/save docker image:
        AWS console >> Go to ECR >> Region: us-east-1 >> Hit create repository >>
        repo name: vehicleproj >> hit create repository >> copy and keep uri
      * Now create EC2 Ubuntu server >> AWS console >> EC2 >> Launch Instance >> name: vehicledata-machine
        >> Image: Ubuntu >> AMI: Ubuntu Server 24.04 (free tier) >> Instance: T2 Medium (~chargeable-3.5rs/hr)
        >> create new key pair (name: proj1key) >> allow for https and http traffic >> storage: 30gb >> Launch
        >> Go to instance >> click on "Connect" >> Connect using EC2 Instance Connect 
        >> Connect (Terminal will be launched) 

28. Open EC2 and Install docker in EC2 Machine:
      ## Optinal
      sudo apt-get update -y
      sudo apt-get upgrade
      ## Required (Because Docker is'nt there in our EC2 server - [docker --version])
      curl -fsSL https://get.docker.com -o get-docker.sh
      sudo sh get-docker.sh
      sudo usermod -aG docker ubuntu
      newgrp docker

29. Next step is to connect Github with EC2(Self hosted runner):
      * select your project on Github >> go to settings >> Actions >> Runner >> New self hosted runner
        >> Select OS (Linux) >> Now step by step run all "Download" related commands on EC2 server 
        >> run first "Configure" command (hit enter instead of setting a runner group, runner name: self-hosted)
        >> enter any additional label (hit enter to skip) >> name of work folder (again hit enter)
        >> Now run second "Configure" command (./run.sh) and runner will get connected to Github
        >> To crosscheck, go back to Github and click on Runner and you will see runner state as "idle"
        >> If you do ctrl+c on EC2 server then runner will shut then restart with "./run.sh"

30. Setup your Github secrets: (Github project>Settings>SecretandVariable>Actions>NewRepoSecret)
      AWS_ACCESS_KEY_ID
      AWS_SECRET_ACCESS_KEY
      AWS_DEFAULT_REGION
      ECR_REPO

31. CI-CD pipeline will be triggered at next commit and push.
32. Now we need to activate the 5000 port of our EC2 instance:
      * Go to the instance > Security > Go to Security Groups > Edit inbound rules > add rule
        > type: Custom TCP > Port range: 5080 > 0.0.0.0/0 > Save rules
33. Now paste the public ip address on the address bar +:5080 and your app will be launched.
34. You can also do model training on /training route

# To Remove Virtual Env
conda env remove -n env_name

# Crashcourse

1. What is a pyproject.toml file?

TOML (Tom’s Obvious, Minimal Language): It’s a simple configuration file format (like JSON or YAML) but is easier to read and write. 
TOML is becoming the standard for Python packaging metadata.

2. Why pyproject.toml is important:

> It was introduced with PEP 518 to modernize Python package building. Previously, everything was done using setup.py 
  but now pyproject.toml allows for more flexibility, better dependency management, and cleaner project configuration.
> It centralizes metadata about the project: project name, version, dependencies, authors, etc.
> It supports various build systems (like setuptools, poetry, etc.).

3. Explaining sections of pyproject.toml:

[project]: Defines the basic project information (name, version, description, authors).
[tool.setuptools]: Specifies that setuptools is being used to build the project.
[tool.setuptools.dynamic]: Links the external files (like requirements.txt) to dynamically pull dependencies.

4. setup.py with the advent of pyproject.toml: Some tasks previously handled by setup.py (like metadata) are now managed 
   by pyproject.toml. However, setup.py can still be used, especially if you have complex build steps.

5. How do setup.py, pyproject.toml, and requirements.txt work together?

> pyproject.toml: It’s now the central place for project metadata. Instead of defining your dependencies and project 
  information in setup.py, you can define them in pyproject.toml.
  As we did in your project, the line [tool.setuptools.dynamic] dependencies = {file = "requirements.txt"} links your requirements.txt 
  file to the TOML file, so when the project is built, the dependencies are fetched from requirements.txt.

> setup.py: While it’s still used for custom builds and configurations, most of the basic functionality (like metadata and dependencies) 
  is being transferred to pyproject.toml. You might still keep a minimal setup.py if you have custom build steps, but for many projects, 
  it’s not necessary anymore with pyproject.toml.

> requirements.txt: It lists all project dependencies and their versions.

When you run pip install -r requirements.txt, it ensures that all dependencies are installed. The pyproject.toml file can reference 
it (as we did) so that package dependencies are automatically pulled from there.


------------------------------------------------------------------------------------------------------------------------------------

Cross-Origin Resource Sharing (CORS)

Original: Allow all origins for Cross-Origin Resource Sharing (CORS) Configure middleware to handle CORS, allowing requests 
from any origin.

Simplified: When you load a web app (e.g., from http://localhost:3000) and it tries to access a server (e.g., http://localhost:8000), 
CORS controls whether the browser allows this connection. By default, browsers block requests from one 
"origin" (like a domain or port) to another for security reasons.

In this code, allow_origins=["*"] is set, which means allow all origins (any domain or app can connect). The middleware 
is added so that your FastAPI app accepts requests from any origin, making it easier for you to access the API from anywhere, 
such as different frontend apps.

------------------------------------------------------------------------------------------------------------------------------------

**Original:** > async

Simplified: `async` (short for *asynchronous*) allows your code to run tasks without waiting for each to finish one by one. 

For example, imagine you request some data from a user form on a website. While waiting for the data to be sent back, instead 
of sitting idle, the app can do other tasks (like handling another user's request) in the meantime.

Using `async` helps the app handle multiple requests faster and more efficiently, especially for tasks like reading form data, 
accessing databases, or making network requests. 

In our code:
----------------------------------
async def get_vehicle_data(self):
    form = await self.request.form()
    # fetch form data here
----------------------------------

This means `get_vehicle_data` won’t block the app. The `await` keyword tells Python to pause this function temporarily while it waits 
for data but allows the app to keep working on other tasks.

In short:
- **`async`** lets the function run without waiting.
- **`await`** pauses the function only when necessary, allowing other parts of the app to keep running smoothly.

------------------------------------------------------------------------------------------------------------------------------------

It seems that the self-hosted runner stopped because it's designed to run in the foreground of the terminal when executed with `./run.sh`. This means that if you closed the terminal session or the EC2 instance restarted, the runner would have stopped. To ensure it runs persistently in the background and starts automatically, you should set it up as a service.

Here’s how to do that on your EC2 instance:

### 1. Navigate to the Runner Directory

First, make sure you’re in the `actions-runner` directory on your EC2 instance:

```bash
cd ~/actions-runner
```

### 2. Set Up the Runner as a Service

To run the self-hosted GitHub runner as a background service, GitHub provides a built-in script to install it as a service.

Run the following command in the `actions-runner` directory:

```bash
sudo ./svc.sh install
```

This command will install the runner as a service, making it easier to start, stop, or restart it.

### 3. Start the Service

After installing the service, start it using the following command:

```bash
sudo ./svc.sh start
```

### 4. Check the Status

You can check the status of the runner service to ensure it’s running:

```bash
sudo ./svc.sh status
```

### 5. Enable the Service to Start on Boot (Optional)

To ensure that the runner service starts automatically when the EC2 instance reboots, you can use the following command:

```bash
sudo systemctl enable actions.runner.<your-runner-name>.service
```

### Additional Notes

- **Replace `<your-runner-name>`**: When checking the status or enabling the service, replace `<your-runner-name>` with the specific name of your runner service. You can find the name of the service by running `systemctl list-units | grep actions.runner`.

- **Re-register the Runner**: If you’re still experiencing issues, there may have been an issue during setup. You might need to re-register the runner. To do this, stop the service, remove the runner, and start the configuration process again with a new registration token from your GitHub repository's settings.

### Example Commands

```bash
# Stop and remove the service (if needed)
sudo ./svc.sh stop
sudo ./svc.sh uninstall

# Re-configure the runner
./config.sh --url https://github.com/vikashishere/MLOps-Test-Proj --token <NEW_TOKEN>

# Re-install and start the service
sudo ./svc.sh install
sudo ./svc.sh start
```

After following these steps, the runner should be persistently running as a background service on your EC2 instance and ready to accept jobs.