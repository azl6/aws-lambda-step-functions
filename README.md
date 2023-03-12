# Practices that I want to do

1. Throw a CW alarm on the number of concurrent executions and throttling of a lambda function
2. Integrate Lambda with SQS queue to automatically process messages as soon as they reach SQS
3. Integrate Lambda with S3 Event Notifications
4. Use Lambda Layers for a lib that is not automatically available. (And create a Lambda Layer section in this repo!)

# Pro tips

**Maximum duration and timeouts**

![image](https://user-images.githubusercontent.com/80921933/224430758-9bf1b56c-47a1-469e-bf15-88176d389184.png)

**Memory increase**

![image](https://user-images.githubusercontent.com/80921933/224430677-ef072590-6297-4aa0-aab5-a91fdb58e7d4.png)

**Pricing and optimization**

Can also use the **Lambda Power Tuner** tool instead of the **Compute Optimizer**

![image](https://user-images.githubusercontent.com/80921933/224431776-0df38a65-09f5-47f4-8cd5-ef0c05f59435.png)

**Minimizing cold start**

![image](https://user-images.githubusercontent.com/80921933/224434036-ef3f2243-8080-4417-befd-fa725129c2ae.png)

**Concurrency**

![image](https://user-images.githubusercontent.com/80921933/224443556-8e6db83d-1c00-48dd-bf2e-e61d6ea33142.png)

# Concurrency types

![image](https://user-images.githubusercontent.com/80921933/224436101-0c384332-4b21-4437-ad1b-9389236979a2.png)

# Unreserved concurrency

This is the default behaviour of Lambda. By default, we can have 1000 in-flight requests per account per region. It means that the 1001st request will be throttled.

# Reserved concurrency

This is when we reserve concurrency for a given function. 

Suppose we have **Function A** and reserve 100 requests for it. That means, the rest of the functions will have 900 concurrent requests available, and **Function A** will have a hard-limit of 100 (the 101st request will be throttled)

![image](https://user-images.githubusercontent.com/80921933/224440801-6ea6d571-04ab-4385-98cc-ec8792a5da76.png)

# Provisioned concurrency

Used to have conteiners always on to run your function and therefore bypass cold-start.

![image](https://user-images.githubusercontent.com/80921933/224441528-8c933dc3-4405-4523-8a2c-0f9e4a90d757.png)

**Provisioned concurrency "depends" on the existence of reserved concurrency.**

![image](https://user-images.githubusercontent.com/80921933/224441848-e886a351-7f4d-45d0-a3e7-b4f1f7915e6a.png)

# Lambda versions and aliases

We can have different versions of the same Lambda function:

![image](https://user-images.githubusercontent.com/80921933/224557915-0e8b58af-0f77-405d-839a-6202165f90e3.png)

We can also alias our versions with a certain string, such as **prod**:

![image](https://user-images.githubusercontent.com/80921933/224558048-ffb3e62c-28c5-4628-b00f-68f727a3927c.png)

Aliases can be used to either associate this string with a version, or to balance a % of traffic to two versions simultaneously (e.g the image above, that show 80% of traffic going to V1 and 20% going to V2)

# Environment variables

We can provide environment variables to our Lambda functions.

```python
import json
import os

def lambda_handler(event, context):
    myEnv = os.environ["AWS_REGION"] # Python's way of grabbing env variables
    print(myEnv)
```

`AWS_REGION` is a pre-built environment variable. We can provide our own by going to **Configuration -> Environment Variables** and adding our desired environment variables.

# Important Lambda metrics

![image](https://user-images.githubusercontent.com/80921933/224559737-fe1697ca-97a7-43ec-ba35-ab49c717db1e.png)

![image](https://user-images.githubusercontent.com/80921933/224559753-fecd7eb2-8ba7-4b3a-8cfe-8fc932fd1eeb.png)

# Lambda metrics filter

By default, **prints in Lambda write to CloudWatch Logs**.

That means that we can define a default "error template" and print them when an error happens, **because this is more cost-effective than using the cloudwatchClient API call**.

```python
import json

def lambda_handler(event, context):
    
    # Whoops, error happened. Let's write this to CW printing it to stdout.
    print("UploadError 1")    
```

Now, in CW, we can go to **Logs Groups -> \<OUR_FUNCTION_LOG_GROUP> -> Metric filters -> Create metric filter**

In the **Filter pattern** field, we can fill the following syntax:

![image](https://user-images.githubusercontent.com/80921933/224563642-3b2d4b2b-1fb9-48e1-a190-174dc7c1ea7d.png)

Let's fill the rest of the options, with the following observation:

- The **Metric value** field should be **$value** (because we named it value in the Filter pattern)
- The **Unit** field should be **Count**

After that, we can go to **Metrics -> All metrics**, select the namespace created for the function, keep going until we find the metric, and when we do, throw the metric into the graph. **Remember:** the field **Statistic** in the **Graphed metrics** section should be **SUM**

![image](https://user-images.githubusercontent.com/80921933/224564248-e99fca68-3433-435a-a996-664a7c4f6d93.png)

# Lambda Power Tuning tool

This tool can be used to generate a graph that will help us to choose the best possible option of memory setup for a Lambda function.

1. Go to https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:451282441545:applications~aws-lambda-power-tuning
2. Click in the following button: <br>
    ![image](https://user-images.githubusercontent.com/80921933/224567655-0c202b86-b6c6-4e77-9425-85c9996e4e62.png)
3. At the end of the opened page, we check the box and click in **Deploy**: <br>
    ![image](https://user-images.githubusercontent.com/80921933/224567761-d0b1085b-733b-4e6c-ab89-e1187e6d8a55.png)
4. The application takes some time to deploy. We can check the progress in the **Applications** section, choosing our deployed application: <br>
    ![image](https://user-images.githubusercontent.com/80921933/224567926-7f102f3d-7d9f-409f-91a7-957942e5b2b4.png)
5. After the **Create in progress** turns into **Create complete**, we can go to the **Step Functions** service and choose the generated state-machine <br>
    ![image](https://user-images.githubusercontent.com/80921933/224568084-6c592a2f-66e2-4bea-81e6-443df53d49e4.png)
6. After choosing the state-machine, we start an execution and provide the following JSON (also documented in the first link of this tutorial) + our Lambda function ARN:

```json
{
    "lambdaARN": "your-lambda-function-arn",
    "powerValues": [128, 256, 512, 1024, 2048, 3008],
    "num": 10,
    "payload": "{}",
    "parallelInvocation": true,
    "strategy": "cost"
}
```

7. Finally, after all steps finish, we can click in the **Optimizer** step, go to the **Output** section and copy the URL to a browser to see the results. <br>
    ![image](https://user-images.githubusercontent.com/80921933/224568742-86fbccf9-19ab-4c7c-aab1-6edf67634604.png)



