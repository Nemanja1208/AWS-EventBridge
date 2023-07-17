### Documentation source - `https://aws.amazon.com/tutorials/scheduling-a-serverless-workflow-step-functions-amazon-eventbridge-scheduler/`

# Implementation steps

1. Create an AWS Identity and Access Management (IAM) role

   AWS Step Functions can run code and access other AWS resources (for example, data stored in Amazon S3 buckets). To maintain security, you must grant Step Functions access to these resources using AWS IAM.

   - Open the AWS Management Console in another browser window, so you can keep this step-by-step guide open. When the screen loads, enter your user name and password to get started. Then enter IAM in the search bar and select IAM to open the service console.

   - Select Roles in the left navigation pane, then choose Create role.

   - On the Select trusted entity page, under AWS service, select Step Functions from the list, and then choose Next.

   - On the Add permissions page, choose Next.

   - On the Review page, enter step_functions_basic_execution for Role name and choose Create role. Your new IAM role is created.

2. Create an AWS Step Functions state machine

   In this step, you will create a simple, independently running state machine using a Pass state. A Pass state simply passes its input to its output, and doesn’t actually perform any work

   - AWS Step Functions offers various predefined state machines as templates. For this tutorial, you will create a state machine using the Hello World template. Navigate to the State machines section of the Step Functions console, and choose Create state machine.

   - Select Write your workflow in code. Step Functions populates the State machine definition pane with the Amazon States Language description of the state machine. For more information on how to define state machines, see State Machine Structure. Choose Next.

   - In the Specify details section, enter the State machine name as ScheduledWorkflow, then, under Permissions > Execution role, select Choose an existing role. Under Existing roles, select the IAM role you created earlier, step_functions_basic_execution. Choose Create state machine.

   - Your state machine is now ready to be executed.
