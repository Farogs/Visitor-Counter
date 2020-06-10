This is a simple visitor counter :-)

It was built in nodejs, and leverages a number of services including, 
1. Serverless(Lambda): For delivering a cost-effective run-time environment
2. Pusher: For delivering a real-time API to 
   a. Provide the number of people viewing the page
   b. Update this number when someone opens the page or leaves it
3. AWS Environment: This includes configuring an IAM role with administrator priviledges for providing access to all necessary aws services including cloudformation,cloudwatch,cloudtrail etc. 

The services are briefly described below. 

1. Serverless
	This is provisioned by installing the serverless cli tool.
        The command for this is "npm install -g serverless"

        Once the binaries are installed we install the aws Nodejs.template using the following command
        "serverless create --template aws-nodejs"

        This then populates the local directory with the following files,
        - handler.js
        - serverless.yml


        The serverless.yml is a yaml template that describes our services for provisioning to
        our provider, which in this case is AWS. This file can be found in the repository. It includes,
        The service: The name of our service (In this case - visitorcounter)
        The Provider: In this cas: AWS :-)
        The Environment: Pusher
        The functions: These are the building blocks of our service. In this case there is only one 
                       and it is described by,
                       a. a handler: our javacript function that will be executed by lambda when 
                          the funtion is triggered. This can be found in our handler.js file
                       b. an event: this triggers the function. In this case it is a GET request to the
                          root url of our app
                       c. Webhook: This is our endpoint that will get notified by pusher of key events
        
        In our handler, we wrote a home function and exported it from handler.js. In order for this to
        execute properly, we'll install a number of dependencies in a package.json file in our root 
        directory 
                       a. handlebars: v 2.0.11
                       b. pusher: 1.4.1

        We then run "npm install" to install all modules listed in the package.json file and their 
        dependencies
   
        Next we will create our homepage and save the contents in the file "home.hbs", after which we 
        will, update the code in the handler.js file. In this function, we grab the
        handlebars template file "home.hbs", pass the contents to handlebars and render the results
        as a web page in the caller's browser. 

        Real-Time Backend
        The following section is to build the real-time component which is what pusher, a managed
        srvice for delivering APIs provides.
       
        Our backend will record the current count of visitors and pass this to the view before
        rendering. 
        Whenever the page is rendered on a browser, it subscribes to two public pusher chanels
                       a. a channel for receiving updates on the number of visitors (visitors updates)
                       b. a randomly named channel for triggering when a user is occupying the site
                          When the user leaves, the connection will be terminated, resulting in a 
                          channel-vacated notification
                       c. When the backend receives the channel_occupied or channel_vacated 
                          notifications, it re-calculates the visitor count and broadcasts the new
                          value on the visitor-updates channel 
        Now that our serverless environment, our backend (handler.js), and our handlebars template (home.hbs) are scripted, we simply need to 

        1. Pass our IAM credentials to AWS to grant serverless access
        2. Deploy the service by running "serverless deploy"
             
        This will populate and provide us with two endpoints
        1. A GET service for accessing the visitor acounter
        2. A post service for registering the webhooks to pusher for capturing the events.


        Happy Deploying! Thanks!
