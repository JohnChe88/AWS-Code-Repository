AWS lambda Build, Deploy and test example

Inspired by beabetterdevv/sam-app



AWS SAM(Serverless Application Model)is an open source framework that will help the AWS users to build, test locally and deploy AWS Lambda to the Cloud. We can organize related components configurations and deploy all the connected resources together as an entity. It uses cloud formation behind the scenes for building resources. It provides an environment where you can test AWS Lambda locally and fix bugs, saving cost and time. It integrates well with AWS Code deploy, AWS Codebuild and AWS Codepipeline. The instructions for the build and deploy are provided in the form YAML file. For regular deployment of AWS lambda using AWS cloud formation requires S3 folder for the code artifacts but deployment via AWS SAM cli it handles the folder on its own.
Prerequisites
Install the AWS CLI and AWS SAM on your operating system.
Windows - install using MSI installer link 
Linux- Download the Zip file and follow the link
Mac- install using Brew and follow the link

Build, Test and Deploy

Build 
Create a local python project using any IDE. The python project folder should contain below files for SAM to work
* lambda.py - Main python code for the business logic, 
* requirements.txt- contains all the required libraries. It is formed as subfolder in the AWS lambda console
* event.json - Sample event for the corresponding trigger event. simulates real world scenarios like S3 event, MSK Kafka trigger
* template.yaml -AWS Cloudformation instructions to build the AWS lambda, Role and other triggers if required.

2. Test- To test AWS lambda locally, you can either invoke lambda with the event-generated command or enable HTTP trigger for Lambda invoke and run automated test.

Test Event generation and invoke by SAM
You can generate sample events for local testing using the SAM command line. The command sam local generate-event followed by the AWS resource name and parameters creates the event payload.
The command sam local invoke will invoke a local AWS lambda script and quits after the invocation is completed. Below command simulates a PUT event in the S3 bucket and simulates the invocation of AWS lambda on cloud locally

# using the SAM command you can generate certain AWS resource event locally and test Lambda locally
sam local generate-event s3 [put/delete] --help

# Below command automatically generate the put and delete event locally and pass it to Lambda.py code for testing purposes.
sam local generate-event s3 [put/delete] --bucket <bucketname> --key <prefixname> | sam local invoke -e - <sample SAM function lambda.py >

Test AWS lambda using URL option

This approach allows invoking the local Lambda function and exposing the code using a URL. Once the Lambda is available as a URL then automated test cases can be applied before any code change and deployment . Using the boto3 AWS SDK you can automate the testing by providing different testing scenarios using the URL.

# browse to the folder and start the Lambda
sam local start-lambda [OPTIONS]

#publish the local lambda app to HTTP url
aws lambda invoke --function-name "SAMTestApp" --endpoint-url "http://127.0.0.1:3001" --no-verify-ssl out.txt

3. Deploy- Once the build and local testing is complete, the next step is to deploy lambda to AWS cloud. Using the command line interface, browse to the project folder and run the below command

$ sam build

Above command builds the application and takes care of the dependencies to the related project. You will notice a new folder and build.toml created under the python project folder with details to the build and deploy AWS Lambda

$ sam deploy 
# or
$ sam-deploy --guided

Above command deploy will ask for some questions and confirmation before creating resources using cloud formation and continue with deployment. This process will take a few minutes based on the resource configuration. The deploy command locates all the required files like template.yaml in the current project folder. For more details
Check the AWS Cloudformation console to ensure all the components are build and successfully created. Also check the AWS lambda console to ensure that the function is created and IAM role for the lambda is created.