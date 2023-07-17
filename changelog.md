### Documentation source - `https://aws.amazon.com/tutorials/scheduling-a-serverless-workflow-step-functions-amazon-eventbridge-scheduler/`

# Implementation steps

1.  Create an AWS Identity and Access Management (`IAM`) role

    AWS Step Functions can run code and access other AWS resources (for example, data stored in Amazon S3 buckets). To maintain security, you must grant Step Functions access to these resources using AWS IAM.

    - Open the AWS Management Console in another browser window, so you can keep this step-by-step guide open. When the screen loads, enter your user name and password to get started. Then enter IAM in the search bar and select IAM to open the service console.

    - Select Roles in the left navigation pane, then choose Create role.

    - On the Select trusted entity page, under AWS service, select Step Functions from the list, and then choose Next.

    - On the Add permissions page, choose Next.

    - On the Review page, enter step_functions_basic_execution for Role name and choose Create role. Your new IAM role is created.

2.  Create an `AWS Step Functions` state machine

    In this step, you will create a simple, independently running state machine using a Pass state. A Pass state simply passes its input to its output, and doesn’t actually perform any work

    - `AWS Step Functions` offers various predefined state machines as templates. For this tutorial, you will create a state machine using the Hello World template. Navigate to the State machines section of the Step Functions console, and choose Create `state machine`.

    - Select Write your workflow in code. `Step Functions` populates the State machine definition pane with the Amazon States Language description of the state machine. For more information on how to define state machines, see State Machine Structure. Choose Next.

    - In the Specify details section, enter the State machine name as ScheduledWorkflow, then, under Permissions > Execution role, select Choose an existing role. Under Existing roles, select the IAM role you created earlier, `step_functions_basic_execution`. Choose Create state machine.

    - Your state machine is now ready to be executed.

3.  Create a schedule using `Amazon EventBridge Scheduler`

    Now that you have created your state machine, you can run your state machine according to a schedule using `Amazon EventBridge Scheduler`. You can create schedules using cron and rate expressions for recurring patterns, or configure one-time invocations without the need to provision and manage infrastructure.

    - Navigate to the `EventBridge Scheduler` console by entering Scheduler in the search bar.

    - On the Schedules page, choose Create schedule.

    - On the Specify schedule detail page, do the following:

      - Enter a Schedule name such as `ExecuteStateMachine` and a Description (optional) such as Execute state machine every minute.
      - In the Schedule pattern section, choose Recurring schedule, then choose `Rate-based schedule`.
      - In the Rate expression section, enter the number `1` for Value, then choose `minutes` for Unit from the dropdown options.
      - For Flexible time window, choose `Off` to turn off the option. Leave all other options as default and choose Next.

    - On the Select target page, do the following:

           - In the Target detail section, choose ``AWS Step Functions`` from the Frequently used APIs options.
           - In the StartExecution section, choose ``ScheduledWorkflow`` from the State machine dropdown.
           - Update the Input with the following JSON code. `{"Payload": "This is a test."}` - Choose Next.

    - On the Settings page, leave all default values. `Amazon EventBridge` will automatically create a new role with required permissions. Choose Next.

    - On the Review and create schedule page, choose Create schedule. A new execution of your state machine will now run every minute.

4.  Customize how your state machine handles input and output

    Next, you will customize how your state machine handles input and output. Understanding how information flows from state to state, and learning how to filter and manipulate this data, will help you to effectively design and implement workflows in `AWS Step Functions`.

    - Back in the `Step Functions` console, your ScheduledWorkflow state machine should still be displayed. Choose Edit.

    - In the Definition window, refer to lines 7 and 12 of your state machine. The Result field in the Pass state specifies the output that should be passed on to the next state. Since your state machine consists of two states executing sequentially, the message “Hello!” will be passed from the first Hello state to the World state, and then subsequently the message “World” will be passed to the end state of your workflow.

    - You may recall that a `Step Functions` Pass state simply passes its input to its output. You can use the ResultPath field to modify the output of a state. For example, you can replace the state input with the result it produces (for example, output from a Task state’s Lambda function). To combine the state’s input with its result, add new lines to both states as follows:

      ```{
      "Comment": "A Hello World example of the Amazon States Language using Pass states",
      "StartAt": "Hello",
      "States": {
      "Hello": {
      "Type": "Pass",
      "Result": "Hello",
      "ResultPath": "$.taskresult",
            "Next": "World"
            },
            "World": {
            "Type": "Pass",
            "Result": "World",
            "ResultPath": "$.taskresult",
      "End": true
      }
      }
      }
      ```

      - This will combine the details of the `EventBridge Event` that invoked the state machine execution with the output of each state. For more information, see Input and `Output Processing` in `Step Functions`. Choose Save.

5.  Confirm your workflow is working as expected

    `Amazon EventBridge Scheduler` is triggering an execution of your state machine workflow every minute. In this step, you will observe those executions and verify that the details of the event are being captured by Step Functions.

    - Back in the State machines section of the `Step Functions` console, select ScheduledWorkflow.

    - Under Executions, you can observe the executions that are fired by `EventBridge Scheduler`. You can select the refresh button to update the window to observe new executions of your state machine appear. Wait a couple of minutes to allow at least two instances of your workflow to be initiated.

    - Open the context (right-click) menu of one of the executions and open the link in a new browser tab. Select the Execution input and output tab.

    You can see that the input event was combined with the output of the state machine.

### Congrats, you have successfully completed this exercise !

6. Clean up resources

   In this step, you will clean up your `AWS Step Functions` and `Amazon EventBridge Scheduler` resources.

   ### Important: Deleting resources that you are not using reduces costs and is a best practice. Not cleaning up your resources can result in a charge.

   - Close the tabs for your individual state machine executions. In the upper left of the Step Functions window, choose State machines. In the State machines window, select the state machine you created for this tutorial and select Delete.

   - Confirm the action by entering delete in the text field and choosing Delete in the Delete state machine dialog box. Your state machine will be deleted in a minute or two once Step Functions has confirmed that any in-process executions have completed.

   - Next, you will delete your Amazon EventBridge Scheduler schedule. In the EventBridge Scheduler console, choose Actions > Delete. Confirm the deletion by choosing Delete again.

   - Choose Services in the upper left and then enter IAM in the search bar to navigate to the IAM console. Select Roles in the left navigation pane. Select both of the IAM roles that you created for this tutorial, then choose Delete. Confirm the deletion by entering delete in the text input field and choosing Delete in the dialog box.

   - You can now sign out of the AWS Management Console.
